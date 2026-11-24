---
title: Lightstick
subtitle: A wireless concert light stick that 150 high-school students built themselves — and that one operator can light up together, or each person can control from their phone
date: 2026-01-01
category: maker
detail: true
selected: true
tags: [Tangible, Performance, Workshop]
venue: 28th Engineering Science Camp, NCKU
team: "<span class=\"me\">Li-Cheng Tseng</span> (solo design) · built by ~150 camp participants"
role: Everything — enclosure, circuit, firmware, control software, and the 6-hour workshop that taught it
summary: >
  A concert light stick designed from scratch — 3D-printed enclosure, circuit, firmware, and control software — and
  turned into a **6-hour workshop** where about **150 high-school students** built their own. Sticks follow a central
  operator over ESP-NOW, and fall back to **phone control** on their own when the operator goes silent.
img: https://i.ytimg.com/vi/-e6HaGDkWbk/maxresdefault.jpg
hero: https://i.ytimg.com/vi/-e6HaGDkWbk/maxresdefault.jpg
tech: [ESP8266, ESP-NOW, WS2812B, Node.js, SolidWorks, 3D printing]
links:
  - label: Code
    url: https://github.com/chenfeng1212/lightstick_esc28
  - label: Demo clips
    url: https://youtube.com/playlist?list=PLXYGFynZ8qx8
videos:
  - title: Early test — many sticks following music
    id: jmrhSnc7wB8
    vertical: true
---

## At a glance

| | |
| --- | --- |
| **Context** | The final performance of an engineering camp for high-school students |
| **People** | ~150 participants (builders and audience) · 1 operator at the control console |
| **What I made** | Enclosure, circuit, firmware, control console, phone control page, and the workshop |
| **Outcome** | Participants built their own light sticks and used them in the camp's final show |

## Design question

A single light stick can't do much. But when hundreds of them respond together, an audience becomes part of the show. I wanted to know:

> **How can people with no electronics background build their own networked light stick — and how can those sticks work both as one synchronized crowd and as personal objects people control themselves?**

This led to two groups of users with different needs:

- **Participants** build the stick in a workshop and hold it during the show. They need something they can assemble in a few hours, that feels like a real product, and that still does something when the show isn't running.
- **The operator** runs the show from a laptop. They need to change the whole audience at once, trust that every stick follows, and never show the audience a half-finished effect.

## Design goals

1. **Buildable by beginners** — few parts, a case that opens easily, steps that fit into one workshop.
2. **Works with or without a show** — sticks should never become dead plastic when the operator is offline.
3. **One crowd, many groups** — the operator can light different sections of the audience differently, and they all switch at the same moment.
4. **Operator confidence** — preview changes privately, then send them to everyone at once.

## The light stick

- **Electronics:** an ESP8266 board, 4 WS2812B addressable LEDs, a 3.7 V 800 mAh LiPo battery, and a charging module — rechargeable and fully self-contained.
- **Enclosure (SolidWorks, 3D-printed)**, shaped after commercial light sticks with three changes for builders:
  - **switch and charging port at the bottom**, so the stick looks clean and isn't switched off by accident;
  - **a sliding shell** that opens without tools, for assembly and repairs;
  - **a screw-on diffuser**, assembled and removed by hand.

<div class="pj-figure-row pj-row-3">
  <figure class="pj-figure">
    <img src="{{ '/assets/img/projects/lightstick/lit.jpg' | relative_url }}" alt="The light stick switched on, glowing warm white through the clear diffuser" loading="lazy">
    <figcaption>Switched on: four LEDs behind a printed diffuser.</figcaption>
  </figure>
  <figure class="pj-figure">
    <img src="{{ '/assets/img/projects/lightstick/bottom.jpg' | relative_url }}" alt="Bottom of the light stick showing a USB-C charging port and a push switch" loading="lazy">
    <figcaption>Switch and USB-C charging port hidden at the bottom.</figcaption>
  </figure>
  <figure class="pj-figure">
    <img src="{{ '/assets/img/projects/lightstick/parts.jpg' | relative_url }}" alt="The light stick opened: diffuser, LED strip, ESP8266 board and charging module in one half of the shell, and the other half" loading="lazy">
    <figcaption>The sliding shell opens without tools for assembly and repairs.</figcaption>
  </figure>
</div>

<figure class="pj-figure pj-inline-video">
  <div class="pj-video-frame">
    <iframe src="https://www.youtube-nocookie.com/embed/6ROM78xIKqk" title="The six effects: steady, breathing, rainbow, window, beat, and gradient." loading="lazy" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  <figcaption>The six effects: steady, breathing, rainbow, window, beat, and gradient.</figcaption>
</figure>

## Two ways to control it

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/lightstick/architecture.jpg' | relative_url }}" alt="System diagram: control console, Node.js server, master node, optional gateway, and groups of light sticks; below, a phone connected directly to one light stick" loading="lazy">
</figure>

### Central control: one operator, the whole audience

The operator uses a **web console** on a laptop. A Node.js server passes commands over USB to a **master node**, which broadcasts them with **ESP-NOW** — a connectionless Wi-Fi protocol that needs no router, so hundreds of sticks can listen at once. Optional **gateway** nodes repeat the signal for larger rooms.

- **10 groups + "all".** Each stick belongs to a group (set from its phone page), so different sections of the audience can show different colors.
- **Preview → GO.** Changes are tried out in a preview area that the audience never sees; pressing **GO** sends all groups at once. A **LIVE** switch skips the preview when the operator wants to follow the music by hand.
- **Scenes and fades.** The state of all 10 groups can be saved as a scene and recalled with one click, with a 0–3 s cross-fade.
- **Six effects** — steady, breathing, rainbow, window, beat, and gradient — tuned with speed, spread, duty, BPM (including tap-tempo), and a four-color palette.

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/lightstick/console.png' | relative_url }}" alt="Control console: scenes on the left, preview and on-air rows of 10 groups, a GO button, and an editor for effects, brightness, speed and palette" loading="lazy">
  <figcaption>The control console. Left: saved scenes. Top: the <em>preview</em> row (what the operator is editing) above the <em>on air</em> row (what the audience sees). Bottom: the effect editor for the selected group. Every action also has a keyboard shortcut, so the show can be run without the mouse.</figcaption>
</figure>

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/lightstick/console_go.png' | relative_url }}" alt="Preview row with pending changes marked by orange dots, on-air row, and a red GO button showing 10 groups changed" loading="lazy">
  <figcaption>Preview → GO: orange dots mark groups with pending changes; the GO button shows how many groups will change, and Enter sends them all at once.</figcaption>
</figure>

<figure class="pj-figure pj-inline-video">
  <div class="pj-video-frame">
    <iframe src="https://www.youtube-nocookie.com/embed/30Pfo1LLL5c" title="Demo: running the sticks from the control console." loading="lazy" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  <figcaption>Demo: running the sticks from the control console.</figcaption>
</figure>

### Standalone: the stick is yours

If a stick hears nothing from the master for **5 seconds**, it follows its own phone page instead. Every stick runs its own Wi-Fi hotspot with a **captive portal**: join the network and the control page pops up — no app, no typing an address. The page previews the four LEDs live, and lets the owner rename the hotspot, set a password, and choose a group; settings are saved and survive a restart.

<div class="pj-phones">
  <figure class="pj-figure">
    <img src="{{ '/assets/img/projects/lightstick/phone_wifi.jpg' | relative_url }}" alt="iPhone Wi-Fi settings showing the stick's hotspot chenflff_lightstick" loading="lazy">
    <figcaption>① Join the stick's hotspot.</figcaption>
  </figure>
  <figure class="pj-figure">
    <img src="{{ '/assets/img/projects/lightstick/phone_control.jpg' | relative_url }}" alt="Phone control page with a live preview of the four LEDs, six effect buttons, brightness and color" loading="lazy">
    <figcaption>② The control page pops up, with a live preview of the 4 LEDs.</figcaption>
  </figure>
  <figure class="pj-figure">
    <img src="{{ '/assets/img/projects/lightstick/phone_settings.jpg' | relative_url }}" alt="Phone settings page with Wi-Fi name, new password, group 1 to 10, and current password" loading="lazy">
    <figcaption>③ Rename the hotspot, set a password, pick a group.</figcaption>
  </figure>
</div>

<figure class="pj-figure pj-inline-video">
  <div class="pj-video-frame">
    <iframe src="https://www.youtube-nocookie.com/embed/Eet8EZNlfvw" title="Demo: controlling a single stick from a phone in standalone mode." loading="lazy" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  <figcaption>Demo: controlling a single stick from a phone in standalone mode.</figcaption>
</figure>

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/lightstick/modes.jpg' | relative_url }}" alt="State diagram: power on, standalone mode following the phone, central control following the master; switches on packet received or 5 seconds of silence" loading="lazy">
</figure>

<figure class="pj-figure pj-inline-video">
  <div class="pj-video-frame">
    <iframe src="https://www.youtube-nocookie.com/embed/f0hqFOOCWCE" title="Demo: the master goes silent, and the sticks hand control back to their phones." loading="lazy" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  <figcaption>Demo: the master goes silent, and the sticks hand control back to their phones.</figcaption>
</figure>

## Keeping hundreds of sticks in sync

Wi-Fi in a room full of people is unreliable, so the protocol is designed to tolerate lost packets:

- **Burst sending and heartbeats.** The master sends each command several times, and every 0.5 s re-sends the current state of one group in turn — so a stick that missed a packet, or was switched on mid-show, catches up within seconds.
- **A shared clock.** Every packet carries the master's time, and each stick corrects its own clock, so breathing and beat effects stay in phase across the room.
- **Switch at the same moment.** Packets include an *apply-at* time, so all groups change together instead of one after another.
- **Duplicate filtering.** Gateways ignore packets they have already repeated, with a random back-off to avoid collisions.


## Teaching it: a 6-hour workshop for 150 students

As course director of the camp, I turned the design into a workshop that students with no electronics background could follow:

1. **Circuits** — what each part does
2. **Programming** — flashing the firmware
3. **SolidWorks** — how the enclosure was designed
4. **Soldering** — wiring their own stick
5. **Testing**
6. **Wireless communication** — how one console can reach every stick

Designing the course meant breaking every step down and predicting where students would get stuck — soldering and flashing the board were the usual trouble spots.

<div class="pj-figure-row">
  <figure class="pj-figure">
    <img src="{{ '/assets/img/projects/lightstick/teaching.jpg' | relative_url }}" alt="Li-Cheng presenting a slide about TCP packet headers to students" loading="lazy">
    <figcaption>Teaching the wireless-communication session: how data is split into packets.</figcaption>
  </figure>
  <figure class="pj-figure">
    <img src="{{ '/assets/img/projects/lightstick/repair.jpg' | relative_url }}" alt="Two staff members repairing light sticks at a lab bench with a soldering iron and a laptop" loading="lazy">
    <figcaption>Fixing participants’ sticks with a fellow staff member between sessions.</figcaption>
  </figure>
</div>

## In the show

At the camp's final performance, participants held the light sticks they had built, and the hall lit up with them.

<figure class="pj-figure pj-inline-video">
  <div class="pj-video-frame">
    <iframe src="https://www.youtube-nocookie.com/embed/-e6HaGDkWbk" title="The final performance at the 28th Engineering Science Camp." loading="lazy" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  <figcaption>The final performance at the 28th Engineering Science Camp.</figcaption>
</figure>

## What I learned

This was the first project I took from idea to something people actually used — enclosure, circuit, and code all at once — and it taught me to debug hardware and software step by step instead of giving up. Being course director taught me something more important: **good technology isn't just something you can build, it's something other people can understand and use**. Watching 150 students build and wave their own sticks is what made me curious about human–computer interaction — after making something, I want to know how it feels to use it.
