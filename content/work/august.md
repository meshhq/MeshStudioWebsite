---
title: August Mobile SDKs
sub_title: August had perfected access to their hardware, but it was time to open the doors to other developers.
tags: ["Mobile", "SDK", "Integrations"]
date: 2018-02-01T12:21:35-08:00
publishdate: 2018-02-01T12:21:35-08:00
logo: /images/caseStudies/august-vector.svg
logo_max_height: 45
hero_image: /images/caseStudies/august-hero.png
---

[August Home](http://august.com/) is the brainchild of famed industrial designer [Yves Behar](https://twitter.com/yvesbehar) and [Jason Johnson](https://twitter.com/jcjohnson). Founded in 2013, the company designs and manufactures beautiful, connected home security products. August’s product lineup has grown to include connected locks, doorbells, and cameras.

The company had raised over $70MM from-top tier venture capitalists like [Maveron](https://www.maveron.com/) and [Bessemer Venture Partners](https://www.bvp.com/) and was acquired by [Assa Abloy](https://www.assaabloy.com/en/com/) in October of 2017. 

## Problem

August’s connected products are controlled with a smartphone through a mix of Bluetooth and WiFi. To facilitate this functionality, August engineers had to develop sophisticated and proprietary networking technology.

In the spring of 2017, August was approached by a large partner to integrate their core wifi and Bluetooth technology into the partner’s application. This technology, however was deeply embedded within August’s existing mobile application code bases and was not easily transportable. 

If the integration were to happen, large portions of the existing core application logic would need to be extracted and organized into a consumable package.

## Solution

The technology leadership team at August decided to build mobile Software Development Kits (SDKs). SDKs would allow the current partner to quickly integrate August technology into their application.They would also open the door for other potential integration partnerships in the future. Mesh partnered with August and was asked to deliver SDKs for both Android and iOS. 

## Execution

### SDK Build

We worked closely with the mobile engineering team to get up to speed on the technical details of August’s connected devices and networking logic. We then carefully evaluated the existing mobile codebases to determine which units of logic would need to be included in the SDK. After identifying these components, we were able to extract the relevant code into a standalone project and assemble a cohesive package. We also added unit test coverage to ensure the SDKs functioned as expected

### Interface Design 

Interface design is critical to the successful implementation of any developer product. With an emphasis on developer experience and simplicity, we worked to design an interface that was predictable and easy to integrate. After designing and implementing this public interface, we also produced thorough API documentation and an implementation guide.

### Design Implementation 

We implemented redesigns of several portions of August’s application so that the user interface design would match the partner application designs. We also added several new views to the application to match partner specific requirements.

### Legacy Refactor

Significant portions of Augusts codebase needed to be refactored to support the partner requirements. We refactored where necessary and added comprehensive test coverage. 

### Framework Automation

We delivered automation tools that would allow August mobile engineers to build, test and distribute framework binaries. This automation eliminates the potential for human error when building the SDKs and makes it trivial to ship SDK releases.

## Results

Mesh engineers were able to complete the August SDKs for both Android and iOS ahead of schedule. We developed a close working relationship with members of August mobile teams to build high-quality SDKs that are simple to use and easy to implement. August was able to deliver the SDKs to their partner for integration on time and on budget.