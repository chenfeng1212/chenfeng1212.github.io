---
title: Timing Fixing by Useful Skew
subtitle: A clock-tree optimizer that fixes every setup and hold violation in the public test cases — while cutting clock-tree area by about 64%
date: 2026-11-01
category: research
detail: true
selected: true
tags: [EDA, Algorithms]
venue: 2026 IC/CAD Contest, Taiwan · Problem D · Award
team: "<span class=\"me\">Li-Cheng Tseng</span>, Li Juin Tan, Yi Cheng Chen, Zhi Qian Xiao (equal contribution) · Advisor: Prof. Jai-Ming Lin"
role: Built the Stage-2 PostGreedy optimizer (team of 4, equal contribution)
recognition: Award, 2026 IC/CAD Contest — final ranking announced at the EDA Workshop, December 2026
summary: >
  Our solution to Problem D (proposed by Realtek): fixing **timing violations** without touching the circuit logic,
  by deciding where to delay the clock. A two-stage greedy optimizer fully fixed setup and hold timing on all five
  public test cases and reduced clock-tree area by about **64%**.
img: /assets/img/projects/useful-skew/thumb.jpg
tech: [C++]
links:
  - label: Code
    url: https://github.com/chenfeng1212/ICCAD_Timing_Fixing_by_Useful_Skew
---

## The problem

As chips get faster, signals have less time to travel between flip-flops, and **timing violations** become one of the hardest parts of the design flow. Fixing them by changing the logic is expensive.

*Useful skew* is the alternative: instead of changing the logic, we **delay the clock** at selected flip-flops by inserting or resizing buffers in the clock tree. A late clock "borrows" time from the next path. The challenge is doing this with as little extra area as possible, for both corners at once — setup time at the slow (SS) corner and hold time at the fast (FF) corner — and within a **10-minute** runtime limit on designs that range from thousands to about a million flip-flops.

## Our approach

The program reads the clock-tree structure, the buffer library, and timing reports for both corners, then runs two optimization routes in parallel and keeps the one with the better contest score.

1. **Start from the smallest legal tree.** Every buffer is first resized to its smallest legal size. This reverses the obvious instinct to add buffers everywhere, and is the main reason the final area is so low.
2. **Stage 1 — fix timing.** Align the target clock arrival of all flip-flops, then rebalance buffer delays with a bottom-up and a top-down pass. For flip-flops that still violate setup time, mark them, **propagate the mark along their timing paths** until it converges, and insert new buffers in front of them. A repair pass then fixes any fanout or drive violations the insertions created.
3. **Stage 2 — PostGreedy refinement (my part).** A greedy local search tries inserting, removing, and resizing buffers, computes how each move changes the contest score, and only accepts moves that improve it. It runs in two rounds: first with weight only on setup time, to guarantee basic timing correctness, then on a weighted mix of setup, hold, and area. This is also where most of the area savings come from — PostGreedy keeps shrinking or removing buffers that are no longer needed.
4. **Time budget by design size.** Each stage gets a time budget based on the number of flip-flops, so the program always finishes within 10 minutes and outputs a legal result.

## Results

On all five public test cases (t0–t4), the WNS and TNS scores for both **setup and hold were 1.0** — every violation fixed — and the clock-tree area dropped by **63.5%–65.2%**.

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/useful-skew/area.jpg' | relative_url }}" alt="Bar chart: clock-tree area after optimization is about 35% of the original on all five test cases" loading="lazy">
</figure>

| Case | Setup WNS (before) | Hold WNS (before) | Area before | Area after |
| --- | --- | --- | --- | --- |
| t0 | −0.529 | −0.109 | 6,997 | −64.0% |
| t1 | −0.402 | −0.080 | 1,653 | −63.5% |
| t2 | −0.302 | −0.031 | 980 | −65.2% |
| t3 | −0.459 | −0.107 | 2,784 | −63.5% |
| t4 | −0.594 | −0.122 | 10,539 | −63.5% |

## What I learned

The hardest part was balancing two goals that pull in opposite directions: fixing timing pushes you to add buffers, but the score punishes area. We only found a good balance after **flipping the starting point** — begin from the smallest legal tree and add only what is needed. It taught me that when an optimization problem feels stuck, changing the frame often matters more than tuning the details.
