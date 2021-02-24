---
title: "Change your file permissions, baby!"
date: 2021-02-24T08:28:54-08:00
tags: ['bash', 'command-line']
draft: False
---

Knowing how to set file permissions is very useful (for a much more thorough primer than I provied here, see [this article](https://docs.nersc.gov/filesystems/unix-file-permissions/)). Unfortunately, while this is very easy to do, it is even easier to forget the codes used to do so. So here's how you do it! 

The easiest (shortest) way to change permissions is using [octal](https://en.wikipedia.org/wiki/Octal) syntax. Octal syntax goes like this:

```
chmod <owner><group><others> filename
```

Where `<owner>`, `<group>` and `<others>` stand for the octal numeral pertaining to that user or group. Conveniently, there are eight values these codes can take. Coincidence? I think not!

| Code | Permissions granted                         |
| :--: | ------------------------------------------- |
| `0`  | No permissions                              |
| `1`  | Execute only                                |
| `2`  | Write only                                  |
| `3`  | Write and execute                           |
| `4`  | Read only                                   |
| `5`  | Read and execute                            |
| `6`  | Read and write                              |
| `7`  | Read, write, and execute (full permissions) |

For example, this lets anyone do anything they want to the file,

```
chmod 777 file.txt
```

this only lets the owner read and write to the file,

```
chmod 700 file.txt
```

And this gives the owner full permissions, other users in the same group as the owner read and execute permissions and everyone else only execute permissions:

```
chmod 751 file.txt
```

Now go ye forth and modify permissions.