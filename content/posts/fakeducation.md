---
title: FAKEducation -  A Deep Fake approach to Education
date: 2021-08-02T11:15:07+05:30
lastmod: 2021-08-02T11:15:07+05:30
author: MajAK
avatar: /img/me.png
# avatar: /img/author.jpg
# authorlink: https://author.site
cover: /img/original.jpg
categories:
  - hackathon
tags:
  - deepfake
  - education
# nolastmod: true
draft: false
---

__My Journey on how I built a product using Deep Fakes which ended up as a runner up at SigmaHacks3__

<!--more-->

## The Context
Bored of the normal job like every other newly joined SDE, me and two other fellow human friends decided that let us give a hackathon. We came across __SigmaHacks 3__. It was a one day hackathon and seemed quite interesting. _The Education Path_ tbh seemed exciting to me quite a lot. But like every friend ever, they backed out at the last moment :D Still since for the sole reason I had nothing else to do and was a tad bit excited I decided let us give it. It was a short one day hackathon so I was sure that my excitement would not fade away that soon!

## The Ideation phase
The __initial idea__ was to work in the domain of making sure students attended the online classes by means of some interesting activities. Asked a few of my friends around on firstly how to detect that one is actually attending the lecture. For the one’s where the video camera was supposed to be kept on all the time, it was easy, use __temporal FFTs__ to check for repeating patterns. The hard part was to come up with something where it was not possible for students to keep their web cameras on. I was thinking something in line with random attendance for each student or __random questions__ based on the field, but with my friends dropping out, it seemed a bit of a stretch to do it all by myself.

With so much social media presence and students getting used to it, it is sometimes hard for students to get through **boring** lengthy texts or lecture notes. Also the need for **entertaining teaching methods** for toddlers and younger kids has been on a rise. In some rural areas, online lectures are not possible which in the end results in students losing a lot. So I thought, why not use **Deep Fakes** for some good. For those who don’t know what deep fakes are, **in layman terms** they are fake videos created based on an audio sample and anything ranging from an image to a video. With the Hackathon not being according to my timezone, I decided to proceed with this and got some sleep at night.

## The Building phase
It was __Sunday morning__ and like every other Sunday Morning in my life, I woke up Late. My parents were travelling to Ahmedabad to meet Masi(Maternal Aunt) and I had heavy heartedly given up on going to attend this hackathon, simply because I needed some excitement back in life. So lazily waking up, the game plan was clear: There is __no time__ to make and train my own Deep Fake, so go through all available online Deep Fake generators and choose a good lightweight one. Build upon it and then think on what to do next.

After looking at a lot of codes available online, making them work on my system, trying and testing with my own video and audio, I came across [__one__](https://arxiv.org/abs/2008.10010) from my Institute itself. It was __kind of state of the art__ work and seemed pretty good. But there was a catch, it was heavy. I could not make it run on my system locally. After successfully letting it __crash__ my PC 2-3 times, I gave up. That is when I decided I will host it on colab. But there was something I needed to figure out, I was planning to make a full fledged UI to make it easy to use. How would I __trigger__ my colab code from my Flask app? The solution was simple, I won’t :D I would rather __host__ my Flask App in __colab__. So all set to go and some rigorous coding later, I was ready with making the Deep Fake work.

This is when I decided that since text is the most boring entity out there, I also need to use __TTS__ to make text interesting. After experimenting a bit I decided not to look for that great of a TTS and simply go with __Google TTS__ for now. Meanwhile, I discussed this idea with some of my friends and took their feedback. Some liked it, some were like _Meh_! Anyways I was too deep into it to overthink. With the base system in place, it was time to experiment. I needed some __good examples__ to show in the presentation and hence I started searching.

__A sample here combines fortnite character with an excerpt from audio book about earthquakes:__
<center>
<iframe width="440" height="260" src="https://www.youtube.com/embed/Bkw3E6AaysA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</center>

But I was bored, so in the middle of all the fuss, I __went out__ with my friends, had a bit of fun, talked to them, brought shakes back home and sat with them and _holy cow_, it was 10 PM already. I had loads of work left to do. After they went back home because of the lockdown, Like every other __lazy kid’s inner voice__ I thought : _Kushal see you have to complete it and you need to pull off an all nighter, better watch a movie and then start :facepalm_. Call it dumb but it probably helped with keeping my mind __fresh__.

After __experimenting__ a lot from audio lectures to extracting audio from youtube videos and finding inspiration in animated characters of Fortnite, I found quite some good examples. Tbh I even took my friend’s profile picture from WhatsApp for one of the experiments just for fun :P It was now time to make the __UI component__ and link it. __Flask__ with __ngrok__ was a viable option and I was ready with it. It sounds simple but there was a lot of UI theme selection, building, making appropriate examples from Deep Fakes. It was around _5 AM_ and I was done. All I had to do was record a video and submit. With 3-4 takes, making a submission on Devpost it was 6AM and I was done. I was literally done, didn’t care anymore about the hackathon, __overthinking__ that probably should have gone to Ahmedabad, all I saw was my bed :D

The final presentation looked something like this _(watch in 0.75x ples)_:
<center>
<iframe width="440" height="260" src="https://www.youtube.com/embed/q38nD-nSLW4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</center>

## The Results
Tbh I don’t even remember what time the __results__ were being declared. It was like _12:30 AM_ I think and there was a __live stream__ by the organizers to declare the results. I was __not sure__ it would have been the best idea of the lot, since I was working alone on it, but __they said FAKEducation__ and my heart melted. It was the **_Eureka Eureka_** moment, except there was no one around. So I remember _texting my junior_ since she was the only one awake and she was probably confused why I was so excited xD. Super excited, I took a photo that I won and __sent__ it to my __friends__. I was excited and proud and happy. It was a __good detour__ from my daily software life and maybe was a day back to my college days… Won _50$_, gave it to my cousins because Paypal in India sucks. All in all it was the most __fun experience__ I had in a long long while!

## My Takeaway
I learnt that it is __okay__ to __do__ whatever you want __by yourself__, because it will make you a __proud__ human. Also having such __supportive friends__ around always helps and it sucks when they sleep early. And the most important one : __Hackathons are alwayssss fun!__

## Scope of Improvement
There is a lot of scope for improvement. With __new__ and improved __deep fake methods__ coming in, one with __gestures__ can be used to make sure it seems more real. TTS can be improved simply by using a __paid service__ which is near __human voice__. Also __watermark__ should be added to prevent misuse.
## Credits where credits are due
<code>
Ravi Sawlani and Mishal Shah for cancelling on me to take part -.-<br>
Mishal Shah and Harnish Rajput for listening to my idea<br>
Ravi Sawlani, Mishal Shah and Harnish Rajput for going out that night (obv joined by above 2 fellow humans)<br>
</code>
<br>

__P.S Call it a coincidence, I just took my vaccine the day before. It seems vaccine has something to do with boosting my brain xD__


## Some links
- [Devpost](https://devpost.com/software/fakeducation)
- [Samples](https://drive.google.com/drive/folders/1pArTv2H5-RTsscaRTEdN8Qy3bY189Jzx?usp=sharing)
- [Code](https://github.com/kUSHAL0601/FAKEducation)
- [Colab](https://colab.research.google.com/drive/1j8nrSvUmMNWnt--ArEBSWf74twX5PGGp?usp=sharing)