---
title: Streaming with GawkBox
sub_title: Mesh helped GawkBox build a suite of internal tools.
tags: ["Go", "Vue.js", "Web"]
date: 2018-06-28T15:35:36-08:00
publishdate: 2018-06-28T15:35:36-08:00
logo: /images/caseStudies/gawkbox/gawkbox-vector.svg
logo_max_height: 30
hero_image: /images/caseStudies/gawkbox/gawkbox-hero.png
---   

GawkBox is building an app that allows fans to acquire subscriptions on Twitch and Mixer for free. Subscriptions on Twitch and Mixer are one of the key points of engagement between creators and their communities, enabling fans to interact more closely with their favourite creators. Instead of paying between $5 and $25 per month for subscriptions, GawkBox allows users to pay for them by downloading and playing free games.

The company was founded by Chris Brownridge, Tony Chong, and Andrew Allison in the summer of 2016. GawkBox has raised north of $4mm from top-tier venture investors, including  Madrona Venture Group. 

## The Work

The GawkBox team approached Mesh Studio in January of 2018 to help them build a suite of internal tools. By partnering with Mesh, the GawkBox leadership team was able to keep their engineers focused on developing their core product, while Mesh engineers focused on building tools that would help power the business.   

### StreamFinder

StreamFinder is a tool that helps the GawkBox community team identify new creators for the GawkBox platform. It offers an intuitive query interface that allows community members to search for creators based on a set of criteria. 

For instance, a community member could use the tool to “Find all creators that have over 10,000 followers and have streamed in the past 30 days”. Once the creators have been identified, they can be injected into Salesforce as Leads via a direct StreamFinder integration with the Salesforce API. 

While the front-end interface for StreamFinder is simple and intuitive, the back-end logic needed to synchronize data from the streaming platform APIs, is sophisticated. The StreamFinder service is essentially a giant synchronization pipeline. It is continuously pulling streaming information from the Twitch, Mixer, and YouTube APIs, and saving that information as time series data. 

We leveraged the Vue.js framework to build the front-end application for StreamFinder, and a combination of Go, Postgres, and Redis for the back-end service. The service is hosted as a docker container via Kubernetes on AWS.

### Cecil

Cecil is a purpose-built tool that helps inject information from GawkBox’s internal databases, into Salesforce. Cecil has two primary functions:

Enrichment - The enrichment process updates all Salesforce opportunities with data from GawkBox platform on a daily basis.
Conversion - The conversion process looks at the activity each opportunity record may have taken within the GawkBox platform over the past 30 days. It then will convert opportunities between stages based on that activity. 

## Results

When the GawkBox team initially engaged Mesh, they only planned to have us work on StreamFinder. We were able to deliver StreamFinder ahead of schedule, however, which left time for us to tackle Cecil. 

Both StreamFinder and Cecil are live and in production today.  The community team is using StreamFinder on a weekly basis to identify creators that they would like to target for GawkBox. They are then able to pipe those leads into Salesforce where Cecil can enrich and convert opportunities as needed.

We had a fantastic time working with Tony, Chris and the rest of the GawkBox team. The passion for their problem space is undeniable, and we look forward to watching their continued growth and success over the coming years. 
