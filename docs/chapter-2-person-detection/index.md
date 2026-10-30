# Chapter 2: Finding people in the drone's photos

!!! note "Status"
    Prototype. Tested on 2 daylight RGB images, tuned on 1. See [Limits](../limits.md).

## The problem

The drone from [Chapter 1](../chapter-1-search-path/index.md) comes back with many photos. Each one can cover a whole field, but a person in it may be only a few pixels wide. A human looking through every photo is slow and gets tired.

## What this tool does

It scans each photo and draws a box around anything that looks like a person, so the human reviewer knows where to look first. **The human still decides.**

It uses three building blocks:

- **Ultralytics YOLO**: a fast object detector
- **VisDrone**: a public set of drone photos with labeled people, used to train the model
- **SAHI**: cuts big photos into smaller tiles so small people are easier to spot

## Write-ups in this chapter

- [Finding people in drone images: what worked and what didn't](../blog/posts/2026-10-finding-people-in-drone-images.md)

## Results so far

| Image | Method | Flagged | False positives | Missed |
|---|---|---|---|---|
| A (tuned on) | Whole image | [ ] | [ ] | [ ] |
| A (tuned on) | SAHI | [ ] | [ ] | [ ] |
| B (not tuned) | SAHI | [ ] | [ ] | [ ] |

## Credits

- VisDrone dataset: Zhu et al., *Detection and Tracking Meet Drones Challenge*, IEEE TPAMI 2021.
- SAHI: Akyon et al., *Slicing Aided Hyper Inference and Fine-tuning for Small Object Detection*, ICIP 2022.
- Ultralytics YOLO.

## Code

- GitHub: [link]
- Colab notebook: [link]
