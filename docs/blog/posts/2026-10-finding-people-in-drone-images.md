---
date: 2026-10-28
categories:
  - Project write-ups
---

# Finding people in drone images: what worked and what didn't

A drone photo taken from high up can cover a whole field, but a person in it may be only a few pixels wide. I built a small tool to flag likely people for a human to review. Here's what worked, what didn't, and what I still don't know.

<!-- more -->

!!! note "Status"
    Prototype. Tested on 2 daylight RGB images, tuned on 1. See [Limits](../../limits.md).

## The problem

In search and rescue (SAR), a drone can photograph a large area fast. The slow part is a person staring at hundreds of images looking for someone small. My goal: **flag spots worth a second look**, so the human reviews fewer areas. The human still decides.

## What I used

- **Ultralytics** — a Python library that runs YOLO, a fast object detector.
- **VisDrone** — a public dataset of drone images with labeled people. The model I used was trained on it, so it has seen people from above.
- **SAHI** — cuts a big image into smaller overlapping tiles, runs the detector on each, then merges the boxes.

New terms are in the [Glossary](../../glossary.md).

## Attempt 1: run the detector on the whole image

[Describe what happened. Example: the model shrinks the image to fit its input size, so small people become a few blurry pixels and get missed.]

**Result on image A:** [X] people found, [Y] false positives, [Z] missed.

## Attempt 2: slice the image with SAHI

The key block (simplified from my notebook):

```python
from sahi import AutoDetectionModel
from sahi.predict import get_sliced_prediction

model = AutoDetectionModel.from_pretrained(
    model_type="ultralytics",
    model_path="visdrone_model.pt",   # [replace with my model]
    confidence_threshold=0.3,         # [my value]
)

result = get_sliced_prediction(
    "drone_image.jpg",
    model,
    slice_height=640, slice_width=640,                  # tile size
    overlap_height_ratio=0.2, overlap_width_ratio=0.2,  # 20% overlap
)
```

What it does:

- **The model block** loads the detector and sets the **threshold**: boxes below this confidence are dropped.
- **The slice block** cuts the image into 640×640 tiles. Each tile is closer to the size the model expects, so small people stay big enough to see.
- **The overlap** (20%) means a person on a tile edge appears in two tiles and isn't cut in half. SAHI then merges duplicate boxes.

[Replace with my real code and values. Note which parts AI helped write.]

## Results

| Image | Method | Flagged | False positives | Missed |
|---|---|---|---|---|
| A (tuned on) | Whole image | [ ] | [ ] | [ ] |
| A (tuned on) | SAHI | [ ] | [ ] | [ ] |
| B (not tuned) | SAHI | [ ] | [ ] | [ ] |

![Before and after: whole-image detection vs. SAHI tiles](../../assets/images/before-after.jpg)

**Image B matters most.** I tuned settings on image A, so A's results are partly "seen the answer." B is the only fairer check, and one image is far too few to judge.

## What worked

- [e.g., slicing found small people the whole-image run missed]

## What didn't

- **False positives:** [e.g., shadows, bags, posts flagged as people]
- **Missed detections:** [e.g., person partly under trees]
- [Speed: slicing runs the model many times, so it's slower — X seconds vs Y]

## What I don't know yet

- How it does on more than 2 images.
- Night, thermal, smoke, or forest cover — untested.
- Whether my threshold is a good trade-off between misses and false alarms. In SAR, a miss is usually worse than a false alarm, but too many false alarms waste reviewer time.

## Credits

- VisDrone dataset — Zhu et al., *Detection and Tracking Meet Drones Challenge*, IEEE TPAMI 2021.
- SAHI — Akyon et al., *Slicing Aided Hyper Inference and Fine-tuning for Small Object Detection*, ICIP 2022.
- Ultralytics YOLO.

## Code

- GitHub: [link]
- Colab notebook: [link]

**Next:** test on more labeled images and write up the results, good or bad.
