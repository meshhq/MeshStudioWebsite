---
title: Avoiding Retain Cycles in iOS
sub_title: Using the tools in Xcode to debug retain cycles in an ARC project.
tags: []
date: 2017-10-13
publishdate: 2017-10-13
hero_image: /images/blog/2017-10-13-avoiding-retain-cycles-in-iOS/hero.png
author:
  name: Reid Weber
  url: https://medium.com/@reidweber_34407
  mail: reid@meshstudio.io
  avatar: "https://avatars2.githubusercontent.com/u/8824846?s=460&v=4"
---

Retain cycles can be easily overlooked in the post ARC (automatic reference counting) world of iOS. They can quickly balloon out of control and crash your app with very cryptic crash logs if you don’t know how to catch them.

