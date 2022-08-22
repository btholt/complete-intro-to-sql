---
title: "Introduction"
description: "Brian welcomes you to the course and sets expectations of what you will learn in the Complete Intro to SQL and PostgreSQL"
---

Hello! And welcome to the seventh edition of the Complete Intro to SQL and PostgreSQL as taught by [Brian Holt][twitter].

![course logo](./images/course-icon.png)

I wrote this course to help developers get more acquainted with what can be an intimidating topic: SQL. SQL, like coding in general, has a great amount of depth to it which can make it seem unapproachable to people who haven't spent much time with it. I can tell you as a veteran developer with lots of experience with SQL that while there is much depth to be had, there is a lot you can get done with an introductory set of knowledge. A cursory knowledge of SQL yields most of the benefit in my opinion and the depth only makes it better.

In this course you will learn

- How to set up your first database and tables
- How to think about data going into your tables
- The best ways to query your data for your application
- How to identify and optimize slow queries
- How to connect your SQL queries to Node.js

It does bear talking about that we are _not_ going to be talking about managing and operating database e.g. how to deploy it, how to containerize it, how to replicate and shard data, etc. This is a developer intro and solely focused on the developer's perspective.

## Who is this course for / Prerequisites

You, hopefully!

The course is designed for developers who want to know better how to use SQL and PostgreSQL. Here's what I'd suggest you know before starting this course:

- [Internet Fundamentals][internet-fundamentals] – This is essential. You should have the fundamentals of using a computer and the Internet down.
- [Basic coding abilities, preferably JavaScript][web-dev-v3] – A little coding experience at the very least will help a lot with the logical reasoning in this class.
- [A little Node.js][njs] – Less essential but will help a lot with the exercises. Outside of the two exercises there are no Node.js sections.

## Set up

This course works and has been tested on both macOS and Windows 10/11. It also will work very well on Linux (just follow the macOS instructions). You shouldn't need a particularly powerful computer for any part of this course. 8GB of RAM would more than get you through it and you can likely get away with less.

- You will need to install Docker. Instructions are in the next lesson.
- While you do not have to use [Visual Studio Code][vsc], it is what I will be using and I'll be giving you fun tips for it along the way. I was on the VS Code team so I'm a bit biased!
- People often ask me what my coding set up is so let's go over that really quick!
  - Font: [MonoLisa][monolisa]. Be sure to [enable ligatures][ligatures] in VS Code! If you want ligatures without Dank, check out Microsoft's [Cascadia Code][cascadia].
  - Theme: I actually just like Dark+, the default VS Code theme. Though I do love [Sarah Drasner's Night Owl][night-owl] too.
  - Terminal: I just switched back to using macOS's built in terminal. [iTerm2][iterm] is great too. On Windows I love [Windows Terminal][terminal].
  - VS Code Icons: the [vscode-icons][icons] extension.

## Where to File Issues

I write these courses and take care to not make mistakes. However when teaching many hours of material, mistakes are inevitable, both here in the grammar and in the course with the material. However I (and the wonderful team at Frontend Masters) are constantly correcting the mistakes so that those of you that come later get the best product possible. If you find a mistake we'd love to fix it. The best way to do this is to [open a pull request or file an issue on the GitHub repo][issues]. While I'm always happy to chat and give advice on social media, I can't be tech support for everyone. And if you file it on GitHub, those who come later can Google the same answer you got.

We'll talk about GitHub at the end of this class if that's unfamiliar to you.

## Who am I

My name is Brian Holt and I am a product manager at Stripe. I work on all sorts of developer tools like the Stripe VS Code extension, the Stripe CLI, the Stripe SDKs, and other tools developers use to write code for Stripe. Before that I worked on Azure and VS Code at Microsoft as a PM and before that I was JavaScript (both frontend and Node.js) developer for a decade at companies like LinkedIn, Netflix, Reddit, and some other startups. I've written _a lot_ of code and written a lot of queries.

I learned to code when I was somewhere around 10 years old. My brother used to make me write C++ before he'd let me play video games. This definitely set me up for success when I started to learn to code in college but let me tell you: it is not too late for anyone to code. The skills here are very learnable for anyone with enough gumption to get going. I started writing database queries against MySQL at my internship.

When I'm not working or developing new Frontend Masters courses, you'll find me in Seattle, WA. I love to travel, hang out with my wife and son, get out of breath on my Peloton, play Dota 2 and Overwatch poorly, as well as drink Islay Scotches, local IPAs and fruity coffees.

![Brian teaching](./images/social-share-cover.jpg)

Catch up with me on social media! I'll be honest: I'm not great at responding at DMs. The best way to talk to me is just to tweet at me.

- [Twitter][twitter]
- [LinkedIn][linkedin]
- [GitHub][github]
- [Peloton][pelo] (you have to be a member and signed in for this link to work)

And one last request! [Please star this repo][site]. It helps the course be more discoverable and with my fragile ego.

[twitter]: https://twitter.com/holtbt
[vsc]: https://code.visualstudio.com/
[monolisa]: https://www.monolisa.dev/
[ligatures]: https://worldofzero.com/posts/enable-font-ligatures-vscode/
[night-owl]: https://marketplace.visualstudio.com/items?itemName=sdras.night-owl
[cascadia]: https://github.com/microsoft/cascadia-code
[terminal]: https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701?activetab=pivot:overviewtab
[icons]: https://marketplace.visualstudio.com/items?itemName=vscode-icons-team.vscode-icons
[iterm]: https://iterm2.com/
[issues]: https://github.com/btholt/complete-intro-to-sql/issues
[github]: https://github.com/btholt
[linkedin]: https://www.linkedin.com/in/btholt/
[gh]: https://btholt.github.io/complete-intro-to-sql
[site]: https://github.com/btholt/complete-intro-to-sql
[tweet]: https://twitter.com/holtbt/status/493852312604254208
[pelo]: https://members.onepeloton.com/members/btholt/overview
[internet-fundamentals]: https://internetfundamentals.com/
[fem]: https://www.frontendmasters.com
[twitter]: https://twitter.com/holtbt
[fem]: https://www.frontendmasters.com
[web-dev-v3]: https://frontendmasters.com/courses/web-development-v3/
[njs]: https://frontendmasters.com/courses/node-js-v2/
