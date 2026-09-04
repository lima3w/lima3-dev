+++
draft = false
date = 2026-09-04T15:01:52-05:00
title = "A blinking ornament is still a prototype"
description = "An August 2026 snapshot of turning a simple 2×AAA blinking-ornament idea into a first PCB fabrication release without pretending the physical board is already proven."
authors = ["Zack"]
categories = ["Hardware"]
tags = ["electronics", "kicad", "555", "pcb", "prototyping", "soldering"]
disableComments = true
+++

*This is a snapshot of the design work I finished in August 2026. The fabrication package was accepted, but the physical board had not been tested yet, so this is not a finished-kit announcement.*

![Front of the unpopulated purple first-prototype ornament PCB, showing the circular outline, hanging hole, and labeled component footprints.](/images/posts/ornament-555/pcb-front.jpg)

*Front of the first prototype board, before assembly and electrical testing.*

I wanted to make a small ornament that could also work as a beginner soldering kit: a board with a few visible parts, two AAA batteries, and three red LEDs that blink together. No microcontroller, no firmware, and no mystery black box. Just a timer and enough parts to make it feel like someone built something.

That sounded like a simple project. It was simple in the way a lot of hardware projects are simple: the circuit idea fits in a few lines, and then the details start showing up.

## The first constraint was the batteries

I wanted two AAA cells instead of a coin cell. They are easier to replace, give the circuit more room to work, and do not need a separate battery holder hanging off the board. Four Keystone clips make the cells part of the PCB itself.

That immediately ruled out some ideas. A normal bipolar NE555 is not a good fit for a low-voltage battery project, so I used a CMOS LMC555 instead. I also passed on self-flashing LEDs and other effects that needed more voltage or current than I wanted to promise from this little circuit.

The resulting circuit is deliberately boring: the LMC555 runs as an astable timer, and its output drives three separate LED branches. Each LED gets its own resistor. Sharing one resistor or putting the LEDs in series would have been a shortcut in the wrong place, especially on a two-AAA supply.

The nominal timing works out to about a 7.2-second full cycle: roughly 3.3 seconds on and 4 seconds off. Since the timer sinks the LED current, the LEDs are lit during the output-low part of that cycle. That is a calculation, not a measurement, and I am trying to keep that distinction clear.

## The circuit was only part of the work

The board became a 90 mm circular ornament with a hanging hole, a front-mounted power switch, and the battery clips on the back. I made the battery-clip footprint from the manufacturer mounting drawing, which is exactly the kind of thing that looks finished in a layout editor while still needing a real board in hand.

Before sending it out, I checked the schematic and PCB for the basic things I could check there: no ERC violations, no PCB DRC violations, no unrouted connections, and clean schematic-to-board parity. I also caught an LED-polarity issue before the final check and moved rear copper clear of the battery-cell envelopes.

The first Gerber package was accepted for prototype fabrication. After that, I revised the outline to add a hanging tab and regenerated the fabrication output. The electrical design did not change in that revision.

## What has not been proven yet

A manufacturer accepting a Gerber package means the files were acceptable to manufacture. It does not mean the ornament is a good kit.

The next part is physical testing: dry-fitting the clips and switch, confirming battery direction and retention, powering it first from a current-limited bench supply, measuring real timing and current, checking the LEDs in normal and dim light, and leaving it running on fresh batteries for at least eight hours. I also need to find out whether the board hangs without flexing and whether somebody new to soldering would have an easy time assembling it.

That is the boundary right now. I have a documented first prototype and a test plan, not a product claim. If the board arrives and the real-world behavior disagrees with the assumptions, that is not a failure of the project. It is the reason to build a prototype before calling something done.
