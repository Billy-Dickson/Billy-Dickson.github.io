---
title: Handy IT websites
date: 2024-05-01
categories: [Homelab, Handy Websites]
tags: [homelab, handy websites]     # TAG names should always be lowercase
image:
   path: ../assets/img/posts/2024-05-01-IT-Websites/header.webp
---

A bit of a work in progress, I'm going to add sites and snippets of instructions as and when I find them.

## Instructions on fixing bufferbloat on pfsense

I've recently had my home internet upgraded to fibre on 2nd May 2024, It didn't go smoothly or well.

Anyway, below are some intructions on how to set up a codel-limiter to combat buffer bloat, the instructions worked really well and I'm really chuffed with the results.

I’ve recently had my home internet upgraded to fibre on 2nd May 2024. It didn’t go smoothly or well, but that’s a different story.
Anyway, below are some instructions on how to set up a codel-limiter to combat buffer bloat, the instructions worked really well and I’m really chuffed with the results.

1. Test your broadband speed with this site, do make sure that it’s quiet on your network (not downloading or watching YouTube), take a note of the speed, Its best tested directly via Ethernet.
2. Test for buffer-bloat here and take a note of your results.
3. After testing the virgin install, it was showing a B, which was quite reasonable.
4. Fix buffer-bloat via the instructions on the pfsense website
5. Retest your network speed, you might find its slightly slower, maybe 1% to 2% (but I’m ok with that).
6. Retest your network for buffer-bloat and all being well, you should get a better result, see mine below.

![Bufferbloat Website](../assets/img/posts/2024-05-01-IT-Websites/Waveform-Bufferbloat-Website.webp)

## References

* [MAC address lookup](https://hwaddress.com/) site, particularly handy if your trawling through dhcp logs.
