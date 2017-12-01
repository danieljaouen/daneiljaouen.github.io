---
layout: post
title:  "Managing Dotfiles With Rake"
date:   2017-12-01 10:57:24 -0400
categories: programming
---
Today I'm going to be talking about how I manage my dotfiles with Rake.  Feel
free to follow along by browsing to my dotfiles's directory's
[Rakefile](https://github.com/danieljaouen/dotfiles/blob/master/Rakefile).
First,  let's talk about the structure of my dotfiles repository.

I have two directories in the top level of the
[repo](https://github.com/danieljaouen/dotfiles), `locations` and `topics`. I do
this to to make it easier to browse the repo (feel free to steal anything you
find useful!). The "topics" directory contains several directories based on
"topics" and the "locations" directory contains the locations of my dotfiles
(relative to `~`). We see that the "locations" directory contains symlinks to
files located under the "topics" directory. The Rakefile uses these symlinks to
discover the "old_path" (as in, `ln -s old_path new_path`).

The Rakefile contains a function `file_listing` which takes an `end_pattern` in
as an argument and returns a dictionary of a file's `old_path` and `new_path`.
The Rakefile also has a function called `current_path` which takes a `link`
dictioary returned by `file_listing` and returns a dictionary containing the
`type`, `path`, `old_path`, and `new_path`. This function returns values
depending on whether or not `f[:new_path]` already exists on disk. We also have
functions called `directory_listing`, `locals_listing`, and `dotfile_listing`
which all return lists containing `file_listing` dictionaries based on whether
or not `f[:new_path]` matches certain patterns.

We then have functions `maybe_overwrite_symlink!` and `maybe_overwrite_file!`,
which overwrite files if `noinput` is set to true and prompts for a response if
`noinput` is set to false. We then have the function `symlink_file!` which calls
out to `maybe_overwrite_symlink!` and `maybe_overwrite_file!`. We then define
the tasks `ensure_directories_exist` (which calls out to `FileUtils.mkdir_p`),
`ensure_locals_exist`, and `ensure_links_exist`. We also have an `uninstall`
task (which unlinks all the dotfiles). We also see that the `default` task is
set to `ensure_links_exist`.

And that's it!  Now just run "rake" in the dotfiles repo and the dotfiles will
symlink into the appropriate places.
