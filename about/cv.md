---
title: Curriculum Vitae | Ryan Lue
teaser: Software Engineer / Full Stack Web Developer
layout: bare
stylesheet: /about/cv.css
---

<header>
  <a href="moc.eulnayr04%ollehA3%otliam" class="nameplate__email">
    <h1 class="nameplate__heading">Ryan Lue</h1>
  </a>

  <ul class="nameplate__contact">
    <li><span class="nameplate__contact-list-marker">🌏</span> <a href="http://ryanlue.com/" target="_blank" class="nameplate__blog">ryanlue.com</a></li>
    <li><span class="nameplate__contact-list-marker">🇹🇼</span> Taipei, Taiwan</li>
    <li><span class="nameplate__contact-list-marker">🇺🇸</span> U.S. Citizen</li>
    <li><span class="nameplate__contact-list-marker">📞</span> <a href="64865462651B2%A3%let" class="nameplate__phone">645-6846</a></li>
    <li class="nameplate__pdf-link"><span class="nameplate__contact-list-marker">📄</span> <a href="Ryan%20Lue%20(CV).pdf">Download as PDF</a></li>
  </ul>
</header>

Projects
--------

### Web Development
{: .projects__section }

* <div class="projects__role projects__role--core">Core Team</div>

  <span class="projects__name">[Zammad][zmd]{:target="_blank"}:</span> An open-source customer helpdesk platform.

  A unified inbox for all your customer correspondences, over any channel—email, telephone, live chat, Twitter, Facebook, Telegram, and more.

  Packed with team-management and automation features. Built with Rails + CoffeeScript.

* <div class="projects__role projects__role--core">Core Team</div>

  <span class="projects__name">[Bulavard][blvd]{:target="_blank"}:</span> A social media marketing utility.

  Put your CTA in front of any third-party site whenever you share a link. Built with Rails + Vue.js + Foundation. Deployed with Semaphore + Heroku.

### Vim Plugins
{: .projects__section }

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Getting Things Down][gtd]{:target="_blank"}:</span> Outlining and TODO shortcuts for GTD in Markdown.

  Provides automatic text folding, TODO highlighting, and per-section progress indicators for tracking productivity in plain text files using the Markdown format.

Praise for Technical Writing
----------------------------

* [@wilson says](https://github.com/rbenv/ruby-build/pull/1114): “+300, this is a fantastic diff. As soon as a maintainer notices, I hope it gets merged.”
  <div class="print-only"><small>(via https://github.com/rbenv/ruby-build/pull/1114)</small></div>
* [@DirtyF says](https://github.com/jekyll/jekyll/pull/5912): “Much clearer indeed, thanks for this useful addition to the docs ✍️”
  <div class="print-only"><small>(via https://github.com/jekyll/jekyll/pull/5912)</small></div>
* [/u/frickenate says](https://www.reddit.com/comments/6k8nw3/_/dqf6h2f/) : “Thank you so bloody much... Your guide was perfect.”
  <div class="print-only"><small>(via https://www.reddit.com/comments/6k8nw3/_/dqf6h2f/)</small></div>

Skills
------

* <span class="skills__list-marker">💬</span> Technical communication  
* <span class="skills__list-marker">🖥️</span> Ruby / Rails / Roda / RSpec
* <span class="skills__list-marker">🖥️</span> React / JavaScript (ES6) /
HTML / CSS 
* <span class="skills__list-marker">🖥️</span> SQL / Relational Databases
* <span class="skills__list-marker">🖥️</span> Git

Work History
------------

### Full-Stack Developer
{: .work-history__position }

* <div class="work-history__tenure">2018–Present</div>
  [Zammad GmbH][zmd]{:target="_blank"}

  * **Migrated a large test suite** from Minitest to RSpec.
  * Added test coverage for previously-untested core business logic.
  * Undertook **major refactorings** to eliminate incidental complexity.
  * Authored & edited official docs, internal literature, and marketing copy.

* <div class="work-history__tenure">2017</div>
  [Bulavard, Inc.][blvd]{:target="_blank"}

  * Prepared a comprehensive **unit and integration test suite** in RSpec.
  * Implemented premium features for paid users.
  * Extensively **documented internal API** with YARD.


Education
---------

### Online Courses

* <div class="education__date">2017</div>

  [Algorithms, Parts I & II][prin]{:target="_blank"}
  <span class="education__institution">Princeton University, via Coursera</span>

* <div class="education__date">2016</div>

  [Agile Development Using Ruby on Rails][berk]{:target="_blank"}
  <span class="education__institution">UC Berkeley, via edX</span>

* <div class="education__date">2015</div>

  [Introduction to Computer Networking][stan]{:target="_blank"}
  <span class="education__institution">Stanford University, via Lagunita</span>

### Formal Degrees

* <div class="education__date">2010</div>

  Bachelor of Arts in Urban Studies and Planning
  <span class="education__institution">UC San Diego</span>

<script type="text/javascript">
var Contact = {};

Contact.deobfuscateLink = function(element) {
    var absolutePath   = element.href,
        pathSegments   = absolutePath.split('/'),
        obfuscatedLink = pathSegments[pathSegments.length - 1],
        unreversedLink = obfuscatedLink.split('').reverse().join(''),
        deobfuscation  = decodeURIComponent(unreversedLink);
    return deobfuscation;
}

Contact.patchButtons = function(klass) {
    var elements = document.getElementsByClassName(klass);
    for (i = 0; i < elements.length; i++) {
        elements[i].href = Contact.deobfuscateLink(elements[i]);
    }
}

Contact.patchButtons('nameplate__email');
Contact.patchButtons('nameplate__phone');
</script>

[gh]: https://github.com/rlue/
[zmd]: https://zammad.org/
[blvd]: https://bulavard.com/
[msh]: https://desolate-brushlands-25174.herokuapp.com/
[mem]: http://www.gelbukh.com/CV/Publications/2009/Generalized%20Mongue-Elkan%20Method%20for%20Approximate%20Text%20String.pdf
[gtd]: https://github.com/rlue/vim-getting-things-down
[spr]: https://github.com/rlue/spore
[that]: https://thatinventions.com/
[freez]: https://www.kickstarter.com/projects/that/freezthat-frozen-treats-in-a-flash
[prin]: https://www.coursera.org/learn/algorithms-part1
[stan]: https://lagunita.stanford.edu/courses/Engineering/Networking-SP/SelfPaced/info
[berk]: https://courses.edx.org/courses/course-v1:BerkeleyX+CS169.1x+3T2015SP/info
