+++
draft = false
date = 2026-10-18T22:37:48-05:00
title = "The problem was not Postiz"
description = "Trying a self-hosted social-media scheduler turned into a lesson about platform permissions, app review, and where the real limitation lives."
authors = ["Zack"]
categories = ["Homelab"]
tags = ["self-hosting", "social-media", "postiz", "automation"]
disableComments = true
+++

I wanted one place to write a post, schedule it, and send it to the social sites I actually use. That seemed like a reasonable thing to self-host, so I gave [Postiz](https://postiz.com/) a try.

The application itself was not really the problem. I could get it running and start working through the setup. The part that turned into a rabbit hole was connecting the services.

## The wall was on the other side of the integration

A self-hosted scheduler still has to use each platform's API. That means it is not enough to have an app running in my lab. Every service gets to decide who can connect, what permissions an app can request, whether an app needs review, and whether the kind of account I have can use the feature at all.

So the work became less about Postiz and more about developer dashboards, callback URLs, scopes, approval status, and access rules. Some of that is normal security work. A platform should not let a random application publish as somebody else without a real authorization flow.

But it does make the experience feel backward. I was not trying to build a giant social-media service or manage a hundred accounts. I wanted a small posting queue for my own projects. The scheduler was ready before the services were willing to cooperate.

## Self-hosting does not remove the platform gate

That was the part I had wrong going in. Self-hosting removes the cost and trust problem of handing a third party my posting schedule. It gives me control over the software and the data. It does not remove the platform's control over publishing access.

If anything, it makes that boundary easier to see. When a hosted service works, it is easy to assume the integration is just a button somebody built. Underneath, that service has already dealt with the same approvals and restrictions, then made them invisible for its customers.

With Postiz, I was seeing the actual shape of the problem: the application can support an integration, but support in the application does not guarantee that a particular platform will grant the access needed for a particular account.

## Still worth trying

I do not think that makes Postiz a bad project. It did what I expected on its side. The limits I ran into were services deciding how their publishing APIs can be used.

For now, I am treating this as a reminder to separate two questions before I spend a weekend setting something up:

1. Can the software do what I want?
2. Will the services I want to use actually allow it?

Those sound like the same question until they are not. In this case, the first answer was mostly yes. The second one is where the project slowed down.

That is where it sits right now: Postiz was a useful test, not a failed install. I have a better idea of what I need to verify before I pick the next tool or decide whether a simpler hosted option is worth the tradeoff.
