---
title: save-message → sync-mailbox Automatically in Mutt
teaser: Making mutt sync the mailbox on its own is messier than it should have to be — but it can be done.
category: tips
tags: [mutt]
reddit_post: 'https://www.reddit.com/r/linux/comments/6cl0pv/how_to_make_mutt_syncmailbox_automatically_after/'
---

This question was first raised [on the mutt-users@mutt.org mailing list back in 2008][op]{:target="_blank"}:

> Hello
> 
> how can I sync the mailbox after save a message to any folder like this:
> 
> `macro s "<save-message>?"`
> 
> the above works great, but after this save mutt should sync
> automatically. Like this:
> 
> `macro s "<save-message>?<sync-mailbox>"`
> 
> but, this doesn't work, because mutt wait for any data from user and
> take then ync-mailbox> [sic] as mailbox-destination, which is wrong.
> 
> any tips?
> 
> Thank you.  
> Raphael

The Problem
-----------

But first, some context: Certain aspects of mutt’s design betray its 90s-era provenance. A particular thorn in my side has been the way it handles making changes to your mailstore: Anytime you delete, move, or duplicate a message, it doesn’t actually register that change until you `sync-mailbox` (the `$` key, by default).

This behavior can be useful if bandwidth is scarce and you’re accessing your email remotely — back in ’95, on a 28.8k modem, you might have wanted to be economical with the number of POP/IMAP requests you initiated. (Disclaimer: I was 9 years old in 1995, so I’m not actually speaking from firsthand experience. But one can speculate.)

In 2017, collecting CRUD operations and limiting syncs to conserve bandwidth is beyond miserly, to say nothing of the fact that many users (likely a majority) aren’t even accessing remote mailstores in the first place, but rather using mutt to manage a local mirror of their remote mailboxes.

The Fix
-------

### Looks easy enough

To automate the `sync-mailbox` function, you have to manually remap each CRUD operation as a macro, adding a call to `sync-mailbox` to the end. It’s hacky, but it’s all that can be done with the tools mutt gives you.

This is simple when you just want to `delete-message`,

```
macro index,pager d "<delete-message><sync-mailbox>" "move message to trash"
```

but moving messages and saving copies to different folders is tricky: you want to press the keybinding, then manually select a folder, then have it sync. But as described by OP above, there’s no facility in mutt keybindings that says “wait until the user is done selecting a folder.”

### Getting clever

So instead, what you have to do is this:

```
macro index s ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015\"\015<save-message>?" "save message to a mailbox"
```

This is starting to get messy, so let’s break it down and see what’s really happening. In the lines below, all backslash-escaping has been removed, and each nested macro has been extracted into the subsequent line.

1. Bind `s` to a macro that…

       macro index s "…" "save message to a mailbox"

2. …brings up the `save-message` folder selection menu, just after binding `<Return>` to a macro that…

       :macro browser <Return> "…"<Return><save-message>?

3. …selects a folder to save, syncs the mailbox, and finally restores `<Return>` to its original binding.

       <select-entry><sync-mailbox>:bind browser <Return> select-entry<Return>

^

This solves our problem, but creates a new one: if you go to save a message and then change your mind, you can exit the folder selection menu by pressing `q`. Only now, the macro hasn’t had a chance to clean up after itself (as in Step 2 above); `<Return>` is still mapped to a macro that will `select-entry`, `sync-mailbox`, and then restore `<Return>` to its original binding.

### We need more macros

To fix this, we have to incorporate yet another macro definition into our already very complicated meta-macro. We have to map both `q` and `<Return>` to their own separate “clean-up” macros:

```
macro index s ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<save-message>?" "save message to a mailbox"
```

Let’s break this down again.

1. Bind `s` to a macro that…

       macro index s "…" "save message to a mailbox"

2. …brings up the `save-message` folder selection menu, just after binding `<Return>` and `q` to two separate macros…

       :macro browser <Return> "…"<Return>:macro browser q "…"<Return><save-message>?

3. …the first of which selects a folder to save, syncs the mailbox, and finally restores `<Return>` and `q` to their original bindings…

       <select-entry><sync-mailbox>:bind browser <Return> select-entry<Return>:bind browser q exit<Return>

4. …while the other exits and cleans up likewise.

       <exit>:bind browser <Return> select-entry<Return>:bind browser q exit<Return>

^

The Final Configuration
-----------------------

I have my own `muttrc` [set up to be as ‘vimmy’ as possible][vimmy], so the macros described above are actually bound to `dd` and `ss`, with `dat` and `sat` set up to “delete a thread” and “save a thread,” respectively.

Also worth noting is the inclusion of threadwise saving and copying. No such function is built into mutt, so these mappings automate `tag-thread` + `save-message` (even in pager view, where `tag-thread` is not available).

```
macro index,pager    dd  "<delete-message><sync-mailbox>"                                 "move message to trash"
macro index,pager    dat "<delete-thread><sync-mailbox>"                                  "move thread to trash"
macro index,pager    ss  ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<save-message>?"                                                                                                                                     "save message to a mailbox"
macro index          sat ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015'q<untag-pattern>.\\015\"\015<mark-message>q<enter><untag-pattern>.<enter><tag-thread><tag-prefix-cond><save-message>?"                                    "save thread to a mailbox"
macro index          \;s ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<tag-prefix-cond><save-message>?"                                                                                                                    "save tagged messages to a mailbox"
macro pager          sat ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015'q<untag-pattern>.\\015<display-message>\"\015<exit><mark-message>q<enter><untag-pattern>.<enter><tag-thread><tag-prefix><save-message>?" "save thread to a mailbox"
macro index,pager    cc  ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<copy-message>?"                                                                                                                                     "copy message to a mailbox"
macro index          cat ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015'q<untag-pattern>.\\015\"\015<mark-message>q<enter><untag-pattern>.<enter><tag-thread><tag-prefix-cond><copy-message>?"                                    "copy thread to a mailbox"
macro index          \;c ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<tag-prefix-cond><copy-message>?"                                                                                                                    "copy tagged messages to a mailbox"
macro pager          cat ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015'q<untag-pattern>.\\015<display-message>\"\015<exit><mark-message>q<enter><untag-pattern>.<enter><tag-thread><tag-prefix><copy-message>?" "copy thread to a mailbox"
```

And this is why sometimes I wish I had a different hobby.

[op]: http://mutt-users.mutt.narkive.com/IQMJfPuh/save-message-to-and-then-sync-the-folder
[vimmy]: {% post_url 2017-05-21-mutt-the-vim-way %}
