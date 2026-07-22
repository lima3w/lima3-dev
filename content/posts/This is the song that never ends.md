+++
draft = false
date = 2024-11-30T00:21:00Z
title = 'This is the song that never ends'
description = "The start of a homelab journal and a November 2024 snapshot of the systems running in it."
authors = ["Zack"]
categories = ["Homelab"]
tags = ["homelab", "self-hosting", "unraid", "esxi", "home-assistant"]
disableComments = true
+++

*This is a snapshot of the lab from November 2024, not a current inventory.*

Cuz here we go again, starting a new blog. This is less about the blog itself and more about trying new stuff.

I saw the idea of using Obsidian as a notes editor and publishing it with Hugo via GitHub for semi-automated posts. We will see how that goes.

I tend to start projects and never finish them. Maybe this time I will use this as a journal for my homelab ideas instead of trying to make it a complete inventory.

## What was running then

At the time, the lab was shut down for the week because my in-laws were in town. My network closet heats up quickly with the door closed, but it is too loud to keep open when they are here for a few days.

Back to the lab: I was running two hosts, one with ESXi and the other with Unraid. I had a few Linux VMs, vCenter because I liked the interface better, and Home Assistant as an appliance VM. I also had Ubiquiti, UniFi, and Zabbix.

On the Unraid side, I had a bunch of Docker containers: health checks, an SMTP relay, Syncthing, Uptime Kuma, and a few others.

I also had a few physical "servers." One old laptop ran Technitium DNS and provided ad-blocking and malware-blocking DNS to the whole network. Another old laptop ran Plex and used its mobile NVIDIA graphics card for transcoding.

## Why I keep doing this

My goals for the homelab are not really defined. I like to tinker with interesting projects. Previously, it was a place outside production to test things for work, but my current employer has a dedicated test lab for that. Now I do provide a few services to the house, like DNS with ad blocking, but I am usually the only one using most of them.

That is fine. The point is not to build a perfect setup. It is to have a place to try things and write down what I learn before I forget it.
