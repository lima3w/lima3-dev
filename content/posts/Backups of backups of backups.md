+++
draft = false
date = 2025-02-08T06:39:00Z
title = 'Backups of backups of backups'
description = "A 2025 homelab backup design using Restic for local backups and Duplicacy for encrypted offsite copies."
authors = ["Zack"]
categories = ["Homelab"]
tags = ["backups", "restic", "duplicacy", "automation", "self-hosting"]
disableComments = true
+++

*This post describes my 2025 setup and is a snapshot of that design, not a current deployment guide.*

They (the all-knowing vendors who want to sell you more hardware for storage, software for backups, etc.) always say you should follow the 3-2-1 rule for backups. I've never been good at this. So I set out on a mission. I wanted a backup system that was easy to deploy to my new servers and worked without a bunch of manual intervention.

## Local backups

I built a simple web server that housed two scripts: `install` and `run-backups`. The install script installed the required components like `wget`, Restic, and autofs. It configured a mount point on my Unraid server in a backups directory with the hostname as the final folder. It created a Healthchecks-style check on my self-hosted instance, pulled down the backup script, and created a cron job to run Restic nightly. If the job failed, it pinged my ntfy server. If it did not run, Healthchecks pinged me after 24 hours.

This all worked well. I could install the whole system with a simple command:

```
curl http://backups.lab/install.sh | bash
```

That was a shortcut for my own lab. I would not recommend piping an unreviewed script into `bash`; if I were setting this up again, I would download the script and inspect it first.

Once it ran, it was mostly done. I normally kicked off the first backup manually. It backed up `/root`, `/data`, `/opt`, and `/home`, which is where most of my installs lived.

## Offsite copies

Now on to the 1 of 3-2-1: offsite. I installed a Duplicacy container that connected to my cloud storage. I had 2 TB available, so space was not an issue. It took my entire Unraid backups share and uploaded it, encrypted, to cloud storage. Initial space used was only 25 GB, which surprised me.

I could restore directly from the cloud storage without additional cost. Even if someone got into the storage account, they would still need the Duplicacy encryption key phrase to read the backup data.

## What went wrong

One issue was odd: I got a permission-denied error when setting up the cloud provider. The fix was to go into the container, delete the executable, and restart the container. It had not been given the executable permission correctly.
