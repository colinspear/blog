---
title: "Move and rename all files in a folder"
date: 2021-02-17T12:49:31-08:00
draft: false
tags: ['bash', 'command-line']
disqus: false
---

I wanted to move and rename a bunch of excel files in a folder. This is one way to do it from the command line, copying into a directory in the parent folder. I did it this way to be able to `rm -r old_folder` after copying my files to the new folder. You could modify it to rename them in place, but I've accidentally `rm -rf`ed enough things to get nervous doing it that way. 

```
for f in *; do mv "$f" "../new_folder/old_$f; done"
```

And just for good measure (and to include a note about string substitution), if all of you files were of the form `file.suffix` and you wanted the `old` at the end of the new files it would go like this:

```
for f in *; do mv "$f" "../new_folder/${f/.suffix/_old.suffix}"; done"
```
 
 This will do the same as the first example, replacing `.suffix` with `_old.suffix`. So `file.suffix` becomes `file_old.suffix`. In general, the format is:

 `${parameter/pattern/string}`

 Where `parameter` is the thing you want to change, `pattern` is a string or regular expression and `string` is what you want to replace it with.