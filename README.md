# Camera Calibration & Perspective Measurement

CSc 8830 Computer Vision — Module 2 assignment. Camera calibration with OpenCV,
then using that calibration to pull real-world 2D measurements out of a plain
photo, and finally validating how accurate that turns out to be over 20
objects.

Everything below can be run either from the command line or from the small
web app included in this repo (`app.py`) — the app is just a thin UI
wrapper around the same three scripts.

## Directory

| File | Purpose |
|---|---|
| `calibration.py` | Step 1 — OpenCV chessboard calibration from a folder of phone photos |
| `dimensions.py` | Step 2 — turns a pixel measurement + known distance into a real-world length |
| `validate.py` | Step 3 — error statistics (mean/RMSE/% error) over the 20-object validation set |
| `app.py` + `templates/` | Web app tying the three steps together in a browser |




## Setup- Virtual Env and install packages

```bash
python -m venv venv
source venv/bin/activate        # venv\Scripts\activate on Windows
pip install -r requirements.txt
```

## Step 1 — Calibration

Print a chessboard 
and take 15–25 photos with your phone from different angles and distances.
. Save
photos in `calibration_images/` and run:

```bash
python calibration.py --images calibration_images --cols 10 --rows 7 --square 25.0
```

This prints the camera matrix (fx, fy, cx, cy), the distortion coefficients,
and the mean reprojection error, and saves everything to `camera_calib.npz`
for the next step to use.


## Step 2 — Measuring an object

```bash
python dimensions.py --image object_images/item1.jpg --distance 2500 --calib camera_calib.npz
```

`--distance` is how far the camera was from the object plane, in mm. A
window pops up — click the two endpoints of the edge you want measured and
press any key. The projection formula:

```
L = (pixel_distance x Z) / f
```

where `f` is the calibrated focal length in pixels (fx or fy) and `Z` is the
distance you supplied.

## Step 3 — Validating accuracy

Measure 20 different objects at a known distance (anything past 2 m ), record the real length with a tape measure, and record what
`dimensions.py` computed for each. Fill those into a CSV:

```
object_id,actual_mm,measured_mm,distance_mm
book_width,180,176.4,2500
...
```

then run:

```bash
python validate.py --csv my_measurements.csv --out results
```

This writes `results/validation_summary.txt` (mean error, RMSE, % error,
std. dev.), `results/validation_detail.csv`, and two plots comparing actual
vs. measured length and per-object percent error.

## Running the web app

```bash
python app.py
```

Then open `http://127.0.0.1:8000`. 