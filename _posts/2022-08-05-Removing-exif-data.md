---
title:  "Removing exif data using Linux exiftool"
date:   2022-08-10
categories: [Homelab, Linux, Web]
tags: [homelab, linux, web]
image:
    path: ../assets/img/posts/2022/2022-08-10-Removing-exif-data/Install_exiftool.png
---

## How to remove exif data from photos

Here's a handy linux command line tool that can remove all exif data from your photographs. This one I use to remove the exif data from my photographs before I publish them to my website.

These instructions are aimed at people who use linux as their daily driver (like myself), if you use a debian based distribution, then all the instructions below will apply.

If you use a distribution not based on debian, then the install syntax will vary accordingly.

Removing EXIF is a smart idea, particularly if you're especially privacy-conscious however, your biggest concern is most likely the geolocation information.

### Install the application

~~~bash
sudo apt install exiftool
~~~

### Show the metadata

~~~bash
exiftool photo.jpg
~~~

### Show metadata for all *.jpg files. Note: The extension is case sensitive

~~~bash
exiftool -ext jpg .
~~~

### Same as above, but include sub directories

~~~bash
exiftool -r -ext jpg .
~~~

### Remove all metadata

~~~bash
exiftool -all= -overwrite_original photo.jpg
~~~

### Remove all metadata of all *.jpg files in the current directory

~~~bash
exiftool -all= -overwrite_original -ext jpg .
~~~

### Does the same as above, but includes subdirectories

~~~bash
exiftool -all= -r -overwrite_original -ext jpg .
~~~

### Remove all GPS metadata of *.jpg files in the current directory

~~~bash
exiftool -gps:all= *.jpg
~~~

## References

- [Linux Nightly](https://linuxnightly.com/how-to-remove-exif-data-via-linux-command-line/) online Linux Magazine.
- Removing exif data using [other operating systems](https://www.howtogeek.com/203592/what-is-exif-data-and-how-to-remove-it/)
