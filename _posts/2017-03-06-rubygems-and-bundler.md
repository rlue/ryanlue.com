---
title: RubyGems & Bundler in 2 Minutes
teaser: What are they, and how do they fit together?
category: programming-101
hidden: true
tags: [gems]
---

What Is RubyGems?
-----------------

RubyGems is Ruby’s package manager. `gem` automates the process of installing Ruby software on your computer, along with its dependencies (and _their_ dependencies in turn, and so on). Conversely, it provides a format to take Ruby software you’ve written and make it available for automated installation by others.

Gems are typically hosted on Rubygems.org, but the package manager and the hosting platform are not the same thing (<i lang="fr" class="foreign">à la</i> git vs. GitHub).

What Is Bundler?
----------------

Bundler is a software development tool for managing a project’s various dependencies at each stage of the production cycle. That is, Bundler ensures that a machine has all the right versions of the gems a given project depends on, and creates a self-contained runtime environment in which those dependencies (and only those dependencies) are loaded.

What’s the Difference?
----------------------

Bundler is, among other things, a wrapper around RubyGems. You can install gems manually with

    $ gem install [GEMNAME]

or you can make Bundler do it for you by listing them in a project-specific Bundler config file (`Gemfile`) and then running
    
    $ bundle install

The difference is that Bundler keeps track of which _versions_ of which gems you need, _on a project-by-project basis_. This is important for two reasons:

1. Unlike consumer software, libraries used in development should be updated as seldom as absolutely necessary, since unforeseen changes can break existing functionality (which can then break your project).
2. Developers tend to have more than one active project stored on any given computer, so if two projects happen to depend on different versions of the same library (as they invariably will), something’s going to break.  

This means that if you’re using Bundler, you’re using both. And if you’re _not_ using Bundler, either you never have before (in which case you really should) or you know something I don’t (in which case I’d love for you to educate me about it).

Update (13 April 2017)
----------------------

Turns out Noah Gibbs wrote [a _far_ more comprehensive treatment of this same topic][gibbs]{:target="_blank"} around roughly the same time. Give it a read if you’re ready for more.

[gibbs]: https://appfolio-engineering.squarespace.com/appfolio-engineering/2017/2/27/ruby-rubygems-and-bundler
