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
    <li class="nameplate__pdf-link"><span class="nameplate__contact-list-marker">📄</span> <a href="cv.pdf">View as PDF</a></li>
  </ul>
</header>

Selected Work History <span class="work-history__note">(5 years’ experience in all)</span>
------------------------------------------------------------------------------------------

### Full-Stack Developer
{: .work-history__position }

* <div class="work-history__tenure">2020–2021</div>
  <div class="work-history__employer">University of Southern California (Freelance)</div>

  Part of an $800,000 NASA research grant, [_Source-Differentiated Air Quality System_][] reports air contaminant levels at military bases across the Middle East.

  Built using Roda + React + Mapbox GL + Tailwind CSS.

  * Sole developer of a **single-page React app + REST API** for
    summarizing data from a 200GB+ dataset.
  * Achieved a **29% reduction in median response time** by replacing the
    default web server.
  * Deployed app to a self-hosted staging server using **Docker + traefik**.
  * Conducted client design review meetings to refine functionality and UI.

### Backend Developer
{: .work-history__position }

* <div class="work-history__tenure">2018–2020</div>
  <div class="work-history__employer">Zammad GmbH</div>

  [Zammad][]{:target="blank"} is a unified inbox for all your customer correspondences, over any channel—email, telephone, live chat, Twitter, Facebook, Telegram, and more.

  Hybrid open-source + SaaS model (700,000+ downloads / 1,200+ corporate subscribers). Built with Rails + CoffeeScript.

  * Completed broad-scoped refactorings & migrations; _e.g.:_
    * rewrote 13,000+ lines of dense, messy, procedural test logic
      as **descriptive, self-documenting specs**.
    * replaced home-rolled authentication logic (used in 150+ classes)
      with a widely-used, commercially-sponsored open-source library.
  * Conducted code review for pull requests from community contributors.
  * Reduced support team workload by rewriting official docs and Getting
    Started guide; authored and edited internal literature and marketing copy.

Open-Source Projects
--------------------

### JavaScript Libraries
{: .projects__section }

* <div class="projects__role projects__role--contributor">Contributor</div>

  <span class="projects__name">[flatpickr][]{:target="_blank"}:</span> Lightweight, powerful datetime picker

  [Discovered and built out missing functionality][] in an official plug-in. Added test coverage around obscure UI bugs.

### Linux Utilities
{: .projects__section }

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Xferase][]{:target="_blank"}:</span> Always-on, fully-automated photo import daemon

  Part of a self-hosted alternative to cloud services like Google Photos or iCloud.

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Cram][]{:target="_blank"}:</span> An extensible toolkit for automating Anki flash card creation

  Studying with Anki? Build “notes” (flash cards) faster with cram.

### Vim Plugins
{: .projects__section }

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Barbaric][]{:target="_blank"}:</span> Automatic input method switching

  vim + non-Latin input = pain. Barbaric is the cure. (⭐ 150+ stars on GitHub!)

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

* <span class="skills__list-marker">🖥️</span> Ruby / Rails / Roda / RSpec
* <span class="skills__list-marker">🖥️</span> React / JavaScript (ES6) /
HTML / CSS 
* <span class="skills__list-marker">🖥️</span> SQL / Relational Databases
* <span class="skills__list-marker">🖥️</span> Linux system administration

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
[_Source-Differentiated Air Quality System_]: https://github.com/rlue/sdaqs-sada
[Zammad]: https://zammad.com
[Discovered and built out missing functionality]: https://github.com/flatpickr/flatpickr/pull/2297
[Xferase]: https://github.com/rlue/xferase
[Cram]: https://github.com/rlue/cram
[Barbaric]: https://github.com/rlue/vim-barbaric
