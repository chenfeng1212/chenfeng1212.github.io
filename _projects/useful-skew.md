---
title: Timing Fixing by Useful Skew
subtitle: A clock-tree optimizer, reworked over five rounds of progress reports, that fixes every setup and hold violation in the public test cases while cutting clock-tree area by about 64%
date: 2026-11-01
period: 2026.04 – 2026.11
category: research
detail: true
selected: true
tags: [EDA, Algorithms]
venue: 2026 IC/CAD Contest, Taiwan · Problem D · Excellent Award
team: "<span class=\"me\">Li-Cheng Tseng</span>, Li Juin Tan, Yi Cheng Chen, Zhi Qian Xiao (equal contribution) · Advisor: Prof. Jai-Ming Lin"
role: Built the Stage-2 PostGreedy optimizer (team of 4, equal contribution)
recognition: Excellent Award (特優, top prize), 2026 IC/CAD Contest — presented at the EDA Workshop, December 2026
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

*Useful skew* is the alternative: instead of changing the logic, we **delay the clock** at selected flip-flops by inserting or resizing buffers in the clock tree, so a late clock "borrows" time from the next path. The catch: every change moves many paths at once, it has to work for **setup time at the slow (SS) corner and hold time at the fast (FF) corner** together, extra buffers cost area, and the program must finish within **10 minutes** on designs with up to about a million flip-flops.

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/useful-skew/concept.jpg' | relative_url }}" alt="Diagram: a new buffer delays the clock to the capture flip-flop, so the data path meets its deadline" loading="lazy">
  <figcaption>Useful skew in one picture: delaying the clock at the capture flip-flop moves its deadline later.</figcaption>
</figure>

## How our approach evolved

We gave a progress report every few weeks, and each round of results changed the design.

<ol class="pj-timeline">
  <li>
    <span class="pj-tl-date">May 2026</span>
    <strong>Dynamic programming on the clock tree.</strong>
    We adapted the classic Van Ginneken buffer-insertion algorithm to two timing corners, tracking delay and area per option and keeping only Pareto-optimal states.
  </li>
  <li>
    <span class="pj-tl-date">Jun 2026</span>
    <strong>Target scheduling + bounded DP.</strong>
    We split the problem: first compute the ideal clock arrival time of every flip-flop as a longest-path problem on two constraint graphs (solved with SPFA, a fast Bellman–Ford), then use a bottom-up DP to make the tree match those targets. Because the contest's score weights were hidden, we picked the final tree by <em>scenario voting</em> — a Borda count over several plausible weightings.
  </li>
  <li>
    <span class="pj-tl-date">Jul 2026</span>
    <strong>The DP hit a wall.</strong>
    Alpha tests showed that averaging targets inside each subtree threw away per-flip-flop information, and the SS corner dominated the score. We moved to a greedy search that repairs critical paths directly, with incremental slack updates.
  </li>
  <li>
    <span class="pj-tl-date">Aug 2026</span>
    <strong>Smallest tree + greedy refinement.</strong>
    We started every run from the smallest legal clock tree and built the greedy post-optimizer that became the final stage of our submission. We also tried a force-directed ("spring") model for target arrival times, but replaced it before the final submission.
  </li>
  <li>
    <span class="pj-tl-date">Nov 2026</span>
    <strong>Wire-aware version.</strong>
    An extension proposed by our advisor's lab: every clock wire now adds delay (see below).
  </li>
</ol>

## The final pipeline

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/useful-skew/pipeline.jpg' | relative_url }}" alt="Pipeline: parse, smallest tree, Stage 1 with routes P1 and P2, PostGreedy, keep the best" loading="lazy">
</figure>

1. **Start from the smallest legal tree.** Every buffer is first resized to its smallest legal size. This reverses the obvious instinct to add buffers everywhere, and is a big reason the final area is so low.
2. **Stage 1 — fix timing.** Align the target clock arrival of all flip-flops, then rebalance buffer delays with a bottom-up and a top-down pass. For flip-flops that still violate setup time, mark them, **propagate the mark along their timing paths** until it converges, and insert new buffers in front of them. A repair pass then fixes any fanout violations the insertions created.
3. **Stage 2 — PostGreedy refinement (my part).** A greedy local search that only accepts moves that improve the exact contest score — first weighting only setup time, then a mix of setup, hold, and area (details below). It runs on two candidate trees and the better one is kept.
4. **Time budget by design size.** Each stage gets a time budget based on the number of flip-flops, so the program always finishes within 10 minutes and outputs a legal result.

## My part: the PostGreedy optimizer

PostGreedy takes the tree from Stage 1 and keeps polishing it until time runs out. What made it work:

- **Exact, incremental scoring.** It maintains the total buffer area, total negative slack, and a sorted multiset of all negative slacks, so the effect of a move on the real contest score can be computed without re-running timing analysis. A move is committed only if the score strictly improves; otherwise it is rolled back.
- **Constant-time subtree checks.** Delaying a buffer shifts the clock for its whole subtree, which changes only the paths that cross the subtree boundary. Using DFS entry/exit times, it decides in O(1) whether a flip-flop is inside the subtree and updates only those paths.
- **Four kinds of moves:**
  - remove newly inserted buffers that drive a single sink;
  - remove or re-parent **split buffers** (inserted buffers with two or more sinks) — profiling showed none of our earlier passes touched them, leaving a permanent area overhead;
  - **resize** buffers to smaller cells;
  - **repair** remaining violating paths by inserting the best-sized buffer before the capture flip-flop.

## Results

On all five public test cases (t0–t4), the WNS and TNS scores for both **setup and hold were 1.0** — every violation fixed — and the clock-tree area dropped by **63.5%–65.2%**.

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/useful-skew/area.jpg' | relative_url }}" alt="Bar chart: clock-tree area after optimization is about 35% of the original on all five test cases" loading="lazy">
</figure>

| Case | Setup violations (before) | Setup WNS (before) | Hold WNS (before) | Area before | Area after |
| --- | --- | --- | --- | --- | --- |
| t0 | 18,737 paths | −0.529 | −0.109 | 6,997 | 2,516 (−64.0%) |
| t1 | 4,292 paths | −0.402 | −0.080 | 1,653 | 604 (−63.5%) |
| t2 | 2,156 paths | −0.302 | −0.031 | 980 | 341 (−65.2%) |
| t3 | 6,942 paths | −0.459 | −0.107 | 2,784 | 1,017 (−63.5%) |
| t4 | 32,250 paths | −0.594 | −0.122 | 10,539 | 3,846 (−63.5%) |

After optimization, every case has zero setup and zero hold violations.

## Follow-up: when wires have delay

After submitting to the contest, our advisor's lab gave us a harder extension of the problem: every wire segment in the clock tree now adds delay proportional to its length squared, and inserting a buffer must split a wire so the segment lengths add up to the original. This breaks a basic assumption — **one more buffer no longer means one buffer's worth of delay**, because splitting a wire also *reduces* wire delay. We added wirelength-aware rules for adding, merging, and deleting buffers.

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/useful-skew/wire_delay.jpg' | relative_url }}" alt="Line chart: with wire delay, each inserted buffer adds less delay than in the original problem" loading="lazy">
</figure>

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/useful-skew/wire_paths.jpg' | relative_url }}" alt="Bar chart on a log scale: setup-violating paths drop by more than 90% on every test case" loading="lazy">
</figure>

| | Before | After |
| --- | --- | --- |
| Setup violating paths (all 5 cases) | 68,928 | 4,452 (−93.5%) |
| Setup TNS | all 5 cases violated | −99.7% on average |
| Hold violating paths | 850 | 0 |
| Clock-tree area | original trees | −62.8% on average |

All five outputs passed the official legality checker.

## What I learned

The biggest lesson was how much the *framing* mattered. Our first two designs were elegant on paper — DP with Pareto pruning, target scheduling with graph algorithms — but the data told us they lost too much information. Things improved fastest when we **changed the starting point** (the smallest legal tree) and **measured the real score at every step** instead of trusting estimates. Building PostGreedy taught me to care about incremental data structures: the difference between recomputing timing and updating it in O(1) is what lets a greedy search explore thousands of moves within the time limit.
