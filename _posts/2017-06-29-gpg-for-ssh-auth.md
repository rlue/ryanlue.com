---
title: Using a GPG key for SSH Authentication
teaser: One less private key to keep track of.
category: guides
tags: [ssh, pgp, cli, mac]
reddit_post: 'https://www.reddit.com/r/linux/comments/6k8nw3/killed_two_days_figuring_out_how_to_use_gpg_keys/'
featured_comments:
  - url: 'https://www.reddit.com/r/linux/comments/6k8nw3/killed_two_days_figuring_out_how_to_use_gpg_keys/djk8fbu/'
    context: true
  - url: 'https://www.reddit.com/r/linux/comments/6k8nw3/killed_two_days_figuring_out_how_to_use_gpg_keys/djk73ro/'
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
> made it), or decrypt messages intended only for you (among other things).
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

  This is a lightweight program used to accept password input so that GnuPG doesn’t have to (for more on the security considerations behind this design, see [here][pinentry]{:target="_blank"}). This is installed as a dependency of `gpg`, but for some reason, fails to be invoked on SSH logins. If you want to put a password on your SSH authorization key, be sure to `brew install pinentry-mac`.

The Procedure
-------------

### Generating an authentication-only subkey

Keys are used for three purposes (signing, encrypting, and authenticating), and it’s a best practice to generate and use separate **subkeys** exclusively for each separate purpose.

If you’re reading this guide, you probably already have a PGP key; we just need to make sure you have an **authentication-only subkey**. If you do, you should see output for the following command:

    $ gpg -k | grep "\[A\]"

and may skip down to Step 1 below.

If not, follow the directions under “Create an authentication subkey” [here][incenp]{:target="_blank"}. During the process, you’ll be prompted for a passphrase **twice**:

  1. first, to set a passphrase for the new subkey,
  2. and then to provide the passphrase belonging to its parent key.
  
If your security needs are not particularly sensitive, I suggest leaving the first one blank (or else you’ll have to enter it when logging into your SSH servers from time to time, and may even run into the aforementioned issues with pinentry). I believe it only poses a security risk in the event that someone gains access to your system and manages to copy the contents of your `~/.gnupg` directory, but [if someone knows something I don’t][infosec]{:target="_blank"}, I’m always happy to learn something new.

### Basic configuration

1. Bookmark your remote server:

       # ~/.ssh/config
       Host          server_nickname
       HostName      server.domain.com
       User          username

2. Always display key IDs in gpg:

       # ~/.gnupg/gpg.conf
       keyid-format short

3. Enable SSH support in gpg-agent:

       # ~/.gnupg/gpg-agent.conf
       enable-ssh-support

4. Initialize `SSH_AUTH_SOCK` and [launch gpg-agent][new]{:target="_blank"} on login:

       # ~/.bash_profile
       export SSH_AUTH_SOCK=$(gpgconf --list-dirs agent-ssh-socket)
       gpgconf --launch gpg-agent

5. Source your `.bash_profile`, (re)starting gpg-agent to load the new config:

       $ gpgconf --kill gpg-agent  # (just in case it’s already running)
       $ source ~/.bash_profile

### Adding keys

6. Tell gpg-agent which subkey to pass to ssh by adding its “keygrip” to `~/.gnupg/sshcontrol`:

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

7. Confirm key has been added:

       $ ssh-add -l
       2048 SHA256:zQ1wF6qOq8UNqcSRMYhDc+Cg3yM9lgp8dWvXwjnPcvU (none)
       (RSA)

8. Authorize key on remote server (note the capital `-L`, which returns the public key rather than the fingerprint):

       $ ssh-add -L > authorized_keys
       $ scp authorized_keys server_nickname:~/.ssh/authorized_keys
       Host key fingerprint is SHA256:MFojxaB+ZEppy+wCK7loru5d1dADh02feKSkeZdrxPA

       username@server.domain.com's password:
       authorized_keys                               100%  388     0.4KB/s   00:00
       $ rm authorized_keys

   (Be careful with the `scp` command above: it assumes you already have a `~/.ssh` directory on your SSH server, and that it doesn’t already contain anyone else’s public keys. If it does, you’ll have to go in and add your key manually.)

9. Log in!

       $ ssh server_nickname

Caveats
-------

### `pinentry-mac`

If you have a password on your authorization-only subkey, be sure to have `pinentry-mac` installed and to add the appropriate setting to your `gpg-agent.conf` file:

    $ echo "pinentry-program $(which pinentry-mac)" >> ~/.gnupg/gpg-agent.conf

Otherwise, PGP-key-authenticated SSH login may fail silently altogether (with a fallback to password authentication, if you have that enabled).

### GNOME/XFCE Users

This guide is written for Mac users, but if you’re a GNOME user who found yourself looking for insight here, it appears that GNOME keyring runs its own, conflicting version of gpg-agent. This might be outdated information, since the last reference I saw to it was in 2012, but if you’re still having problems, [see here for more][gnome]{:target="_blank"}.

[interch]: https://superuser.com/questions/360507/are-gpg-and-ssh-keys-interchangable
[iu]: https://kb.iu.edu/d/aews
[rfc]: https://tools.ietf.org/html/rfc4880
[ssh-agent]: https://lists.gnupg.org/pipermail/gnupg-users/2012-July/045036.html
[pinentry]: https://superuser.com/a/1108407/444076
[incenp]: https://incenp.org/notes/2014/gnupg-for-ssh-authentication.html
[infosec]: https://security.stackexchange.com/questions/163026/does-it-defeat-the-purpose-to-create-a-gpg-authentication-subkey-that-is-not-pas
[new]: https://gnupg.org/faq/whats-new-in-2.1.html#autostart
[gnome]: https://budts.be/weblog/2012/08/ssh-authentication-with-your-pgp-key
