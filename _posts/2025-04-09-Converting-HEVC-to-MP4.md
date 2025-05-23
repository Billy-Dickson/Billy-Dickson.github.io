---
title: Using ffmpeg to convert video formats
date: 2025-04-08
categories: [Homelab, Linux, Debian]
tags: [homelab, linux, debian]     # TAG names should always be lowercase
image:
   path: ../assets/img/posts/2025/2025-04-09-Converting-HEVC-to-MP4/header.webp
---

## Installing ffmpeg on Ubuntu and Debian

If you are using Ubuntu or Debian as I am, you can install ffmpeg using the following command. There instructions would also work if you happen to be running WSL (Windows Subsystem For Linux), which I also run.

```bash
sudo apt install ffmpeg
```

If you're an iPhone user and have a need to convert a video file that you've dragged off your device on a windows or linux machine, then the command below will convert your file to a mp4 container, using the X264 video codec, which is natively supported windows.

### What are containers and codecs

A container is a file format that holds digital media data, like video and audio streams, along with metadata like subtitles and audio tracks. Think of it as the wrapper or package for the media. A codec, on the other hand, is a software algorithm that compresses and decompresses digital data, like video or audio. It's the tool that encodes the media and decodes it for playback.

### Converting containers and codecs

Converting a h265 (HVEC) container to a h264 continer

```bash
ffmpeg -i input.hevc -c:v libx264 -c:a copy output.mp4
```

Converting a h264 container toa h265 container (HVEC)

```bash
ffmpeg -i input.mp4 -c:v libx265 -vtag hvc1 output.mp4
```

Changing containers, just ensure that you change the file extension on the output file to whatever container you want to use. eg.. output.mp4, output.avi

```bash
ffmpeg –i input.mkv -c:v copy –c:a copy output.mp4
```

Removing meta data.

```bash
ffmpeg –i <inputfile> –metadata title=”” -map_metadata -1 <outputfile>
```

Converting DTS stream to an AC3 stream at the maximum allowed bitrate for AC3 of 640kbs and just copying the video file.

```bash
avconv -i <inputfile> -c:v copy -c:a ac3 -b:a 640k <outputfile>
```

Convert the Video stream to h264 and the audio stream to ac3 (with the bitrate the same rate as the input), a simple ffmpeg -i filename will give you the audio and video bitrates.

```bash
ffmpeg -i <inputfile> -c:v libx264 -c:a ac3 <outputfile>
```

Converting a 4K video file to 1080p just testing this command line to see how it turns out, might have to tweak it a bit. References at the end of the document.

```bash
ffmpeg -i <inputfile> -vf scale=1920:-2 <outputfile>
```

Remove all metadata from a video file keeping the container and the codec the same.

```bash
ffmpeg -i <inputfile> -map_metadata -1 -c:v copy -c:a copy <outputfile>
```

Another option to use is the map option, for example, if you happen to have a number of channels with different languages and you only want to include the one when you remix the file, you can pick the video and audio channels to select by using the following syntax.

```bash
ffmpeg -i <inputfile> -map 0:0 -map 0:2 -c:v copy -c:a copy <outputfile>
```

Choosing  all channels (including subtitles) but changing the container to mp4.

```bash
ffmpeg -i <inputfile> -map 0 -c:a copy -c:s copy -c:a copy <outputfile> 
```

Downmixing a 4k mkv container to 1080p mp4 container and converting the audio stream to ac3.

```bash
ffmpeg -i <inputfile.mkv> -vf scale=1920:-2 -c:v libx264  -c:a ac3 <outputfile.mp4>
```

How to change the video or audio language using ffmpeg.

I’m transcoding a media files that contains a video tracks 0:0 set to Polish, it also contains 2 audio tracks, the default track 0:1 is a Polish audio track and the secondary audio track 0:2 is English.

What I’m doing with the command below is selecting the video track (pol) and the audio track (eng) then changing the language of the video track to (eng) and changing the container type from a Matroska to the mp4 container.

```bash
ffmpeg -i <inputfile.mkv> -map 0:0 -map 0:2 -c copy -metadata:s:v:0 language=eng <outputfile.mp4>
```

I intend to add other ffmpeg commands below, as and when I have time 😃

## References

* superuser.com - Video encoding using [ffmpeg](https://superuser.com/questions/785528/how-to-generate-an-mp4-with-h-265-codec-using-ffmpeg)
* captureguide.com - [iPhone video format](https://www.captureguide.com/iphone-video-format/)
* What is HEVC or [High Efficiency Video Coding](https://en.wikipedia.org/wiki/High_Efficiency_Video_Coding)
* ffmpeg [wiki](https://trac.ffmpeg.org/wiki)
* What are [codec's and containers](https://www.dacast.com/blog/codec-vs-container/)
* Explaining Computers Video - [Explaining Digital Video: Formats, Codecs & Containers](https://www.youtube.com/watch?v=-4NXxY4maYc)
* [Normalising Audio](https://superuser.com/questions/323119/how-can-i-normalize-audio-using-ffmpeg) using ffmpeg
* [Converting various video formats](https://bytescout.com/blog/2016/12/ffmpeg-command-lines-convert-various-video-formats.html) using ffmpeg
* Scaling and [keeping aspect ratio](https://trac.ffmpeg.org/wiki/Scaling)
* Open source guide to [converting media file formats](https://opensource.com/article/17/6/ffmpeg-convert-media-file-formats)
