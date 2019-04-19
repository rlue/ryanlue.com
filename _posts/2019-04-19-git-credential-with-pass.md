---
title: git-credential with Pass
teaser: Because storing passwords in plaintext is for suckers.
category: tips
tags: [git, cli, foss, pgp, security]
---

I just stumbled upon [an absolutely fantastic, couldn’t-be-simpler tutorial
for _git send-email_][]. It really tickled the old-school, lo-fi geek in me to
see git’s email workflow in action, and it only took a couple minutes—the
developer’s equivalent of a cigarette break, really.

Just one snag: I couldn’t figure out how to set up credentials management
(_i.e.,_ “passwordless” authentication) for _git send-email,_ and my brain
absolutely refused to postpone this problem until I actually needed a solution
(which will probably be never).

### Context

I use [Pass][] to manage my passwords. I know every modern desktop has its own
credentials manager (OS X Keychain, gnome-keyring, kwallet), but I prefer Pass
for its simplicity, portability, and adherence to the UNIX philosophy (it
stores passwords as GPG-encrypted plaintext files in your home directory). The
one beef I have with it is its tragically generic name, which makes it a total
nightmare to Google for.

Anyway, I already have git configured to use Pass for authentication to
GitHub. It looks like this:

```conf
[credential "https://github.com"]
	helper = !pass git/rlue@github
```

where the output of `pass git/rlue@github` is:[^1]

```conf
username=rlue
password=password
```

But for the life of me, I couldn’t figure out how to extend this pattern to
_git send-email_ authentication, and the official docs for
[`git-send-email`][] and [`git-credential`][] were not much help.

### The solution

<https://git-send-email.io> already instructs you to configure git with your
SMTP config:

```conf
[sendemail]
	smtpserver = smtp.example.com
	smtpuser = rlue@example.com
	smtpencryption = ssl
	smtpserverport = 465
```

With this in your `.gitconfig`, you can basically follow the same model as the
“credential” section above; you just need to HTML-escape the special
characters in the address of the server you’re authenticating against:[^2]

```conf
[credential "smtp://rlue%40example.com@smtp.example.com%3a465"]
	helper = !pass git/rlue@example.com
```

where the output of `pass git/rlue@example.com` is:

```conf
password=password
```

(You only need the “password” line here since the “sendemail” section above
already gives the username.)

---

[^1]:

    If you’re using two-factor authentication, you’ll want to generate a
    [“personal access token” (GitHub)][] or [“app password” (Gmail)][] first
    and use that instead of your actual password here.

[^2]:

    I figured this out by using the `store` git-credential helper first, then
    inspecting the plaintext credential file it created at
    `~/.git-credentials`.

[an absolutely fantastic, couldn’t-be-simpler tutorial for _git send-email_]: https://git-send-email.io/
[pass]: https://www.passwordstore.org/
[`git-send-email`]: https://git-scm.com/docs/git-send-email
[`git-credential`]: https://git-scm.com/docs/git-credential
[“personal access token” (GitHub)]: https://github.com/settings/tokens
[“app password” (Gmail)]: https://myaccount.google.com/apppasswords
