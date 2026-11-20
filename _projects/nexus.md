---
title: neXus
subtitle: A modular multi-sensory system that brings the outdoors into trade-show booths
date: 2026-05-01
period: 2025.07 – 2026.06
category: maker
detail: true
selected: true
tags: [YODEX, Installation, Multisensory]
venue: Young Designers' Exhibition (YODEX)
team: "Design: Pin-Yi Lin, Yu-Hsuan Chen, Ying-Ju Yu, Ying-Chi Lo · Engineering: <span class=\"me\">Li-Cheng Tseng</span>, Wei-Yu Yi"
role: Engineer — control server, iPad control logic, device communication, and the heat module's electronics
recognition: Semi-finalist, 23rd Y.S. Awards · Exhibited at YODEX 2026
summary: >
  A set of quick-assembly **sensory modules** — light, sound, wind, heat, and mist — built with industrial design students
  to bring outdoor experiences into trade-show booths. I built the control server, the iPad remote control,
  and the communication between all the devices.
img: /assets/img/projects/nexus/thumb.jpg
hero: /assets/img/projects/nexus/hero.jpg
tech: [Node.js, Express, ESP8266, UDP, Server-Sent Events, JavaScript]
links:
  - label: Demo video
    url: https://youtube.com/shorts/8y3D0Uu9DdA
  - label: Image video
    url: https://youtu.be/7GYz3Rosn7U
  - label: App intro
    url: https://youtu.be/G5TC6oxMq54
videos:
  - title: Demo
    id: 8y3D0Uu9DdA
    vertical: true
  - title: Image video
    id: 7GYz3Rosn7U
  - title: App intro
    id: G5TC6oxMq54
credits: "Images and videos from the neXus team's portfolio."
---

## Overview

Trade shows increasingly want **immersive experiences**, but limited space, setup time, manpower, and budget make multisensory effects hard to pull off — and temporary booths throw away a lot of single-use material.

neXus answers this with **sensory modules × connectors**: self-contained modules for light, sound, wind, heat, and mist that clip onto exhibition structures in minutes and can be rearranged for every show. Our first use case is **bringing the outdoors into an indoor booth**, so visitors can feel the conditions an outdoor product is made for.

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/nexus/modules.jpg' | relative_url }}" alt="Five sensory modules: light, sound, wind, heat, and mist" loading="lazy">
  <figcaption>Five sensory modules, each a self-contained unit.</figcaption>
</figure>

## My role

I joined the industrial design team as one of two engineers. I built the **control server**, the **logic behind the iPad interface** the team designed, and the **communication between the iPad, the modules, and the projector**, as well as the electronics of the heat module. My fellow engineer, Wei-Yu Yi, built the circuits for the wind and mist modules.

## How the system works

One laptop runs everything: it hosts a **Node.js server** that talks to all the hardware, and drives the projector as a second screen. Exhibitors control the booth from an **iPad web app** over the local Wi-Fi.

| Module | Hardware | How it is controlled |
| --- | --- | --- |
| Light | Wi-Fi smart bulb | UDP broadcast — on/off, brightness, color temperature (2200–6500 K), RGB |
| Heat | Heater + ESP8266 with a relay | HTTP on/off |
| Wind | Brushless fan + ESC + ESP8266 | HTTP speed control, plus a physical switch and a status LED |
| Sound | Laptop audio | Ambient tracks with volume control |
| Mist | Mist maker | Operated by hand on site |
| Projection | Projector on the laptop's second screen | Server-Sent Events switch videos instantly |

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/nexus/integration.jpg' | relative_url }}" alt="Hardware output and software remote control" loading="lazy">
  <figcaption>The modules create the environment; the iPad is the remote control.</figcaption>
</figure>

## One tap, one scenario

Each scenario sets wind, sound, light, heat, and the projected video together:

| Scenario | Wind | Sound | Light | Heat |
| --- | --- | --- | --- | --- |
| Forest in the sun | 30% | Forest ambience | Bright daylight, 5200 K | On |
| Riding at night | 60% | Cycling ambience | Warm streetlights flashing past | Off |
| Bright coast | 40% | Coastal waves | Harsh midday sun, 6500 K | On |

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/nexus/scenarios.jpg' | relative_url }}" alt="Concept renders: outdoor glasses, helmet, and cooling jacket in the booth" loading="lazy">
  <figcaption>Concept renders: one booth, different outdoor products.</figcaption>
</figure>

## Engineering details

- **The interface is the single source of truth.** Scenario presets don't talk to the hardware directly — they move the sliders on the Control tab, and the sliders send the commands. Manual control and presets can never disagree.
- **No race conditions on startup.** While the page lines up its sliders on load, a guard flag blocks hardware commands so they don't fight the welcome lighting.
- **Lights that feel alive.** A warm "breathing" light welcomes visitors with a 2-second sine wave; the night-ride scenario speeds it up between 20% and 100% brightness to feel like streetlights flashing by.
- **Debounced commands.** Dragging a slider sends at most one lighting command every 100 ms, so the bulbs are never flooded.

## Development

<div class="pj-figure-row">
  <figure class="pj-figure">
    <img src="{{ '/assets/img/projects/nexus/development.jpg' | relative_url }}" alt="Prototyping the modules and the exhibition stand" loading="lazy">
    <figcaption>From module prototypes to the final exhibition stand.</figcaption>
  </figure>
  <figure class="pj-figure">
    <img src="{{ '/assets/img/projects/nexus/team.jpg' | relative_url }}" alt="Interface sketches, the team, and module wiring" loading="lazy">
    <figcaption>Designers and engineers working on one system.</figcaption>
  </figure>
</div>
