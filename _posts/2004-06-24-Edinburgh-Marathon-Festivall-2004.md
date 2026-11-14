---
title: "Edinburgh Marathon 2004"
date: 2004-06-24
draft: false
categories: [Gigs and Socials]
tags: [gigs and socials]     # TAG names should always be lowercase
image: ../assets/img/posts/headers/edinburgh.webp
---

Well done Chris for completing the Marathon in 3 hours 30 Minutes. Well done Jan for competing it in a bit over 4 hours (which would probably be about an hour quicker than me), here are a few small video clips of the event.

My mate Chris.
{% include embed/youtube.html id='Q_8EBpjscW0' %}

Batman Runing the Edinburgh Marathon.
{% include embed/youtube.html id='GrSoxF4j-bA' %}

A Bear in a kilt and shades running the Edinburgh Marathon.
{% include embed/youtube.html id='G0j6YpBKHNA' %}

## Techie blurb

I had to convert the codex to h264 and ac3 with a container of mp4 (it was avi). Excuse the crap quality, it was taken on a very cheap digital camera, no posh mobile phones in those days. 😀

```bash
ffmpeg -i Edinburgh-Marathon-2004-Chris.avi -c:v libx264 -c:a ac3 Edinburgh-Marathon-2004-Chris.mp4

title           : FUJIFILM AVI STREAM 0100
  Stream #0:1: Audio: pcm_u8 ([1][0][0][0] / 0x0001), 8000 Hz, mono, u8, 64 kb/s
Stream mapping:
  Stream #0:0 -> #0:0 (mjpeg (native) -> h264 (libx264))
  Stream #0:1 -> #0:1 (pcm_u8 (native) -> ac3 (native))
```

## Reference

* Marathon View [Statistics](https://marathonview.net/race/98399)
