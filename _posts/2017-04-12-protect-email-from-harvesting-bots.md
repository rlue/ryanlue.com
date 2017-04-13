---
title: Protect Your Email Address from Harvesting Bots
teaser: Is it still really relevant in 2017?
category: problems
tags: [javascript, security, usability]
---

tl;dr How I Do It
-----------------

Since this site uses Jekyll, I’ve opted to obfuscate my email address programmatically using Liquid:

```liquid
{% raw %}{% assign plain_link = 'mailto:' | append: site.contact %}
{% assign obfuscated_link = plain_link | url_encode | split: '' | reverse | join: '' %}
<a class="email" href="{{ obfuscated_link }}">Contact</a>{% endraw %}
```

Thus, a link to `mailto:pamela@baywatch.org` becomes `gro.hctawyab04%alemapA3%otliam`, which gets corrected at load time with the following JavaScript:

```javascript
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

Contact.patchButtons('email');
```

### Security implications

This solution provides [security through obscurity][sto]{:target="_blank"}, which is fine for a Joe Blow like me to use in a low-stakes scenario like this, but would be totally bogus for a large-scale company when their users’ personal information is on the line. If you want actual security, use a CAPTCHA.

The Problem
-----------

### Need help? Contact pamela (at) baywatch (dot) org

You’ve seen it before: an email address written out as a string of separate words or shown as a PNG image, or even a phone number formatted like so:

> Call Clint at 5 six 2 8 five 8 - 1 five 7 nine

Why do people do this?

Spammers need lists of email addresses, so they buy them from people who write <dfn>email harvesting bots</dfn> that crawl the Internet all day long, looking for text that resembles an email address. Rewriting your contact information in an unpredictable format keeps it out of a bot’s filters.

### Does it work?

Probably. I don’t write these bots, but if I did, I’d definitely go for the low-hanging fruit and not worry too much about building some very sophisticated regular expression to catch outliers. These guys deal in _millions_ of emails, so a single address can only be worth so much. Sifting through all the outliers couldn’t possibly be worth the effort.

(It’s probably [totally unnecessary for phone numbers][phn]{:target="_blank"}, though.)

### Does it matter?

I really don’t know. Spammers certainly have other ways of getting email addresses, too. Unless you know what all those other ways are and have a plan to protect against them (I sure don’t), the benefit is probably negligible.

Of course, as a developer, it’s my responsibility to be a good steward of users’ personal data _even if they themselves are not,_ so if I were to build an app that gave users the option to have their email addresses exposed publicly, I would _never_ pass the buck on this one. In practice, there might not be a single good business reason to devote resources to this problem (I mean, who’s going to figure out that _your site_ is the reason they started getting more spam, if they even did at all? and what are the odds you’d actually lose users over it?). But on principle, it’s _someone’s_ responsibility to understand the nuances of this problem, and that person is not your users.

### Seriously? Who even deals with spam anymore?

Again, I don’t know. _I_ sure don’t. While I do still receive about three spam messages a day, it’s a vanishingly rare occurrence that one will make it past Gmail’s spam filter. Then again, I’m not the average user, and if you’re reading this blog, neither are you.

As seldom as it pops up on our collective radar, the fight against spam is real and ongoing. The U.S. Department of Justice made headlines just today after [taking down a major botnet][kel]{:target="_blank"}:

> Taking Kelihos down will likely result in a substantial decline in the global
> production of billions of spam emails every day, according to [Keith Jarvis,
> a senior researcher at computer security firm SecureWorks]. 

People have been talking about the “end of spam” since [at least 2011][atl]{:target="_blank"}, but its status as a Google search term hasn’t changed much over the past ten years:

<script type="text/javascript" src="https://ssl.gstatic.com/trends_nrtr/981_RC01/embed_loader.js"></script>
<script type="text/javascript">
  trends.embed.renderExploreWidget("TIMESERIES", {"comparisonItem":[{"keyword":"email spam","geo":"","time":"all"}],"category":0,"property":""}, {"exploreQuery":"date=all&q=email%20spam","guestPath":"https://trends.google.com:443/trends/embed/"});
</script>

Don’t get me wrong – I’m not trying to advocate for more spam protection, or argue that it doesn’t get enough attention. Quite the opposite: I’m naturally optimistic, and part of me believes – based on my own personal experience – that maybe spam just isn’t worth worrying about anymore. But as a professional, I have to seek out evidence to keep that part of me in check.

So on the one hand, I hardly ever see spam anymore. On the other, that’s probably the result of the hard work of countless engineers (and activists, lawyers, politicians, and law enforcement) at each link in the chain keeping it from ever getting to me. And in my own work as an engineer, I share a little responsibility in keeping things that way.

Even if you believe that the existing infrastructure is good enough and that it’s totally paranoid to do anything more about it, some of your users are probably a little paranoid, and it’s not unwise to appease them with a little superfluous security.

### ...Fine. But does it have to be so ugly?

The clumsy workarounds described above would be acceptable if you were selling a bike on Craigslist in the early aughts, but it’s hardly befitting of a web developer in 2017. There are better ways, but how much work are they going to take?

Supposing you actually care, think of it like trying not to get your bike stolen – your lock doesn’t have to be bulletproof; it just has to be thick enough to get the thief to bother someone else instead.

Solutions
---------

1. Obfuscate email addresses.
2. Anonymize them by implementing a relay (like [Craigslist does][cl]{:target="_blank"}).
3. Hide them behind a CAPTCHA (like Craigslist does for phone numbers).

### Option 1: The easy way out

Obfuscating email addresses is the simplest, in addition to being virtually unnoticeable (a usability bonus) and not particularly secure (which makes it not much of a solution at all).

#### In plain HTML

[This is how CloudFlare][cf]{:target="_blank"} and GitHub do it (among others): in HTML, every ASCII character can be represented by an <dfn><a href="http://www.freeformatter.com/html-entities.html" target="_blank">entity code</a></dfn> (for instance, “a” is `&#97;`, or `&#x61;` in hexadecimal). If you place this entity code directly into an HTML file, a browser will render the character it represents. Thus, you can create the following link:

> <a href="mailto:&#x70;&#x61;&#x6D;&#x65;&#x6C;&#x61;&#x40;&#x62;&#x61;&#x79;&#x77;&#x61;&#x74;&#x63;&#x68;&#x2E;&#x6F;&#x72;&#x67;">&#x70;&#x61;&#x6D;&#x65;&#x6C;&#x61;&#x40;&#x62;&#x61;&#x79;&#x77;&#x61;&#x74;&#x63;&#x68;&#x2E;&#x6F;&#x72;&#x67;</a>

using the following markup:

    <a href="mailto:&#x70;&#x61;&#x6D;&#x65;&#x6C;&#x61;&#x40;&#x62;&#x61;&#x79;&#x77;&#x61;&#x74;&#x63;&#x68;&#x2E;&#x6F;&#x72;&#x67;">&#x70;&#x61;&#x6D;&#x65;&#x6C;&#x61;&#x40;&#x62;&#x61;&#x79;&#x77;&#x61;&#x74;&#x63;&#x68;&#x2E;&#x6F;&#x72;&#x67;</a>

The problem is that it’d be trivial for a bot to convert all HTML entities before running an email regex on the page. I can’t speak to how often bots are designed to do this, nor to why high-profile, high-traffic sites rely on this method, but in theory, it’s not all that much better than nothing.

If I had to venture a guess, I’d say it’s because it’s simply not worth the effort. If a high-profile site decides that a security hole is worth patching, they’d better do a very good job, and nothing short of a CAPTCHA would constitute a very good job here.

#### With the help of JavaScript

But if you’re small fries, you can get by with a half-baked solution. See the [tl;dr at the top][tldr] if you skipped it.

### Options 2 & 3: If you actually care about security

...and if you really want your users to know you’re doing something about it, CAPTCHAs and relays are probably your best bet.

[sto]: https://en.wikipedia.org/wiki/Security_through_obscurity
[phn]: https://security.stackexchange.com/a/26316
[kel]: http://www.rappler.com/technology/news/166631-us-takes-down-huge-botnet-spain-arrests-notorious-russian-hacker
[atl]: https://www.theatlantic.com/technology/archive/2011/01/the-end-of-spam/69003/
[cl]: https://www.craigslist.org/about/help/email-relay
[cf]: https://security.stackexchange.com/a/116360
[tldr]: #tldr-how-i-do-it