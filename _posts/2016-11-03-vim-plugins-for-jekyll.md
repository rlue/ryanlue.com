---
title: 2 Vim Plugins for a Better Jekyll Experience
teaser: Set up a live preview of your Jekyll site from Vim, and get multi-language syntax highlighting working right.
category: tips
tags: [vim, jekyll, workflow]
---

What is Jekyll?
---------------

Jekyll is a command-line tool for building websites (and especially blogs) from text files. In the words of creator Tom Preston-Werner, Jekyll is for “[blogging like a hacker][tpw].”

More precisely, Jekyll takes a set of plain-text templates, content, and other files in a format that _it_ understands, and assembles them into another set of plain-text files in a format that _a web browser_ understands – the benefit being that the former is easier to work with (manage, maintain, extend, reuse) than the latter.

The resulting files can then be uploaded to a simple webserver and made available to your adoring public without all the overhead and security issues that typically come with a CMS like WordPress (namely, a database and server-side application logic). Many programmers opt to [host their Jekyll sites on GitHub Pages][ghp]. In fact, you could even host a Jekyll site from the `Public` directory of your DropBox, if you wanted. 

Many programmers tend to favor Jekyll for the same reason that, say, hippies like to grow their own food: 

1. they want complete ownership of production and storage, which
2. cultivates a more intimate understanding of what’s going on under the hood, and
3. tends to enforce a leaner system than mass-produced solutions can offer.

Jekyll + Vim
------------

In the spirit of blogging like a hacker, this site is built using Jekyll and Vim. There are some excellent plugins out there to extend Vim’s functionality and make it a bit more IDE-like, but sometimes little hiccups arise that are small enough or not relevant enough to other people to warrant a fork, patch, and pull request.

### Live refresh

[Browserlink][blk] is an indispensable Vim plugin for basic web development. It hooks into your browser and forces a refresh every time you save a file, so you can have your website open in Chrome and your `index.html` file open in Vim, and when you `:write` changes to the latter, they just show up in the former without any manual intervention. 

It’s a huge time-saver and fits well with Jekyll’s built-in development server. Ordinarily, to preview your site as you work on it, you would invoke

~~~ bash
$ jekyll serve
~~~

in a terminal, then visit http://localhost:4000/ in your browser. Jekyll watches for changes within the project folder, and rebuilds the site from scratch with each one. The only problem is that Browserlink is a bit too fast for Jekyll, and will force a refresh before the new changes are ready.

Follow the installation and configuration instructions for [Browserlink][blk], then add this to your `.vimrc`:

~~~ viml
" Disable built-in event handling...
let g:bl_no_autoupdate = 1

" ...in lieu of custom event handler to force delay inside Jekyll directories.
let s:delay_interval = '1000m'
let s:bl_pagefileexts  = 
      \ [ 'html' , 'js'     , 'php'  ,
      \   'css'  , 'scss'   , 'sass' ,
      \   'slim' , 'liquid' , 'md'     ]

function! s:setupHandlers()
  let s:path_flag = '%:p:h' | let s:this_path = expand(s:path_flag)
  while s:this_path != $HOME 
    if !empty(globpath(s:this_path,'_config.yml')) 
      exec 'sleep ' . s:delay_interval | break 
    endif 
    let s:path_flag .= ':h' | let s:this_path = expand(s:path_flag) 
  endwhile 
  if expand('%:e:e') =~ 'css' 
    :BLReloadCSS 
  else
    :BLReloadPage 
  endif
endfunction

augroup browserlink
  autocmd!
  exec 'autocmd BufWritePost *.' . join(s:bl_pagefileexts, ',*.') . ' call s:setupHandlers()'
augroup END
~~~

Now, when you save changes within a Jekyll project folder, the browser should reload after a 1-second delay, which should be just enough for the new changes to spawn first. (If you have a large site that requires more time to build, [try `$ jekyll serve --incremental`][inc].) 

### Syntax highlighting & comment wrapping

Jekyll mixes multiple languages into each file. Your `index.html` template file will contain **HTML** (naturally) alongside **Liquid** (for templating) and **YAML** (for storing metadata).

Composite filetypes are ordinarily no problem for vim, which offers multi-syntax highlighting out-of-the-box – _but only when Vim knows it’s displaying a Liquid file._ (That is to say, multi-syntax highlighting is disabled if `filetype` is set to anything other than `liquid`.) Vim usually guesses the filetype based on the file extension, but Jekyll throws a wrench in the works by omitting the `.liquid` extension altogether in project files, meaning that a Liquid XML file ends in `.xml` and _not_ `.xml.liquid`, and Vim will open it as such.

Tim Pope’s [vim-liquid][vlq] plugin solves this problem by automatically setting the Liquid filetype on any file that includes YAML frontmatter (as most Jekyll project files do), but this fix introduces a new wrinkle: when you `:set filetype=liquid`, Vim defaults to using Liquid _comment markers_, too. And since I use [vim-commentary][vcm] to wrap text in comment markers, I wind up with  `{% raw %}{% comment %} Liquid comments {% endcomment %}{% endraw %}` when I’m really trying to set `<!-- HTML/XML comments -->`. Fortunately, it’s an easy fix.

Create a `.vim/after/ftplugin/liquid.vim` file with the following code:

~~~ viml
" Set native comment markers in Liquid files
let liquid_ext = expand('%:e:e')
if liquid_ext =~ '\(ht\|x\)ml'
  set commentstring=<!--%s-->
elseif liquid_ext =~ 'css'
  set commentstring=/*%s*/
endif
~~~

This snippet will run whenever Vim opens a Liquid file, re-setting the comment string based on what it sees in the file extension.

As it turns out, vim-liquid only recognizes HTML, XML, Markdown, and Textile files, so if you ever find yourself calling Liquid variables inside a CSS or Javascript file, go ahead and add the following to `.vim/ftdetect/liquid.vim`:

~~~ viml
" Extend vim-liquid filetype setting for CSS and Javascript files
au BufNewFile,BufRead *.css,*.scss,*.js
  \ if getline(1) == '---' | set ft=liquid | endif
~~~

and be sure to add a YAML front-matter declaration to the top of your that .css/.js file, which is how Jekyll recognizes Liquid files (an empty one will do).

[tpw]: http://tom.preston-werner.com/2008/11/17/blogging-like-a-hacker.html
[ghp]: https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/
[blk]: https://github.com/jaxbot/browserlink.vim
[vlq]: https://github.com/tpope/vim-liquid
[vcm]: https://github.com/tpope/vim-commentary
[inc]: http://idratherbewriting.com/2015/11/04/jekyll-30-released-incremental-regeneration-rocks/
