---
title:  Comparing Directory Contents
date:   2026-09-08
categories: [Homelab, Linux]
tags: [homelab, linux]
draft: false
image:
    path: ../assets/img/posts/headers/Tux_Linux.webp
---



A good way to do this comparison is to use find with md5sum, then a diff, a big thank you to [askubuntu.com](https://askubuntu.com/) for the answer to this one. I've found it quite useful when I'm using [Windows Subsystem for Linux](https://learn.microsoft.com/en-us/windows/wsl/install) and comparing Windows NTFS directories.

## Example

Use find to list all the files in the directory then calculate the MD5 hash for each file and pipe it sorted by filename to a file.

```bash
find /dir1/ -type f -exec md5sum {} + | sort -k 2 > dir1.txt
```

Do the same procedure to the other directory

```bash
find /dir2/ -type f -exec md5sum {} + | sort -k 2 > dir2.txt
```

Then compare the resulting two files with diff

```bash
diff -u dir1.txt dir2.txt
```

Or as a single command using process substitution

```bash
diff <(find /dir1/ -type f -exec md5sum {} + | sort -k 2) <(find /dir2/ -type f -exec md5sum {} + | sort -k 2)
```

If you want to see only the changes

```bash
diff <(find /dir1/ -type f -exec md5sum {} + | sort -k 2 | cut -f1 -d" ") <(find /dir2/ -type f -exec md5sum {} + | sort -k 2 | cut -f1 -d" ")
```

The cut command prints only the hash (first field) to be compared by diff. Otherwise diff will print every line as the directory paths differ even when the hash is the same.

But you won't know which file changed...

For that, you can try something like

```bash
diff <(find /dir1/ -type f -exec md5sum {} + | sort -k 2 | sed 's/ .*\// /') <(find /dir2/ -type f -exec md5sum {} + | sort -k 2 | sed 's/ .*\// /')
```

This strategy is very useful when the two directories to be compared are not on the same machine and you need to make sure that the files are equal in both directories.

Another good way to do the job is using Git’s diff command (may cause problems when files have different permissions -> every file is listed in the output then)

```bash
git diff --no-index dir1/ dir2/
```
