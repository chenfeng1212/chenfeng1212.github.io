---
title: Lux — POV Light Performance Props
subtitle: Wireless LED props that draw images in the air, synchronized to music for camp stage performances
date: 2025-12-01
category: maker
detail: true
selected: false
tags: [Performance, LED, POV]
venue: 118th & 119th Freshman O-Camp · 28th Engineering Science Camp
team: "Ping-Jhao Hsu, Fan-Cheng Su, Che-Wei Chang · joined later: <span class=\"me\">Li-Cheng Tseng</span>, 林承緯"
role: Joined mid-project — built the control interface with 林承緯, contributed to the server and firmware, helped build the props, and performed with them
summary: >
  Spinning LED props that use **persistence of vision** to draw images in the air, controlled wirelessly in sync with music.
  I joined the senior team mid-project: with a classmate, I built the **control interface** used during shows,
  contributed to the server and firmware, and helped build the light sticks and light snakes.
img: https://i.ytimg.com/vi/Ix1kZmECrI4/maxresdefault.jpg
tech: [ESP32-S3, Node.js, Custom PCB]
links:
  - label: Code & PCB files
    url: https://github.com/ivan125126/light_light_light
  - label: Original project page
    url: https://hsupingjhao.info/projects/Lux/
videos_source: 'Earlier performances by the Lux team, from <a href="https://hsupingjhao.info/projects/Lux/" target="_blank" rel="noopener">Ping-Jhao Hsu’s project page</a>.'
videos:
  - title: 27th Engineering Science Camp — stick, ball, and snake
    id: DXz8Qr7GCnU
  - title: 117th Freshman O-Camp — light stick
    id: YyiI2fg6KxA
  - title: 117th Freshman O-Camp — light snake
    id: Cy09c8EzSV8
  - title: 117th Freshman O-Camp — light ball
    id: KIUfSW9J6ho
credits: 'Lux was started by Ping-Jhao Hsu, Fan-Cheng Su, and Che-Wei Chang. The overview, technical details, and videos on this page are adapted from <a href="https://hsupingjhao.info/projects/Lux/" target="_blank" rel="noopener">Ping-Jhao Hsu’s original project page</a>.'
---

## Overview

The NCKU Maker Space once had a set of LED performance props for camp shows, but they had been lost. Working only from photos of a past performance and an old GitHub repo, a team of seniors rebuilt them from scratch — redesigning the **hardware, firmware, and effect editor** so the props could be maintained and handed down to future camps.

The spinning props — a **light stick**, a **light snake**, and a **light ball** — use **persistence of vision (POV)**: as a performer spins them, a fast-changing column of LEDs draws 2D images in the air, all synchronized wirelessly to the music.

## My part

I joined the project partway through, together with my classmate 林承緯, and worked on the props for the **118th Freshman O-Camp**, the **28th Engineering Science Camp**, and the **119th Freshman O-Camp**. The two of us:

- **Built the control interface** — the front-end screen used to run the light effects during a performance.
- **Contributed to the server** and to **hardware and firmware changes**, taking part in the design discussions with the original team.
- **Helped build the light sticks and light snakes** used on stage.
- **Performed** with the props on stage at the 118th Freshman O-Camp.

<div class="pj-video-grid pj-inline-video">
  <figure class="pj-video">
    <div class="pj-video-frame">
      <iframe src="https://www.youtube-nocookie.com/embed/Ix1kZmECrI4" title="118th Freshman O-Camp — I am one of the performers on stage." loading="lazy" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>
    <figcaption>118th Freshman O-Camp — I am one of the performers on stage.</figcaption>
  </figure>
  <figure class="pj-video">
    <div class="pj-video-frame">
      <iframe src="https://www.youtube-nocookie.com/embed/S6A5_S4tXNA" title="119th Freshman O-Camp — performed with props I helped build." loading="lazy" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>
    <figcaption>119th Freshman O-Camp — performed with props I helped build.</figcaption>
  </figure>
</div>

## How it works

- **Electronics.** Each stick and snake runs on an **ESP32-S3** with a Li-ion battery, a charging chip, and regulated 3.3 V and 5 V rails. The team went from a hand-soldered prototype to **custom PCBs**, shrinking the board each version so it fits inside a slimmer tube.
- **Enclosure.** Clear **polycarbonate tubes** survive the hits that come with spinning props.
- **Firmware.** One CPU core handles Wi-Fi while the other handles LED timing, so network traffic never disturbs the light output. The frame rate is tuned to a typical spinning speed so the images appear while performers spin.
- **Wireless group control.** A **Node.js** server registers devices, tracks heartbeats, and sends each prop its queue of effects in advance, keeping every prop in sync despite unreliable Wi-Fi.
- **Effect editor.** Effects are designed on a timeline that shows the music's waveform, instead of being hard-coded and flashed to the device.
