---
title: "Lightshow, Part 1: Introduction"
date: 2022-08-19T13:55:32-04:00
draft: false

tags: ["lightshow"]
categories: ["projects"]
---

I like having some light behind my monitor when I'm working in a dark/dim room, as I find it helps
reduce eyestrain.  I've been accomplishing this for years using a simple [architect desk lamp](https://www.wayfair.com/lighting/pdp/17-stories-lakiva-28-desk-lamp-w004243933.html)
that I got back in college, but I've lately been wanting something a little more immersive and 
evenly lit.  What I wanted is something like the [Philips Hue Play HDMI Sync Box + lightstrip](https://www.theverge.com/23302947/philips-hue-play-sync-box-gradient-lightstrip-review), 
but that works for my gaming PC which uses DisplayPort.  I briefly played around with some [Nanoleaf Shapes Mini Triangles](https://www.amazon.com/dp/B08N68WPPB?psc=1&ref=ppx_yo2ov_dt_b_product_details),
but they had a deal-killing WiFi-only limitation for their screen mirroring functionality, which 
isn't very helpful when your PC is hardwired to increase throughput and decrease latency.

Given the lack of products that I could find to fulfil my desire that cost a reasonable amount of 
money, I thought it would be fun to try to build this myself.  It would give me a reason to play 
with microcontrollers, something I haven't done since college, and hopefully build something that 
will be really neat.

## The Idea

A little research showed some [cool things](https://www.youtube.com/watch?v=rPvGLSuMaLA&list=WL) 
that you could do with WS2812 light strips, using an Arduino to drive them.  I plan on using this 
setup as the base for my project.

```goat
        .---------.    .--------------------.
   .--->| Arduino +--->| WS2812 Light Strip |
.--+-.  '---------'    '--------------------'
| PC |
'--+-'                      .---------.
   '----------------------->| Monitor |
                            '---------'
```

My current goal is to build this out in two parts: a Windows program that collects screenshots and
generates the RGB values for each LED pixel in the backlight, and an Arduino controller which 
receives the pixel data and sends it along to the WS2812 LED strip.

My bill of materials (still being acquired):

* $32 [WS2812B LED strip](https://www.amazon.com/dp/B01CDTEJBG/?coliid=IOGBBIV9NQAVQ&colid=U5OP8LH30PYS&psc=1&ref_=lv_ov_lig_dp_it)
  * 16.4ft is more than enough to make it around my 27" monitor, but the next size down from this 
    manufacturer would have been too short
* $18 [Arduino Nano Every](https://www.amazon.com/dp/B07VX7MX27/?coliid=I15MKCOKK76KVB&colid=U5OP8LH30PYS&psc=1&ref_=lv_ov_lig_dp_it)
* $24 [10A DC power supply](https://www.amazon.com/dp/B0852HL336/?coliid=I1565VY948AG10&colid=U5OP8LH30PYS&psc=1&ref_=lv_ov_lig_dp_it)
  * I likely could get by with a 5A power supply once I trim the LED strip down to size, but I'd
    rather play it safe.  Also, this could allow me to scale up to a second monitor without needing
    a second power supply in the future
* $17 [Assorted electrical components kit](https://www.amazon.com/Capacitor-Resistor-Resistance-Universal-Transistor/dp/B087WWRK66)
  * 😔 Did I mention that this is my first electronics project since college?

### The Sampler

The Sampler is what I'll be calling the program that runs on my PC.  I've been using Rust for all
my personal projects for the past couple of years, and I feel like it will be a good fit here as
well since I'll need high performance to be able to do the image sampling to generate the RGB pixel
settings in a reasonable amount of time.  Since I want this to be usable when I'm playing games, I'm
not going to try to process every frame since I would likely need to write GPU shaders to process
large bitmaps quickly enough to keep up with 60+ fps in the game, which would just put more work on
the GPU which would already be busy doing work for the game.  I figure shooting for processing 
around 4fps should be sufficient to not have the LEDs appear to be too laggy compared to the monitor
they are backlighting.  I'll make sure to implement performance metrics within the code so I can 
easily monitor the FPS of the LED updates.

I want the sampler to not be tied to any particular sampling algorithm, so I'll make sure to hide
this behind a trait.  To keep things fast and simple to start, I'll implement a rectangle average
algorithm, where I take the pixels contained within a rectangle (aligned to the grid, no subpixels)
that borders the edge of the monitor where the LED is, and just average the RGB values within that 
region.  I'm not sure if this will produce useful colors, but it will be a good starting point.

To help develop the sampling algorithm, I want to make a simple visualizer tool that will allow an 
image to be provided, and then produce a simulated backlighting of the image with the original image
superimposed.  This will both allow the performance of the algorithm to be measured along
with its results.  I might even have some fun and add some brightness falloff calculations (yay, 
physics!) along with light blending in order to produce more accurate simulated results.

I foresee the need to add features to the sampling algorithm like doing a weighted
average of the region based on the distance from the idealized border LED pixel location (stronger
contribution for pixels closest to LED, weakening as you get farther away, maybe linearly decreasing,
maybe some kind of quadratic, exponential, or logarithmic falloff).  I might also want to tweak the
sampling region dimensions, such as having the regions all touch vs having a gap between them, or
maybe using non-rectangular regions that are shaped using triangles drawn from the center point or
center line(s) of the screen.

### The Controller

The Arduino will be acting as the LED controller for this project.  The sampler will communicate
with the controller over USB, sending LED light values either in "frames" or as each pixel is
calculated.  I want to keep the complexity of the controller as simple as possible since (a) I'm
very rusty in my embedded development game, (b) the chip on the controller is much weaker than
the CPU in my gaming machine, and (c) the Arduino has limited program storage and RAM, while my PC
has orders of magnitude more of both types of storage.  To achieve this, the controller will be
essentially a translator from the protocol I generate to send LED pixel data over USB into the 
protocol that WS2812 light strips use to command their LEDs.

## The Plan

Since I'm utilizing my birthday as my acquisition method for getting the parts I'll need to do this
project, I'm at the mercy of family scheduling when it comes to actually receiving the parts.  (For
once I actually have a birthday list to give to people!)  While I'm waiting for the parts (some of
which I may still end up needing to buy myself), I plan on working on the Sampler.  The first order
of business there will be getting screenshots in a performant way, which will inform the way I 
represent image data within the rest of the Sampler.  Once I have that taken care of, I'll write up 
the initial sampling algorithm.  Then it will be time to build out the sampler debug GUI, my first 
foray into GUI programming in Rust.

As I work through this project, I'll be posting updates on this blog, hopefully including
screenshots, screencasts, pictures, and code snippets when I have something to show off.  The code
itself will be posted on GitHub at https://github.com/frohman04/lightshow.  I'm guessing the
circuitry will be simple, but I may post some schematics as well for people who like that kind of
stuff.
