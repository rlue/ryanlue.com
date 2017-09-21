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

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Bodega][bdg]{:target="_blank"}:</span> A supply chain management tool for wholesalers.

  Made for a local restaurant to facilitate logistics and provide automated reports for accounting.

  Built with Rails (**MVC** / **relational database design** / **RESTful architecture**). API actions supported by **jQuery/AJAX UI components** for seamless interaction.

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Solana][sol]{:target="_blank"}:</span> A blog template for the Jekyll static site generator.

  **Responsive layout** and **touch gestures** written in HTML, vanilla JavaScript, & CSS (Sass), **without Bootstrap or jQuery**. Features built-in protection against email harvesting bots.

### Ruby Gems
{: .projects__section }

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Something Like That][slt]{:target="_blank"}:</span> A fuzzy string matcher for long, multi-word phrases.

  Most fuzzy string matchers use algorithms best suited for comparing _individual terms._ This library uses the <dfn>Monge-Elkan method</dfn>, a two-stage process designed specifically for _multi-word phrases_ ([Jimenez et al. 2009][mem]{:target="_blank"}).
  
  Good for **ranking search results collected from diverse sources**.

### Vim Plugins
{: .projects__section }

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Getting Things Down][gtd]{:target="_blank"}:</span> Outlining and TODO shortcuts for GTD in Markdown.

  Provides automatic text folding, TODO highlighting, and per-section progress indicators for tracking productivity in plain text files using the Markdown format.

### Command Line Utilities
{: .projects__section }

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Spore][spr]{:target="_blank"}:</span> A lightweight dotfile management solution.

  Populates a home directory with dotfiles from an XDG-style `~/.config` directory, in a manner similar to GNU Stow.

Skills
------

* <span class="skills__list-marker">💬</span> Technical communication  
* <span class="skills__list-marker">🖥️</span> Ruby / Rails / RSpec
* <span class="skills__list-marker">🖥️</span> HTML / CSS / JavaScript (ES6)
* <span class="skills__list-marker">🖥️</span> SQL / Relational Databases
* <span class="skills__list-marker">🖥️</span> Git

Work History
------------

### International Marketing Associate
{: .work-history__position }

* <div class="work-history__tenure">2015-2016</div>
  [奇想生活 THAT! Inventions][that]{:target="_blank"}

  * Crafted marketing language to support the brand identity of a boutique kitchenware maker.
  * Supported international distributors in adopting our ecommerce platform.
  * Translated critical sales presentations from Chinese into English.
  * Set up and monitored analytics for a [successful Kickstarter
  campaign][freez]{:target="_blank"}.

### Academic Program Coordinator
{: .work-history__position }

* <div class="work-history__tenure">2011-2014</div>
  [Trinity Scholar][trin]{:target="_blank"}

  * Developed a research-backed curriculum to prepare Taiwanese teenagers for college overseas.
  * Managed a team of senior educators to prepare day-by-day lesson plans for 36 separate units of instruction.
  * Collaborated with Bay-Area startup [YadaZing][yada]{:target="_blank"} to incorporate video entertainment into our language instruction program.

Education
---------

### Online Courses

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

<!--- 
Interests
---------

* <span class="interests__list-marker">⌨️</span> Vim
* <span class="interests__list-marker">⛰️</span> Hiking
* <span class="interests__list-marker">🐶</span> Dog training
* <span class="interests__list-marker">🔧</span> Bicycle mechanics
-->

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
[bdg]: https://bodega.ryanlue.com/
[sol]: https://github.com/rlue/jekyll-solana
[slt]: https://github.com/rlue/something_like_that
[mem]: http://www.gelbukh.com/CV/Publications/2009/Generalized%20Mongue-Elkan%20Method%20for%20Approximate%20Text%20String.pdf
[gtd]: https://github.com/rlue/vim-getting-things-down
[spr]: https://github.com/rlue/spore
[that]: https://thatinventions.com/
[freez]: https://www.kickstarter.com/projects/that/freezthat-frozen-treats-in-a-flash
[trin]: http://trinityscholar.com/
[yada]: https://angel.co/yadazing
[stan]: https://lagunita.stanford.edu/courses/Engineering/Networking-SP/SelfPaced/info
[berk]: https://courses.edx.org/courses/course-v1:BerkeleyX+CS169.1x+3T2015SP/info
