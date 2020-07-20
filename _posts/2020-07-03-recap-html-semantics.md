---
title: "Reading Recap: About HTML Semantics and Front-End Architecture"
teaser: A condensed summary of Nicolas Gallagher’s 2012 write-up.
category: recaps
tags: [css]
---

From [_About HTML semantics and front-end architecture_][] by Nicolas Gallagher (2012).
Nicolas Gallagher’s post was an inspiration for Adam Wathan’s
[_CSS Utility Classes and “Separation of Concerns”_][],
which in turn introduced [tailwindcss][].

---

HTML5 introduced the concept of **semantic markup**, including descriptive
element names (`<article>`), attributes (`<input list="...">`), and microdata
([`itemscope`][]). The specification even went so far as to explicitly
recommend [applying this principle to class names][]:

> …authors are encouraged to use [class attribute] values that describe the
> nature of the content, rather than values that describe the desired
> presentation of the content.

After getting his hands dirty with it on a number of large projects
(“non-trivial websites and applications that you and others must not only
maintain but actively iterate upon”), Nicolas Gallagher decided that this
recommendation was a mistake—that a class name doesn’t have to be tied to page
content to be “semantic”, and that such content-derived class names tend not
only to be meaningless and redundant, but to make it really difficult to
manage and extract meaningful patterns in a page’s CSS:

```html
<div class="news">         <!-- “news” is a CONTENT-BASED class name -->
  <h2>News</h2>
  <ul class="media-list">  <!-- “media-list” is a PRESENTATIONAL class name -->
  ...
</div>
```

His main points are:

* Class names are principally CSS/JS hooks,
  so the number one consideration when choosing them
  is what is useful to developers.

* Presentational class names lend themselves to all sorts of architectural
  patterns found in other areas of development,
  like “component/template/object-oriented architecture”,
  which confers important benefits to development teams
  (composition, reusability, and extensibility, to name a few).

* If a recommendation based on principle is not pragmatic,
  it might be worth reconsidering the principle.
  That is, if presentational class names enable architectural design
  in ways that content-based class names do not,
  maybe it’s not a crime to think of presentation as something
  worth semantically encoding into your HTML.

  > **Author’s note:** This point runs counter to the approach promoted by
  > [CSS Zen Garden][], which is often upheld as a canonical demo of what can
  > be achieved with strict separation of concerns. But the use case of
  > retheming the same content a dozen different ways doesn’t reflect the
  > actual needs of most websites in production today, so maybe don’t take it
  > as gospel?

* To keep classes maximally composable
  and keep the “cascading” part of CSS predictable,
  always stick to the same (top) level of selector specificity:

  ```css
  .btn { ... }
  .uilist { ... }

  .uilist a { ... }    /* bad */
  .uilist-item { ... } /* good */
  ```

* To keep your JS hooks independent of your CSS hooks
  (so that things don’t break if you start rearranging or refactoring stuff),
  rely on classes prefixed with `js-` exclusively in your JS.

* ```css
  /* This pattern: */
  <button class="btn">Default</button>
  <button class="btn btn-primary">Login</button>

  /* is more composable than this pattern: */
  <button class="btn">Default</button>
  <button class="btn-primary">Login</button>
  ```

* Something like BEM (with `--` / `__` separators) makes it a lot easier
  to infer things about the roles of your classes at a glance.

* Don’t stress about duplication and file bloat in your raw CSS;
  HTML gzip compression makes the differences trivial in the end anyway.
  The development benefits of a little duplication in your raw code will far
  outweigh the performance benefits of the few KB you save after compression.

At first blush, it might seem as if this piece is outdated,
and that newly-established practices in the frontend ecosystem
(_e.g.,_ CSS Modules and styled components)
have rendered the analysis obsolete.
But Nicolas Gallagher’s point is less about naming and namespacing,
and more about organization and taxonomy:
which rules belong together, and according to what organizational principle?

In the end, no amount of tooling can replace a good mental model.

[About HTML semantics and front-end architecture]: http://nicolasgallagher.com/about-html-semantics-front-end-architecture/
[CSS Utility Classes and “Separation of Concerns”]: https://adamwathan.me/css-utility-classes-and-separation-of-concerns/
[tailwindcss]: https://tailwindcss.com/
[`itemscope`]: https://developer.mozilla.org/docs/Web/HTML/Global_attributes/itemscope
[applying this principle to class names]: http://dev.w3.org/html5/spec/global-attributes.html#classes
[CSS Zen Garden]: http://www.csszengarden.com/
