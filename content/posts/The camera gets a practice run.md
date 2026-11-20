+++
draft = true
date = 2026-11-20T15:20:00-06:00
title = "The camera gets a practice run"
description = "Trying to read laundry timers locally started with a smaller problem: getting useful pictures and knowing what they actually show."
authors = ["Zack"]
categories = ["Projects"]
tags = ["automation", "computer-vision", "machine-learning", "homelab"]
disableComments = true
+++

I wanted to know how much time was left on the washer and dryer without having to go look at them. I already had a camera pointed at the machines, so my first thought was to build a small model that could look at an image and read the displays. Ideally it would run on a CPU, without making a cloud vision request every time I wanted a number.

That sounds like a machine-learning project. It may become one. The first useful work, though, was a little less exciting: get an actual picture, figure out what it showed, and stop assuming that a plausible answer was the right answer.

## A picture is not a reading

The existing camera setup could provide a still frame. It took a wrong turn to find it: the first check looked on the wrong machine and concluded that the snapshot helper was not running. It was running elsewhere. Once we checked the right place, we could capture an image without changing the existing workflow.

That distinction mattered because I did not need a new camera service just to begin the experiment. I needed to use the one already doing the job. The helper grabs a frame from the camera stream; taking that frame is separate from asking a vision model to interpret it. The capture itself does not need an AI request. Interpretation by a hosted model is a different operation with its own resource and potential billing costs.

The first image showed dark displays. There was a suggestion that the camera and display refresh rates might be interacting. That was possible in the abstract, but it was not what the picture proved. Both machines had finished by the time the image was captured. The blank displays were an off-state example, not evidence of a refresh problem.

I started fresh loads and captured another frame. This time both displays were lit, but the digits occupied a small part of the image. The camera view was upside down, so we rotated and cropped it to look more closely. Even then, the first attempted readings were wrong. I corrected them to 1:11 and 39 minutes.

That was a useful little reality check. A rotated crop can make a picture easier to inspect without making the answer trustworthy. If the person standing in front of the machines has to correct the reader, that correction is a label for the experiment, not proof that the reader is ready to drive an automation.

## The boring part is the dataset

One active picture and one off picture are a start, but they are not enough to tell me whether a reader will work. A timer changes over a cycle. Different digits appear, the display can be blank, and the picture can look different with lighting or camera changes. A model trained or judged on one convenient frame could be very confident about the wrong thing.

I set up a bounded capture run to collect pictures over roughly an hour while the machines were running. The local collection includes the two reference images and the frames saved during that run. The capture script checks that each response is a valid JPEG before saving it. It also has a deadline, because I wanted a small collection for an experiment, not an open-ended camera archive.

The scheduled captures in the saved log landed about four minutes apart, even though I had asked for three. That is worth keeping in the story. A requested interval and an observed interval are not the same evidence, and a folder full of pictures does not prove that a scheduler hit the timing I asked for.

The frames still need careful labels. A timestamp on an image tells me when it was saved, not automatically what either timer said. I also need enough examples to see different digits and states, not just a long sequence of nearly identical pictures. The collection is raw material for a dataset, not a completed training set.

I am keeping the camera images private. There is no reason to put pictures of the room or the camera connection details in a public post to explain how a timer reader might work.

## A small model is one option, not the requirement

My original question was about building a model, and I still like the idea of learning how to train a small one for this specific task. But a fixed camera looking at fixed displays gives me another option: crop the display and test whether ordinary image processing can identify the lit segments. If that works reliably enough, I might not need a trained model for the reading step at all.

The tradeoff is that fixed rules can be sensitive to position, brightness, glare, and the particular display. A small classifier could be more flexible in some conditions, but it needs representative labeled examples and its own evaluation. Neither approach gets to skip checking whether the source image contains enough information to read.

There is also a difference between recognizing a number and deciding what to do with it. A blank display, an unreadable frame, and a real zero are not necessarily the same state. If the eventual reader cannot tell, it should be allowed to say it does not know instead of quietly turning uncertainty into a finished-cycle event.

I would want to compare several frames and check whether readings make sense over time before letting a result affect the existing automation. That is a design direction, not something I have tested or deployed. A second frame can help, but repetition alone would not prove a reading is correct if the same visual problem appears in every frame.

## Where it sits

As of this experiment in November 2026, I have a way to collect local frames and a small set of examples, including an off state and an active frame whose initially guessed readings I corrected. I do not have a trained CPU reader, a measured accuracy rate, or a replacement for the current workflow.

The next useful step is to label and inspect the collection, try a simple crop-and-read approach against examples it has not been tuned on, and see where it fails. If a small model makes more sense after that, the pictures and the failures will help explain what it needs to learn.

I started by asking how to build a model. The camera reminded me that the first job is knowing what the picture actually says.
