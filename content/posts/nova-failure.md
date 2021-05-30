---
title: How I successfully failed to create a 1000 user webapp!
date: 2021-05-30T20:10:42+05:30
lastmod: 2021-05-30T20:10:42+05:30
author: MajAK
avatar: /img/me.png
# authorlink: https://author.site
cover: /img/NoVA.jpg
categories:
  - Failures
tags:
  - covid19
  - failure
# nolastmod: true
draft: false
---

__A recollection of the week I tried to create an app, learnt a lot but eventually utterly failed!__

<!--more-->

## The Motivation

It was the onset of May and the government of India had just announced a vaccination drive. Everyone was pretty excited and a bit relieved with the second wave being disastrous, but to my surprise, I could not even see a single slot available whenever I opened “_aarogya setu_”. The reason being, everyone was as excited as I was. So like every other Software Engineer, I started searching for apps/webapps which would notify me whenever covid slots were available! It didn’t take more than a day to realise : __None of them worked__.

So for no reason at all, I decided, why not make my own webapp. Afterall after 4 years of Computer Science at a premier institute, I was at least this skilled.The architecture was simple : Have a webapp which would handle user registration, have required DB storage structures, a cron job to get newly available slots and a notification service to notify the users. It __sounded very simple__ in my head and seemed very easy to do. In the meantime, I got a slot for the first dose of COVID vaccine and was scheduled to be taken the next day.

## The Beginning

So figuring things regarding the webapp out on the first day, being sunday, I thought to pull off an all nighter. I started with writing a script in python which would use cowin api to get slots available at the current moment. Tested it for regions where covid slots were usually working and it seemed to work fine! So then I started with the webapp for registration, for a change thinking to have a decent UI. Pulled a template off the internet and edited and _'boom'_, I had a semi-decent buggy UI ready with the welcome screen and pincode/district collection.

### Teaming up

Experiencing the after-effects of vaccination as I was warned about, I skipped a day of work and thought to ask around if anyone wanted to help. Initially one of my friends said no, because sometimes it gets hard to manage work and things like this, but then one of the other “bored” friends agreed to help! Comes into the picture Nikita. So she agreed to take up the notification service part, since she had already worked with sendgrid during her internship. The plan seemed robust and we were good to go. With a bit of late night work, I managed to pull off the auto-population of states and districts and the registration part. The initial plan being we would support sms and mail notifications, because sms notifications would be a lot more helpful for the end users.

## Roadblocks

### The first is always the worst - SMS Notifications
So pulling off all-nighters and balancing work and the project, we were ready with a working model of the webapp. But there comes the first road-block in the picture! SMS Notification. So I registered myself on various services which provided text messaging in India. I came to know that sending sms in India requires a whole lot of registration process and a separate process for obtaining license. This is when I came across a shady website, which on its homepage said that no registration was required and we could send the messages as promotional ones. After buying a sms pack from the website it started working well. But the website was shady indeed, because a day into using it, I received a mail saying I do not have DCT registration and hence my account has been suspended. When it worked, the notifications were of this sort...

<!-- <center>
<img src="/img/SMS.jpeg" alt="OneSignal" height="250"/>
</center> -->

### The second one is here - Hosting
Then in line comes the second road-block, being a microsoft employee I planned to host my application on azure. But I was not able to host a flask webapp for all the random reasons, so I took the easy way out : Heroku - simple, fast, easy. I hosted the cron jobs using azure function set to run a time interval of 2-3 minutes. As for the implementation details, the registration webapp was in flask, the cronjob in python and for the database I used __azure tables__ (since my data was structured and it is very cheap).

### A successful release?
We planned to release the app with just email notifications and planned to test the app. I did not want to bring one more non-working app into the system. I asked a few of my friends to help me beta-test and overall it went kinda __meh__. As such it was working fine but there were a lot of issues with UI and usability. People were not able to understand the navigation system. Hence one more all-nighter, fixing all the issues, sleeping at 5 AM and attending meetings at 8.30 AM. And all those hackathon days flashed in front of my eyes, being motivated as ever. The plan was to release the app on the day itself. We put up a simple insta story and a linkedin post and the response seemed positive.

### The day where it all went wrong!
Onto D-Day. So 3 hours into the web app being released, we started getting feedback about UI issues and people not receiving mails. Fixing all of those issues and making it more informational, we started getting feedback that it does not work. A few minutes of digging made me realise : My cron jobs were failing! Baffled and wondering what was going wrong, I came across a news article. The GOI had issued a notice saying that it has banned all the IPs from which > 100 requests are received in an interval < 5 mins. I didn’t expect the number of queries to go so high, only to realize that we had almost 200 users by this point of time, and with almost 70+ pin codes registered, it repeating every 2 mins, made GOI servers block my azure servers. The buggy app looked something like this (source - one of the users)...

<center>
<img src="/img/BuggyMe.gif" alt="Buggy Me" height="400"/>
</center>

Meanwhile, my friend and the founder of piblitz had sublet a subdomain to the service and was telling people in his group. I discussed the issue with him and his brother(who helped a lot figuring things out) and we came to the conclusion, since there are around 800 districts in India, let us disable registration through pincode and make 10 separate function apps to host cron jobs (I made 11 to be safe) based on the district ids in cowin. It seemed to resolve the issue of IP getting blocked. Again the app was working fine and the updates regarding new slots opening were relevant. But then just when you feel that things are going well is when things really start going south.

### OneSignal to the rescue?
So juggling my work and changes in the webapp, I realized we are soon going to run out of sendgrid emails. I had bought a sendgrid account on azure with a capacity of 40k emails and it would get depleted in just a day or two. So I decided to move away from emails and came across OneSignal. OneSignal helps you send push notifications using web browsers and actually seemed to be a really good option. Going through a lot of coding, deletion, frustration, bug fixing and testing, finally it was ready. But I did not want to put it out without testing. So I thought of testing it for a few hours in my area and after fixing a lot of bugs, it was working perfectly and was ready to be released. The notifications were strutured something like this on web and mobile respectively...

<center>
<img src="/img/Notifs.jpg" alt="OneSignal" height="250"/>
</center>


### The nail in the coffin!
But as I said, only when things seem to be working well, things start to go south. And _'boom'_, I realized that the notifications for the age group of 18+ were hardly there. The one’s for 45+ was working just fine. I came to realize that the government had cached the data upto 30 mins and hence there was no way to be sure that the slots were available at the time of notification. So the notifications did come, the slots did show on the cowin homepage but when you logged in… _'poof'_ they were not available anymore! As frustrating as it was, I first called my friend to stop sharing the webapp with others(since he was the major source of it spreading xD).  I put down my webapp saying that we are figuring a way around and have stopped the service right now and sent an email to all the registered users apologising for not meeting their expectations. And the whole week long drama and late nights came to an end. Still tired and frustrated, I still ran one of the instances for my personal use and got covid slots for my mom booked with the help of it. At Least it helped! Although all in all, by the end of it all, we had more than a 1000 users registered and we had successfully failed to create a 1000 user app! Hopefully it helped at least a few people except me. In its last moments it looked something like this...

<center>
<img src="/img/OneSignal.gif" alt="Final Version" height="400"/>
</center>

## My Takeaway
But I learnt. I learnt how to build a scalable app, I learnt how helpful push notifications can be, I learnt how to work under pressure, I learnt deploying a lot of things to azure, I learnt how to fail and I learnt how to learn from failures! It was just a small step to be helpful in these covid days with the skills I got and for me at the end of it, my heart was contended! More than that I was happy that I tried, although I failed!

## Credits where Credits are due
<code>
Nikita Rungta for going through the BT with me,<br>
Harnish Rajput for helping me with the subdomain and constructive feedback,<br>
Dharmesh Rajput Bhaiya for helping to figure things out on the go,<br>
My Juniors Prerna Gupta and Shradha Sehgal for Beta Testing the original webapp<br>
And Mishal Shah for testing the OneSignal Beta version with me.
</code>
<br><br>
<b>All in all it was a fun week, a reminder why we code!</b>