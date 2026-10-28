# Limits

Last updated: 2026-10-28

!!! danger "Not for real search-and-rescue use"
    These are proof-of-concept tools. Do not use them to make real search decisions.

## Tool 2 — person detector

- **Tiny test set:** tested on **2 images**, tuned on **1** of them. Results may not repeat on other images.
- **Daylight RGB only:** not tested on night, thermal, fog, smoke, snow, or heavy tree cover.
- **No accuracy claim:** I don't report accuracy yet. I report counts of false positives and missed detections instead.
- **Human in the loop:** the tool flags *likely* people for a person to review. It does not confirm anyone.
- **Training data bias:** the model learned from VisDrone (mostly urban scenes in China). Rural fields and forests may look different to it.
- **Altitude and resolution:** results depend on drone height, camera, and image size. Not tested across these.

## Tool 1 — search planner

- [Add limits: e.g., flat terrain assumed, no wind/battery model, no airspace rules.]

## What would change this page

More test images with ground truth, night/thermal data, and field tests with SAR partners.
