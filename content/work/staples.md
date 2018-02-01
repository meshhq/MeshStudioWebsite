---
title: Making Staples Easy Again
sub_title: Staples asked Mesh to help them deliver the connected Easy Button.
tags: ["innovation", "security"]
date: 2017-03-23
publishdate: 2017-03-23
logo: /images/caseStudies/staples-vector.svg
logo_max_height: 30
hero_image: /images/caseStudies/staples-hero.png
---
Introduction 

[Staples](https://www.staples.com/) is known as the office supply superstore, with retail locations throughout North America. Founded in 1986, Staples has grown from its humble beginnings to to over 1,500 stores. The company is publicly listed and included in the [Fortune 500](http://fortune.com/fortune500/staples/). 

Despite being known for their extensive network of brick and mortar retail locations, [over 60% of Staple’s annual revenue](https://www.digitalcommerce360.com/2017/05/16/b2b-sales-account-60-staples-q1-revenue/) comes from its direct business-to-business (B2B) relationships. 

The Problem

[In the summer of 2016](http://www.businessinsider.com/amazon-business-top-priority-court-documents-2016-9), these direct customer relationships were being threatened by digital native retailers, the biggest being Amazon. As a result, a product team at the [Staples Innovation lab](http://www.staplesinnovation.com/) in Seattle was looking for ways to fend off competitors. How could they deepen existing relationships with those B2B customers and make it easier for them to order products from Staples? 

The Solution

The solution was to breathe life into the [Easy Button](https://www.staples.com/Staples-Easy-Button/product_606396), an famous piece of brand marketing from the early 2000s. The Staples innovation team wanted to revive the Easy Button by turning it into an intelligent personal assistant similar to the [Google Home](https://store.google.com/product/google_home) or [Amazon Alexa](https://www.amazon.com/Amazon-Echo-And-Alexa-Devices). 

To do so, Staples needed to build an artificially-intelligent conversational commerce platform that would power the Easy Button. This platform, called the ‘[Easy System](http://www.staplesinnovation.com/innovations/staples-easy-system/)’, would allow Staples’ customers to interact with its business and product systems via voice and chat interfaces, built on natural language processing platforms. 

Execution

The Mesh team was asked to design, architect, and implement all of the software for the Easy System. We collaborated with the amazing hardware team at [Mindtribe](https://mindtribe.com/) to design and implement a custom streaming audio protocol. This protocol would be necessary in order to allow customers to converse with the Easy Button in real-time. 
   
Over a period of eight months, we successfully executed on all facets of the Easy System vision. Mesh engineers were forced to tackle a myriad of technical challenges in order to bring this system to life. Some of the project highlights include:

Easy System Server 
Known as an ‘Orchestration Layer’, this was a Node.js application hosted on AWS [Elastic Container Service](https://aws.amazon.com/ecs/). The server was responsible for coordinating interactions with the Easy System Channels (Easy Button, Mobile Chat, Desktop chat), the Easy System AI engine, and Staples internal business systems. 

Streaming Audio 
The ability to stream bi-directional audio from the Easy Button to the Easy System Server in real-time was one of the most technically challenging tasks the Mesh team encountered. We developed a custom streaming audio protocol that provided for audio transmission with sub-millisecond latency. 

AI Engine 
Mesh leveraged a combination of cognitive APIs and built a custom context tracking framework to power the Easy System AI engine. The combination allowed customers to engage in multi-context, multi-utterance conversations with the Easy System. It also handled error case fall-back gracefully. The cognitive APIs leveraged include [IBM Watson](https://www.ibm.com/watson/), and [Google Cloud Natural Language](https://cloud.google.com/natural-language/).

Easy Analytics 
Analytics are absolutely critical when building out an artificial intelligence agent. In 2016, a quality AI analytics offering did not exist in the market, so we were forced to build an analytics tool from scratch. We turned to a combination of serverless technology via [AWS Lambda](https://aws.amazon.com/lambda/) and [Amazon RDS](https://aws.amazon.com/rds/) to build our tracking engine. The dashboards were built using [Metabase](https://www.metabase.com/), and open-source visualization tool. 

Companion iOS Application 
Similar to Amazon Alexa and Google Home, the Easy Button needed a companion application. The Mesh team built a custom iOS application that was fully integrated with the Easy System and Staples’ business systems. Customers were able to converse with the Easy System through in-app chat. They could also view a history of their voice utterances with intelligent chat cards. 

Results 

The Mesh team brought the Easy System from an exciting and visionary concept, to a full-fledged enterprise-grade system that has been deployed to hundreds of thousands of Staples customers. This was undoubtedly the most technically challenging project that Mesh has undertaken in our short history, but the results speak for themselves. 

What started out as a skunkworks innovation project is now one of the most important technology initiatives in the Staples engineering organization. The project has garnered organizational support all the way up to the CEO and President of the company. The Easy System has also received [widespread media attention](https://www.forbes.com/forbes/welcome/?toURL=https://www.forbes.com/sites/chriscancialosi/2016/12/13/how-staples-is-making-its-easy-button-even-easier-with-a-i) and was the [keynote featured product](https://www.computerworld.com/article/3135090/artificial-intelligence/ibm-looks-into-the-future-of-ai-at-world-of-watson.html) at IBM’s 2016 [World of Watson](https://www.ibm.com/events/think/) conference in Las Vegas. 



