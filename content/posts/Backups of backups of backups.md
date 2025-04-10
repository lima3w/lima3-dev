+++
draft = true
date = 2025-02-08T06:39:00Z
title: 'Backups of backups of backups'
slug: "Backups-of-backups-of-backups"
authors = ["Zack Lewis"]
comments: false
+++

They (the all knowing vendors who want to sell you more hardware for storage, software for backups, etc) always say you should follow the 3-2-1 rule for backups. I've never been good at this. So I set out on a mission. I wanted a backup system that was easy to deploy to my new servers and worked without a bunch of manual intervention.  I built a simple web server that houses 2 scripts. Install and run-backups. The install script will install the required components like wget, restic, autofs, etc. And it would configure these to create a mount point on my Unraid server in a backups directory with the hostname as the final folder. It would create a healthcheck.io type check on my selfhosted version of healthcheck. It would then pull down the run-backup script and create a cron job to run nightly using restic. If the job fails, it pings my ntfy server. If it doesn't run, healthchecks pings me after 24 hours. This all works well. I can even install the entire system with a simple command:
```
curl http://backups.lab/install.sh | bash
```

Once this runs, it's done. I normally kick off the first one manually. It backs up /root, /data, /opt, and /home. This is where all of my installs take place. 

Now on to the 1 of 3-2-1. Offsite. I installed a docker container of duplicacy that connects to my cloud storage of choice. I have 2TB available, so space isn't an issue. This software takes my entire backups share from unraid and uploads in, encrypted, to cloud storage. Initial space was only 25GB used. I was thoroughly surprised. But it's done. I am able to restore directly from the cloud storage with no additional cost. I'm sure the cloud vendors have redundant systems in place. And even if someone were able to get in, they'd have to set up duplicacy with the right encryption key phrase. So it's secure. 

The one issue was odd in that it showed a permission denied when attempting to set up the cloud provider.  The fix was to go into the docker container and delete the executable, then restart the container. It did not properly set the executable flag in permissions. 
