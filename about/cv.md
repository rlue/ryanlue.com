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

  <span class="projects__name">[Bulavard][blvd]{:target="_blank"}:</span> A social media marketing utility.

  Put your CTA in front of any third-party site whenever you share a link. Built with Rails + Vue.js + Foundation. Deployed with Semaphore + Heroku.

  Responsible for preparing a comprehensive **unit and integration test suite** in RSpec. Implemented premium features for paid users. Extensively **documented internal API** with YARD.

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Bodega][bdg]{:target="_blank"}:</span> A supply chain management tool for wholesalers.

  Made for a local restaurant chain to facilitate logistics and provide automated reports for accounting. Built with Rails (**MVC** / **relational database design** / **RESTful architecture**).

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Marshmallows][msh]{:target="_blank"}:</span> A personal finance app for tracking CD accounts.

  Demo app built with Rails. **Three-tiered user authorization scheme**. Responsive design with Bootstrap. **RESTful JSON API** with token authentication.

### Ruby Gems
{: .projects__section }

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Something Like That][slt]{:target="_blank"}:</span> A fuzzy string matcher for long, multi-word phrases.

  Most fuzzy string matchers use algorithms suited to comparing _individual terms._ SLT uses <dfn>Monge-Elkan</dfn>, a two-stage process designed specifically for _multi-word phrases_ ([Jimenez et al. 2009][mem]{:target="_blank"}).
  
  Good for **ranking search results collected from diverse sources**.

### Vim Plugins
{: .projects__section }

* <div class="projects__role projects__role--creator">Creator</div>

  <span class="projects__name">[Getting Things Down][gtd]{:target="_blank"}:</span> Outlining and TODO shortcuts for GTD in Markdown.

  Provides automatic text folding, TODO highlighting, and per-section progress indicators for tracking productivity in plain text files using the Markdown format.

Skills
------

* <span class="skills__list-marker">💬</span> Technical communication  
* <span class="skills__list-marker">🖥️</span> Ruby / Rails / RSpec
* <span class="skills__list-marker">🖥️</span> HTML / CSS / JavaScript (ES6)
* <span class="skills__list-marker">🖥️</span> SQL / Relational Databases
* <span class="skills__list-marker">🖥️</span> Git

Work History
------------

### Web Development Consultant
{: .work-history__position }

* <div class="work-history__tenure">2017</div>
  [Bulavard, Inc.][blvd]{:target="_blank"}

  * Communicated task progress and project status daily via Slack.
  * Met with founder weekly to discuss product direction.

### International Marketing Associate
{: .work-history__position }

* <div class="work-history__tenure">2015-2016</div>
  [奇想生活 THAT! Inventions][that]{:target="_blank"}

  * Crafted marketing language to support boutique kitchenware brand identity.
  * Supported international distributors in adopting our ecommerce platform.
  * Translated critical sales presentations from Chinese into English.
  * Set up and monitored analytics for a [successful Kickstarter
  campaign][freez]{:target="_blank"}.

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
[blvd]: https://bulavard.com/
[msh]: https://desolate-brushlands-25174.herokuapp.com/
[bdg]: https://bodega.ryanlue.com/
[slt]: https://github.com/rlue/something_like_that
[mem]: http://www.gelbukh.com/CV/Publications/2009/Generalized%20Mongue-Elkan%20Method%20for%20Approximate%20Text%20String.pdf
[gtd]: https://github.com/rlue/vim-getting-things-down
[spr]: https://github.com/rlue/spore
[that]: https://thatinventions.com/
[freez]: https://www.kickstarter.com/projects/that/freezthat-frozen-treats-in-a-flash
[prin]: https://www.coursera.org/learn/algorithms-part1
[stan]: https://lagunita.stanford.edu/courses/Engineering/Networking-SP/SelfPaced/info
[berk]: https://courses.edx.org/courses/course-v1:BerkeleyX+CS169.1x+3T2015SP/info
