Smarter, Safer Refactoring with Scientist 🔬⚗️
==============================================

[Scientist][] is a Ruby library that takes the uncertainty out of refactoring.
It’s not for writing tests, and it’s not meant to replace them.  Rather, it
shows you _how refactored code would work in production_ without actually
_using it in production._  If you do any kind of refactoring around code that
you cannot afford to break, Scientist is for you.

This post will cover how to get started with Scientist, along with some common
pitfalls—but first, a little context.

The Trouble with Refactoring
----------------------------

In any business that’s under pressure to deliver working software on time (as
in, _all startups_), refactoring is a tough sell.  It doesn’t fix your bugs
and it doesn’t deliver new features, so its value is entirely invisible to
your customers.  And when your customers are paying you to solve their
problems, you might find it hard to justify paying that money back to your
development team just to redesign code that works already.

But give me an engineer who’s joined a project that someone else built in a
hurry, and I’ll give you someone who swears up and down that it’s long, long
overdue for a facelift.  There’s plenty of great reading out there already
about the virtues of refactoring, so I won’t get into it (but if you need a
little more convincing, [take it from Martin Fowler][], the Father of
Refactoring himself).

Trouble is, when you actually sit down to do it, there’s always the same
hiccup: _the code that really needs to be refactored is fragile,
mission-critical code that no one wants to touch with a ten-foot pole._

![](https://proxy.duckduckgo.com/iu/?u=https%3A%2F%2Fmedia.giphy.com%2Fmedia%2FwYyTHMm50f4Dm%2Fsource.gif)

Sandi Metz [described this phenomenon in a talk last year][].  She calls it
the **Death Star Anti-Pattern**, and goes so far as to say, _“Everyone_ has
this problem.”  Her talk offers OOP principles as a remedy (it’s good; watch
it), but that only fixes half the problem.  Your test suite is _supposed_ to
take care of the other half—namely, that you cannot afford to break the code
you’ve just refactored—but sometimes, it’s just not enough.

Maybe it calls out to a third-party API in a way that your test suite can’t.
Maybe it used to be slow and now it’s not, and a performance regression would
cost you precious customers.  Maybe there are just more inputs and edge cases
than you could possibly plan for. 

Whatever the case may be, you’ve got yourself in a catch-22: You can’t push
your changes to production because you don’t know how they’ll behave in the
wild; on the other hand, you can’t know how they’ll behave in the wild until
you push them to production.  They’re effectively stuck in refactoring
purgatory.

That’s where Scientist comes in.

The Basics
----------

With Scientist, your production codebase contains both old and new versions of
the refactored code _at the same time._  Your app will continue to use the old
version (so it behaves exactly the same as before), but each time the code is
invoked, it test-drives the new version and reports any differences.

![](https://proxy.duckduckgo.com/iu/?u=http%3A%2F%2Fcdn.fansided.com%2Fwp-content%2Fblogs.dir%2F276%2Ffiles%2F2014%2F03%2FSCIENCE-BITCH.gif)

### Usage

is dead simple:

```ruby
science "My first experiment" do |experiment|
  experiment.use { original_code }
  experiment.try { refactored_code }
end
```

(I’ve omitted a little bit of boilerplate to keep things simple, but you can
look it up in the [README][].)

### Configuration

is just a tiny bit messier:

```ruby
class MyExperiment
  include Scientist::Experiment
  attr_accessor :name

  def initialize(name)
    @name = name
  end

  def enabled?
    ...
  end

  def publish(result)
    ...
  end
end

# Tell the `science` method to use your new experiment class
module Scientist::Experiment
  def self.new(name)
    MyExperiment.new(name)
  end
end
```

You’ll have to define custom implementations for `#enabled?` and `#publish`.
One is called _before_ each experiment and determines whether it will run at
all; the other is called _after_ each experiment and—you guessed it—publishes
the results.

If you’re on a Rails project, put this stuff in an initializer.

Check the [README][] for a detailed rundown of configuration options and the
API.  For now, let’s dig a little deeper into the two methods we saw above.

### `#enabled?`

The easiest approach is to just run the experiment every single time:

```ruby
def enabled?
  true
end
```

So why would you do it any other way?

* You probably don’t want to run experiments in your development or testing
  environments.  In a Rails app, limit experiments to staging/production with

  ```ruby
  def enabled?
    Rails.env.staging? || Rails.env.production?
  end
  ```

  There are [other approaches to this][], if you want to get clever.

* Both branches of your experiment run in the foreground, one after the other.
  If they’re resource-intensive operations, it might not be worth it to run
  them every single time.  Former GitHub principal engineer Jesse Toth [explains][]:

  > We often slowly ramp up the percentage of requests that run a Scientist
  > experiment if we think the experiment will be very expensive.  Running an
  > experiment on 1% or 5% of the traffic can be enough to gather a lot of
  > performance and mismatch data.

  The official docs contain a good sample configuration for [“ramping up
  experiments”][] in this way.

### `#publish`

The README contains a great [sample implementation of `#publish`][] that
pushes results to Graphite (via StatsD) and Redis.  But if you don’t have that
kind of reporting apparatus set up around your application, not to
worry—there’s no reason we can’t adapt it to use plain old text files instead.

The following configuration will publish results under
`log/scientist/<experiment-name>/`, with timing and mismatch data stored as
YAML and capped at 1,000 entries max:

```ruby
class MyExperiment
  include Scientist::Experiment
  attr_accessor :name

  def initialize(name)
    @name = name
    FileUtils.mkdir_p(log_dir)
  end

  ...

  def publish(result)
    store_timing_data

    if result.matched?
      increment('matched')
    elsif result.ignored?
      increment('ignored')
    else
      increment('mismatched')
      store_mismatch_data(result)
    end
  end

  private

  # requires active_support/core_ext/string
  def log_dir
    @log_dir ||= Rails.root.join('log', 'scientist', name.parameterize)
  end

  def store_timing_data
    fname = log_dir.join('timing.yml')

    log = begin
            YAML.safe_load(File.read(fname), [Symbol])
          rescue Errno::ENOENT
            { control: [], candidate: [] }
          end

    log[:control].push(result.control.duration)
    log[:candidate].push(result.candidates.first.duration)
    log.values.each { |timings| timings.shift if timings.length > 1000 }

    File.write(fname, log.to_yaml)
  end

  def increment(type)
    fname = log_dir.join(type)
    File.write(fname, File.read(fname).next)
  rescue Errno::ENOENT
    File.write(fname, "0\n")
  end

  def store_mismatch_data(result)
    payload = {
      name:            name,
      context:         context,
      control:         observation_payload(result.control),
      candidate:       observation_payload(result.candidates.first),
      execution_order: result.observations.map(&:name)
    }

    fname = log_dir.join('mismatch_data.yml')

    log = begin
            YAML.safe_load(File.read(fname), [Symbol])
          rescue Errno::ENOENT
            []
          end

    log.push(payload)
    log.shift if log.length > 1000
    File.write(fname, log.to_yaml)
  end

  def observation_payload(observation)
    if observation.raised?
      {
        exception: observation.exception.class,
        message:   observation.exception.message,
        backtrace: observation.exception.backtrace
      }
    else
      {
        value: observation.cleaned_value
      }
    end
  end
end
```

Pitfall: Side Effects
---------------------

One of the biggest gotchas with Scientist is that it’s principally focused on
**return values** rather than **side effects** (functional programmers
unite!).  That is, the success or failure of your experiments depends solely
on whether your `#use` and `#try` blocks (or “control” and “candidate”) return
the same value (though there’s [room for configuration][]).

This can prove problematic when, for instance, dealing with logic that
dispatches emails or processes payments. Because Scientist executes _both_
versions of the code, one of two things must be true:

1. Either the code is idempotent, which means Scientist has no way to verify
   that the second branch of the experiment actually did anything, or
2. the code is _not_ idempotent, in which case emails get sent (or payments
   processed!) twice.

This is a significant limitation of the library, although there is one
potential workaround when the side effect in question is a database write:
wrap your `#try` branch in a transaction and issue a rollback at the end.

Pitfall: Impure Functions
-------------------------

One refactoring I worked on recently was for a “counter” method: each time you
call it, it returns a unique numeric string starting with the current date.
So for instance, if you called it three times today and once tomorrow, the
four return values you’d get are:

* `"201906290001"`
* `"201906290002"`
* `"201906290003"`
* `"201906300001"`

Technically, this constituted a special case of side effects: the numbers are
assigned to “tickets” in the system, so each time the `#try` block runs, it
would advance the counter and a ticket number would be “wasted”.

But even without this quirk, it would still be a poor fit for Scientist.
Imagine if instead the function simply returned the current Unix time: side
effects or no, there’d be no way to reliably compare the return values. 

Unfortunately, I am not aware of any workarounds for impure functions with
Scientist, unless the expected difference between the return values can be
reliably predicted _a priori._

Wrapping Up
-----------

In spite of these pitfalls, Scientist is a remarkably clever tool for
refactoring sensitive parts of your application with confidence.  (There’s a
reason it’s been ported to thirteen other languages!)

If your codebase is in dire need of a redesign, give it a try. Who knows—you
might even manage to convince your client/manager/etc. that a little
refactoring is worth the time.

[Scientist]: https://github.com/github/scientist
[take it from Martin Fowler]: https://refactoring.com/
[described this phenomenon in a talk last year]: https://www.youtube.com/watch?v=XXi_FBrZQiU#t=8m57s
[README]: https://github.com/github/scientist
[other approaches to this]: https://blog.mavenhive.in/5ba0644bc0ac
[explains]: https://www.infoq.com/news/2016/02/github-scientist-refactoring/
[“ramping up experiments”]: https://github.com/github/scientist#ramping-up-experiments
[sample implementation of `#publish`]: https://github.com/github/scientist#publishing-results
[room for configuration]: https://github.com/github/scientist#controlling-comparison
