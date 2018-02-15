---
title: Session Tokens
sub_title: A deep-dive into what session tokens are, and what you need to be aware of when implementing them.
tags: ["innovation", "security"]
date: 2018-01-24
publishdate: 2018-01-24
hero_image: /images/blog/session-token-hero.png
author:
  name: Taylor Halliday
  url: https://twitter.com/tayhalliday
  mail: taylor@meshstudio.io
  avatar: https://avatars2.githubusercontent.com/u/1266416?s=460&v=4
---

### Session Tokens
Authentication and session management are two areas almost all developers have to deal with repeatedly throughout their career. It’s common for us to reach for a well tested open-source library to bolt onto our app, and that’s a good thing since security logic is one of those things you want a lot of eyes on. But as everything in programming, it helps to know the underlying logic behind the tools we use.

Authentication is the process of verifying you are who you say you are. When you enter a username/password to Facebook/bank/etc, you attempt to validate your identity - that process is called authentication. All you’re doing here is (attempting) to prove you are the same person who signed up for XYZ service in the first place. There’s plenty of articles on authentication methods (username/password, OAuth, social logins, etc), so we’re going to look at what happens after you’ve accomplished the authentication step - Session Management.

Session management is the process of keeping track of who you are after you’ve authenticated. For security and practical considerations, it’s generally not a good idea to hand over your authentication credentials every time you wish to access a protected resource. 

By way of example, I had the opportunity to visit the pentagon a few years back. When I arrived, the front desk spent 30+ min validating (authenticating) that I was a US citizen and was scheduled to be on a tour. After that process they gave me a badge (session token), which was continuously checked by security guards as I moved to different places in the building. Same practice holds true for most secure buildings - hospitals, exclusive events, overly concerned corporate offices, etc.

This is also the same song/dance that all websites, apps, and servers follow, but, what makes a good session token and why does it matter? Session tokens need to follow a few principles to be considered secure. Let’s look at the major pieces:

#### Meaningless Content
Session tokens should NEVER contain any identifiable information about the user outside of the token itself. You gain nothing from this practice, and at a minimum expose breadcrumbs for an attack to recreate your token. Never do this.

#### Entropy and ID Length
Entropy is just a fancy way of saying that the token needs to be random…. Like really random. When you get into security, there’s differing levels of [randomness](https://www.owasp.org/index.php/Insecure_Randomness). The point is that session tokens can’t relate to any information about the user and need to be near impossible to guess. On top of randomness, keeping these strings sufficiently long with a large character set will make it robust.

#### Key Obscurity
When we’re storing these tokens in a browser, or header, there’s no point in calling out what it’s there for. Don’t use keys that are obvious, such as “SESSION_TOKEN”, opt for something that doesn’t imply to an attacker that this is where they should be concentrating their efforts. 

#### Ephemerality and Rotation
If you’ve adhered to the points above, then your session tokens are truly meaningless outside of the association you make behind the scenes with the user profile. These tokens should be expired and recreated on a regular basis. Your app may have different needs, but 1-2 weeks is generally a good lifetime for a session token.

### Common Attacks
If you don’t implement sessions properly, you leave your app open to a few common attacks. It’s worth going through some to help reinforce the lessons from above:

#### Man in the Middle [MIM](https://www.owasp.org/index.php/Man-in-the-middle_attack)
A man in the middle attack is when the attacker has found some way to place themselves between your users and your app. The strongest way to prevent this is to require a recent version of TLS to be enforced for all communication, but they still can happen for various reasons. If you have a user who’s fallen victim to this attack, their session token will be leaked. Aggressive rotation of your session tokens will be the best defense outside of patching the MIM vulnerability itself.

#### Cross Site Request Forgery [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) and Cross Site Scripting [XSS](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS))
Like the MIM attack, CSRF allow an attacker to pose as one of your users and make ‘authenticated’ requests. XSS attacks involve running malicious javascript in the browser, which can lead to leaked client-side keys as well. Again, rotation of session tokens will be your best defense outside of patching those attacks themselves.

#### Brute Force
The last thing you want is an attacker to be able to be able to reconstruct one of these tokens, or infer how to alter one in such a way that they gain more privileged access. The recommendations on meaningless content and sufficient entropy guard against this attack.

## It’s not all that scary
Depending on your language / framework of choice, there are lots of libraries that supposedly do session tokens right. But, ultimately it’s up to you as the developer to deliver an application that meets the security needs of the project you’re working on. Knowing the ins and outs of session tokens and the motivations behind their features will better help you assess and implement the best security for your application.

