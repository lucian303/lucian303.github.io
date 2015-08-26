---
layout: post
status: publish
published: true
title: "AWS: I'd rather cut my own arm off"
author:
  display_name: lucian303
  email: lucian303@gmail.com
  url: http://lucianux.com/
categories:
- Blog
tags:
- aws
- amazon
- cloud
- paas
- saas
- iaas
- ops
- operations
- devops
- dev
- sysadmin
- systems
- networking
---

Lately, I've been tasked with releasing some applications on AWS. Initially, this was an exciting endeavor that turned mostly sour quickly. AWS is a giant black box. When something fails, you hardly ever know what it is or how to fix it and it provides few tools for diagnostics and repair. Let's look at some of the problems AWS has in detail and why I'd rather cut my own arm off than work with them in the future.

## Documentation

For all intents and purposes, there is none. Actually, there are hundreds of pages of mostly outdated, mostly useless information that will throw you off course. Avoid at all costs. You can never be sure you're reading the latest documentation. There are multiple toolkits, multiple versions of everything, and it's impossible to Google. It's hard to be sure one's even using the right service for the job given the plethora of services and the lack of proper documentation. **For all intents and purposes, there is no documentation.**

## Support

You're going to need a LOT of support since there is no documentation. Support is generally pretty good, though it usually takes hours for them to respond to Urgent requests.  AWS is so buggy, even as an expert, you will run into support issues--that's guaranteed. **We have the business plan, which takes 3 - 10% of all your spending or a minimum of $100 every month, so this is completely unacceptable, especially when you consider that support is necessary for almost every single thing you do on AWS.** I even had to get support for an EC2 instance that was launched with the default Amazon Linux AMI because it didn't boot properly. Everything was default on that instance and AWS had no answer on why it didn't boot properly.

## Bugs

The system itself is full of bugs. Just a few I can list off the top of my head from about a month of working with the system:

* Stock Amazon Linux AMIs fail to build a proper EC2 instance (EC2)
* PHP app "successfully" deployed with no files in the docroot (Beanstalk)
* Newlines removed from source code resulting in syntax errors (Beanstalk)
* PHP Exceptions with no messages (as in null) (Beanstalk)
* AWS pushing the wrong git branch and even after explicitly told to push a branch, still pushing old config. This required about four hours to successfully redeploy the Beanstalk app, mostly just waiting around for time outs to happen. (Beanstalk)
* No way to hide services from the public internet without creating a private subnet with a NAS server and private ELB. You can't just close the ports on your security group if you're using a Beanstalk app. (Beanstalk/VPC/EC2)
* Security groups that don't work as expected (see above about the private subnet) (VPC/EC2)
* 30 minute default timeouts for Beanstalk apps so that if anything in your config is wrong, you can throw another 30 minutes out the window (or read Hacker News, but you run out of content quickly waiting for AWS services) (Beanstalk)
* Some zones, have EC2-Classic turned on by default. Basically, if you launch into this atrocity, you will have to erase everything in that region, ask AWS to kill EC2-Classic, and relaunch everything. EC2-Classic cannot work with their VPC architecture (it could in theory but even their reps are afraid to try it) and it's not readily apparent you have EC2-Classic until you've already wasted a lot of time deploying stuff. (all AWS services)
* Incredibly hard to follow resource IDs that make figuring out what's going on almost impossible (whole AWS service)
* Slow as all hell to do even the simplest of tasks like terminating an instance or environment (whole AWS service)
* Couldn't create folders for tomcat and httpd when deploying a Java 8 server in us-west-1 (Beanstalk)
* Containers couldn't communicate with outside world (ECS)
* Deploying an app and then needing to make it private requires redeployment (Beanstalk)
* Migrating RDS/EC2 instances from one AZ to another easily requires downtime (EC2, RDS)
* All systems are basically black boxes. You have some logs that help sometimes, but many problems do not produce errors or do not produce helpful errors. Much of the UI also spits out useless error messages. (all AWS services)
* A lot of UI glithces like not being able to expand instances without refershing or not refreshing data at all. Also, lots of cryptic, useless errors (all AWS services)

So imagine for every little issue, waiting 1-2 hours (estimated time is always 5-30 mins) just to talk to a rep, waiting 30 minutes for your server to restart only to find out you have to wait another 30 mins again because of a bug only to find out after a few more of these cycles that it's an AWS bug that was causing everything. Hopefully you won't get disconnected cause then you'll have to wait another 1-2 hours for another rep. **AWS support wait times are NOT part of the contract, so they're not actually obliged to answer within any timeframe or at all. Non-urgent requests never get answered.** There's so much waiting, you might have time to write a blog post ... maybe about AWS ...

Now imagine this when you're awake at 3am because something went wrong (most likely on the AWS side if you've set up your stuff right). Yeah, I can't either. And that's why I'm not going to review the actual services themselves because, in my opinion, you'd have to be insane or rich enough to hire someone insane enough to put up with all this. And, at least with the business support plan, you can forget about 24/7 uptime. **Downtime is guaranteed if you need help from AWS support** (except maybe with the enterprise plan, the cost of which is undoutedly insane itself).

## Cost

**AWS is incredibly expensive.** Period. It's way more expensive than just about any other service I've looked at. The whole idea that cloud services are cheap is laughable at AWS. You will spend many times over the amount you'd spend on dedicated hardware if you have any sort of decently sized app. And in that, I'm including time for your engineers to build the hardware from parts. I'd estimate it takes about 10x the time to release on AWS than it does on metal (most of the time spent dealing with AWS bugs and waiting for reps), including building all that hardware from parts and co-locating the hardware. That also includes time for transportation, meals, and even recreation (which you won't have any of if you deploy on AWS.) Not only that, while the costs of hardware are mainly one time (except co-location) AWS will continue to charge you forever.

Expect at least a few thousand a month and many weeks of hair pulling to release a typical Beanstalk app with redundant servers (you still need at least two servers live for each app because bringing up new servers from scratch takes way too long in case of failure). That's a typical app with one db (master/slave), a cache, and a couple of backend servers. And, if your engineers are not experienced in AWS, it will take a very long time. Especially if you have to deploy and redeploy everything because you can't move your app around once deployed, something you can do very easily even with physical servers. If you want to stay the cloud route, there are plenty of cheap cloud providers that can give you servers for as low as $5 to $10 per month and will serve most needs a lot better. They're also much easier to set up, deploy and mange. Look at Linode or Digital Ocean.

## Conclusion

Overall, AWS is a giant black box you pay for dearly. And by pay for, I mean pay for monetarily and with sweat, blood, and tears. I already know it was not the right choice here and we haven't even finished setting up our application. I'm especially glad I won't be footing the bill. I honestly do not think it gets worse than this as far as deployment. It's impossible to have a reliable application set up due to all the AWS bugs, customer support (even at the business level) takes hours so downtime is almost guaranteed, and the prices are astronomic. It seems their target customer is a business with an endless supply of money, engineers, and time. Exactly the things that are generally in short supply when one goes looking for a cloud provider. Unfortunately, the service they provide is atrocious and unacceptable from any company, let alone one as big and resourceful as Amazon.
