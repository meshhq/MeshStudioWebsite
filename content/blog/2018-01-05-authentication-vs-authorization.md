---
title: Authentication vs. Authorization
sub_title: A deep-dive into what session tokens are, and what you need to be aware of when implementing them.
tags: ["security"]
date: 2018-01-05
publishdate: 2018-01-05
hero_image: /images/blog/authorized-hero.png
author:
  name: Taylor Halliday
  url: https://twitter.com/tayhalliday
  mail: taylor@meshstudio.io
  avatar: https://avatars2.githubusercontent.com/u/1266416?s=460&v=4
---

### You allowed in here?
Lately I have noticed quite a few people mixing up the the concepts of Authentication and Authorization. This isn’t a big deal if you’re using them interchangeably when talking about most anything besides tech, but these concepts are quite different and worth explaining.

### So, What is the Difference
Authentication is simply the notion of identity, but it’s a powerful concept. It’s proving to someone (your local TSA agent), or something (the IRS website) that you are who you say you are. 

Authorization actually has nothing to do with the former, it’s just the concept of whether some identity has the ability to access some resource. Just because I know, for a fact, that you are my dentist, that doesn’t mean you should be authorized to access my bank account. Likewise, if my accountant is authorized to access my bank, I need to know that the person writing me an email desiring to move money is who they say they are (authenticated). 

### How this plays out in computers
Most app developers have to interface with these concepts when deciding who should be permitted to use the shiny new application they are building. Commonly we turn to pre-built frameworks like Devise, Passport, or Goth, and that’s a good thing since it would suck to screw up the implementation of these concepts. 

### How does OAuth play into this conversation
Let’s say you and I know and really trust each other. I trust you so much that I take your word on who I should also trust. This is essentially what’s going on with OAuth implementations - we defer the idea of determining identity to another party who we trust. Once identity has been determined (authentication), it’s on us to determine the what the person is authorized to access in our application. 

