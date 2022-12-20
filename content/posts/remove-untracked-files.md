+++
title = "Git: Removing Untracked Files"
date = "2022-03-21T10:23:29-07:00"
authorTwitter = "rxkironto" #do not include @
cover = ""
tags = ["tips", "git"]
keywords = ["git", "untracked files"]
description = "How do I delete all untracked files in git?"
showFullContent = false
hideComments = false
author = "Amy Dang"
+++

```
git clean -fdx
```

-f - force

-d - include directories

-x - include ignored files

---


**!Use with Caution!**

This command will delete files that you haven't thought of. Be careful whenever
running a command with `-f`
