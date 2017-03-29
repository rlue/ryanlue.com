---
title: How to Structure a Ruby Library
teaser: Not a tutorial. Just a rundown of conventions and best practices.
category: guides
tags: [ruby, gems, oop]
---

If you’ve ever been on GitHub, you may have noticed that software projects consist of lots of different files stashed away in lots of different folders. And if you’re like I was, none of the programming books you’ve read or interactive lessons you’ve worked through will have prepared you for this fact. How do you know what folders to create? Which one does each file belong in? Which ones are optional? Is there anything I don’t know about that I’ll look like a total goober for leaving out?

This guide is for those who already know [what a library is][wial] and have some sense of what they intend to build, but still don’t know what to make of the seemingly strict, unwritten conventions surrounding how to create and organize the files they’re made of. 

`lib/` is for Library
---------------------

When you get right down to it, a library is just a program, and a program is made of source code. All the source code constituting the core functionality of your library belongs in the project root’s `lib/` directory. (There are exceptions outlined in the final section below, such as test, executables, and extensions, but we’re principally concerned here with the code that makes your library tick.)

### Rule #1: One file per class

Ruby is an object-oriented language, which means your library should be designed as a set of classes that get things done by sending messages to each other.[^1] Each class or module gets its own file, and the file structure mirrors the program’s namespace hierarchy.[^2] That means that this file

~~~ ruby
module Lagoon
end
~~~

would reside at `lagoon.rb`, while this file 

~~~ ruby
module Lagoon
  class Inlet
  end
end
~~~

would reside at `lagoon/inlet.rb`. Both would be placed under your project root’s `lib/` directory:

    lagoon
    └── lib
        ├── lagoon
        │   └── inlet.rb
        └── lagoon.rb

Note that file structure and namespacing have no effect on each other <i lang="la" class="foreign">per se;</i> the expectation that they match is purely a matter of convention (and [enshrined in Rails][ralp]).

#### Concept check

Take a moment to consider what you would do if you wanted to create a subclass of `Lagoon::Inlet` – say, `Lagoon::Inlet::Stream`.

### Rule #2: Everything descends from a single, unique namespace

You can arrange the class and module hierarchy of your library however you like – just make sure to keep it all under one roof.

That means that this is okay,

    lagoon
    └── lib
        ├── lagoon             # These two files are the only ones
        │   ├── inlet
        │   │   └── stream.rb
        │   ├── inlet.rb
        │   └── reef.rb
        └── lagoon.rb          # in the top-level namespace.

but this is _not_ okay,

    lagoon
    └── lib
        ├── reef.rb            # These guys
        ├── inlet              # don’t belong
        │   └── stream.rb
        ├── inlet.rb           # in the top-level namespace.
        └── lagoon.rb

but this _is_ okay:

    lagoon
    └── lib
        ├── lagoon             # That’s more like it.
        │   ├── reef.rb
        │   ├── inlet
        │   │   └── stream.rb
        │   └── inlet.rb
        └── lagoon.rb

Why? Because a library, like love, is for sharing. And namespaces, like dental dams, let you go a little crazy without everybody getting mixed up in each other’s business.[^3]

#### Concept Check

Does this rule apply to applications (as opposed to libraries)?

### Rule #3: One file to rule them all

So now you have a slew of files organized into a directory tree that mirrors the underlying logical structure of your library. Each of those files is necessary to make it work, so when another program wants to make use of it, that program must be sure to include (or `require`) each separate file.

The customary way to do this is to take your library’s top-level source file (the one that defines its parent namespace) and `require` each of the remaining files in it, one at a time:

~~~ ruby
# lib/lagoon.rb
require 'lagoon/reef'
require 'lagoon/inlet'
require 'lagoon/inlet/stream'

module Lagoon
# ...
~~~

That way, the entire library can be loaded from a single file.[^4]

For this reason, this top-level source file plays a prominent role in your library, though I have yet to encounter a definitive name for it. I have seen it referred to variously as the “root” file, the “main” file, or the “entrypoint”, but since each of these words may be confused with unrelated concepts in computing, I shall refer to it in subsequent writing as the <dfn>pilot file</dfn>. (If you know a better name for it, please let me know.)

Everything Else
---------------

### Required

Your project should also include, at a minimum, `README.md` and `LICENSE` files. Put both in the project root.

### Optional

In addition, you may also have noticed the following in other projects:

* `test/` for tests (if you use Unit::Test or minitest)
* `spec/` for tests (if you use RSpec)
* `bin/` for executables
* `ext/` for extensions written in other languages
* `data/` for raw data your program works on/with
* `doc/` for documentation (if you use RDoc, YARD, etc.)

See the formal specification [here][rps].

Many other files (`.git/`, `Gemfile`, `Rakefile`, `.rubocop.yml`, `.travis.yml`) belong to useful development tools, but are not a part of the library itself. As a general rule, if you don’t know what it’s for, you probably don’t need it (yet). 

Finding out what certain tools are and how to use them can make you a better programmer, but forcing them into projects that don’t need them will neither improve the quality of your software nor give other programmers the impression of competence.

Sharing is Caring
-----------------

If you’ve been keeping up with the footnotes, you’ll know that the conventions outlined above describe _how to prepare_ a library, but not _how to use it._ And not being able to use it can rather interfere with _developing and debugging_ it. 

So as strange as it sounds, to put all this into practice and actually start building a library, you’ll have to jump two steps ahead and learn [how to publish a gem][htpag].

---

[^1]:
    If you don’t know what that means or how to do it elegantly, read [<cite>Practical Object-Oriented Design in Ruby</cite>][poodr] by Sandi Metz, and don’t come back until you’ve finished. <cite>POODR</cite> conveys, in clearer terms than any other book I’ve found, how object-oriented programming works and why it’s a big deal – indeed, why it’s the paradigm around which Ruby (among other powerfully expressive languages) was built. 

    It’s so important, I’ll say it twice – do yourself a favor and read Sandi Metz’s book right now. Don’t add it to your reading list; put down your current read and pick this one up instead. If you don’t understand what you can do with OOP, you’re wasting Ruby’s greatest potential.

[^2]:
    [This StackOverflow question][nmspc] should clarify how namespaces work.

[^3]:
    Except that no one uses dental dams.

[^4]:
    These instructions omit a tedious but crucial detail: once you have arranged your library this way, you’ll still run into errors trying to `require` it – unless you **prepare the runtime environment** by **setting up the load path** first. (That is, unless you tell the interpreter where to look for the files you’re trying to `require`.)

    Ordinarily, this isn’t something you have to do yourself – when you use other people’s libraries (gems), RubyGems automatically manages the load path for you. But the library you’ve just started to build isn’t a fully-fledged gem yet, so RubyGems doesn’t know it exists. If you want to manually test or debug your code, you’ll need to set up the load path somehow.

    Resist the temptation to set it up from within the library itself (a [common anti-pattern][load]). In my next post, I describe [how to get Bundler to do it for you][reqr] – but in any case, it’s crucial that you arrange your library according to convention. Many different development tools and workflows exist, but they all depend on being able to find things where they usually belong. In a post dating from 2009, Yehuda Katz explains how [developers should be free to decide how to manage their load paths themselves][lpth]:

    > Rubygems provides two things for the Ruby community.
    > 
    > 1. A remote repository/packaging format and installer
    > 2. A runtime dependency manager
    >
    > The key to good rubygems practice is to treat these two elements of
    > Rubygems as separate from each other. Someone might use the Rubygems
    > packaging format and the Rubygems distribution but not want to use the
    > Rubygems runtime.
    > 
    > And why should they? The Rubygems runtime is mainly responsible for
    > setting up the appropriate load-paths, and if you are able to get the
    > load paths set up correctly, why should you care about Rubygems at all?
    > 
    > In other words, <mark>you should write your libraries so that their only
    > requirement is being in the load path.</mark> Users might then use
    > Rubygems to get your library in the load path, or they might check it out
    > of git and add it themselves.

    You are free to tamper with the load path in your own applications, but remember: libraries are for sharing, and if you alter the load path from within a library, those changes will be applied in any other project that uses it.

[wial]: {% post_url 2017-03-02-what-is-a-library %}
[ralp]: https://stackoverflow.com/a/11534880/4865822
[rps]: http://chneukirchen.github.io/rps/
[htpag]: {% post_url 2017-02-18-how-to-publish-a-gem %}
[poodr]: https://www.sandimetz.com/products#product-poodr
[nmspc]: https://stackoverflow.com/questions/7821459/whats-the-difference-between-these-ruby-namespace-conventions
[load]: http://guides.rubygems.org/patterns/#loading-code
[reqr]: {% post_url 2017-02-18-how-to-publish-a-gem %}#how-to-require-source-files
[lpth]: http://yehudakatz.com/2009/07/24/rubygems-good-practice/
