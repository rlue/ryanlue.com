---
title: PGP Is Hard
teaser: so... maybe don’t worry about it?
category: tips
tags: [pgp, cli]
---

> ### 📣 Big Shout-Out
>
> to [@Jens Erat][] for being the absolute authority on GPG on [SE InfoSec][].
> If you google obscure, highly specific, conceptual questions about GPG
> for more than ten minutes, you’re bound to run into one of his answers.
>
> If you’re reading this, you’re a fkn legend, bro.

GPG keys can be used for four things:

* Certification (_i.e.,_ vouching for other people’s keys)
* Authentication (_e.g.,_ passwordless SSH login)
* Signing
* Encryption

When used correctly, these features solve a couple very thorny problems
with collaboration online: **proof of identity** and **privacy**.
But as with birth control, “when used correctly” is a pretty big, fat, honking caveat,
and GPG keys are very, very, very hard to use correctly.[^1]

Preface: PGP is Hard
--------------------

Or more precisely, they’re hard to _manage_ correctly.
If you had someone else set them up for you, _using_ GPG keys couldn’t be easier
(though it would arguably defeat the purpose).
But managing GPG keys isn’t a one-and-done kind of deal:

* best practices dictate that keys **expire and be renewed** at regular intervals;
* if your keys are lost or compromised, you need a way to **revoke them**
  (_i.e.,_ either a revocation certificate or a spare copy of the master key on-hand);
* you should occasionally [ask other people to **sign your key**][] (and vice versa)
  to build your web of trust;
* when you make changes to _any_ of your keys (master or subkeys),
  you have to **re-publish** your new pubkey and notify everyone you work with;
* conversely, you should make sure you have the **latest version of _other people’s_ pubkeys**
  (in case they’ve pushed a revocation you didn’t hear about);
* and on top of all that, you need to decide where and how to **back up your master key**
  (which can be a subject of [extensive debate and comically paranoid problem-solving][]).

With PGP, you have the power (responsibility, burden, whatever)
to verify the authenticity & secrecy of your messages yourself,
but in exchange, it demands more than just a conceptual grasp of how it works:
you need at least a superficial understanding of how it’s implemented;
an awareness of best practices, limitations, and risks; opinions about threat modeling;
and the confidence that there aren’t any glaring gaps in your knowledge.

With the added impediment of the network effect,
it’s no wonder that virtually everyone
(except devs, whistleblowers, national defense folks, and the like)
is happy to trust their Internet privacy needs to **☑️ blue checkmarks**
and **🔒 End-to-end encrypted** labels in their chat windows instead.

(Not convinced? Look no further than [/r/adultery][],
where cheating spouses [share notes on how they conduct their “OPSEC”][]:
I can scarcely imagine a higher-stakes scenario for most people’s online privacy
than hiding an extramarital affair, and yet [no one there ever has uttered the phrase “GPG”, even once][].
I guess military-grade encryption just isn’t as sexy as I thought.)

Why Do You Use It, Then?
------------------------

To be fair, there are ways to use GPG keys that don’t involve this level of complexity. 
For several years, I've used GPG keys for [SSH authentication][],
as well as encrypting sensitive secrets with [`pass`][].

How to Make It Less Hard
------------------------

GnuPG is notoriously arcane, and while I can’t speak for anyone else,
I had been using GPG keys for several years before I worked up the courage
to mess with my keyring and get a feel for .
(It was really more a mental block than anything else—the veil of cybersecurity black magic it’s shrouded in
fed a kind of don’t-touch-that-you’ll-break-something anxiety
over making the slightest experimental change to my keyring.)

Findings
--------

1. Until you feel like you’ve got the hang of things, you can (and should)
   feel free to experiment liberally with `gpg` on the command line.

   Treat your keyring like a scratch text file and `gpg` like the text editor you’re learning your way around.
   Poke around, try out commands you don’t understand, and don’t hesitate to break things.
   It may not always be clear how to _undo_ a change, but you can always save, delete, and restore:

   ```sh
   # Save
   $ gpg --export-secret-keys --armor $KEY_ID > /tmp/sec.asc

   # Delete
   $ gpg --delete-secret-and-public-keys $KEY_ID

   # Restore
   $ gpg --import < /tmp/sec.asc
   ```

Best Practices\*
----------------

(\*IMHO.)

1. GPG keys support four “capabilities” (see [Intro](#top)).
   Create **one master (primary) key for certification** and **separate subkeys for the other three**.

2. **Set expirations on your subkeys.**
   This will limit how stale other people’s copy of your public key is.
   It will also ensure that if you lose access to one of your private subkeys before you get a chance to revoke it,
   you don’t have a rogue, valid key floating around out there until the end of time (or until its master key is revoked).

3. Keep your master key **encrypted and stored safely offline**.
   Hardware interfaces evolve, so it’s not a bad idea to have a backup on paper, too (_e.g.,_ QR code).

   You will routinely need your master key again
   (_e.g.,_ to renew the expirations on your subkeys, or certify someone else’s key),
   so make sure it’s not too much work to import it again.

4. After you move your private key off of your system,
   re-import your public key and mark it trusted (5/ultimate).

Lorem ipsum dolor.

[^1]:
    By the same token, though, improper use does not reduce the benefits to zero,
    so maybe take this whole piece with a big, fat, honking grain of salt.

[@Jens Erat]: https://security.stackexchange.com/users/19837/jens-erat
[SE InfoSec]: https://security.stackexchange.com/
[ask other people to **sign your key**]: https://gist.github.com/F21/b0e8c62c49dfab267ff1d0c6af39ab84
[extensive debate and comically paranoid problem-solving]: https://news.ycombinator.com/item?id=15408712
[/r/adultery]: https://reddit.com/r/adultery
[share notes on how they conduct their “OPSEC”]: https://www.reddit.com/r/adultery/comments/15mkyz9/what_is_your_opsec_level_whats_your_understanding/
[no one there ever has uttered the phrase “GPG”, even once]: https://www.google.com/search?q=site%3Areddit.com%2Fr%2Fadultery+%22gpg%22
[SSH authentication]: /posts/2017-06-29-gpg-for-ssh-auth
[`pass`]: https://www.passwordstore.org/

Resources
---------

* [A Visual Explanation of GPG Subkeys \| Richard Goulter](https://rgoulter.com/blog/posts/programming/2022-06-10-a-visual-explanation-of-gpg-subkeys.html)
* [drduh / Yubikey-Guide \| GitHub](https://github.com/drduh/YubiKey-Guide)
* [gpg.wtf](https://gpg.wtf)

Questions
---------

* Key ID, keygrip, fingerprint—what’s the difference?

