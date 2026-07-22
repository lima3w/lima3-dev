+++
draft = false
date = 2026-07-21T00:00:00-05:00
title = 'Getting a little more organized'
authors = ["Zack"]
categories = ["Homelab"]
tags = ["dns", "dhcp", "high-availability", "automation", "rudder"]
disableComments = true
+++

I have been doing a lot of cleanup and infrastructure work in the lab recently. Some of it was planned. A lot of it was just me finding one thing that needed fixed, then finding three more things connected to it. That is usually how it goes.

DNS was the big one. I have been running Technitium as DNS and DHCP for the network, but it was all on one system. DNS is one of those services that nobody thinks about until it stops working, then nothing else seems to work either. I set up a second Technitium instance on a separate VM and started working on clustering them together.

It was not as simple as clicking the cluster button. The first attempts failed because I had old records and zones using names that the cluster wanted to use. I ended up making a clean test cluster on the second node just to prove the software worked on a fresh install. After cleaning up the old DNS setup, I was able to create the real cluster with `ns1.dns.lab` and `ns2.dns.lab`.

The cluster itself was only part of it. Existing zones do not automatically become shared just because the DNS servers are clustered. I had to add the normal forward and reverse zones to the cluster catalog. Now the `lab`, `svc`, and reverse DNS zones are replicated between both systems. DHCP clients are also being given both DNS server addresses. DHCP is still active on one server for now. I do not want two DHCP servers trying to hand out leases without proper failover support.

I also started making the VM build process less random. I created fresh AlmaLinux and Rocky Linux test VMs, then added the parts I normally end up doing by hand later. The provisioning process now reserves an address in DHCP, creates the A and PTR records, gives the VM the expected hostname, and checks that reverse DNS points back to the right place. It also installs VMware Tools during the build. Small stuff, but it saves me from fixing the same details every time I create another VM.

On the management side, I tried Uyuni and decided it was more than I needed. I shut it down and moved forward with Rudder instead. Rudder is now watching the application and infrastructure VMs that make sense to manage. I am keeping it in audit mode for now. I want to see what it reports before I start using it to enforce settings everywhere.

None of this is exciting by itself. It is mostly getting rid of single points of failure, old systems, and little manual steps that keep piling up. But that is the part of the homelab I like. I get to try things, break them in a place that does not matter, then slowly make the stuff I actually depend on a little less fragile.
