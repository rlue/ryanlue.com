---
title: 3 Rules for Not Getting Stonewalled on GitHub Issues
teaser: If you’re arguing, you’ve already lost. Good advice for contributors and project maintainers alike.
category: tips
tags: [communication, github, foss]
---

Most issues filed on GitHub (if they are issues at all) fall into one of three categories: <dfn>bug reports</dfn>, <dfn>feature requests</dfn>, and <dfn>other change requests</dfn>. Each type of issue requires stronger communication skills to be taken seriously than the one before it:

* Fixing bugs is a chore, but one which requires no persuasion – bugs don’t go away on their own, and the cost of fixing them later is virtually always greater than the cost of fixing them now.
* Feature requests take more than a little salesmanship – good ideas are a dime a dozen, and each new feature adds to a project’s long-term maintenance workload (or alternately, the risk of eventual code rot).
* But other changes can be met with outright hostility – they may contradict prior design decisions, bulldoze over someone else’s hard work, or break backwards compatibility – all in addition to having long-term maintenance costs of their own.

This is a story about the third kind.

Synopsis
--------

Ruby has a package manager called RubyGems. Like all package managers, it defines a specification for precisely what a package (or <dfn>gem</dfn>) consists of. If you want to create a gem, you have to prepare both <dfn>data</dfn> and <dfn>metadata</dfn> (or, the software itself and a short survey of information about it) to bundle together into a `.gem` file.

I wanted to create a gem. So, I read through the official documentation. It was easy to follow, but I still had some questions. Google turned up no definitive answers – my best lead was [a convoluted and inconclusive discussion on GitHub Issues][dadp], posted to the RubyGems project itself, raising precisely the same question I had been wondering about: how important is it to list development dependencies in the gem metadata?

Here’s how it went down:

GitHub user @e2 wanted to see the `add_development_dependency` attribute deprecated. In the thread, he enumerates his grievances, only to have them rebutted one at a time by a project maintainer (@drbrain). @e2 replies to each point in agonizing detail; his responses exceed _3,000 words_ on more than one occasion. @drbrain defends his position, @e2 tries in earnest to reconcile their views, and after two arduous days of debate… nothing. Christmas comes. The issue is forgotten.

Nearly two months later, the saga continues – another user chimes in, voicing support for @e2’s position, only to be summarily rejected by another project maintainer:

> <b>@evanphx:</b> I don't see any reason to deprecate it. Having development dependencies is still super useful and there is really no reason to remove them.

@e2 resumes the mantle. 1,800 words and five exasperated messages later, on the very same day that the issue was revived, he closes it in defeat.

So what went wrong?

Lessons
-------

### 1. Say less, ask more

To be clear, this is not to say that I disagree with our ill-fated hero. But there is an important difference between being right and being heard. If you fail to recognize this distinction, you’re going to wind up wasting your breath.

How do you get heard? Remember who you’re talking to, and **get them on your side by showing some humility.** Rather than this:

> #### Please deprecate `add_development_dependency`
> 
> The main reasons:
>
> [Reasons 1–10…]
> 
> Summary: gem development and testing is not the responsibility of RubyGems - 
> and should never be, because otherwise it's just wasting precious developer 
> hours.

try this:

> #### Deprecate `add_development_dependency`?
> 
> I am running into problems integrating the `add_development_dependency`
> gemspec attribute into my workflow, and am having trouble understanding its
> purpose in light of the following…
> 
> Do you think that it could make sense to deprecate it altogether, or are there
> valid use cases I’ve missed here?

Free software is anarchical in theory but hierarchical in practice[^1] – maintainers work on a volunteer basis, and rightly expect a little authority in return. It doesn’t matter if you’re Supreme Master Linus Torvalds with a sage decree from on high; if you come off sounding like you think you know better than the project maintainers do, then the remainder of the conversation will be spent addressing that perception.

In the beginning, give the original design the benefit of the doubt. If something disagrees with your sensibilities, assume there’s a good reason that you haven’t thought of. Personalize the problem (“I’m having trouble with something”), or if you know it’s affected other people, cite your sources. Even then, frame it as a user error that could be mitigated with an improved design, rather than a design flaw in itself (if at all possible). _Never give someone cause to go on the defensive._ Not because she might be right in the first place (she might), but because once you do, you can forget about getting her to see things your way.

#### Corollary: Respond less, explain more

If you are a project maintainer and see yourself being led into an unproductive or adversarial exchange, don’t be afraid to short-circuit it. While the onus is not on you, you may spare yourself a heap of trouble if you **take a proactive approach and make the case for the existing design** rather than responding to each of your opponent’s points in turn. (And if you can’t do that, perhaps the request merits a second look.)

In the above case, a more conclusive response might have been,

> As a package management format, RubyGems aims to hold an authoritative and
> _complete_ representation of any given gem – meaning both production and
> development versions. We don’t believe it makes sense for two coevolving
> versions of a piece of software to be represented separately on two different
> platforms.
>
> It sounds like the current arrangement is not ideal for the use cases and
> workflows you are familiar with, but I can confirm that this feature is still
> in use. We recognize the need for improvement and are open to suggestions,
> provided they are consistent with the goal stated above.

It may take an extra ten minutes to step back and find the right angle, but as they say, an ounce of prevention is worth a pound of cure.

### 2. One > many

Just as there is a difference between being right and being heard, so is there between having the right answer and knowing the right question. What’s the right question, then? _Exactly._

@e2 enumerates ten reasons for the change he’s requesting, but when they fall apart under scrutiny, he conveniently reframes (or forgets) their relevance to his argument. This is what became of the first of ten reasons he listed (heavily pared down, but unparaphrased):

> <b>@e2:</b> [this feature I want deprecated] is used incorrectly more often than not.
> 
> <b>@drbrain:</b> I've never heard this complaint before.
> 
> <b>@e2:</b> <i class="tacit">(Provides examples of `gem install --dev` throwing errors.)</i> All 4 examples suggest incorrect usage…
> 
> <b>@drbrain:</b> It seems that is a bug in RubyGems which should be fixed, not a reason to remove development dependencies.
> 
> <b>@e2:</b> No one actually cares it's even broken… Instead of working of fixing something no one cares about, I believe it would be better to show a deprecation.
> 
> <b>@drbrain:</b> I've fixed at least two bugs in development dependency installation in the past six months… so I know the feature gets used.
> 
> <b>@e2:</b> <i class="tacit">(Abandons this line of argument, without acknowledgment.)</i>

@drbrain addresses each “reason” in like fashion, to no effect on @e2’s conviction. Why? Because none of his ten reasons is the _real_ reason for his request. The real reason was hiding in plain sight, under the so-called “summary” of his original post:

> <b>@e2:</b> Gem development and testing is not the responsibility of RubyGems…

Imagine how much more on-point the conversation would have been if this were the _only_ thing he’d said to start with. Ironically, the issues he raised to buttress his main point only served as a wall of noise to drown it out.

Project maintainers are generally eager to help their users. Help them help you by **presenting a single problem and letting them explore solutions with you**, rather than giving an exhaustive case to negotiate for your particular idea.

#### Corollary: Don’t take the bait

If you are a project maintainer, don’t waste your time swatting down weak support for an argument. Don’t comment on it, don’t dismiss it, don’t even _acknowledge_ it – anything you say will invite a response, which will quickly lead you both down a long road to nowhere.

Instead, **play the devil’s advocate** and reframe the argument altogether, distilling it down to the core point _you_ think is worth talking about:

> It sounds like you’re saying that `add_development_dependency` doesn’t
> belong in RubyGems because development versions of gems should be managed
> with Bundler and published separately from production versions. Would that
> be fair to say?

If you can make his point better than he can, odds are he will happily concede all the cruft you left out.

### 3. Blog about it

On the rare occasion that you are invited to present your case, resist the temptation to address every single point your opponent has raised. It’s a self-perpetuating anti-pattern in communication; when a message takes this form, it invites responses to follow in kind. It sabotages communication in subtle ways: the spirit of cooperation is the first to go, followed shortly by organization, clarity, and succinctness.

@e2 is an exemplary victim to this temptation. Communicating in this way, he logs over 8,000 words in just three days of discussion – that’s _15–20 pages in paperback form_. At one point, [@drbrain openly admits][tldr1],

> I've read and skimmed about a fifth of what you've written, I don't have the time or energy to read it all…

On a different thread, [@mvz remarks][tldr2],

> @e2 your comment was indeed TL, so I DR it until now ;-).

Such herculean feats of expression seldom yield any kind of return. Communication is, at heart, a collaborative effort – if your intended audience deems your message too long to be worthy of its attention, then you’ve failed before you’ve begun.

For comparison, this 2,000-word blog post has taken me the better part of a week to put together. (If the thought of investing that much time in articulating your ideas does not appeal to you, see my remarks above on being right vs. being heard.) If you’re not seeing eye-to-eye, **take a day to cool off and reformulate your ideas in a blog**. Framing your ideas for a broader audience will clarify your thinking and release you from the clutches of point-by-point disputation.[^2]

Final Thoughts
--------------

Part of what makes working in software development so gratifying is the opportunity to collaborate with some exceptionally intelligent minds. They say the smartest people make the worst communicators because when you’re too smart, you forget how much other people don’t know. In my experience, poor communicators are rarely _that_ smart.

Thankfully, extended disagreements like this are not as common as you might expect, given the challenges faced by the open-source community. When they do arise, however, knowing how to handle them delicately can spare everyone a load of troubles: needless frustration, wasted time, eroded trust, and – in extreme cases – forked projects and split user bases.

Remember:

1. Get them on your side.
2. One thing at a time.
3. Explain it to an outsider first.

If all that fails, you might be better off leaving well enough alone.

---

[^1]:
    Eric Steven Raymond discusses this contradiction in the [third section][ptpp] of his landmark essay, <cite>[Homesteading the Noosphere][htn]</cite> (2000).

[^2]:
    Daniel Dennett presents a variation on this point in <cite>Intuition Pumps and Other Tools for Thinking</cite> (2014), remarking that academic philosophers can sometimes wind up talking past each other,

    > When experts talk to experts, whether they are in the same discipline or
    > not, they always err on the side of under-explaining. The reason is not
    > far to seek: to overexplain something to a fellow expert is a very
    > serious insult—“Do I have to spell it out for you?”—and nobody wants to
    > insult a fellow expert. So just to be safe, people err on the side of
    > under-explaining.

    and that a handy remedy is to have them present their arguments to a “lay audience” (in this case, a group of students with no specialized training in philosophy):
    
    > The experts often find that being set the task to explain their position
    > under these conditions helps them find better ways of making their points
    > than they had ever found before. Sometimes these experts have been
    > “protected” for years by layers of fellow experts, postdocs, and advanced
    > graduate students, and they really need the challenge.

[dadp]: https://github.com/rubygems/rubygems/issues/1104
[tldr1]: https://github.com/rubygems/rubygems/issues/1104#issuecomment-67688646
[tldr2]: https://github.com/bbatsov/rubocop/issues/1555#issuecomment-76669210
[ptpp]: http://www.catb.org/esr/writings/homesteading/homesteading/ar01s03.html
[htn]: http://www.catb.org/esr/writings/homesteading/homesteading/

