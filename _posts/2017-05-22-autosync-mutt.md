---
title: save-message → sync-mailbox Automatically in Mutt
teaser: Making mutt sync the mailbox on its own is messier than it should have to be — but it can be done.
category: tips
tags: [mutt]
---

This question was first raised [on the mutt-users@mutt.org mailing list back in 2008][op]:

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
macro index,pager    d   "<delete-message><sync-mailbox>"                                 "move message to trash"
```

but moving messages and saving copies to different folders is tricky: you want to press the keybinding, then manually select a folder, then have it sync. But as described by OP above, there’s no facility in mutt keybindings that says “wait until the user is done selecting a folder.”

### Getting clever

So instead, what you have to do is this:

```
macro index s ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015\"\015<save-message>?" "save message to a mailbox"
```

This macro does the following:

1. Create a new macro (in preparation for the folder selection menu) to make `<Return>` (`\015`) select a folder and then sync the mailbox.
2. But also, after selecting a folder and syncing the mailbox, make sure the macro “cleans up” after itself, resetting `<Return>` to its original keybinding.
3. Then, enter the `save-message` folder selection menu.

This solves our problem, but creates a new one: if you go to save a message and then change your mind, you can exit the folder selection menu by pressing `q`. Only now, the macro hasn’t had a chance to clean up after itself (as in Step 2 above); `<Return>` is still mapped to a macro that will `select-entry`, `sync-mailbox`, and then restore `<Return>` to its original binding.

### We need more macros

To fix this, we have to incorporate yet another macro definition into our already very complicated meta-macro. We have to map both `q` and `<Return>` to their own separate “clean-up” macros:

```
macro index s ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<save-message>?" "save message to a mailbox"
```

Now, when you hit `s`, you will enter the `save-mailbox` folder selection menu. `<Return>` will be bound to a macro that chooses the folder, syncs the mailbox, and restores both `<Return>` and `q` to their original bindings. At the same time, `q` will be bound to a macro that exits before cleaning up similarly.

The Final Configuration
-----------------------

I have my own `muttrc` [set up to be as ‘vimmy’ as possible][vimmy], so the macros described above are actually bound to `dd` and `ss`, with `dat` and `sat` set up to “delete a thread” and “save a thread,” respectively.

```
macro index,pager    dd  "<delete-message><sync-mailbox>"                                 "move message to trash"
macro index,pager    dat "<delete-thread><sync-mailbox>"                                  "move thread to trash"
macro index          ss  ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<save-message>?"                                                                                             "save message to a mailbox"
macro index          sat ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<untag-pattern>.<enter><tag-thread><tag-prefix-cond><save-message>?"                                         "save thread to a mailbox"
macro index          \;s ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<tag-prefix-cond><save-message>?"                                                                            "save tagged messages to a mailbox"
macro pager          ss  ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015<save-message>?"                                                           "save message to a mailbox"
macro pager          sat ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015<exit><untag-pattern>.<enter><tag-thread><tag-prefix-cond><save-message>?" "save thread to a mailbox"
macro index          cc  ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<copy-message>?"                                                                                             "copy message to a mailbox"
macro index          cat ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<untag-pattern>.<enter><tag-thread><tag-prefix-cond><copy-message>?"                                         "copy thread to a mailbox"
macro index          \;c ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<tag-prefix-cond><copy-message>?"                                                                            "copy tagged messages to a mailbox"
macro pager          cc  ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015<copy-message>?"                                                           "copy message to a mailbox"
macro pager          cat ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015<exit><untag-pattern>.<enter><tag-thread><tag-prefix-cond><copy-message>?" "copy thread to a mailbox"
```

And this is why sometimes I wish I had a different hobby.

[op]: http://mutt-users.mutt.narkive.com/IQMJfPuh/save-message-to-and-then-sync-the-folder
[vimmy]: {% post_url 2017-05-21-mutt-the-vim-way %}
