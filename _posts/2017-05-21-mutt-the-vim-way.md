---
title: Mutt, the Vim Way
teaser: Mutt has a learning curve. Here are some settings to help vim users feel right at home.
category: tips
tags: [mutt, vim, bashrc]
---

I’ve recently made the switch to managing my email from the terminal, and settled on mutt as my client of choice. Luckily, it’s well-documented, but like any fully-featured terminal utility, it still takes some time to find your way around. Below are the customizations I’ve made to my `muttrc` to make things as ‘vimmy’ as possible.

But First: The Unfixable
------------------------

In vim, you can usually rely on `<Esc>` / `<C-c>` / `<C-[>` to get back to home base if you ever unwittingly back yourself into a corner.

In mutt, the corresponding command is `<C-g>`. There is no way to remap other keys to this function, so it’s just one little nugget of emacs influence you’re going to have to get used to.

My `muttrc`
-----------

Or at least, the part that makes it vimmy:

```
# Navigation
# ----------------------------------------------------

bind generic,index,pager \Cf       next-page
bind generic,index,pager \Cb       previous-page
bind generic             gg        first-entry
bind generic,index       G         last-entry
bind pager               gg        top
bind pager               G         bottom
bind generic,pager       \Cy       previous-line
bind generic,index,pager \Ce       next-line
bind generic,index,pager \Cd       half-down
bind generic,index,pager \Cu       half-up
bind generic             zt        current-top
bind generic             zz        current-middle
bind generic             zb        current-bottom
bind index               za        collapse-thread
bind index               zA        collapse-all
bind index,pager         N         search-opposite
bind index               <Backtab> previous-new-then-unread

# Go to folder...
macro index,pager gi "<change-folder>=INBOX<enter>"       "open inbox"
macro index,pager gd "<change-folder>=Drafts<enter>"      "open drafts"
macro index,pager gs "<change-folder>=Sent<enter>"        "open sent"
macro index,pager gt "<change-folder>$trash<enter>"       "open trash"
macro index,pager gf "<change-folder>?"                   "open mailbox..."

# Actions
# ----------------------------------------------------

bind  index,pager    a   group-reply
macro index,pager    dd  "<delete-message><sync-mailbox>"                                 "move message to trash"
macro index,pager    dat "<delete-thread><sync-mailbox>"                                  "move thread to trash"
macro index          ss  ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<save-message>?"                                                                            "save message to a mailbox"
macro index          sat ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<untag-pattern>.<enter><tag-thread><tag-prefix-cond><save-message>?"                        "save thread to a mailbox"
macro index          \;s ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<tag-prefix-cond><save-message>?"                                                           "save tagged messages to a mailbox"
macro pager          ss  ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015<save-message>?"                                                           "save message to a mailbox"
macro pager          sat ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015<exit><untag-pattern>.<enter><tag-thread><tag-prefix-cond><save-message>?" "save thread to a mailbox"
macro index          cc  ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<copy-message>?"                                                                            "copy message to a mailbox"
macro index          cat ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<untag-pattern>.<enter><tag-thread><tag-prefix-cond><copy-message>?"                        "copy thread to a mailbox"
macro index          \;c ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015\"\015<tag-prefix-cond><copy-message>?"                                                           "copy tagged messages to a mailbox"
macro pager          cc  ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015<copy-message>?"                                                           "copy message to a mailbox"
macro pager          cat ":macro browser \\015 \"\<select-entry\>\<sync-mailbox\>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015:macro browser q \"<exit>:bind browser \\\\015 select-entry\\015:bind browser q exit\\015<display-message>\"\015<exit><untag-pattern>.<enter><tag-thread><tag-prefix-cond><copy-message>?" "copy thread to a mailbox"
bind  generic        tt  tag-entry
bind  index          tat tag-thread
bind  pager          tt  tag-message
macro pager          tat "<exit><mark-message>q<enter><tag-thread>'q<display-message>"    "tag-thread"
macro index,pager    gx  "<pipe-message>urlview<Enter>"                                   "call urlview to extract URLs out of a message"
macro attach,compose gx  "<pipe-entry>urlview<Enter>"                                     "call urlview to extract URLs out of a message"

# Command Line
# ----------------------------------------------------

bind editor \Cp history-up
bind editor \Cn history-down
```

Conflicts with Default Bindings
-------------------------------

This configuration overrides some default bindings, and it may behoove you to know what you’re missing. Here’s a line-by-line breakdown:

### Navigation

    bind generic,index,pager \Cf       next-page
    bind generic,index,pager \Cb       previous-page

* `^F` » `forget-passphrase`

  **Unlikely to be used** (only needed when accessing a remote POP/IMAP server directly; it’s advisable to keep a local mirror of your mailstore with a tool like mbsync or OfflineIMAP instead).

* `^B` » `call urlview to extract URLs out of a message`

  **Remapped** to `gx` (this is how you visit links in mutt).

^

    bind generic             gg        first-entry
    bind generic,index       G         last-entry
    bind pager               gg        top
    bind pager               G         bottom

* `g` » `group-reply`

  **Remapped** to `a` (à la Gmail).

* `G` » `fetch-mail` (index) / unmapped (pager)

  **Unlikely to be used** (retrieves mail from a POP server; see above).

^

    bind generic,pager       \Cy       previous-line
    bind generic,index,pager \Ce       next-line

* `^Y` » unmapped

  N.b., `^Y` is normally [intercepted by the terminal as a DSUSP signal][ctrly]. In order to enable this keybinding in mutt, DSUSP must be disabled in the terminal. To do this, add the function definition below to your `.bashrc`:

      mutt()
      {
        old=$(stty -g)                               # Capture old termio params
        stty dsusp undef                             # Disable DSUSP
        trap "rc=$?; stty $old; exit $rc" 0 1 2 3 15 # Restore termios on interrupt
        /usr/local/bin/mutt "$@"                     # Run mutt
        stty $old                                    # Restore termios on exit
      }

* `^E` » `edit-type`

  **Unlikely to be used** (for manually editing the MIME type of an email attachment).

^

    bind generic,index,pager \Cd       half-down
    bind generic,index,pager \Cu       half-up

* `^D` » `delete-thread`

  **Remapped** to `dat` (for “delete a thread”).

* `^U` » `undelete-thread`

  **Rendered superfluous** by this configuration. (By default, “deleting” a message in mutt only _flags it for deletion;_ until changes are registered with `$`/`sync-mailbox`, the actual mailstore remains unaffected, and messages may still be “undeleted”. Once changes are registered, flagged messages are moved to a trash folder if the [`trash` configuration variable][trash] is set, or irrecoverably purged if not. This configuration alters the default behavior to register changes automatically, and relies on the user to set the `trash` variable if she wishes to retain the option to recover deleted messages.)

^

    bind generic             zt        current-top
    bind generic             zz        current-middle
    bind generic             zb        current-bottom
    bind index               za        collapse-thread
    bind index               zA        collapse-all

* `z` » unmapped

^

    bind index,pager         N         search-opposite

* `N` » `toggle-new`

  **Redundant** with `wn`
    
^

    bind index               <Backtab> previous-new-then-unread
    
* `<Backtab>` » unmapped

### Actions / Command Line

The remaining sections have no conflicts with default key bindings, though the very long macros deserve some explanation, which will be covered in a future post.
    
[func]: http://www.mutt.org/doc/manual/#functions
[ctrly]: https://www.mail-archive.com/mutt-dev@mutt.org/msg10141.html
[trash]: http://www.mutt.org/doc/manual/#trash
