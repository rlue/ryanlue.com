---
title: How to Publish Your Own Gem
teaser: Find out the best practices, common pitfalls, and how it all fits together before you start.
category: guides
tags: [gems, workflow, foss]
featured_comments:
  - url: 'https://www.reddit.com/r/ruby/comments/61jlka/a_beginners_guide_to_publishing_a_gem_because_the/dg69k9l/'
---

Releasing a gem is easy-peasy:

1. Write the gemspec
2. Build the library
3. `gem build [GEMSPEC_FILE]`
4. `gem push [GEM]`

That’s it! So why are both this guide and the [official guide][myog]{:target="_blank"} in the neighborhood of 2,000 words long? Mostly because Steps 1 and 2 can require a lot of elaboration, depending on how much prior experience you have. Where the official guide is a hello-world tutorial for the absolute beginner, this guide is presented as a conceptual primer and description of best practices for those who are new to the RubyGems system, but not the creation of software libraries in general.

The Blind Men and the Elephant
------------------------------

As a user of the RubyGems system, you never actually come into contact with a gem itself. RubyGems conceals the implementation behind a simplified user interface: run

    $ gem install pry

and <strong>\*poof\*</strong>, the library is there on your system. Now you can run it in the shell

    $ pry

or invoke it from a Ruby file

~~~ ruby
require 'pry'
binding.pry
~~~

without having to know how any of it came to be. It Just Works.

Just what is a gem, then, from a user’s point of view? Nothing more than a **name** and an **API** for getting certain things done. (The astute reader will observe that a gem is also represented as a set of **files** on the filesystem, but even _that_ isn’t the whole picture.) Think of your own experience using gems, and you’re sure to agree that the system is designed to stay out of your way. 

But if you intend to _publish_ a gem, this user-friendly view raises more questions than it answers. To put it all together, you will need to see what the beast looks like from another angle.

The Anatomy of a Gem
--------------------

So let’s peel back the curtain a bit and see what a gem is really made of. This time, rather than _installing_ Pry, we’ll retrieve the originating `.gem` package file.

    $ gem fetch pry
    Fetching: pry-0.10.4.gem (100%)
    Downloaded pry-0.10.4

This is the gem in its raw form – the same form we’ll be packaging our own gem into just before we push it up to the RubyGems server. Let’s take a look inside.

    $ tar xvf pry-0.10.4.gem 
    x metadata.gz
    x data.tar.gz
    x checksums.yaml.gz

As it turns out, a `.gem` file is just a tar archive with three gzip files inside: **metadata** & **data** (authored by you), and checksums (automatically generated when you run `gem build`).

### Metadata

When you install Pry, it comes with a few dependencies (coderay, method\_source, and slop). The `gem install` command checks that these gems are installed on your system, and if not, installs them for you. How does it know Pry’s dependencies? That information (and more) is packaged into the gem as metadata. Take a look inside with `zless`:

    $ zless metadata.gz

    --- !ruby/object:Gem::Specification
    name: pry
    version: !ruby/object:Gem::Version
      version: 0.10.4
    platform: ruby
    authors:
    - John Mair (banisterfiend)
    - Conrad Irwin
    - Ryan Fitzgerald
    autorequire:
    bindir: bin
    cert_chain: []
    date: 2016-07-11 00:00:00.000000000 Z
    dependencies:
    - !ruby/object:Gem::Dependency
      name: coderay
    ...

If you’ve seen the [listing for Pry on RubyGems][pry]{:target="_blank"}, you’ll notice that a lot of this information looks familiar. In fact, each listing on RubyGems is automatically generated from this metadata file – there is no way for developers to manually edit a gem’s listing through the website.

That means that before you can package a gem (Step 3 above), you must compile all of this information into a metadata file of your own (Step 1 above). If you’re sweating over the length of the file you’re looking at now, relax: you don’t write the `metadata.gz` file by hand. Instead, it’s automatically generated from a shorter, simpler file called a gemspec. Pry’s gemspec is only 18 lines long.

Note that this file is extraneous to the library itself. It is not included in what gets installed on your system; rather, it’s a profile of _information about the library,_ without which RubyGems couldn’t do its job. Library + metadata = package.

### Data (or, the library itself)

`data.tar.gz` holds the part of the gem that gets installed to your system. (I wouldn’t judge you for just taking my word for it, but you’re welcome to confirm this on your own.[^1]) 

_Every file that belongs in your gem must be explicitly listed in your gemspec._ Files do not get automatically added just because they were in your project root. The question of exactly which project files should be included in a gem [has been disputed <i lang="la" class="foreign">ad nauseam</i>][rubo]{:target="_blank"}; my recommendations are provided in the following section.

Enough overview. Let’s make some software!

Step 1: Write the Gemspec
-------------------------

You could save it for last, but if you do, you’ll end up having to rewrite your Gemfile to tie the two together (details below), so you might as well just get it out of the way.

There’s no reason to start from scratch, though. Just use another project’s gemspec as a template (you can find them all over GitHub). [Pry’s][prygs]{:target="_blank"} is as good as any, and as promised, it’s only 18 lines long:

~~~ruby
require File.expand_path('../lib/pry/version', __FILE__)

Gem::Specification.new do |s|
  s.name    = "pry"
  s.version = Pry::VERSION

  s.required_ruby_version = '>= 1.9.3'

  s.authors = ["John Mair (banisterfiend)", "Conrad Irwin", "Ryan Fitzgerald"]
  s.email = ["jrmair@gmail.com", "conrad.irwin@gmail.com", "rwfitzge@gmail.com"]
  s.summary = "An IRB alternative and runtime developer console"
  s.description = s.summary
  s.homepage = "http://pryrepl.org"
  s.licenses = ['MIT']

  s.executables   = ["pry"]
  s.require_paths = ["lib"]
  s.files         = `git ls-files bin lib *.md LICENSE`.split("\n")

  s.add_dependency 'coderay',       '~> 1.1.0'
  s.add_dependency 'method_source', '~> 0.8.1'
  s.add_development_dependency 'bundler', '~> 1.0'
end
~~~

Create a file named `<gem_name>.gemspec` in your project root, save this code to it, and edit as needed.

See the [official gemspec reference guide][spec]{:target="_blank"} for more. At a minimum, you must provide the `name`, `version`, `author`, and `summary` attributes, or else `gem build` will fail.

### `s.name` & `s.version`

Check the official documentation for a rundown on the conventions for [naming][nyg]{:target="_blank"} and [versioning][semver]{:target="_blank"}.

You can place the version number directly into the gemspec, but since that never makes its way into the source code, it’s customary to define it as a constant within your gem’s primary namespace:

~~~ruby
# lib/my_new_gem/version.rb
module MyNewGem
  VERSION = '0.1.0'
end
~~~

### `s.files`: What should I include?

When in doubt, consider the context in which your gem is being downloaded, and include only the files necessary for that context.

When other developers **require your gem in their own projects**, they will install it through the RubyGems system (typically by way of Bundler). This group should be able to obtain a lean, production-ready copy of the library, unbloated by files used solely for its own development (<i class="foreign">e.g.,</i> unit tests or `.rubocop.yml`).

When other developers want to **modify or contribute to your gem**, they will usually fork the project on GitHub (using the `homepage` URL you provide in the gemspec). This group should be able to obtain the whole kit and caboodle (with a couple exceptions, noted below).

Thus, use the `files` attribute in the gemspec to include:

* `README.md`, `LICENSE`, and other documentation that cannot be automatically generated from source;
* `lib/` (the library itself); and
* `bin/` (executables, if your gem has any).

Use the git repository to include **everything except:**

* files that can be automatically generated from source (such as RDoc documentation, ctags, and the `.gem` file itself); and
* `Gemfile.lock`[^2]

### `s.add_dependency`: Don’t dependencies go in the Gemfile?

It’s redundant to keep a list of dependencies in two separate places; luckily, Bundler has a built-in facility for inheriting dependencies directly from the gemspec: 

~~~ruby
source 'https://rubygems.org'

gemspec
~~~

That’s the whole Gemfile, start to finish.[^3]

Step 2: Build the Library
-------------------------

This guide assumes that you already know [how to structure a Ruby library][htsarl], and that you have your own preferred tools and workflow. While there is much to be said on this topic, this guide is concerned solely with issues related to the RubyGems system, so there’s just one point to cover in this section: don’t mess with `$LOAD_PATH`.

### How `require` really works

If your library is broken into separate files (it should be), or if you use the interactive console for debugging (you should), you may have run into trouble with `require` before.

What is `require`? It’s the method we used way back in the first section of this guide to load a newly installed gem:

    >> require 'pry'
    => false            # (means it’s already loaded, _not_ that it failed)

This method takes the name of another Ruby file as its argument (minus the `.rb` extension) and then loads the code contained in that file for you to use in your program. How does it know where to look? Ruby’s runtime environment includes a list of directories that get checked whenever you call `require`. This list is stored in the variable `$LOAD_PATH`.

So when we call `require 'pry'`, it _seems_ like we’re just telling the system to load up the gem called ‘Pry’ – but in reality, we’re telling the system to check all the folders in the `$LOAD_PATH` for a file called `pry.rb`, and load up the first one it finds.

To confirm this yourself, try the following command to see where a given gem is stored on your system:

    $ gem which pry

and in the Ruby console, see what happens when you require this file explicitly, rather than the bare gem name:

    >> require `gem which pry`.gsub(/(?<=\w)\.rb\s*/,'')
    => false

The illusion of simplicity is maintained by the RubyGems system, which manages your load path automatically and politely asks gem developers to always provide a top-level source file (or <dfn>pilot file</dfn>) named after the gem it represents.[^4]

### Everything under one roof

What’s in the pilot file, then? More `require` calls! If you can load an entire library simply by requiring one file, then that file must be written to require the rest of the library in turn. As before, let’s look to Pry as a model:

    $ tail $(gem which pry)
    require 'pry/pager'
    require 'pry/terminal'
    require 'pry/editor'
    ...

Pry’s pilot file loads the code from each of its constituent classes one by one using _relative paths._ Relative to what? To the pilot file (and thus, the project root’s `lib/` directory). If you `cd` to the directory where Pry’s pilot file is stored, you’ll find a `pry/` directory alongside it with all these files inside.[^5]

### Getting your project on the load path

This pattern only works because RubyGems automatically adds each gem’s `lib/` directory to the load path. When you `require 'pry'`, Ruby knows where to look, because RubyGems told it to, because it remembers installing Pry on your system.

But if you’ve just started to build your gem, there’s no way for RubyGems to know about it yet, so there’s no way for your project to be on the load path – which means if you follow the scheme described above, it will blow up in your face:

    >> require 'my_new_gem'
    LoadError: cannot load such file -- my_new_gem

So how do you `require` your library for manual testing and debugging when it’s still in its infancy? **Fire up the console with `bundle exec pry` instead**, and let Bundler do the dirty work. (Just make sure your Gemfile and gemspec are set up first.)

Step 3: Home Stretch
--------------------

Once all that’s finished, you’re on Easy Street. Just build and push:

    $ gem build my_new_gem.gemspec
      Successfully built RubyGem
      Name: my_new_gem
      Version: 0.1.0
      File: my_new_gem-0.1.0.gem
    $ gem push my_new_gem-0.1.0.gem
    Enter your RubyGems.org credentials.
    Don't have an account yet? Create one at https://rubygems.org/sign_up
       Email:   
    Password:   
    Pushing gem to RubyGems.org...
    Successfully registered gem: my_new_gem (0.1.0)

Congratulations! In just a few minutes, you should see your new gem on RubyGems.org, now available for inclusion in other people’s projects with a single line of code.

Thanks for your contribution. Developers like you are what make this vibrant community possible. If you found this guide useful, drop me a line – I’d love to hear about the gem you created with it.

Addendum: Workflow Automation with `hoe`
----------------------------------------

_Editor’s note: The following section was added on 12 September 2019._

If you make a habit of releasing and updating gems, you’ll eventually discover that a lot of the work involved is tedious and repetitive. Fortunately for you, you won’t have been the first to notice.

As with nearly everything else in software development, someone older and smarter than you has suffered the same pain (to a far greater degree) and built some tooling to make it disappear. An added benefit of this tooling is that once it has established itself as a norm in its particular development community, it becomes a way of encoding best practices about the problem it solves.

In the case of releasing Ruby gems, one of the oldest and best-known is [hoe](http://www.zenspider.com/projects/hoe.html). Check out the [Hoe Developer’s Guide](http://yukimotopress.github.io/gem-tasks) by Ryan Davis, the author of hoe, to learn more.

---

[^1]:
    If you can’t figure out where Pry is installed on your machine, try:

        $ gem which pry

[^2]:
    [Yehuda Katz explains this at length][gsvgf]{:target="_blank"}, but the upshot is this: the purpose of `Gemfile.lock` is to ensure that anyone else working on your project or running your app has the same exact runtime environment (the same exact versions of all the same dependencies) that you did when you made it. This way, everything is set up exactly as it was the last time your application worked, without any unexpected errors (due to dependencies, at least).  

    But if your project is a gem, you don’t want to enforce dependency versions so strictly. If your gem only works with a very narrow range of versions of its dependencies, that can cause problems down the road for developers who depend on your gem in turn. It’s best to let other contributors test and develop your gem against the full range of dependency versions specified in your Gemfile.

[^3]:
    You might have noticed that the gemspec provides separate attributes for “runtime dependencies” and “development dependencies”. `$ gem install` grabs the former, while `$ gem install --dev` grabs both. Why the distinction?  

    RubyGems predates both Bundler and GitHub by about five years (2003 vs. 2008). Time was someone might `$ gem install --dev` to start making contributions to a project, but I can’t imagine why anyone would choose that method over `$ git clone` in 2017.  

    Apart from supplying information for the `--dev` CLI option, the only other practical consequence of development dependencies is that they will appear in the metadata, and thus in the gem’s listing on the RubyGems site. The importance of either of these benefits is beyond the author’s realm of expertise, but a quick survey of the [top 10 most-downloaded gems of all time][top10]{:target="_blank"} indicates that even seminal projects are divided on this practice: 

    * 3 have no Gemfile at all (<i class="foreign">i.e.,</i> don’t use Bundler, because one of them _is_ Bundler);
    * 2 Gemfiles inherit from the gemspec entirely;
    * 2 list all dev deps in the gemspec, and use the Gemfile for additional, non-dev groups (like `:doc` or `:extra`);
    * 2 list Bundler as the only dev dep in the gemspec, then provide additional `:development`, `:test`, and other dependency groups in the Gemfile; and
    * 1 leaves dev deps out of the gemspec altogether, placing them in groups in the Gemfile instead.  

    As you can see, there is no consensus or common practice here. As I discuss in another post, [this question has been a subject of heated debate][comm] among people much smarter than I.
    
    If I had it my way, the gemspec would be able to inherit from the Gemfile rather than the other way around, but that functionality may never be implemented (because legacy), so do whatever feels right to you.

[^4]:
    I devised the term “pilot file” myself for lack of a better alternative. If you know of one, let me know.

[^5]:
    If this directory structure mystifies you, be sure to read my guide on [how to structure a Ruby library][ofpc].

[myog]: http://guides.rubygems.org/make-your-own-gem/
[pry]: https://rubygems.org/gems/pry
[rubo]: https://github.com/bbatsov/rubocop/issues/1555
[prygs]: https://github.com/pry/pry/blob/master/pry.gemspec
[spec]: http://guides.rubygems.org/specification-reference/
[nyg]: http://guides.rubygems.org/name-your-gem/
[semver]: http://guides.rubygems.org/patterns/#semantic-versioning
[htsarl]: {% post_url 2017-03-03-how-to-structure-a-library %}
[gsvgf]: http://yehudakatz.com/2010/12/16/clarifying-the-roles-of-the-gemspec-and-gemfile/
[top10]: https://rubygems.org/stats
[comm]: {% post_url 2017-03-01-not-getting-stonewalled %}
[ofpc]: {% post_url 2017-03-03-how-to-structure-a-library %}#rule-1-one-file-per-class
