---
title: API Strategy for an Insurance Giant
sub_title: How Mesh Studio Brought a Modern API Strategy To Symetra
tags: ["Containers", "API", "Cloud"]
date: 2018-02-14
publishdate: 2018-02-14
logo: /images/caseStudies/symetra/symetra-vector.png
logo_max_height: 45
hero_image: /images/caseStudies/symetra/symetra-hero.png
---
Founded in 1957 as a subsidiary of [Safeco Insurance](https://www.safeco.com), [Symetra Financial Corporation](https://www.symetra.com/) offers a suite of insurance, benefit, and retirement products to consumers in the United States. Through a nationwide network of financial institutions, broker-dealers, independent agents, advisors, and benefits consultants, the company serves over 2 million customers and has over $42 billion in assets under management.

As of February 2016 Symetra is wholly owned subsidiary of [Sumitomo Life](https://en.wikipedia.org/wiki/Sumitomo_Life) in Japan and is headquartered in [Bellevue, Washington](https://en.wikipedia.org/wiki/Bellevue,_Washington).

## The Problem

Symetra's internal teams have accumulated a large number of repetitive and manual processes as the company has grown over several decades. These processes, while vital to Symetra's operations, offer a significant opportunity for cost savings and efficiency gains if they can be automated.

In the Spring of 2017, a team of Symetra architects set out to develop a platform that would automate these tasks. The platform would integrate with several existing Symetra business systems and be exposed via a RESTful API. This API would allow both internal and external partner applications to leverage the capabilities of their new system.

The Symetra architects recognized that API architecture, design, and deployments were not yet a core internal competency, so they needed to find outside expertise.

### The Solution

Symetra asked Mesh to help develop their Symetra platform API strategy. This strategy would encompass many facets including an API Management Gateway, deployment tooling, and infrastructure. We were also asked to develop a Symetra platform API which would incorporate our recommendations into a functioning prototype.

### Execution

Mesh engineers performed a landscape analysis and evaluated several different on-premise and hosted API management vendor tools. We recommended that Symetra adopt [Kong](https://getkong.org), by [Mashape](https://techcrunch.com/2015/04/28/mashape-open-sources-its-kong-api-management-platform/), because of its rich ecosystem of plugins, first-party Docker support, and its ability to be deployed on-premise with ease.

Our team also recommended that Symetra adopt [Docker](https://www.docker.com/), a containerization technology, to deploy both Kong and their application servers. We worked closely with Symetra architects to ensure that Docker could be deployed on Symetra's Linux VMs, and we then deployed the first Docker container inside of a Symetra data center. This technology stack was completely new in the Symetra enterprise, so the Symetra team relied on Mesh for leadership and troubleshooting.

Our engineers developed and deployed the Platform API as recommended. The service consisted of a Windows Server behind a Kong API with [LDAP authentication](https://en.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol), all deployed inside of Symetra's [DMZ](https://en.wikipedia.org/wiki/DMZ_(computing)). This project was used to demonstrate the viability of the platform to other Symetra internal teams.

### Results

Over the course of several months, we were able to deliver a suite of recommendations for how Symetra should build their API platform. We also successfully deployed a service built with our recommendations into Symetra's network that serves as a prime example for how Symetra can modernize access to the rest of their resources. This success is being used to drive the adoption of these technologies and concepts at Symetra.
