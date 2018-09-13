---
title: Lock Down Your AWS Security With 5 Easy Steps
sub_title: AWS security doesn't have to be hard. 
tags: [cloud, aws, infrastructure]
date: 2018-03-29
publishdate: 2018-03-29
hero_image: /images/blog/2018-03-29-locking-down-your-aws-in-5-steps/hero.jpg
author:
  name: Taylor Halliday
  url: https://twitter.com/tayhalliday
  mail: taylor@meshstudio.io
  avatar: https://avatars2.githubusercontent.com/u/1266416?s=460&v=4
---

For the practically paranoid, improving the security of your AWS infrastructure should be an ever-changing goal to strive towards. Large organizations understand this reality. That’s why they employ entire teams that are dedicated to continually reviewing and auditing their cloud security infrastructure.

For us startups, however, we’re focused on trying to run our company; this security stuff is just another thing to think about. That said, there are some quick and easy improvements we implement for all our customers that dramatically improve their hosting security.

In this post, I’ll provide 5 quick and easy steps that any company hosted on AWS should take to improve the security of their infrastructure.

### 1. Properly Configure IAM
[IAM](https://aws.amazon.com/iam/) is a beast with many facets, which is probably why many shy away from getting deep into it. You don’t need to though, adhering to the following will get you 99% of the way there:

#### Enforce MFA
Short of memorizing and rotating long passwords with heavy [entropy](http://whatis.techtarget.com/definition/password-entropy) (randomness), Multi-Factor Authentication (MFA) is simply the best tool people have come up with for assisting systems to verify authentication. Anyone can do this, all you need is a [free](https://itunes.apple.com/us/app/google-authenticator/id388497605?mt=8) [2FA](https://itunes.apple.com/us/app/authy/id494168017?mt=8) [App](https://duo.com/product/trusted-users/two-factor-authentication/duo-mobile), and to enforce 2FA with IAM. Here's a [great, short video](https://www.youtube.com/watch?time_continue=1&v=MWJtuthUs0w) that can walk you through it.

#### Don’t login as root! Use IAM Accounts!
Even though this may seem obvious to some, I have to call it out because we see it in the wild so much. If you haven't done so already, go to IAM and create an IAM user account to login with. You can assign full privileges if you like, but at least restricting logging in via an IAM profile will accomplish the following:

- Allow you to *more* easily deal with canceling those credentials if they get compromised
- Restrict access to certain parts of AWS you don't need to use on a daily basis.

Treat this new account like it is your primary account, and get in the habit of creating new accounts for your employees, contractors, friends, butler, etc. in this manner. IAM allows you to effectively manage access from a bird's-eye view for all of your critical resources. 

>Tip: Never, ever, assign someone's account more permissions than they need. It will make your life easier when you know who explicitly has access to what.

Disclaimer: Policies can be a bit of a pain. Here's a [good link](https://start.jcolemorrison.com/aws-iam-policies-in-a-nutshell/) for some help.

#### Use IAM Roles for Resource Access (EC2)
If you have thing X in AWS that needs to talk to thing Y, do NOT issue them a new set of IAM credentials, and don’t ever use an existing access key / secret combo. Use [IAM roles]()! Assigning a specific IAM role for your resource alleviates the burden that comes with having to handle access keys securely. I promise this makes life easier. Here's an [article](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html) to help you get there.

### 2. Inspect Your Security Groups
Security groups are the best tool for defining access permissions in the cloud. They allow you to restrict access at the port level for every AWS resource through defining allowed ingress (incoming packets) and egress (outgoing packets). Security groups are basically easy to configure firewalls for your entire AWS setup.

Like stale credentials through, security groups can fall victim to neglect. Despite best intentions, developers often leave gaping holes in the security groups due to:

- Misconfiguration / misunderstanding of how they work
- Forgetting they 'temporarily' altered one during the course of a setup or debug session
- Not targeting other security groups for ingress/egress

Luckily there are some cool tools out there to help you identify gaps in your setup. One of our favorite ones is [Scout2](https://github.com/nccgroup/Scout2). It's made by NCC group and does a good job of helping you understand the exposed ingress and egress in your setup.

### 3. AWS-Vault
When you hand out those new IAM Access/Secret Keys we created in step #1, you hope/pray/doubt that they are handled in a secure fashion. Make it easy on the recipients of those credentials and get them set up on [AWS-Vault](https://github.com/99designs/aws-vault) from the folks at [99 Designs](https://99designs.com/). 

AWS-Vault helps by forcing a user to enter credentials only once, but enables you to use them whenever needed. It has great support for 2FA and makes rotation a breeze. It really makes life easier on everyone.

### 4. Turn on Cloud Trail
This is a simple thing to do that will keep a record of all access to your AWS instances. It takes next to no time and will be vital in the event of a breach. A quick start guide can be found [here](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html)

Once enabled, Cloud Watch will record all incoming requests into your account and deliver them within 15 minutes to an S3 bucket. It will also include API calls made by other high-level services (CloudFormation, OpsWorks, Beanstalk) into your resources. 

When you get 'hacked', the first thing your triage team will do is look for logs and pray you have extensive ones set up. Enabling this provides:
- Who made the API call
- When it was made
- What was the API call
- What resources were acted upon
- Where it was made from

### 5. Set up a bastion host
Having all of your public facing instances accept SSH makes it harder to effectively manage access to your resources. Public facing instances also make it more difficult to retroactively understand how some malicious access came through. A bastion host is a funny term that basically means there is one point of access into your firewall/cloud/DMZ/AWS. 

Since every EC2 instance you launch has SSH enabled out of the box, restricting access to one really helps you identify the point of access into your cloud. Your bastion host can just be a normal EC2 instance, but the caveat is that it needs to be the only one that can accept an incoming SSH connection. You can achieve this via security groups (only allowing the bastion host to receive incoming requests on port 22). 

Once you have SSH'd into the bastion host, you can SSH directly into other instances using their private IP addresses. Other than SSH'ing, you may want to inspect a database or [Redis](https://redis.io/) from the command line. Setting up a bastion host just gives you a safer place to do these things rather than exposing multiple EC2 instances, or even worse, exposing services like RDS to the broader internet. 

If you want to go the extra mile, use [public and private subnets](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Subnets.html) to restrict which resources can even be allowed to access the internet.

### Anything else?

#### There are some other cool things you can do to beef up your security picture
- [GitSecrets](https://github.com/awslabs/git-secrets) is a really cool tool for helping you monitor and guard against sensitive passwords being checked into your repos. It's primarily used as a [CI](https://en.wikipedia.org/wiki/Continuous_integration) tool, but is great to even run once on your repo to make sure no one has checked any secrets in to the git history by accident... which you know someone probably has ;)

- [Security Monkey](https://github.com/Netflix/security_monkey) is a really great and interesting project by Netflix. It can attach to your AWS or GCP instance to give you feedback on your current cloud security picture and track it with ongoing monitoring. Another really cool feature they rolled out is a GitHub integration that monitors your organization.

- Export your security settings with [this script](https://www.slideshare.net/AmazonWebServices/intrusion-detection-in-the-cloud-sec402-aws-reinvent-2013). There is a [great talk from AWS re:Invent 2013](https://www.slideshare.net/AmazonWebServices/intrusion-detection-in-the-cloud-sec402-aws-reinvent-2013) on intrusion detection. It covers some of the points in this post, but also some great insight on how to protect against and detect intrusion.
