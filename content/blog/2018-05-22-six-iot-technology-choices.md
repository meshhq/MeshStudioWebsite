---
title: The 6 IoT Technology Choices
sub_title: Depending on the needs of your IoT project, you'll need to choose wisely.
tags: ["IoT", "Cloud", "Software"]
date: 2018-05-20
publishdate: 2018-05-20
hero_image: /images/blog/2018-05-22-six-iot-technology-choices/hero.png
author:
  name: Taylor Halliday
  url: https://twitter.com/tayhalliday
  mail: taylor@meshstudio.io
  avatar: https://avatars2.githubusercontent.com/u/1266416?s=460&v=4
---

Over the past year, we've worked on several high profiles IoT projects. Throughout this process, we have become acutely aware of the technical challenges that software teams will face when it comes to the shipping production grade connected devices.
 
Regardless of form factor, most IoT products will share a standard set of fundamental technical requirements. In this post, we enumerate several of these requirements and frame them as questions that technical leaders should be asking themselves at the onset of any IoT project.

## Provisioning

_How will my devices be provisioned?_

Provisioning is the process of embedding firmware on a physical device that enables it to communicate with a cloud service. This process can be cumbersome if not executed with a streamlined and secure approach. 

Ideally, the firmware should be able to auto-provision and securely register itself with a cloud-based IoT management solution.  When the device turns on, it should reach out to the cloud IoT hub to generate a unique certificate which will facilitate secure communication going forward.

## Communications

_How will our devices communicate with the cloud?_

Connected devices are defined as being 'connected' based on their ability to communicate with the broader internet. IoT devices typically fall into one of two communications categories: self-powered transmitting (WiFi/Cellular Antenna) or proxying through a companion app.

When determining the communication mechanism, the device's power source will typically be the primary factor. Cellular and WiFi antenna usage are very expensive regarding battery life, whereas Bluetooth Low Energy (BTLE), as the name would imply, is not. The cost of cellular data and the availability of a mobile development team may be deciding factors as well. 

## Over The Air (OTA) Updates

_How do we update our on-device firmware once the devices are in the wild?_

An over-the-air update is the ability to remotely update the firmware that runs on a connected device, once it is deployed in the wild. Evolving business needs demand that modern IoT products support the ability to perform these updates. 

Ideally, the on-device firmware should be able to conduct regular checks for the availability of a new software update. This software should be hosted in a cloud service that is accessible to the devices. The device should also be able to self-update when it finds that new firmware or client code becomes available. 

## Event/Topic Collection

_How will we collect data from our devices?_

Once a fleet of IoT devices is in the field, they are going to start producing massive amounts of machine data. It is critical that operators can capture this data and send it to cloud-based systems for post-processing. After all, you can’t improve what you can’t measure. 

Developing a data pipeline that can handle the vast amount of captured data at high volumes of throughput is a technical challenge for the majority of software teams. IoT development teams should look to leverage vendor solutions from cloud providers such as AWS or GCP to lighten this burden.

## Post-Collection Processing

_How will we perform post-processing on our machine data?_

Post-processing is the step where raw data collected from our IoT devices is transformed into a desired state or data structure. The purpose of this function is to marshall and structure the data into a format that is optimal for performing an analysis, building metrics dashboards, and producing reports. 

The post-processing solution must also be able to handle a very high volume of data throughput. 

## Data Storage

_How will we store our structured and unstructured data?_

Structured and unstructured data that is produced by IoT devices and post processing systems should be stored in databases that suit their intended uses. Structured data should be stored in a database that is optimized for advanced querying and allows for easy post-analytics. 

Raw data coming from the IoT devices should be targeting storage solutions that are tuned for a high-velocity of writes, but do not necessarily optimize for querying. Many in-market NoSQL databases are typically perfect solutions for ingesting unstructured data.

## Conclusion

Building a high quality connected devices that millions of consumers love and use on a daily basis is a monumental product and design challenge. The technology that powers these devices represents a completely different set of technical challenges that are monumental in their own right.

We hope this blog will help you think through some of the major the technical challenges that you will encounter when building your next IoT project. As always, Mesh Studio is here to help with your next project. Feel free to drop us a line!
