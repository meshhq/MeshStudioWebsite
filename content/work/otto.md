---
title: Home Security, Cloud Delivered
sub_title: Mesh helped Otto modernize its cloud infrastructure and security.
tags: ["AWS", "Infrastructure", "Security"]
date: 2017-04-23
publishdate: 2017-04-23
logo: /images/caseStudies/otto-vector.svg
logo_max_height: 30
hero_image: /images/caseStudies/otto-hero.png
---

Founded in 2014 by serial entrepreneur [Sam Jadallah](https://www.linkedin.com/in/samjadallah/), [Otto](https://meetotto.com/) manufactures the future of ‘smart locks.’ Sam saw an opportunity to redefine home access and build a category-defining product. He felt that “a digital locking system was necessary to replace the antiquated and shockingly insecure physical key system of today.” 

To power this smart lock, Otto needed to build multiple sophisticated cloud services. These services would be responsible for coordinating interactions between the locks, users, and their smartphones. 

## The Problem

As Otto marched toward the release of their flagship product in the summer of 2017, they struggled with reliability and scalability issues, which in turn hampered their feature development efforts. Four years of development and multiple technology team iterations later, the Otto cloud services were in a sad state. Bugs were rampant, test coverage was non-existent, hosting was fragile, and APIs did not perform as expected. Simply put, Otto’s services were far from being production-ready. 

## The Solution

Otto’s engineering leadership realized they needed to find outside cloud expertise before they could ship their flagship product. This would help ensure that Otto's cloud services would be performant and reliable once their smart locks hit the market. 

Otto asked Mesh to step in and lead the effort to get their cloud applications to a production-ready state. We were also asked to execute a series of other projects across server, web, and mobile development platforms. These projects included a complete cloud infrastructure migration, a security audit with [NCC](https://www.nccgroup.trust/us/), several internal dashboards, and training of Otto’s core cloud engineering team. 

## Execution

### Otto Cloud Services 

We refactored the majority of Otto's main server application, the Account Service. When we took over, the application had a concerning number of logic flaws, poor documentation, and zero test coverage. It was held together by the technical equivalent of duct tape. 

Mesh engineers transformed the Accounts Service into a modern and robust TypeScript application, with a comprehensive and automated test suite. We instrumented logging throughout the codebase to give the engineering team visibility into behavior and performance. Lastly, we Dockerized the application so that it could be deployed consistently and reliably. 

We also recommended and implemented multiple security measures including a complete overhaul of Otto’s session management system. Lastly, we worked closely with engineers from NCC on a full independent third-party security audit, and implemented all of their recommended changes.

### Infrastructure Migration 

While working through the aforementioned difficulties with Otto’s cloud services, it became apparent that the existing infrastructure lacked organization and cohesiveness. An enterprise-grade hosting solution was needed, and Mesh was there to assist with a complete infrastructure migration from [Joyent](https://www.joyent.com/) to AWS.

With security as our top concern, we configured an entirely new cloud network for Otto applications using several AWS networking services. By utilizing [Amazon VPCs](https://aws.amazon.com/vpc/), we were able to deploy separate environments for test, staging, and production. We instrumented automated zero-downtime deployments and delivered comprehensive logging infrastructure for monitoring critical resources. Otto applications are now deployed using [Elastic Container Service](https://aws.amazon.com/ecs/) with [Auto Scaling](https://aws.amazon.com/autoscaling/). 

We created this new infrastructure using [Terraform](https://www.terraform.io/), a framework that allows developers to express infrastructure via a series of configuration files. All of Otto's infrastructure is now well-documented, reproducible, and version controlled. Changes to this infrastructure can also be tested and applied safely and securely. 

### Continuous Integration Pipelines

We developed continuous integration pipelines for several of Otto’s cloud, mobile, and firmware applications using [CircleCI](https://circleci.com/). Reporting was integrated with Otto’s Slack channels to notify development teams about the status of builds. 

Internal Dashboards
We built several internal dashboard applications for the Otto engineering and customer support teams. These dashboards are written in [TypeScript](https://www.typescriptlang.org/) using [React/Redux](https://reactjs.org/) and provide operational visibility into user and lock data contained in Otto's IoT platform. 

## Results

Mesh transformed Otto’s cloud services into enterprise-grade, production-ready applications. We provided the Otto team with robust and reliable infrastructure on which to host their applications and services. Lastly, we helped the mobile and firmware teams build more reliable applications through continuous integration pipelines. 

After four years of development, Otto was able to ship their locks to initial beta customers and felt confident enough in their services to launch this past fall. 
