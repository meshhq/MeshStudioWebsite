---
title: The Complete Guide to Static Hosting with AWS 
sub_title: AWS is Mesh's preferred platform for quickly and securely hosting static websites.
tags: ["AWS", "Cloud", "Web"]
date: 2018-06-29
publishdate: 2018-06-29
hero_image: /images/blog/2018-06-29-static-hosting-with-aws/hero.png
author:
  name: Kevin Coleman
  url: https://twitter.com/kcoleman731
  mail: kevin@meshstudio.io
  avatar: https://pbs.twimg.com/profile_images/911288822217924608/Oq631azq_400x400.jpg
---

At Mesh, we host a large number of static websites for ourselves and our customers. As a result, we’ve experimented with a number of different hosting technologies and platforms over the years. We’ve found that static hosting with [AWS Simple Storage Service (S3)](https://aws.amazon.com/s3/) turns out to be a fantastic option. 

In this guide, I’ll demonstrate how you can deploy a static site on AWS, complete with a custom domain, HTTPS, and lighting fast content delivery. 

Before we get going, I’m going to assume that you have the following:
* [An existing AWS Account](https://aws.amazon.com/free/)
* [The AWS CLI configured on your machine](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) 
* [A custom domain name, preferably registered with Route 53](https://aws.amazon.com/route53/)

## Create an S3 Bucket 

S3 is a highly scalable object storage service, and is one of the fundamental products offered by AWS. For our purposes, S3 is going to store and host the actual content we want to serve for our website. This includes all of our HTML, CSS, JavaScript, and asset files. 

To create our bucket, we’ll need to head over to the [S3 dashboard](https://s3.console.aws.amazon.com/s3/home?region=us-east-1) and click the blue `Create Bucket` button. 

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/1.png" 
caption="Creating an S3 bucket." >}}

The name of our bucket is going to be very important. It must match the custom domain name that we are eventually going to use for our website. 

For the purposes of this tutorial, I am going to use the domain `static.meshstudio.io`. Our S3 bucket must match our custom domain name, except that we will need to replace the periods with dashes. So I will need to name our S3 bucket `static-meshstudio-io`. Also, make sure to select a region for your bucket.

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/2.png" 
caption="Naming our bucket and selecting a region." >}}

 That's all the configuration needed to create our bucket, so we can click “Next” through the next few screens and finally click “Create”.

## Upload Content

Now that we have our bucket configured, lets upload some website content. For this tutorial, I am going to use a dead simple website from [the following repo](https://github.com/meshhq/static-sample): 

Clone this repository to your local machine and CD into the directory. 

```
git clone git@github.com:meshhq/static-sample.git && cd static sample
```

This repository contains a Makefile that will help us automate our deploys to AWS. Open up the Makefile and replace `<YOUR-BUCKET-NAME>` with your actual bucket name. 

Save the Makefile and then run `make deploy`. This will deploy your website content to your S3 bucket. 

## Configure Bucket Hosting

After we have created our bucket and uploaded some content, we need to configure it for static hosting. This tells AWS that we want to use this bucket to host a website. 

To do so, we must click on our newly created bucket, which will bring us to the Bucket Overview screen. Click on the “Properties” tab, and then the “Static website hosting” box. 

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/3.png" 
caption="Drivers Club web event list." >}}

This will display options for configuring our bucket to host a website. Select the “Use this bucket to host a website” option, and then enter `index.html` in the “Index Document” box. This tells AWS that we want it to serve our `index.html` document whenever someone visits our website. 

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/4.png" 
caption="Drivers Club web event list." >}}

Copy the “Endpoint” URL and paste it somewhere safe;we will use this URL later in the tutorial. 

Lastly, click `Save` to apply your changes.

## Bucket Permissions

In order to serve your website to your users, your bucket will need to be publicly accessible to the Internet. We make this happen by applying a new IAM policy to your bucket. 

Click on the “Permissions” tab and then the “Bucket Policy” button. Copy and paste the following text into the “Bucket Policy” box. Note: make sure to update the “Resource” value to match your bucket name.

{{< highlight json >}}
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "<RESOURCE_ARN>"
        }
    ]
}
{{< /highlight >}}

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/5.png" 
caption="Drivers Club web event list." >}}

Lastly, click save to apply your changes. Your bucket is now configured for static hosting!

## Test it out 

Copy and paste the endpoint URL, that we saved in the previous step, into your browser. You should see the static website content in the browser!

## Setting up SSL

Modern websites should always be served over HTTPS. In order to do this with AWS, we are going to need to request a new SSL certificate via [AWS Certificate Manager](https://aws.amazon.com/certificate-manager/). 

In order to request our certificate, navigate to the [Certificate Manager dashboard](https://console.aws.amazon.com/acm/home?region=us-east-1#/) and click on the “Request Certificate” button. Select “Request a Public Certificate” and click the “Request a certificate” button in the bottom right corner. 

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/6.png" 
caption="Drivers Club web event list." >}}

Enter `*.your.domain` in the “Domains” box. This will ask AWS to request a certificate that will work with all subdomains associated with your domain. For our domain, we will enter `*.meshstudio.io` and click the “Next” button in the bottom right corner. 

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/7.png" 
caption="Drivers Club web event list." >}}

Certificate manager allows us to validate our domain ownership via DNS records. To do this, we will need to add a CNAME record to our domain’s DNS. The record values will be supplied in a subsequent step. 

Click the “DNS Validation” option and then the “Review” button in the bottom right corner. 

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/8.png" 
caption="Drivers Club web event list." >}}
 
Review your information to be sure it is correct and then click “Confirm and Request” in the bottom right corner. 

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/9.png" 
caption="Drivers Club web event list." >}}

The next screen will provide the information we need in order to validate our domain ownership via DNS. Click the dropdown arrow next to your domain to view the DNS information.  

If you are using Route 53 to manage your domain, you will see a button that says “Create Record in Route 53”. Click this button and the correct DNS record will be automatically added to Route 53. 

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/10.png" 
caption="Drivers Club web event list." >}}

If you are not using Route 53 to manage your domain, add the supplied CNAME record to your DNS in your provider’s dashboard. 

Wait for a few minutes to allow the DNS record to propagate. Once your domain is validated, the Certificate Manager dashboard will show your certificate state as ISSUED.

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/11.png" 
caption="Drivers Club web event list." >}}

## Setting up your CDN with CloudFront

Content Delivery Networks (CDNs) help speed up your website’s load time and deliver your content in a secure manner. AWS has a CDN product called [CloudFront](https://aws.amazon.com/cloudfront/). 

In order to deliver our website via a CloudFront, we need to create what is called a CloudFront Distribution. Navigate to the [CloudFront dashboard](https://console.aws.amazon.com/cloudfront/home?region=us-east-1) in AWS and click “Create Distribution”. 

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/12.png" 
caption="Create a CloudFront distribution." >}}

Under the “Web” section, click “Get Started”.

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/13.png" 
caption="Drivers Club web event list." >}}

The “Create Distribution” page allows us to configure our distribution’s settings. In the “Origin Settings” section, we will paste the bucket URL that we copied earlier in the tutorial into the “Origin Domain Name” box. We will leave off the `https://` however.  

Press the “Tab” key and the `Origin ID` box will autofill, which is what we want. 

Scroll down to the “Default Cache Behavior Settings” section and click the “Redirect HTTP to HTTPS” radio button. This tells CloudFront to force all HTTP requests to use HTTPS. 

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/14.png" 
caption="Drivers Club web event list." >}}

Lastly, scroll down to the “Distribution Settings” section. In the “Alternate Domain Names” box, add your custom domain. In our case, this is going to be `static.meshstudio.io`. 

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/15.png" 
caption="Drivers Club web event list." >}}

That's it! Scroll down and click “Create Distribution” in the bottom right hand corner. 

## Point your domain at CloudFront

The last step is to add a DNS record that tells the internet to direct traffic for our domain to our CloudFront distribution. We do this by copying the Domain Name for our new CloudFront distribution. 

We then need to a new DNS record which tells our domain to route traffic to our CloudFront distribution. If you are using [Route 53](https://aws.amazon.com/route53/), click on your domain’s hosted zone and click “Create Record Set”

Select an “A” record, and click the radio box next to “Alias”. Paste the CloudFront distribution URL into the “Alias Target” box. Lastly, click “Save Record Set”

{{< figure 
src="/images/blog/2018-06-29-static-hosting-with-aws/17.png" 
caption="Drivers Club web event list." >}}

If you are not using Route 53, add an alias record through your DNS provider’s tools. 

That's it! Give your DNS service a few minutes to propagate. Eventually, you will be able to visit your custom domain and view the sample Mesh static website!

### Next Steps	

At this point, your static website is completely configured with a custom domain, SSL, and a CDN for lightning fast content delivery. 

You are free to swap out the sample static website, for any website that your heart desires. All your need to do is swap out the index.html in your S3 bucket for your new website’s index.html, and be sure to upload any other files needed to serve your site. 





