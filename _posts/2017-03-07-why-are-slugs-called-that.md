---
title: Why Are “URL slugs” Called That?
teaser: "(Spoiler: it’s newsroom lingo.)"
category: til
tags: [etymology, jekyll, usability]
---

Slug? What’s a Slug?
--------------------

I stumbled across this term while reading up on [Liquid filters used in Jekyll][lqd]{:target="_blank"}:

> #### Slugify
> 
> Convert a string into a lowercase URL “slug”. See below for options.
>
> <b>Filter:</b> `{% raw %}{{ "The _config.yml file" | slugify }}{% endraw %}`
> 
> <b>Output:</b> `the-config-yml-file`

At first, I thought it might be some orthographical format (akin to camel case or snake case), but it turns out it’s about SEO and usability. Definitions abound online, but in layman’s terms, it’s _the part of a URL that helps you guess what’s on the page it leads to_ (which means it’s often just the title of the page).

Not every URL has one, but when it does, this is what it looks like:

* https://www.reddit.com/r/IAmA/comments/z1c9z/<mark>i_am_barack_obama_president_of_the_united_states</mark>/
* https://blogs.dropbox.com/tech/2012/04/<mark>zxcvbn-realistic-password-strength-estimation</mark>/
* http://ryanlue.com/posts/2017-03-07-<mark>why-are-slugs-called-that</mark>

So Where Did It Get Its Name?
-----------------------------

The term [has its origins in journalism][times]{:target="_blank"}: whenever a story is written, it’s registered into a system and labeled with a short (< 10-letter) code, along with all the other stories being considered for the day’s publication. As one <cite>New York Times</cite> editor put it,

> “Slugs make life easier around the newsroom. Instead of saying, ‘Would you
> please shorten that article about President Obama’s immigration announcement
> by 300 words?’ we say things like, ‘Take 300 out of IMMIG. DIPLO can go a
> little long. REPUBS is strong.’ ”

It’s a far cry from the original meaning, if you ask me (notably, newspaper slugs can be reused from day-to-day, whereas URL slugs must be unique forever) – but that’s living language, I suppose.

[lqd]: https://jekyllrb.com/docs/templates/
[times]: https://www.nytimes.com/times-insider/2014/11/24/whats-in-a-slug/
