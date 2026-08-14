+++
draft = false
date = 2026-08-14T15:00:50-05:00
title = "Putting the posts back on the front page"
description = "A small July 2026 Hugo layout change to make the latest posts visible without turning the homepage into a second archive."
authors = ["Zack"]
categories = ["Website"]
tags = ["hugo", "static-sites", "website", "homepage", "css"]
disableComments = true
+++

*This is a snapshot of a small site change I made on July 22, 2026, not a promise that the homepage is finished forever.*

I had done some cleanup on the site and then noticed the obvious problem: the blog was there, but the homepage did not really lead anywhere except my profile. That works when a site is a résumé with a blog attached. It is less useful when the posts are the part I am actually trying to keep up with.

I did not want to turn the front page into another full archive. I just wanted someone landing there to see that there is writing behind the Blog link.

## The small version

The Hugo layout now asks for regular pages in the `posts` section, sorts them by date, and renders the newest two below the existing home content. Each entry gets the title, date, and description. If I forget the description, it falls back to a shortened plain-text summary instead. There is still one link to the full posts page rather than a bunch of pagination logic on the homepage.

That is also why I added a description field to the post archetype. The short text has a job now; it is not just metadata sitting in a file because a template might want it someday.

## The part that did not work the first time

My first pass added the post section directly after the theme's home partial. It rendered, but the page layout did not put the profile and the posts where I expected. The homepage content needed its own wrapper with a column flex layout so the theme's centered profile stayed above the new section instead of competing with it.

That was a good reminder that overriding one template in a theme is not the same thing as owning the whole layout. The small CSS file is mostly boring spacing and borders, but the wrapper is what made the actual structure match the idea.

## The boundary for now

As of that July change, the homepage intentionally shows only two posts. It is a small signpost, not a feed reader or a redesign. The site is still Hugo and Markdown files in Git, which is exactly the amount of machinery I want for a personal journal right now.

If the writing starts to pile up, I may need to revisit the archive and navigation. For now, getting the newest notes onto the first page without making the page noisy is enough.
