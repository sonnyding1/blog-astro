---
pubDatetime: 2023-03-19T07:22:56Z
title: Voice Conversion with so-vits-svc
postSlug: voice-conversion-with-so-vits-svc
featured: true
draft: false
tags:
  - project
ogImage: ""
description: In this post, I will briefly describe how I trained a voice conversion model using this so-vits-svc 4.0 repository.
---

## Table of contents

## Introduction

In this post, I will briefly describe how I trained a voice conversion model using [this](https://github.com/svc-develop-team/so-vits-svc) so-vits-svc 4.0 repository. As for now, so-vits-svc is a black box to me, I do not understand what happens under the hood. In the future, I might read related research papers to understand the theory behind this project.

## Raw dataset

The input should be a zip folder containing folders representing speakers, with each folder containing many audio files that are at around 10s or less. The audio files should all be preproccessed, such that there is no noise nor background music. There should only be the target speaker's voice. All audio files of a speaker should sum up to 1 to 2 hours of data in order to obtain a decent model. I used a dataset that was already made from [this](https://huggingface.co/datasets/aaawerdtgawet/sovits4.0_Azusa_44100/tree/main) HuggingFace dataset repository, which saved me a lot of time. In `Azusa.zip`, there is only one speaker, to which I named as Azusa. FYI, Azusa is in fact referring to a Chinese VTuber named [阿梓从小就很可爱](https://space.bilibili.com/7706705).

## Pretrained models D_0 and G_0

From the same repository, there are `D_0.pth` and `G_0.pth`, those are the pretrained models, which serve as the starting point of our training process. I did not use `D_0.pth` and `G_0.pth` from the repository above though, I downloaded from some random places, which soon showed me the significance of choosing the correct `D_0` and `G_0`.

For my first try, when I had inferior `D_0` and `G_0`, the output audio at 24800 steps is as follows:

<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/1472255032&color=%23ff5500&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true"></iframe><div style="font-size: 10px; color: #cccccc;line-break: anywhere;word-break: normal;overflow: hidden;white-space: nowrap;text-overflow: ellipsis; font-family: Interstate,Lucida Grande,Lucida Sans Unicode,Lucida Sans,Garuda,Verdana,Tahoma,sans-serif;font-weight: 100;"><a href="https://soundcloud.com/r-t-176456196" title="R T" target="_blank" style="color: #cccccc; text-decoration: none;">R T</a> · <a href="https://soundcloud.com/r-t-176456196/azi-24800" title="Azi 24800" target="_blank" style="color: #cccccc; text-decoration: none;">Azi 24800</a></div>

I wasn't particularly happy with this result, 阿梓's voice quality seems very bad even though I was already at like 150 epochs, and I was seeing diminishing progress. So, I obtained much better pretrained base models, which are essentially neutral woman's voice conversion models. At 4000 steps, it sounds like this:

<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/1472259802&color=%23ff5500&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true"></iframe><div style="font-size: 10px; color: #cccccc;line-break: anywhere;word-break: normal;overflow: hidden;white-space: nowrap;text-overflow: ellipsis; font-family: Interstate,Lucida Grande,Lucida Sans Unicode,Lucida Sans,Garuda,Verdana,Tahoma,sans-serif;font-weight: 100;"><a href="https://soundcloud.com/r-t-176456196" title="R T" target="_blank" style="color: #cccccc; text-decoration: none;">R T</a> · <a href="https://soundcloud.com/r-t-176456196/azi-2-4000" title="Azi 2 4000" target="_blank" style="color: #cccccc; text-decoration: none;">Azi 2 4000</a></div>

Then, at 16000 steps, it is like this:

<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/1472259982&color=%23ff5500&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true"></iframe><div style="font-size: 10px; color: #cccccc;line-break: anywhere;word-break: normal;overflow: hidden;white-space: nowrap;text-overflow: ellipsis; font-family: Interstate,Lucida Grande,Lucida Sans Unicode,Lucida Sans,Garuda,Verdana,Tahoma,sans-serif;font-weight: 100;"><a href="https://soundcloud.com/r-t-176456196" title="R T" target="_blank" style="color: #cccccc; text-decoration: none;">R T</a> · <a href="https://soundcloud.com/r-t-176456196/azi-2-16000" title="Azi 2 16000" target="_blank" style="color: #cccccc; text-decoration: none;">Azi 2 16000</a></div>

First of all, at 4000 steps, the new model already beats the old model in terms of clarity. Secondly, from 4000 steps to 16000 steps, our model's voice slowly shifts away from the base model's voice to our desired target voice. So, it makes sense to choose a base model that can output a girl's voice, because its voice is already very close to our target, it takes less time to obtain better results.

## Audio for inference

After the model is trained well enough, we can use the model for inference. In other words, we feed in an audio file with vocal only, the output will be an audio file with our target speaker saying/singing the same words in the same pitch. For example, the input audio for inference in the examples in the previous section is a segment from the song Nico by advantage Lucy:

<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/1472264518&color=%23ff5500&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true"></iframe><div style="font-size: 10px; color: #cccccc;line-break: anywhere;word-break: normal;overflow: hidden;white-space: nowrap;text-overflow: ellipsis; font-family: Interstate,Lucida Grande,Lucida Sans Unicode,Lucida Sans,Garuda,Verdana,Tahoma,sans-serif;font-weight: 100;"><a href="https://soundcloud.com/r-t-176456196" title="R T" target="_blank" style="color: #cccccc; text-decoration: none;">R T</a> · <a href="https://soundcloud.com/r-t-176456196/trimmed" title="Nico Trimmed" target="_blank" style="color: #cccccc; text-decoration: none;">Nico Trimmed</a></div>

However, it is painful trying to make the audio for inference clean. I use Ultimate Voice Remover 5, or [UVR5](https://github.com/Anjok07/ultimatevocalremovergui), a powerful AI voice removal tool, to separate music and human voice. But usually songs have multiple voices singing at the same time, or human voice was already processed by equalization. In this case, good luck. I haven't found out a way to deal with them yet.

## Final product

Here is 阿梓 singing advantage Lucy's Nico:

<iframe src="////player.bilibili.com/player.html?aid=993714704&bvid=BV1zx4y1P76V&cid=1059263839&page=1&high_quality=1" allowfullscreen="allowfullscreen" width="100%" height="500" scrolling="no" frameborder="0" sandbox="allow-top-navigation allow-same-origin allow-forms allow-scripts"></iframe>

## Conclusion

That was fun. Although I didn't write much code, I learned a lot about audio processing. Furthermore, this may be the beginning of my study about AI in the field of audio processing, I might start with learning VITS, a text to speech technique.
