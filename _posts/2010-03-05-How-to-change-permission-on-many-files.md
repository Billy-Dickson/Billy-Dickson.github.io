---
title: Changing file and directory permissions
date: 2010-03-05
categories: [Homelab, Linux]
tags: [homelab, linux]     # TAG names should always be lowercase
image:
   path: ../assets/img/posts/headers/Tux_Linux.webp
---

I was trawling the web today and found this quick and painless bash script to change all the default folder permission back to default.

```bash
find . -type d -exec chmod 755 '{}' \;
```

You could of course adapt this script to change ownership of folders by changing the chmod command to chown, but I'll leave that one for you to work out.

Here's another to change file permissions back to there default.

```bash
find . -type f -exec chmod 644 '{}' \;
```

Happy scripting 😉
