# Glossary

**Bounding box** — A rectangle the model draws around something it thinks it found.

**Confidence score** — A number from 0 to 1 showing how sure the model is about one box. Not a probability of being right.

**Threshold** — The minimum confidence score I keep. Lower = more boxes, more false alarms. Higher = fewer boxes, more misses.

**False positive** — The model flags a person where there isn't one (e.g., a bush or a bag).

**Missed detection (false negative)** — A real person the model did not flag.

**Ground truth** — The correct answer, marked by a human, used to check the model.

**Inference** — Running a trained model on new images to get predictions.

**YOLO** — "You Only Look Once," a family of fast object-detection models.

**Ultralytics** — The Python library I use to run YOLO models.

**VisDrone** — A public dataset of drone images with labeled people and vehicles. Used to train the model I use.

**Fine-tuning** — Training an existing model a bit more on a specific dataset.

**SAHI** — "Slicing Aided Hyper Inference." Cuts a large image into smaller overlapping tiles, runs the model on each, then merges the results. Helps find small objects.

**Tile / slice** — One small piece of the big image.

**Overlap** — How much neighboring tiles share, so a person on a tile edge isn't cut in half.

**RGB** — A normal color photo (red, green, blue). Not thermal or night imagery.

**Colab** — Google's free online notebook for running Python code.
