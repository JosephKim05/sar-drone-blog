# Chapter 2: Finding people in drone photos

## The problem

Drones can photograph a search area in minutes, but finding a tiny person in hundreds of photos is slow, so this tool flags the photos most likely to show someone, so the SAR team knows where to look first.

## Pipeline

| Step | Where | Link |
|---|---|---|
| Train VisDrone model | Colab | |
| Train HERIDAL model | Colab | |
| Run detection | My laptop | |

## Step 1: Plain YOLO (`detect.py`)

```python
from ultralytics import YOLO

model = YOLO("yolov8n.pt")
results = model("images/test2.jpg", classes=[0], conf=0.15, imgsz=1280, iou=0.5)
print("People found:", len(results[0].boxes))
results[0].save("outputs/out.jpg")
```
- `print("People found:", len(results[0].boxes))`  Counts the number of boxes detected. The boxes each have a confidence score from 0 to 1 and each box marks a suspected person. YOLO returns a list of results, so `[0]` picks the first and only result. Since python counts from 0, the first item is 0. Some boxes may be on bushes or shadows, and some people may be missed.
- `results = model("images/test2.jpg", classes=[0], conf=0.15, imgsz=1280, iou=0.5)` if the model knows 80 kind of objects, class 0 represents person in COCO (dataset this model learned from) it is trying to find. Trade off: 0.15 is low, bringing more false alarms. SAR uses this as a missing person is worse than a false alarm. Boxes below 0.15 are dropped and don't get to have a box created around it. The model shrinks every photo to this size before looking at it. Bigger size images keeps more pixels, allowing a tiny hiker stay big enough for recognition. The program runs slower, however. If two boxes over lap more than 50%, the model keeps the one with the higher confidence and removes the other.
`from ultralytics import YOLO`
- It loads the YOLO tool from Ultralytics library.
`model = YOLO("yolov8n.pt")`
- n in yolov8n represents nano. The speed is fastest, but has the lowest accuracy. I started with the smallest model because I was running the tests on a laptop, not a GPU. There are bigger sizes for yolov8 that runs more accurate, but slower.
`results[0].save("outputs/out.jpg")`
- It saves one image, with the boxes and confidence scores drawn on it.

## Step 2: Slicing with SAHI (`detect_sahi.py`)
```
from sahi import AutoDetectionModel
from sahi.predict import get_sliced_prediction

model = AutoDetectionModel.from_pretrained(
    model_type="ultralytics",
    model_path="visdrone_yolov8s.pt",
    confidence_threshold=0.2,
    device="cpu",
)

result = get_sliced_prediction(
    "images/test4.jpg",
    model,
    slice_height=640,
    slice_width=640,
    overlap_height_ratio=0.4,
    overlap_width_ratio=0.4,
)

result.object_prediction_list = [
    p for p in result.object_prediction_list if p.category.id in (0,1)
]

scores = sorted((p.score.value for p in result.object_prediction_list), reverse=True)
print(scores[:10])

print("People found:", len(result.object_prediction_list))
result.export_visuals(export_dir="outputs/", file_name="sahi_s_test4_192_020")
```

## Step 3: Scale-aware detection (`detect_map.py`)

## Step 4: Results

### Development images (tuned on these)

### Fair test (settings locked before running)

## Step 5: Training on top-down SAR photos (HERIDAL)
## Step 5: Training on top-down SAR photos (HERIDAL)

Ran in Google Colab on a free T4 GPU. [Notebook link]

### Cell 1: Setup
```python
paste cell 1
```
Explain here.

### Cell 2: Download HERIDAL
### Cell 3: Convert labels and cut tiles
### Cell 4: Train
### Cell 5: Test on unseen photos

## Lessons learned

## Limits

## How AI helped

## Credits

## Code
