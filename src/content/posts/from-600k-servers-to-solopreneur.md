---
title: "Leaving Alibaba Cloud after six years"
description: "Why I'm going independent, and what I'm up to now."
pubDatetime: 2026-11-17T00:00:00Z
modDatetime: 2026-11-17T00:00:00Z
tags: ["career", "entrepreneurship"]
draft: false
---

## First, some context

For six years I was tech lead on the IaaS platform team at Alibaba Cloud. We owned 600K+ physical servers, 5 million-plus VMs and containers, and a hundred-odd internal services. We pulled off more than a thousand on-site upgrades without a major incident.

People sometimes ask what that scale feels like. Honestly? It feels like you can never fix anything by hand again. The moment you've got 600,000 machines, every manual step is a ticking time bomb. So you automate it. Then you automate the automation, because the first version breaks in ways you didn't expect.

## Things I learned the hard way

**Boring is good.** The systems that survived were never the clever ones. They were the ones with predictable behavior and no surprises. I got very suspicious of anything that needed a human to babysit it.

**The gap between "works locally" and "works in production" is bigger than most people think.** I spent years on the other side of that gap, dealing with what happens when code meets real traffic and real hardware failures. You can't learn that from a tutorial.

**Most outages come from changes, not load.** Which is why I got obsessed with rollouts, rollbacks, and making every deployment reversible.

## Why I left

Two reasons, mostly.

I wanted to build things I own. At a big company your work serves the company's goals. That's fine, but after a while I wanted to see the direct line between what I build and the person using it.

And AI infrastructure is where the interesting problems are right now. A lot of companies want to run models on their own hardware — for data reasons, cost reasons, or just because they don't want to be locked into someone's API. That's a problem I've spent years working on, just with different workloads.

## What I'm doing now

Two things, at least for now:

- Building websites. Shopify, WordPress, and custom Go backends when a project needs something the platforms can't do.
- Helping teams deploy and run AI models on their own Kubernetes clusters.

I'll use this space to write about the work — things that broke, things that worked, and occasionally a rant about badly designed infrastructure.

Questions or want to talk shop: [wanghanlin12@gmail.com](mailto:wanghanlin12@gmail.com).
