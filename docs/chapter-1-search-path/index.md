# Chapter 1: Planning the drone's search path

!!! note "Status"
    Prototype. [Describe how you tested it, e.g., "checked on 1 rectangular field in a simulator / on a map."] See [Limits](../limits.md).

## The problem

A drone can only photograph the ground right under it. To search a field, it has to fly a path that covers every part, with no gaps where a person could be missed. Planning that path by hand is slow and easy to get wrong.

## The idea: a lawnmower path

A **lawnmower path** flies straight lines back and forth across the area, like mowing a lawn. Each line is called a **lane**. At the end of a lane, the drone shifts over and flies the next one in the opposite direction.

![Lawnmower path over the search area](../assets/images/[path-plot].png)

[Add a plot of your planned path from your code.]

## How far apart should the lanes be?

This is the key decision. If lanes are too far apart, there are gaps. If they're too close, the drone wastes battery.

**Step 1: How wide is one photo on the ground?** This is the **footprint**. It depends on flight height and the camera's **field of view (FOV)**, the angle the camera can see:

$$\text{footprint width} = 2 \times \text{height} \times \tan\left(\frac{\text{FOV}}{2}\right)$$

Example: at 60 m with an 80° FOV, one photo covers about 2 × 60 × tan(40°) ≈ 100 m of ground.

**Step 2: Add overlap.** Neighboring lanes should overlap a bit (called **sidelap**), so a person on the edge of one photo is fully inside the next:

$$\text{lane spacing} = \text{footprint width} \times (1 - \text{sidelap})$$

With 20% sidelap: 100 m × 0.8 = 80 m between lanes.

**Step 3: Count the lanes.**

$$\text{number of lanes} = \left\lceil \frac{\text{area width}}{\text{lane spacing}} \right\rceil$$

The ⌈ ⌉ means "round up," because a partial lane is still a lane.

[Check these formulas match your code. Replace the example numbers with your own.]

## The code

The key block (simplified from my notebook):

```python
[Paste the part of your code that computes lane spacing and builds the waypoints.]
```

What it does:

- **[Block 1]:** [e.g., computes the footprint from height and FOV]
- **[Block 2]:** [e.g., computes lane spacing with sidelap]
- **[Block 3]:** [e.g., loops over lanes and adds the start and end **waypoints** (GPS points the drone flies to), flipping direction each lane]

## Results

| Setting | Value |
|---|---|
| Search area | [ ] m × [ ] m |
| Flight height | [ ] m |
| Camera FOV | [ ]° |
| Sidelap | [ ]% |
| Lane spacing | [ ] m |
| Number of lanes | [ ] |
| Total path length | [ ] m |
| Estimated flight time | [ ] min at [ ] m/s |

## What worked

- [e.g., the path covered the whole rectangle with no gaps]

## What didn't, or isn't handled yet

- [e.g., only works for rectangles; real search areas have odd shapes]
- [e.g., no wind, battery, or terrain model]
- [e.g., doesn't check airspace rules or obstacles]

## How AI helped

[Be specific: which parts AI wrote, what you changed, what you can explain, what you don't fully understand yet.]

## Code

- GitHub: [link]
- Colab notebook: [link]

**Next:** [e.g., test on a real field map, or connect to Chapter 2 so each photo is tagged with its location.]
