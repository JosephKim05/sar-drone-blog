# Chapter 1: Planning the drone's search path

## The problem
After interviewing SAR team leads at Illinois there has been several key issues identified:
- Lack of volunteers
- Time issue narrowing done search areas

The current breakdown of chain of command: 
Command centers give designated search areas to local SAR teams -> SAR teams search areas -> come back to command center to find a new search area.
Inevitably SAR teams (K9 dogs & SAR volunteers) are irreplaceable. However, by using autonomous drones trained by AI models, we can create autonomously narrow down search areas by pre-scanning search areas lost people may be detected at.

This section is dedicated in creating flight patterns for the drone to scan an area beforehand.

## Step 1: Inputs

```python
import math
```

Allows us to use built in math functions such as math.cos, math.ceil, math.pi and more

```python
START_LAT = float(input("Start latitude: "))
START_LON = float(input("Start longitude: "))
RADIUS_M = float(input("Search radius in meters: "))
LANE_SPACING_M = float(input("Lane spacing in meters: "))
SPEED_MPS = float(input("Drone speed (m/s): "))
OUTPUT_FILE = "search_route.gpx"
```

These are inputs, not results. When the program starts, it would ask you to type 5 values: start point (latitude and longitude), search radius, lane spacing, and speed. It'll be helpful if we can command the drones to search on its own in the future if we have an input function like so.

The very last code lets us set the name of the output file.

```python
METERS_PER_DEG_LAT = 111_320
```

A constant: we need to convert the GPS degree units to math in meters. 111,320 m per degree of latitude. Therefore, we use this conversion. _ is simply used for readability.

## Step 2: Building the lanes

```python
paste make_lawnmower here
```

Explain here.
