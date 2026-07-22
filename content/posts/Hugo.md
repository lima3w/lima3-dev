+++
draft = false
date = 2025-04-10T05:59:03Z
title = 'Hugo'
description = "How a CTF scoreboard rabbit hole led me to rebuild this site with Hugo, Git, and Markdown front matter."
authors = ["Zack"]
categories = ["Website"]
tags = ["hugo", "static-sites", "github-pages", "obsidian"]
disableComments = true
+++

> **Update:** This is the original GitHub Pages version of the site. The repository now also has Cloudflare Worker configuration, so treat the deployment commands here as a record of how I got started, not current deployment instructions.

## Starting the site

Rabbit holes are fun. Mine started like this: an organization I am a part of wanted to put on a tech conference, similar to BSides. They always have a decent CTF event going during the day. Let's build a CTF. The first thing is a scoreboard. I saw some other places use [CTFtime](https://ctftime.org/). Oh look, I already had a profile there, so I could use that. If I was going to build a CTF, my profile should have my website, lima3.dev. Well crap, my site that was not being used or updated was no longer even published. I needed to update it.

So I went back to a YouTube video I saw about building a static site using Hugo and hosting it on GitHub Pages. Following along, I ran into issues. That is why my ADHD brain decided to spend two days playing with Hugo.

## Building the first version

I started not knowing squat about how it worked. My thought was to write stuff in Markdown in Obsidian, either on desktop or mobile. Then I could drop those files into Hugo to display them. Easy enough. But not really.

The first issue was that nothing was working right. As I heard someone else put it, it is a chicken-and-egg problem. The best way to build the site from scratch was to start with a test box. I used Ubuntu just to match the GitHub runner: a basic minimal server, nothing special. I installed Hugo from a `.deb` package. They did not seem to have a repo to do this automatically with apt, so I had to get the release link from the GitHub repo.

```
wget -O hugo.deb https://github.com/gohugoio/hugo/releases/download/v0.145.0/hugo_extended_0.145.0_linux-amd64.deb
```

> I later found out that Hugo can be installed with Snap as well as other options: [Hugo installation](https://gohugo.io/installation/linux).

Then install it:

```
dpkg -i hugo.deb
```

Make sure it works:

```
hugo version
```

Create a new site and initialize the Git repo:

```
hugo new site mysite-dev
cd mysite-dev
git init
touch .empty # copy this into every empty folder so git picks it up
git add .
git commit -m "initial commit"
```

This got me started. After this, I had to choose a theme or build my layouts from scratch. I chose Coder from the theme library. Instead of copying the theme directly, I discovered I could use Git submodules to essentially symlink the repo into my site structure:

```
git submodule add https://github.com/author/hugo-theme themes/hugo-theme
```

Then I followed the theme instructions for what to put into `hugo.toml`. That included the links on the homepage, my info, and the subfolder my posts were in.

Now to test:

```
hugo serve -D --bind <ip> --port 80 --baseURL "http://<ip>"
```

Checking this gave me an idea if it was working. So far so good. Well, it is now. This was not how my first run through went. I found that the serve command bound to localhost by default. It also ran on another port and used localhost for the base URL, causing some themes to look for their CSS on my local machine. I was accessing the site from my desktop, not from the server hosting it.

Eventually, I wrote a small script to start the dev server so I did not have to type that out so much. After finding out that worked, I created a repo on GitHub and pushed everything up to it. Then I went into the GitHub Pages settings for the repo and set it to use a script for deployment. I found a script to do the deployment and put it in the `.github/workflows` directory.

## Understanding front matter

Now for the fun part: content. Initially, I copied my Markdown files into the server, in the `content` folder. Then I moved them into a `posts` folder. They showed up under the posts URL, but had a date of Jan 1, 0001 and no title. This is where my misunderstanding about how Hugo is designed came into play.

Hugo is designed to be completely static. Makes sense. But for some reason, I had it in my head that I could pull metadata from my Markdown files. The date for the file could be the last modified date, or the creation date. The filename would be the title of the post. But this is not how it works. I eventually got it to use a date from the modification, but that turned out to be the date it was uploaded into GitHub. I was not a huge fan of that, but no worries. The files still showed up with no title. Oops.

I tried creating custom archetypes and making fields that used custom formats and all that. Nothing worked. The files never had a name. Maybe it was the theme? I tried other themes. Same thing, along with other issues.

After searching through so much of the documentation, I found the answer: the archetypes are only used when a new post is created from the command line.

```
hugo new posts/test.md
```

This copied the post archetype into the new file and replaced variables as it went. This is called front matter. It starts and ends with three plus symbols and includes the filename and date, along with other optional stuff. Everything after that is part of the post text.

So I copied that data into the posts already in the repo, changed the title to match the filename, and set the date to the original date. Commit and see what happens. Success!
