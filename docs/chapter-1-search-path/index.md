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
WIDTH_M = float(input("Search area width, east-west, in meters: "))
HEIGHT_M = float(input("Search area height, north-south, in meters: "))
LANE_SPACING_M = float(input("Lane spacing in meters: "))
SPEED_MPS = float(input("Drone speed (m/s): "))
OUTPUT_FILE = "search_route.gpx"
```

- These are inputs, not results. When the program starts, it would ask you to type 5 values: start point (latitude and longitude), search width, height, lane spacing, and speed. It'll be helpful if we can command the drones to search on its own in the future if we have an input function like so.

The very last code lets us set the name of the output file.

```python
METERS_PER_DEG_LAT = 111_320
```

- A constant: we need to convert the GPS degree units to math in meters. 111,320 m per degree of latitude. Therefore, we use this conversion. _ is simply used for readability.

## Step 2: Building the lanes

```python
def make_lawnmower(width, height, spacing):
    lanes = math.ceil(width / spacing) + 1
    points = []
    for i in range(lanes):
        east = min(i * spacing, width)
        if i % 2 == 0:
            points.append((east, 0))
            points.append((east, height))
        else:
            points.append((east, height))
            points.append((east, 0))
    return points

```

- The image one of the SAR team leads showed me for the search areas appeared to be a rectangle. Therefore, we also used rectangles to replicate search areas.
- Lanes account for the number of lanes  = field width / lane spacing, rounded up +1. 
- Spacing realistically would be represented how wide a strip of ground the camera sees minus the overlap. +1 accounts for the beginning of the count. Just like how a 30m fence with post every 10m has 4 posts and 3 gaps. The posts would represent the lanes.
`points = []`: the coordinates go in it. It is empty because the function hasn't calculated any waypoints yet, and the loop fills it.
- `for i in range(lanes)`: :Repeats n + 1 times.
- `east = min(i * spacing, width)`: i * spacing represents how far the lane is from the starting edge. By the time it reaches outside the search area, the min(..., width) picks the smaller number so the last lane gets pulled back to the fields edge.
- `if i % 2 == 0`: :Change drones direction depending on whether it is on a even/odd lane so that the drone doesn't reposition itself facing the same direction every time it clears a lane, it'll be a waste of time having the drone set up its position after every lane.
- Without a flip, the drone will fly all the way across the field to start again = waste of battery and time.
return points : hands back full list of planned way points.


```python
def path_length(points):
    total = 0
    for (x1, y1), (x2, y2) in zip(points, points[1:]):
        total += math.hypot(x2 - x1, y2 - y1)
    return total
```
- Function uses meter waypoints from make_lawnmower i.e. (0,0) & (0,100).
- Starts at total = 0 because no distance has been counted yet.
- x2 - x1, y2- y1: is Pythagoras, representing the distance it moved from point 1 to point 2. East and north points. Sqrt(east^2 + north^2)
- total +=: adding what is already there - like an odometer.
- Final number represents the drone's planned distance since it hasn't flown yet. The main block divided by speed to get estimated flight time.

```python
def path_length(points):
    total = 0
    for (x1, y1), (x2, y2) in zip(points, points[1:]):
        total += math.hypot(x2 - x1, y2 - y1)
    return total
```

- This determines the length of the path. We start with 0 as our total.
- `zip(points, points[1:])` : pairs each waypoint with the next one, so each pair is one leg of the flight.
- The `math.hypot` function turns two changes (east and north values) into one straight line distance.
- The `+=` function acts as an odometer, additional gain. This allows the main block to divide total distance by drone speed.
- Example: (0,0) --> (0,100)  = 100m, (0,100) --> (30,100) = 30m, so the total distance goes from 0 --> 100 --> 130.

```python
def save_gpx(points, filename):
    lines = ['<?xml version="1.0" encoding="UTF-8"?>',
             '<gpx version="1.1" creator="search_pattern.py" '
             'xmlns="http://www.topografix.com/GPX/1/1">',
             ' <rte>',
             ' <name>Lawnmower search</name>']
    for east, north in points:
        lat, lon = to_lat_lon(east, north, START_LAT, START_LON)
        lines.append(f' <rtept lat="{lat:.6f}" lon="{lon:.6f}"/>')
    lines += [' </rte>', '</gpx>']
    with open(filename, "w") as f:
        f.write("\n".join(lines))
```

- `<rte>` indicates where route section starts in the file and </rte> marks where it ends. <name> gives it name.
- `<?xml...>` and `<gpx...>` acts as headers that tells apps that it is a start of GPX file.
- for east, north in points: takes one meter waypoint at a time
- `to_lat_lon(...)` convert it to GPS latitude and longitude
- `lines.append(...)1 adds one <rtept> route point line with those numbers
- `lines += [...]` adds the closing tags `</rte>` and `</gpx>` to end the route and the file.
- `open(filename, "w")` creates the file. `"w"` means write, and it replaces any old file with the same name.
- `"\n".join(lines)` puts each line on its own row.
- We save gpx points so it can be reopened using Google Earth and show the route on a real map or load it.
- 1 decimal place is 11km so rounding to 6 decimal places helps us narrow down the precision down to 10cm

```python
if __name__ == "__main__":
    route = make_lawnmower(WIDTH_M, HEIGHT_M, LANE_SPACING_M)
    distance = path_length(route)
    minutes = distance / SPEED_MPS / 60
    area_hectares = math.pi * RADIUS_M**2 / 10_000

    save_gpx(route, OUTPUT_FILE)
    print(f"Area: {area_hectares:.1f} hectares")
    print(f"Waypoints: {len(route)}")
    print(f"Path length: {distance:.0f} m")
    print(f"Estimated flight time: {minutes:.1f} minutes")
    print(f"Saved route to {OUTPUT_FILE}")
```

Explain here.
