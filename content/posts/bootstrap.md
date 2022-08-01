---
title: "Bootstrap"
date: 2022-07-31T19:25:51-04:00
draft: false
---

Well, it's certainly been a while since I last blogged.  Hopefully I've done a good enough job of
wiping my previous efforts from the face of the internet that you can't find them :)

Since the last I blogged, I've done my fair share of career advancement:

* Graduated from college
* Got a master's degree
* Worked for a defense contractor doing cool high-perf Java for automated mission planning (4.5 
  years)
* Worked for a security ratings startup doing whatever's needed to get the job done (6.5 years and 
  counting)

I've worked on many side projects over the years as well.  Some of the more interesting ones are:

* `body-stats` (private): This is a program that takes an Excel spreadsheet of my daily weigh-ins and graphs
  them along with various regressions in a nice, interactive diagram viewable in a browser.  This
  project started in Python, moved to Scala, and now is written in Rust
* `utility-stats` (private): This takes CSVs of my monthly gas and electricity meeter readings and plots them
  alongside the daily min and max temperatures.  The temperature data has been pulled from various
  providers over the years, moving to different APIs as previous ones were killed or put behind
  paywalls.  I've had to do some interesting things at points along the way, such as implementing
  my own rate limiting (using a circular buffer) to prevent my account from being suspended for
  exceeding the rate limit while backfilling historical data.  Like `body-stats`, this started in Python, then moved to Scala, before finally settling in Rust
* Advent of Code ([2017](https://github.com/frohman04/advent-2017), 
  [2018](https://github.com/frohman04/advent-2018), 
  [2019](https://github.com/frohman04/advent-2019), 
  [2020](https://github.com/frohman04/advent-2020), 
  [2021](https://github.com/frohman04/advent-2021)): I forget how I was originally introduced to the
  Advent of Code, but I've made an attempt at completing it each year during the month of December.
  I managed to get all 50 stars in 2017 and got almost all in 2020, but other years I got distracted
  on other things and didn't bother to come back to try to finish
* `tomato-exporter` ([GitHub](https://github.com/frohman04/tomato-exporter)): I really liked what
  we did with Grafana at work in regards to monitoring our applications, and discussions with
  the head of our DevOps team made me realize that it could be useful in a home lab setup as well.
  I installed [`node_exporter`](https://github.com/prometheus/node_exporter) on all of my normal
  machines, but my home router runs [FreshTomato](https://freshtomato.org/), which I can't easily
  put `node_exporter` on.  I decided to make a hacky workaround by running this project on a 
  Raspberry PI I have on my home network and instead interacting with the REST API endpoint that is
  normally used to back their interactive shell feature in the admin UI to issue normal Linux 
  commands to the router to get basic system stats, which I then expose using the 
  `node_exporter` Prometheus "schema"
* `digitalocean-dyn-dns` ([GitHub](https://github.com/frohman04/digitalocean-dyn-dns)): I wanted to
  be able to access my local network when I'm away from home, but I have a dynamic IP issued by my
  ISP.  The solution: update a DNS record periodically with my local IP address.  I found some
  Python code that did this, but I didn't feel like trying to get a working Python environment
  setup (and maintained in perpetuity) on my Raspberry PI, so I decided to rewrite it in Rust™
* `renderer-ray-trace` ([GitHub](https://github.com/frohman04/renderer-ray-trace)): I thought it
  would be fun to learn how ray tracers work, given all the hoopla about ray tracing being added to
  the newest generation(s) of GPUs from both nVidia and AMD.  I found a tutorial, 
  "[Ray Tracing in a Weekend](https://www.realtimerendering.com/raytracing/Ray%20Tracing%20in%20a%20Weekend.pdf)",
  which was written in C++.  I decided to flex my muscles a bit and translate it to Rust as I went
  along.  I later found 
  "[Ray Tracing in One Weekend](https://misterdanb.github.io/raytracinginrust/)", which was a 
  translation of "Ray Tracing in a Weekend" into Rust.  I was happy with how similar my code was to
  the translated tutorial.  I played with things a little bit after completing the tutorial, but
  have yet to revisit the code to do some of the more interesting next steps
* `gpu-fan-alert` ([GitHub](https://github.com/frohman04/gpu-fan-alert)): I bought a new GPU at the
  beginning of the pandemic after I got a new game that required DX12, which my old GPU didn't
  fully support.  Unfortunately, I didn't pay enough heed to the reviews the card received on 
  NewEgg.  The card had both a problem with requiring undervolting just to make it stable and also
  with the card's fans not spinning up after the computer woke from the sleep state.  (It would try
  to start them, but it would get a reading of 65k RPM from the fans and think that it couldn't 
  command them to run any faster, leading to a system halt to prevent the GPU from overheating.)  I
  utilized the AMD Catalyst driver C API to detect both the card temperature and fan speed, then
  implemented a really ugly hack to get the ASRock Tweak Tool to reset the fan governor so that the
  fan would start properly and not lead to a system crash.  It works well enough, though the hack
  leads to the tweak tool being force killed and restarted a few times to get the governor to reset,
  causing some flashing each time the tool is started again

Going forward, I have a fun project that I'll be starting soon to add a little pizzazz to my 
physical desktop.  I'll detail what the project is in my next post, and I plan on documenting the 
process of building the project as I go along.
