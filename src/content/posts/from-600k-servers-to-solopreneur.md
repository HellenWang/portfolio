---
title: "Leaving Alibaba Cloud after six years"
description: "Why I'm going independent, and what I'm up to now."
pubDatetime: 2026-11-17T00:00:00Z
modDatetime: 2026-11-26T05:30:00Z
tags: ["career", "entrepreneurship"]
draft: false
---

## First, some context

For six years I was tech lead on the IaaS platform team at Alibaba Cloud. We owned 600K+ physical servers, 5 million-plus VMs and containers, and a hundred-odd internal services. We pulled off more than a thousand on-site upgrades without a major incident.

People sometimes ask what that scale feels like. After a while you stop trusting anything you have to do by hand. With 600,000 machines, a manual step will eventually land on the wrong host. So we automated it. Then we automated the automation, because the first version broke in ways we hadn't expected.

## Things I learned the hard way

**The systems that lasted were dull.** Predictable behavior beat clever design. If an upgrade needed someone standing by to fix it, we treated that as a bug in the automation, not as part of the job.

**A script that works on your laptop is not ready for production.** I spent years on the production side of that gap. Real traffic and real failing hardware turn up cases nobody wrote down in the design doc.

**Most outages I saw came from a change, not from load.** So I cared a lot about rollouts, rollbacks, and keeping every deployment reversible.

## Why I left

Two reasons, mostly.

I wanted to build things I own. At a big company your work serves the company's goals. That's fine, but I wanted to see the line between what I build and who uses it.

AI infrastructure is where I'm spending time now. Companies want to run models on their own hardware, sometimes for data reasons, sometimes to avoid an API bill. It's the same class of problem I worked on at Alibaba, with different workloads.

## What I'm doing now

Two things, at least for now:

- Building websites. Shopify, WordPress, and custom Go backends when a project needs something the platforms can't do.
- Helping teams deploy and run AI models on their own Kubernetes clusters.

I'll write here about the work. When something breaks, I'll write that up too.

Questions or want to talk shop: [wanghanlin12@gmail.com](mailto:wanghanlin12@gmail.com).
