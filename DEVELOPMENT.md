# Development notes

This is a short record of how these resources ended up in their current form. The projects are intentionally narrow, so I wanted to record what was tried, what was dropped, and why the repository did not simply become another starter kit.

## 1. Audit before building

The first pass was mostly looking for repeated friction in the challenge setup. The obvious candidates were:

- a submission-file validator
- a first-submission tutorial
- baseline writers
- a local score wrapper
- an Agent-track evidence packager

I dropped all of those after checking the official `veckit` repository and existing public community tooling. In particular, the public `vec-community-kit` already covers validation, baseline generation, pseudo-validation, Agent evidence packaging, and an English/Chinese walkthrough. A separate `virtual-embryo-cli` covers remote submission from the command line.

That changed the question from “what can I build?” to “what repeated entrant confusion is still unsolved?”

## 2. Metric Lens

The first version simply converted each raw metric to challenge skill and added the published weights. That worked, but it was not very useful: it mostly reproduced arithmetic someone could do by hand.

The useful addition was a simple what-if view: hold every metric fixed, move one metric to its published validation ceiling, and calculate how many total points that change could possibly recover. This does **not** say the metric is easy to improve. It only answers whether improving it is worth much under the public scoring rule.

Before keeping the tool, I added two basic checks that should hold on every supported board:

- published floor values reconstruct to exactly 50/100
- published ceiling values reconstruct to exactly 100/100

I also added a separate test for signed “best at zero” metrics such as `severity_slope`.

Metric Lens stayed as a smaller debugging contribution rather than the main project.

## 3. ML guide

A more foundational gap became clear after auditing the existing setup tutorials.

A new entrant can follow the official challenge docs and existing first-submission walkthroughs all the way to a valid upload while still being confused about the actual learning problem. The missing questions are things like:

- what exactly is one row of the matrix?
- why is there no “same cell later” label?
- why is the target a distribution of cells rather than a regression vector?
- what does adding 3D coordinates change in Task 2?
- why can copying wild type look deceptively good in Task 3?
- what is the smallest modeling step beyond the floor that fixes a real failure mode?

That led to `ml-guide/`. The guide introduces modeling ideas through the failure they fix rather than through architecture names.

## 4. Data Safety Kit

The other gap was less glamorous: the external-data rules are permissive, but nuanced enough that a team can accidentally include protected material from a broad atlas or pretrained model.

A fully automatic “eligibility checker” would be irresponsible because some rules depend on biological judgment. So `data-safety/` is deliberately conservative.

It only automates cases that are mechanically clear from the published rules, such as explicit embryonic-day windows and the exact held-out Task 3 conditions. It returns `ASK_ORGANIZERS` for somite/Theiler mappings, comparable-stage allele questions, and phenocopies.

I also added a source registry + disclosure renderer because the useful workflow is not just “is this source allowed?” but “can I reconstruct exactly what external material entered the method when the final report is due?”

## 5. Related projects split out

Two larger contributions now live separately:

- [Intuition Lab](https://github.com/i-habib/virtual-embryo-community/tree/main/intuition-lab)
- [External Data Catalog](https://github.com/i-habib/external-data-catalog)

This repository keeps the smaller standalone community utilities together instead of making each one look like a separate flagship project.
