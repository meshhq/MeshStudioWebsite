---
title: 5 Practical Steps to Shipping Better Software, Faster
sub_title: All software development teams are unique. They are made up of developers, project managers, quality assurance testers, and designers of vary skill sets and seniority levels. 
tags: [software, speed]
date: 2018-03-22
publishdate: 2018-03-22
hero_image: /images/blog/2018-03-22-five-practical-tips-for-shipping-software-faster/hero.png
author:
  name: Kevin Coleman
  url: https://twitter.com/KevFColeman
  mail: kevin@meshstudio.io
  avatar: "https://pbs.twimg.com/profile_images/911288822217924608/Oq631azq_400x400.jpg"
---

# 5 Practical Steps to Shipping Better Software, Faster

All software development teams are unique. They are made up of developers, project managers, quality assurance testers, and designers of vary skill sets and seniority levels. 

Despite the differences in their makeup, all of these teams share a common objective: *to ship high software quality products, fast*. 

In reality, the overwhelming majority of software development teams are not able to perform at this high of a level. Under performance in development teams  typically is not due to a lack of skill nor a lack of desire, but rather, due to flaws in their software development process. 

In this post, I'll discuss five simple steps that any software development team can take to improve their development process. In turn, this will help you ship better software products, faster. 

# Write Tests

If your team doesn't write automated tests, you should start doing so immediately. Full stop. This is the single most important thing your team can do to start shipping better software. 

Since tests take time to write, the prevailing line of thought from the non-technical crowd is that tests increase the time it takes to deliver software. In reality, testing speeds up your development process dramatically. 

The advantages of software tests are numerous and have been [well documented](https://www.amazon.com/Test-Driven-Development-Kent-Beck/dp/0321146530). I will enumerate a few here: 

* Tests help developers ensure that the code they write is behaving as intended now, and in the future. 
* Tests help developers spot problems with future code that is written (in the form of broken tests). 
* Tests are an amazing tool for debugging. 

Tests do need to be written by a human (in most cases) so creating them does add a bit of upfront time investment when creating features. The coverage pays immense dividends over the lifetime of a software project, however, and will ultimately increase the velocity of a software team.

# Setup Continuous Integration

Once you start writing tests, it is time to instrument a Continuous Integration (CI) pipeline. CI is the process of building and testing a code base, in an automated **continuous** fashion. See my previous blog post on how to [configure a pipeline](https://medium.com/meshstudio/continuous-integration-with-circleci-and-nodejs-44c3cf0074a0). 

Once you have your CI pipeline setup, it is trivial to integrate the build process with GitHub. This allows you to see build status from directly within GitHub. It also allows you to protect specific branches from merges, unless the CI process passes. 

CI pipelines can automatically notify all members of the team if any broken code is introduced. Think about that for a second - CI is basically like an incredibly thorough second pair of eyes looking at every new line of code and monitoring for broken code and failing tests. What’s more, his process can be set up for free! Why would you not use this tool?

Without a continuous integration pipeline running in an automated fashion, and protecting branches on your favorite source code hosting solution, faulty code may get merged. When bugs, broken tests, or faulty builds get merged to master branches, it can cause significant delays for development teams. Conversely, when broken code is prevented from being merged, and fixed, it leads to massive time savings. 

# Perform Code Reviews 

Code reviews are like proofreading for code. Just as a journalist wouldn't publish an article without an editor reviewing the article, software developers shouldn't be able to merge code unless their pull request has been thoroughly reviewed by another developer. 

A code review is an opportunity to catch styling mistakes, bugs, logic errors, and even spelling errors. The simple act of having another pair of eyes on your work is often enough to identify programmer error and mistakes. 

Code reviews are fairly quick to perform, and reviewers can often spot errors that would have taken other developers significant amount of time to debug had those errors been merged into the codebase. 

# Automate all the things

Automation is a developers best friend. We can write little scripts that get computers to do work for us. This is literally like black magic to the rest of the non-technical world. 

All of us should be taking advantage of this super power we have to the fullest extent possible. If there is anything that you do on a day to day basis, you should try an automate it. 

Tests and CI are examples of automating tasks. But there are ample other opportunities for automation. 

Do you manually deploy code? Automate it. Do you perform a set of QA steps before software releases? Automate it. Do you pull data frequently for various reports? Automate it. 


Automation will lead to huge time saves, and you will probably learn a thing or two along the way.

# Write Badass READMEs

Go look at the most popular open source projects on GitHub and you will notice that they all have one thing in common. Fantastic READMEs. 
https://github.com/hashicorp/terraform


If the best open-source software developers in the world agree that a great README is mandatory for a software project, shouldn't you be writing READMEs for your project as well?

One of the important blog posts that I read early on in my development career was [You are what you documents](https://www.ybrikman.com/writing/2014/05/05/you-are-what-you-document/) by Jim Brikman. I suggest you go and read it immediately, and then go look at your own READMEs. You probably have some work to do. 

A good README should do several things well. But perhaps most importantly, it should provide crystal-clear instructions that any developer can follow to get up, running, and productive with the software project quickly. 

This is especially critical in software development teams where developers may be working across multiple different repositories. If one developer needs to bother another developer in order to get a project to build, that is a fail and is a waste of the team’s time. 

Examples of great READMEs. 

# Conclusion 

Writing software is challenging. Writing really good software, and doing so fast is incredibly difficult.

There are many other approaches that teams can take to develop better software, but the above tips are low-hanging fruit that every software development team should absolutely be using.


