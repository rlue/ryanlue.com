---
title: Using a GPG key for SSH Authentication
teaser: One less private key to keep track of.
category: guides
tags: [ssh, pgp, cli, mac]
reddit_post: 'https://www.reddit.com/r/linux/comments/6k8nw3/killed_two_days_figuring_out_how_to_use_gpg_keys/'
featured_comments:
  - url: 'https://www.reddit.com/r/linux/comments/6k8nw3/killed_two_days_figuring_out_how_to_use_gpg_keys/djkgbtn/'
    context: true
  - url: 'https://www.reddit.com/r/linux/comments/6k8nw3/killed_two_days_figuring_out_how_to_use_gpg_keys/djkthy8/'
  - url: 'https://www.reddit.com/r/linux/comments/6k8nw3/killed_two_days_figuring_out_how_to_use_gpg_keys/djk8fbu/'
    context: true
  - url: 'https://www.reddit.com/r/linux/comments/6k8nw3/killed_two_days_figuring_out_how_to_use_gpg_keys/djkkrif/'
    context: true
---

PGP keys and SSH keys are different. It took me longer than I care to admit to understand this, given that the [first StackExchange thread I found on the subject][interch]{:target="_blank"} made it pretty clear that it’s not trivial to use one where you’re supposed to use the other.

To be fair, the _are_ the same _kind of_ thing — that is, asymmetric encryption keys. They can even use the same encryption algorithms. But they are different in their implementation, which makes them not the same thing.

> #### Confused?
> 
> Asymmetric encryption keys are files (technically pairs of files) that are
> used to prove your identity in the course of your digital life — sort of
> like a password, but far more versatile.
> 
> An SSH key authorizes you to log into an SSH server without a password (see
> [this guide from Indiana University][iu]{:target="_blank"} for details), and a PGP key can be
> used to sign a document or a git commit (_i.e.,_ prove you’re the one who
> made it) or decrypt messages intended only for you, among other things.
>
> A note on terminology:
> 
>   * **PGP** (“Pretty Good Privacy”) is the commercial, proprietary software
>     package which introduced PGP encryption to the world.
>   * **OpenPGP** is an Internet Standard message format ([RFC 4880][rfc]{:target="_blank"})
>     initiated by PGP Inc. and followed by all PGP encryption software.
>   * **gpg** (“GNU Privacy Guard”) is a free and open-source PGP
>     encryption program that most people use to manage their keys.
>
> It’s easy to get these terms mixed up, but usually when people say “PGP”,
> they’re talking about the OpenPGP standard.

In any case, I almost never use my PGP key, so I still don’t really know _how_ to use it. But I use my SSH key all the time, so I figured, why not consolidate?

### Because it will eat a day and a half of your precious time.

(Too late!)

As mentioned above, PGP keys and SSH keys are not the same thing, so you can’t use one in place of the other. However, GnuPG _does_ support the use of its PGP keys for SSH authentication. Believe you me, it can be done: the documentation is sparse, but the spirit is willing and the flesh is greasy and sun-starved, and yearns to remain so. My loss is your gain! Come along with me, and you too can make gpg your bitch.

The Players
-----------

* `ssh`

  The OpenSSH client. I believe you need at least v6.7; check with `ssh -V` and upgrade with `brew install openssh` if necessary.

* `ssh-agent`

  Comes bundled with OpenSSH. Its job is to cache SSH key passwords for the duration of the current login session. (That is, you can set a passphrase on your SSH key for extra security. You’ll be prompted for this passphrase the first time you log in, and if ssh-agent is running, you can use the same key to log into other servers, or log out and back in again, without needing the password.)

  GnuPG provides its own utility as a stand-in for ssh-agent ([and has since at least 2005][ssh-agent]{:target="_blank"}), which means **ssh-agent is _not_ required for this to work**. In fact, better safe than sorry: make sure ssh-agent is _not_ running (`ps x | grep [s]sh-agent`). If it is, kill it (`ssh-agent -k`, or `killall ssh-agent` if that doesn’t work). To make sure it stays dead, check your login scripts for a line containing `eval "$(ssh-agent)"` or similar and remove them.

* `gpg`

  The GnuPG suite. You need at least v2.1; check with `gpg --version` and upgrade with `brew install gpg` if necessary.

* `gpg-agent`

  Comes bundled with GnuPG and serves the same purpose that ssh-agent does: caches passwords so you don’t have to enter them twice in the same session.

  There is a separate, keg-only brew formula for gpg-agent. Don’t install it; just use the one that comes with gpg.

* `pinentry` / `pinentry-mac`

  This is a lightweight program used to accept password input so that GnuPG doesn’t have to (for more on the security considerations behind this design, see [here][pinentry]{:target="_blank"}). This is installed as a dependency of `gpg`, but fails to be invoked by `ssh` for reasons beyond the scope of this guide. The GUI version of the utility, `pinentry-mac`, is the easiest alternative.

The Procedure
-------------

### Generating an authentication-only subkey

Keys are used for three purposes (signing, encrypting, and authenticating), and it’s a best practice to generate and use separate **subkeys** exclusively for each separate purpose.

If you’re reading this guide, you probably already have a PGP key; we just need to make sure you have an **authentication-only subkey**. If you do, you should see output for the following command:

    $ gpg -k | grep "\[A\]"

and may skip down to Step 1 below.

If not, follow the directions under “Create an authentication subkey” [here][incenp]{:target="_blank"}.

During the process, you’ll be prompted for your private key’s passphrase. It’s not stated anywhere in the prompt, but you actually have two options:

  1. enter the passphrase (in which case, that same passphrase will apply to your new subkey), or
  2. enter nothing (in which case, you will be prompted to set a separate passphrase for your new subkey, and then asked for your private key’s passphrase again).
  
If, like me, you take infosec purely has a hobby and have no real security concerns to speak of, I suggest taking the latter approach and setting **no password** on the subkey (or else you’ll have to enter it when logging into your SSH servers from time to time, and may even run into the aforementioned issues with pinentry). I believe it only poses a security risk in the event that someone gains access to your system and manages to copy the contents of your `~/.gnupg` directory, but [if someone knows something I don’t][infosec]{:target="_blank"}, I’m always happy to learn something new.

If, on the other hand, your situation demands actual security, you should _absolutely_ set a passphrase on the subkey, which will require the use of a pinentry utility. You’ll have to jump through some hoops to get it to work purely in the terminal (which I’ll cover in an upcoming post); in the meantime, be sure to install `pinentry-mac` instead.[^1]

### Basic configuration

1. Bookmark your remote server:

       # ~/.ssh/config
       Host          server_nickname
       HostName      server.domain.com
       User          username

2. Enable SSH support in gpg-agent:

       # ~/.gnupg/gpg-agent.conf
       enable-ssh-support

3. [Initialize `SSH_AUTH_SOCK` and launch gpg-agent on login][new]{:target="_blank"}:

       # ~/.bash_profile
       export SSH_AUTH_SOCK=$(gpgconf --list-dirs agent-ssh-socket)
       gpgconf --launch gpg-agent

4. Source your `.bash_profile`, (re)starting gpg-agent to load the new config:

       $ gpgconf --kill gpg-agent  # (just in case it’s already running)
       $ source ~/.bash_profile

### Adding keys

5. Tell gpg-agent which subkey to pass to ssh by adding its “keygrip” to `~/.gnupg/sshcontrol`:

       $ gpg -k --with-keygrip
       /Users/you/.gnupg/pubring.kbx
       ------------------------------
       pub   rsa2048/93BDD96B 2017-06-29 [SC]
             D03833D3D52F5FFCCC73452461671825E8DEC139
             Keygrip = 8A6CDC5FCE05A5B251BD8C397B269607534B4702
       uid         [ultimate] Big John <big.john@gmail.com>
       sub   rsa2048/0424163D 2017-06-29 [E]
             Keygrip = E110250E32B811D45879A66F487CE95BC1906D77
       sub   rsa2048/8F228EDB 2017-06-29 [A]
             Keygrip = 32BC5688805A640D495E8A7B41EC78F74E77E098
       $ echo 32BC5688805A640D495E8A7B41EC78F74E77E098 > ~/.gnupg/sshcontrol

6. Confirm key has been added:

       $ ssh-add -l
       2048 SHA256:zQ1wF6qOq8UNqcSRMYhDc+Cg3yM9lgp8dWvXwjnPcvU (none)
       (RSA)

7. Authorize key on remote server:[^2]

       $ brew install ssh-copy-id       # if you don’t already have it
       $ ssh-copy-id server_nickname

8. Log in!

       $ ssh server_nickname

Recap
-----

For SSH key authorization to work, the remote host must store a local copy of all authorized users’ public keys. That way, when a user’s ssh client presents their private key on login, the server can compare that against its collection of public keys to see if there’s a match.

`ssh-agent` manages SSH private keys and presents them to remote hosts for authentication. It comes with a couple helper utilities: `ssh-add` (which, when called with the `-l`/`-L` flags, lists the keys it knows about), and `ssh-copy-id` (which adds those public keys to a given remote host’s list of authorized users). 

`gpg-agent` manages GPG private keys and can be used as a drop-in replacement for `ssh-agent`. In order for this to work, a few things have to happen:

1. `ssh` must be directed to authorize via `gpg-agent` rather than `ssh-agent` (by setting `SSH_AUTH_SOCK`),
2. `gpg-agent` must be directed to receive authorization requests from `ssh` (either by having the `--enable-ssh-support` option included on the command line or having it set in the `gpg-agent.conf` file), and
3. the GPG keys you wish to use must be listed in the `sshcontrol` file.

The rest of the setup (namely, adding the public key to the remote host) is the same as it would be for ordinary SSH keys.

Caveat: GNOME/XFCE Users
------------------------

This guide is written for Mac users, but if you’re a GNOME user who found yourself looking for insight here, it appears that GNOME keyring runs its own, conflicting version of gpg-agent. This might be outdated information, since the last reference I saw to it was in 2012, but if you’re still having problems, [see here for more][gnome]{:target="_blank"}.

---

[^1]:
    To use `pinentry-mac`, first install it,

        $ brew install pinentry-mac

    then tell `gpg-agent` where to find it:

        $ which pinentry-mac
        /usr/local/bin/pinentry-mac

    ^

        # ~/.gnupg/gpg-agent.conf
        pinentry-program /usr/local/bin/pinentry-mac

[^2]:
    Big thanks to /u/ivanwick for [pointing this out][sci]{:target="_blank"} — `ssh-copy-id` makes the process of exporting public keys to an SSH server a breeze.

    But just in case you ever want to reverse the process (or manually manage authorization on a remote host), here’s a rundown of what’s going on under the hood:

    A server stores a list of authorized SSH public keys in each user account’s `~/.ssh/authorized_keys` file, where each line looks something like this:

        ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAedj8cCrSl8bR6/lmAABAQDuo9KF8ulWhbp0sUG6W1NQsQS/svxoY3WpXh8V0gCPc2D+QMj6jIhXS17PgU5dSwEUS07KB4s5Kbvc2SvLjNO9Uio6RK2M1wd0LdS678ZBiC/0LOqsdn6Et6PAte39K1vdC4YUVvVUXONB29aWRpFTfMZ3ZZvhi9L3+/GnwGgWHSpHH9btfstcHh0gPqNIubwtWpy1R27R/dAVElON95JTIk++c2rUoOEyq8CeOMhIEiVd2X1GrJ1Eutr8SkIBpilhLI5lLM85v/ruXDlJCpBzshFJLmdYHvsonfgHQ5ynxwLpMcEwLbqrBiRTGVgIgTS28TtX (none)

    This public key comes from the client (naturally); you can get a listing of public keys on your computer with the following (note the capital `-L`):
        
        $ ssh-add -L

    All `ssh-copy-id` does is take this information and append it to the remote host’s `~/.ssh/authorized_keys` file. On a system with many users, I prefer to do this manually, so that I can add a comment above each public key to remind me whom it belongs to. If you ever wish to deauthorize a key, simply delete the corresponding line from the file.

[interch]: https://superuser.com/questions/360507/are-gpg-and-ssh-keys-interchangable
[iu]: https://kb.iu.edu/d/aews
[rfc]: https://tools.ietf.org/html/rfc4880
[ssh-agent]: https://lists.gnupg.org/pipermail/gnupg-users/2012-July/045036.html
[pinentry]: https://superuser.com/a/1108407/444076
[incenp]: https://incenp.org/notes/2014/gnupg-for-ssh-authentication.html
[infosec]: https://security.stackexchange.com/questions/163026/does-it-defeat-the-purpose-to-create-a-gpg-authentication-subkey-that-is-not-pas
[new]: https://gnupg.org/faq/whats-new-in-2.1.html#autostart
[curses]: https://www.gnupg.org/documentation/manuals/gnupg/Common-Problems.html
[gnome]: https://budts.be/weblog/2012/08/ssh-authentication-with-your-pgp-key
[sci]: https://www.reddit.com/r/linux/comments/6k8nw3/killed_two_days_figuring_out_how_to_use_gpg_keys/djkgbtn/
