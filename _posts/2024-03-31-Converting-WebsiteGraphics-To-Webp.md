---
title:  "Converting website graphics to webp"
date:   2024-03-31
categories: [Homelab, Linux, Web]
tags: [homelab, linux, web]
image:
    path: ../assets/img/posts/2024-03-31-Converting-Website-Graphics-To-Webp/Install_webp.webp
---

## How to convert graphic files to the smaller and faster webp format

Here's a handy linux command line tool that can convert all your graphic files to the faster and quicker loading webp format.

These instructions are aimed at people who use linux as their daily driver (like myself), if you use a debian based distribution, then all the instructions below will apply.

If you use a distribution not based on debian, then the install syntax will vary accordingly.

Unfortunately, websites in general are getting more and more bloated, and slower to load, due to them having massive graphics file, java script and other essential libraries. Although this can't be helped when your using specific platforms, you can still make the graphics on your website load quicker by using an upto date and more web based file format.

### Installing WebP on Ubuntu and Debian

```bash
sudo apt install webp
```

### Convert PNG images to WebP

```bash
cwebp image.png -o image.webp
```

### Use the -q option to control the quality level

This can help you achieve a lower file size while sacrificing some quality in your image. A quality level of 80-85% generally gives acceptable results.

```bash
cwebp -q 85 image.png -o image.webp
```

### It’s also a good idea to use the -mt (multi-threading) option

which will better utilize your system’s CPU and convert the images more quickly.

```bash
cwebp -q 85 -mt image.png -o image.webp
```

### If you have a lot of PNG photos to convert

```bash
for f in *.png; do cwebp -q 85 -mt $f -o ${f%.*}.webp; done
```

### Convert WebP images to PNG

Use the dwebp tool to decode WebP images into PNG, all you need to do is specify the name of your WebP file, the -o (output) option, and the name of your new PNG file.

```bash
dwebp image.webp -o image.png
```

You can use a Bash for loop to bulk convert hundreds or thousands of WebP photos at once.

```bash
for f in *.webp; do dwebp $f -o $f.png; done
```

## References

- An image format [for the Web](https://developers.google.com/speed/webp)
- Convert [PNG Images to WebP](https://linuxnightly.com/convert-png-images-to-webp-on-linux-with-commands/) on Linux (With Commands)
- How to View [WebP Images in Ubuntu](https://itsfoss.com/webp-ubuntu-linux/) and Other Linux Distributions
