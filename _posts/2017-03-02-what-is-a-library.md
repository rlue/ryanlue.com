---
title: What is a Library, and Why Would I Write One?
teaser: Almost simple enough for your mother to understand.
category: programming-101
tags: [gems]
---

What is a Library?
------------------

Broadly speaking, there are two kinds of computer programs: applications and libraries. <dfn>Applications</dfn> are programs for end-users, and are invoked from an operating system (or, in the case of web apps, a browser). <dfn>Libraries</dfn> are programs for programmers, and are invoked from within source code.

Let’s say you want to send your mother an email from your Macintosh computer. Now, the operating system is made to do a lot of things, but sending emails is not one of them. For that, it needs the help of an application (like Outlook, or Chrome + Gmail).

Or, let’s say you want to write a program to send your mother emails for you. Ruby comes with a lot of capabilities built-in, but likewise, dispatching email is not one of them. For that, it needs the help of a library (like [Mail][mail]).

How do you use an application? You double-click to get the operating system to launch it, and then you interact with it using the interface that appears on the screen (<i class="foreign">i.e.,</i> by pointing, clicking, and typing).

How do you use a library? You include it in your code to get the interpreter to load it, and then you interact with it by writing code using the methods described in the documentation (<i class="foreign">i.e.,</i> the <dfn>API</dfn>).

As you can imagine, applications and libraries may overlap in the things they can do, but their use cases are very different.

Why Would I Write One?
----------------------

When you’re first learning to program, you make a lot of toy projects that don’t do anything useful. But as you venture out of the kiddie pool and use programming to solve real problems, you’ll discover that sometimes there are intermediate steps to a solution that are not so easy to overcome.

Maybe you need to [scrape HTML from a webpage][noko]. Maybe you need to [look up the lat/long coordinates of a given address][geo]. Maybe you need to use [a data structure that’s not built into the language][nmp]. You could solve these problems from scratch, but that would require deep technical knowledge that, if you’re reading this article, you most assuredly do not possess. More importantly, they are still only secondary to the problem you originally set out to solve.

Luckily, smarter people than you or I have faced these problems before, and solved them, and extracted those solutions into libraries, and published them for anyone else to use. There may come a time when you are faced with an intermediate step for which no suitable library exists.

When that happens, it’s your chance to pay it forward.

[mail]: https://github.com/mikel/mail
[noko]: https://github.com/sparklemotion/nokogiri
[geo]: https://github.com/alexreisner/geocoder
[nmp]: https://github.com/SciRuby/nmatrix
