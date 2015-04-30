---
layout: post
title: 'The Mother of All Aliases'
date: '2015-04-30'
categories: bash
---

Sometimes I feel old.  Like too old to use zsh or fish instead of bash.  <del>So, I stick to bash (well, sometimes).</del>  Anyways, following instructions <del>probably</del> work for zsh (oh-my-zsh) as well.

I do not use aliases extensively.  But when I do, I want them to be 'just there'.  I do not remember where I got the following trick from, but its one of the adjustments to new systems I find myself to do rather sooner than later.

##Create an alias with a quick command
My aliases go to their own file, `~/.bash_aliases` .

    # ~/.bash_aliases
    # The Mother of Alias
    alias nalias='echo "" >> ~/.bash_aliases;\
      vim -c "startinsert" + ~/.bash_aliases;\
      source ~/.bash_aliases'

After linking this up, e.g. with a
`source ~/.bash_aliases`
in your `~/.bashrc`, you can now type
`nalias` ("new alias") and will be put in vim in your `~/.bash_aliases` ready to enter a new alias.  Once done and `:x`ed, your new alias is ready to be used.

If you happen to work in multiple terminals, add a
`alias ralias='source ~/.bash_aliases'` (reload alias)
to allow you to quickly resource the `~/.bash_aliases`.

