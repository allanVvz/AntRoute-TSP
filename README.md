# AntRoute‑TSP

Ant Colony Optimization (ACO) for the Traveling Salesman Problem (TSP), with results plotted over a background map (e.g., Brazil or US). The project is notebook‑first and includes utilities to convert latitude/longitude to image pixel coordinates and visualize the resulting route.

## Quick Start

1. Create a virtual environment and install dependencies:

   - Windows
     ```bash
     python -m venv .venv
     .venv\Scripts\activate
     pip install -r requirements.txt
     ```

   - Linux/macOS
     ```bash
     python3 -m venv .venv
     source .venv/bin/activate
     pip install -r requirements.txt
     ```

2. Launch Jupyter and open the main notebook:
   ```bash
   jupyter lab   # or: jupyter notebook
   ```
   Open and run: `AntTRip.ipynb`

## Repository

- `AntTRip.ipynb`: main ACO solver, experiments, and plotting.
- `Computação_Natural_Vídeo_aula_6.ipynb`: reference/teaching notebook used for comparison.
- `requirements.txt`: pinned Python dependencies (Jupyter/NumPy/Matplotlib).
- `trip.png`, `united_states_map.png`: example background maps.
- `gmap.png`, `img.png`: additional images for experimentation.

## How It Works

- **Solver**: `AntColonySolver` implements ACO for TSP‑like paths.
- **Runner**: `AntColonyRunner(cities, **kwargs)` configures the solver, runs it, prints stats, and optionally plots.
- **Plotting**: `show_cities()` and `show_path()` overlay points and the best route on the selected map image.
- **Geospatial**: `latlon_to_pixel(...)` converts (lat, lon) to image pixel coordinates given map bounds and image size.

## Usage Example

```python
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.image as mpimg

# Load map
map_image = mpimg.imread('trip.png')
image_height, image_width = map_image.shape[:2]

# Geographical bounds for your map image
min_latitude, max_latitude   = -41.0, 14.0
min_longitude, max_longitude = -91.0, -24.0

def latlon_to_pixel(lat, lon, w, h, min_lat, max_lat, min_lon, max_lon):
    x = (lon - min_lon) / (max_lon - min_lon) * w
    y = h - ((lat - min_lat) / (max_lat - min_lat) * h)
    return x, y

# Your places (name -> (lat, lon))
places = {
    "Example A": (-13.1, -72.5),
    "Example B": ( -3.4, -62.2),
    "Example C": (-24.7, -69.1),
}

# Convert to image coordinates
image_places = {
    name: latlon_to_pixel(lat, lon, image_width, image_height,
                          min_latitude, max_latitude, min_longitude, max_longitude)
    for name, (lat, lon) in places.items()
}

# Prepare cities list for the solver (keeps names)
places_list = list(image_places.items())

# Run and plot
result, ants_used, epochs_used, path_cost = AntColonyRunner(
    places_list, distance_power=1.25, pheromone_power=5, verbose=True, plot=True
)
```

## Key Parameters

- `distance_power`: weight of distance heuristic (higher emphasizes shorter steps).
- `pheromone_power`: sensitivity to pheromone differences.
- `decay_power`: pheromone evaporation rate.
- `reward_power`: relative reinforcement by path quality.
- `best_path_smell`: extra pheromone when a new best path is found.
- `ant_count`, `ant_speed`: number of ants and step speed per epoch.
- `min_round_trips`/`max_round_trips`, `time`/`timeout`: stopping criteria.

## Plotting Notes

- **Background selection**: plotting functions prefer `map_image` if defined, then `Brazil_map` or `united_states_map`.
- **Coordinate systems**: images use `origin='upper'` (Y increases downward). If your points are in Cartesian Y, call `plt.gca().invert_yaxis()` in `show_path`.
- **Labels**: when cities are provided as `[(name, (x, y)), ...]`, names can be annotated next to the points.

## Troubleshooting

- No line, only points: ensure `show_path` uses a visible line style (e.g., `'-o'`) with `zorder` above the image and call `plt.show()`.
- TypeError unpacking starting city: pass coordinates as `(x, y)` or `(name, (x, y))`; the notebook includes a robust extractor for both.
- “Truth value of an array is ambiguous”: avoid `A or B` with NumPy arrays; the plotting code selects the background without boolean chaining.

