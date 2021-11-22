---
layout: post
title: You-get+FFmpeg :->(download video)
date: 2021-11-21 11:06:58 +0300
description: Ways to download web videos. # Add post description (optional)
img: /you-get+ffmpeg/0cover.png # Add image post (optional)
tags: [Windows, You-get, ffmpeg] # add tag
---

## Preface
I have always found interesting videos on many video platforms, hoping to save them from the platform and watch them offline, but there are still many limitations in the caching or downloading methods provided by the platform, which prevents us from actually saving them locally. So I found through searching that a python library like you-get can help us download videos on webpages, and actually save the file downloads in our local. It is worth noting that you-get is only a downloader rather than a cracker, You can also download video content that includes members, but you need to have a member account.

FFmpeg is an open source computer program that can be used to record, convert digital audio and video, and convert them into streams. It provides a complete solution for recording, converting, and streaming audio and video, including a very advanced audio/video codec library libavcodec. In order to ensure high portability and codec quality, many codes in libavcodec are developed from scratch. FFmpeg is developed under the Linux platform, but it can also be compiled and run in other operating system environments, including Windows, Mac OS X, and so on.

My computer environment is windows10, I use python3.8 version, and my browser is Firefox. The downloading method for non-members and members is the same, but downloading for members is a bit more troublesome. Here, I will talk about the simple method first and then the downloading method of member videos.

## Let's get started

#### Basic download method for non-members

![photo1]({{site.baseurl}}/assets/img/you-get+ffmpeg/1.png){:width="80%"}

Here I use Anaconda to configure a python3.8 virtual environment, named swin, install our python library you-get through pip, here I have installed it, so it shows that it has been installed.

![photo2]({{site.baseurl}}/assets/img/you-get+ffmpeg/2.png){:width="80%"}

We are here to test with a random video from bilibili. Of course, this is a non-member restricted video. We found the URL of this video.

![photo3]({{site.baseurl}}/assets/img/you-get+ffmpeg/3.png){:width="80%"}

The download location of the file is the current path location, so we first switch the path to the location where we want the file to be downloaded, and then use the command to download the video we want to download, the format is "you-get url". We can use the -i parameter to display the available resolution of the video first, as shown in the figure above.

![photo4]({{site.baseurl}}/assets/img/you-get+ffmpeg/4.png){:width="80%"}

The next step is to specify the resolution we want to download through the parameter -format, so that we can download the video with the specified resolution.

![photo5]({{site.baseurl}}/assets/img/you-get+ffmpeg/5.png){:width="80%"}

Of course, if you don’t care about the clarity or have no choice, you can directly select "you-get url" to complete the download.

![photo6]({{site.baseurl}}/assets/img/you-get+ffmpeg/6.png){:width="80%"}

This is a way of downloading files for non-members. At that time, it was just a video downloader rather than a cracker. This phenomenon would occur when downloading member videos.

That's right, it is an error, telling us that this is a member's video and cannot be downloaded.

For members' videos, we need some other auxiliary software and a member account of the video platform.

#### Basic download method for members
![photo7]({{site.baseurl}}/assets/img/you-get+ffmpeg/7.png){:width="80%"}

We search for ffmpeg, enter the official website of the software, choose our platform to download the software, my operating system is windows10.

![photo8]({{site.baseurl}}/assets/img/you-get+ffmpeg/8.png){:width="80%"}

Select the ffmpeg file you want to download, the software pointed by the arrow.

![photo9]({{site.baseurl}}/assets/img/you-get+ffmpeg/9.png){:width="80%"}

Download the full version on github. After the download is complete, we don't need to install it, just unzip it and it can be used, but we need to add the path of the bin in the file to the environment variable Path.

![photo10]({{site.baseurl}}/assets/img/you-get+ffmpeg/10.png){:width="80%"}

The method of adding environment variables will not be repeated, just add the directory where bin in the file is located according to the steps.

![photo11]({{site.baseurl}}/assets/img/you-get+ffmpeg/11.png){:width="80%"}

We enter the DOS command line and enter ffmpeg to try to see if the addition is successful. If these contents appear, it means that the environment variables of ffmpeg have been added successfully.

![photo12]({{site.baseurl}}/assets/img/you-get+ffmpeg/12.png){:width="80%"}

Next, let's try to download the content of the member. Because we need to use cookies, and the cookie format of Firefox meets the format requirements in you-get, we recommend using only Firefox for this operation.

Go to our video platform webpage and log in to our member account.

![photo13]({{site.baseurl}}/assets/img/you-get+ffmpeg/13.png){:width="80%"}

The default cookie of Firefox is stored in this directory, C:\Users\\[Username]\AppData\Roaming\Mozilla\Firefox\Profiles\brh0nlmh.default-release.

Where [Username] is the username of your computer.

![photo14]({{site.baseurl}}/assets/img/you-get+ffmpeg/14.png){:width="80%"}

We see the cookies.sqlite file, which stores the cookie data recorded by the browser. We can copy this file to the desktop for convenient use.

![photo15]({{site.baseurl}}/assets/img/you-get+ffmpeg/15.png){:width="80%"}

Note: If you do not find AppData, remember to check the option to hide files, this is a hidden folder. Then go look for it.

![photo16]({{site.baseurl}}/assets/img/you-get+ffmpeg/16.png){:width="80%"}

Then enter our member video and get the URL of this member video.

![photo17]({{site.baseurl}}/assets/img/you-get+ffmpeg/17.png){:width="80%"}

The video download instruction is the same as that of non-members, you-get url -c cookie.sqlite. We can see that the member video can also be downloaded successfully.

![photo18]({{site.baseurl}}/assets/img/you-get+ffmpeg/18.png){:width="80%"}

Next, we can save our video content to our computer offline, without being restricted by the platform's cache or download mechanism.