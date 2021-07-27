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
    <li><span class="nameplate__contact-list-marker">🇺🇸</span> Los Angeles, CA</li>
    <li><span class="nameplate__contact-list-marker">📞</span> <a href="64865462651B2%A3%let" class="nameplate__phone">645-6846</a></li>
    <li class="nameplate__pdf-link"><span class="nameplate__contact-list-marker">📄</span> <a href="Ryan%20Lue%20(CV).pdf">Download as PDF</a></li>
  </ul>
</header>

Work History
------------

### Full-Stack Developer
{: .work-history__position }

* <div class="work-history__tenure">2020–Present</div>
  University of Southern California

  [SDAQS][] (“Source-Differentiated Air Quality System”): Look up historical air contaminant levels at U.S. military bases throughout the Middle East.

  Built for a [federal grant funded by NASA][] using Roda + React + Mapbox GL + Tailwind CSS.

  * Designed UI for single-page map application, supported by periodic design feedback sessions with the client.
  * Constructed a simple API to connect the frontend to a client-provided dataset.
  * Deployed self-hosted staging server using **Docker + traefik**.
  * Bundled the application into a Docker image for easy deployment by the client on private networks.

* <div class="work-history__tenure">2018–2020</div>
  [Zammad GmbH][]{:target="_blank"}

  A unified inbox for all your customer correspondences, over any channel—email, telephone, live chat, Twitter, Facebook, Telegram, and more.

  Packed with team-management and automation features. Built with Rails + CoffeeScript.

  * **Migrated a large test suite** from Minitest to RSpec.
  * Added test coverage for previously-untested core business logic.
  * Undertook **major refactorings** to eliminate incidental complexity.
  * Authored & edited official docs, internal literature, and marketing copy.

* <div class="work-history__tenure">2017</div>
  Bulavard, Inc.

  Put your CTA in front of any third-party site whenever you share a link. Built with Rails + Vue.js + Foundation. Deployed with Semaphore + Heroku.

  * Prepared a comprehensive **unit and integration test suite** in RSpec.
  * Implemented premium features for paid users.
  * Extensively **documented internal API** with YARD.

Side Projects
-------------

### JavaScript Libraries
{: .projects__section }

* <div class="projects__role projects__role--contributor">Contributor</div>

  <span class="projects__name">[flatpickr][]{:target="_blank"}:</span> Lightweight, powerful datetime picker

  [Discovered and built out missing functionality][] in an official plug-in. Added test coverage around obscure UI bugs.

### Linux Utilities
{: .projects__section }

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Xferase][]{:target="_blank"}:</span> Always-on, fully-automated photo import utility

  Part of a self-hosted / DIY alternative to cloud photo services like Google Photos or iCloud.

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Cram][]{:target="_blank"}:</span> An extensible toolkit for automating Anki flash card creation

  Studying with Anki? Build “notes” (flash cards) faster with cram. Currently only includes tools for studying Traditional Chinese.

### Vim Plugins
{: .projects__section }

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Barbaric][]{:target="_blank"}:</span> Automatic input method switching

  vim + non-Latin input = pain. Barbaric is the cure. (⭐ 100+ stars on GitHub!)

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

Education
---------

* <div class="education__date">2010</div>

  B.A., Urban Studies and Planning
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

[flatpickr]: https://github.com/flatpickr/flatpickr
[SDAQS]: https://github.com/rlue/sdaqs-sada
[federal grant funded by NASA]: https://appliedsciences.nasa.gov/sites/default/files/2021-03/A10_Franklin_HAQ2019.pdf
[Zammad GmbH]: https://zammad.org/
[Discovered and built out missing functionality]: https://github.com/flatpickr/flatpickr/pull/2297
[Xferase]: https://github.com/rlue/xferase
[Cram]: https://github.com/rlue/cram
[Barbaric]: https://github.com/rlue/vim-barbaric
