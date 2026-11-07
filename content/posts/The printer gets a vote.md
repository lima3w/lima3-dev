+++
draft = false
date = 2026-11-06T15:01:20-06:00
title = "The printer gets a vote"
description = "A late-October 2026 snapshot of making a one-page community handout that still has to work after a black-and-white printer gets involved."
authors = ["Zack"]
categories = ["Projects"]
tags = ["print-design", "pdf", "qr-codes", "accessibility"]
disableComments = true
+++

*This is a snapshot of a small print-design project I finished in late October 2026. The files are ready to use, but a flyer is not a promise that anybody will read it or show up because of it.*

I needed to make a one-page handout for a local community group. The job sounded simple: explain what the group does, make it look inviting, and give people one obvious next step.

The first thing I had to stop doing was treating it like a web page.

## A screen is not the final format

On a screen, a colorful campfire scene and a dark information card looked fine. The actual handout was likely to come out of an ordinary printer, possibly in grayscale, and get read while somebody was standing in a hallway. That changed the priorities pretty quickly.

I kept the important information out of the illustration. The title, meeting details, and the call to action have their own space with enough contrast to survive losing the color. The QR code has a white card around it instead of being dropped onto the artwork and hoped for the best.

That is not a deep design revelation, but it is the kind of thing that is easy to forget when the preview on my monitor looks good.

## I had to check the less-pretty version

The working file generates a normal PDF, then I made grayscale previews and a higher-resolution print preview. That caught the real question: whether the visual hierarchy still existed when the navy, gold, green, and blue-gray choices all became shades of gray.

Some of the decorative parts got quieter, which was fine. The headline still reads first, the key details still separate from the background, and the QR code still has a clear place to land. That was enough. I did not need every part of the colored version to make the same argument in black and white.

The code also generates the QR image from the destination instead of leaving a random copied image in the folder. It is a small thing, but it makes the handout easier to revise without accidentally pairing new text with an old code.

## The boundary

The result is a usable handout and a repeatable source file, not a universal flyer template or proof that the design will solve recruitment. The next honest test is printing a few copies on the kind of printer people will actually use, scanning the code from paper, and seeing whether the layout holds up at arm's length.

For this version, the printer got a vote before I called it done. That is a better stopping point than trusting the screen alone.
