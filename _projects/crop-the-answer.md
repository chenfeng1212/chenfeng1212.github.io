---
title: Crop The Answer
subtitle: A pixel-art farming game where you have to actually learn about crops and fish to move the story forward
date: 2025-01-01
category: maker
detail: true
selected: false
tags: [Game, Unity, Education]
venue: Windows Programming course project
team: "<span class=\"me\">Li-Cheng Tseng</span>, 石浚邑, 陳宇夫"
role: Fishing system, warehouse system, math mini-game, polish, and the final presentation
summary: >
  A 2D educational farming game in the spirit of Harvest Moon. Players farm, fish, and talk to NPCs, who quiz them on
  what they learned — correct answers unlock the next part of the story. I built the **fishing system**,
  the **warehouse** with its crop encyclopedia, and the **math mini-game**.
img: /assets/img/projects/crop/thumb.png
hero: /assets/img/projects/crop/map.png
tech: [Unity, C#]
links:
  - label: Code
    url: https://github.com/chenfeng1212/unity_CropTheAnswer
  - label: All clips
    url: https://youtube.com/playlist?list=PLfQPvmzV4v3o
videos:
  - title: Full gameplay demo
    id: VjkxRszhylg
---

## Overview

We wanted a game like *Harvest Moon* or *Happy Farm* that also teaches something. In **Crop The Answer**, you run your own farm — planting crops, fishing, and filling your warehouse — while NPCs from the story stop you and **ask questions about what you've grown and caught**. Answer correctly and the next chapter of the story unlocks, so the knowledge has to stick instead of being skimmed.

We wrote the script, drew the map, and designed the NPCs ourselves, in pixel-art style.

## How to play

<div class="pj-clips">
  <a class="pj-clip" href="https://youtu.be/SCUiSYqP1o8" target="_blank" rel="noopener">
    <img src="https://i.ytimg.com/vi/SCUiSYqP1o8/hqdefault.jpg" alt="Farming gameplay" loading="lazy">
    <span class="pj-clip-title">Farming</span>
    <span class="pj-clip-desc">Wait for crops to ripen, press E to harvest. A quick mini-game can double the yield.</span>
  </a>
  <a class="pj-clip" href="https://youtu.be/wzmkkbSJqyY" target="_blank" rel="noopener">
    <img src="https://i.ytimg.com/vi/wzmkkbSJqyY/hqdefault.jpg" alt="Fishing gameplay" loading="lazy">
    <span class="pj-clip-title">Fishing <span class="me">my part</span></span>
    <span class="pj-clip-desc">Cast at the rod, wait for the “!”, press E. A catch opens a math question for a double reward.</span>
  </a>
  <a class="pj-clip" href="https://youtu.be/_WhW1NeMX1k" target="_blank" rel="noopener">
    <img src="https://i.ytimg.com/vi/_WhW1NeMX1k/hqdefault.jpg" alt="Warehouse screen" loading="lazy">
    <span class="pj-clip-title">Warehouse <span class="me">my part</span></span>
    <span class="pj-clip-desc">Press 1–8 to read about each crop — but only once you've actually harvested it.</span>
  </a>
  <a class="pj-clip" href="https://youtu.be/HZFAkEU3zGs" target="_blank" rel="noopener">
    <img src="https://i.ytimg.com/vi/HZFAkEU3zGs/hqdefault.jpg" alt="NPC quiz dialogue" loading="lazy">
    <span class="pj-clip-title">Learning through the story</span>
    <span class="pj-clip-desc">NPCs quiz you on farming and fishing; correct answers unlock the next chapter.</span>
  </a>
</div>

## My part

- **Fishing system.** Walk up to the fishing rod and press E; after a random wait an exclamation mark appears, and you have to react in time. A successful catch opens a **math mini-game** — answer correctly to double the catch.
- **Warehouse and crop encyclopedia.** Your harvest is stored in a warehouse, and number keys 1–8 open an introduction to each crop. Entries stay locked until you've grown that crop, so reading becomes a reward for playing.
- **Balancing with expected value.** To make fishing feel fair, I worked out how long it takes on average to catch one specific fish:
  - one full fishing attempt takes 17.5–36.5 s, so about **27 s** on average;
  - assuming a player misses 3 out of 100 bites, each of the 8 fish is equally likely (12.5%), and the bonus gives a 1-in-3 chance of two fish, the expected catch of a specific fish per attempt is 97/100 × 1/8 × (1/3 × 2 + 2/3 × 1) = **97/1200 ≈ 0.081**;
  - so it takes about 27 ÷ 0.081 ≈ **334 seconds**, a bit over five minutes — long enough to matter, short enough not to be a grind.
- **Polish** across the game, and the final presentation.

## Easter eggs

<div class="pj-clips">
  <a class="pj-clip" href="https://youtu.be/CzpGNO5F_XQ" target="_blank" rel="noopener">
    <img src="https://i.ytimg.com/vi/CzpGNO5F_XQ/hqdefault.jpg" alt="UFO cow easter egg" loading="lazy">
    <span class="pj-clip-title">UFO Cow</span>
    <span class="pj-clip-desc">A cow and a flying saucer hidden somewhere on the map.</span>
  </a>
  <a class="pj-clip" href="https://youtu.be/eo9nPp1F4Tw" target="_blank" rel="noopener">
    <img src="https://i.ytimg.com/vi/eo9nPp1F4Tw/hqdefault.jpg" alt="Season aliens easter egg" loading="lazy">
    <span class="pj-clip-title">Season Aliens</span>
    <span class="pj-clip-desc">Seasonal alien characters that show up at certain times.</span>
  </a>
  <a class="pj-clip" href="https://youtu.be/azpwqq54HTs" target="_blank" rel="noopener">
    <img src="https://i.ytimg.com/vi/azpwqq54HTs/hqdefault.jpg" alt="Flying saucer mini-game" loading="lazy">
    <span class="pj-clip-title">Flying Saucer Game</span>
    <span class="pj-clip-desc">A secret flying-saucer mini-game you unlock by exploring.</span>
  </a>
</div>
