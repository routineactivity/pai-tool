# Place-Based Crime Analysis Tool

A lightweight browser-based tool for **crime concentration, place prioritisation and hotspot prediction evaluation**.

The tool is designed for analysts who have crime counts for a set of spatial units and want to answer one of two questions:

1. **Which places should be prioritised for a place-based intervention?**
2. **How well did places identified in an earlier period predict crime in a later period?**

All analysis runs locally in the browser. CSV data are **not uploaded to a server** by the application.

## Use the tool

Open the GitHub Pages version at:

**https://routineactivity.github.io/pai-tool/**

Alternatively, download the repository and open `index.html` in a web browser.

No Python environment, database or web server is required.

---

## 1. Prioritise places

Use this workflow to identify places with the greatest concentration of crime and estimate the potential study-area impact of reducing crime in those places.

### Required CSV structure

```csv
unit_id,area,crime_count
A001,1.0,18
A002,1.0,9
A003,1.0,4
```

Each row represents one spatial unit.

| Field | Description |
|---|---|
| `unit_id` | Unique identifier for the spatial unit |
| `area` | Area of the spatial unit; all rows must use the same unit of measurement |
| `crime_count` | Number of crimes in the unit |

### Priority rules

Units are ranked by crime density. Priority places can then be selected using:

- **Unit PAI threshold** — default `PAI >= 10`
- **Top X% of study area**
- **Top X% of crime**
- **Top N spatial units**

The PAI threshold is configurable. `PAI >= 10` is a useful default for identifying highly concentrated places, not a universal intervention threshold.

### Outputs

The tool reports:

- crime count and crime density
- unit concentration PAI
- rank
- cumulative crime percentage
- cumulative area percentage
- cumulative PAI and PPAI
- selected priority units
- proportion of all crime contained in priority units
- proportion of the study area covered
- selected-area PAI and PPAI
- crime concentration curve
- downloadable ranked CSV

### Intervention scenarios

For the selected priority places, the tool shows the arithmetic effect of reducing crime by:

- 5%
- 10%
- 15%
- 20%

For example, if priority places contain 20% of all crime, a 10% reduction within those places would be equivalent to a **2% reduction across the whole study area**, assuming everything else remains unchanged.

These scenarios are not causal forecasts. They do not account for displacement, diffusion of benefits, regression to the mean or other changes over time.

---

## 2. Test a prediction

Use this workflow to test whether places identified from an earlier period (**T1**) captured crime in a later period (**T2**).

### Required CSV structure

```csv
unit_id,area,crime_count_t1,crime_count_t2
A001,1.0,18,15
A002,1.0,9,11
A003,1.0,4,2
```

| Field | Description |
|---|---|
| `unit_id` | Unique identifier for the spatial unit |
| `area` | Area of the spatial unit |
| `crime_count_t1` | Crime count in the earlier period |
| `crime_count_t2` | Crime count in the later period |

T1 is used to rank and select the priority places. T2 is then used to evaluate how much future crime those places captured.

### Outputs

The prediction workflow reports:

- T2 hit / capture rate
- spatial coverage
- T1 PAI
- T2 predictive PAI
- T1 and T2 PPAI
- Recapture Rate Index (RRI)
- number of T2 crimes captured
- prospective crime capture curve
- T1 and T2 ranks
- rank change
- crime change
- priority-place stability
- Jaccard overlap between T1 and T2 priority sets
- downloadable results CSV

Each unit is also classified as:

- **Remained priority**
- **Left priority**
- **Entered priority**
- **Not priority**

T2 priority status is calculated independently for descriptive comparison only. It is not used to calculate prospective T2 prediction performance.

---

## Measures

### Hit / Capture Rate

The proportion of crime occurring within the selected places:

`Hit Rate = crime in selected places / total crime`

In the prediction workflow this is the proportion of **T2 crime** captured by places selected using **T1**.

### Predictive Accuracy Index (PAI)

PAI compares crime captured with the proportion of the study area covered:

`PAI = (n / N) / (a / A)`

where:

- `n` = crime in the selected area
- `N` = all crime in the study area
- `a` = selected area
- `A` = total study area

A PAI of `1` represents the study-area average crime density. A PAI of `10` represents a concentration ten times the study-area average.

### Unit concentration PAI

For prioritisation, the tool also calculates PAI for each individual spatial unit:

`Unit PAI = unit crime share / unit area share`

This is useful for comparing relative crime concentration between spatial units. It should not by itself be interpreted as evidence of future predictive accuracy.

### Penalized Predictive Accuracy Index (PPAI)

PPAI provides a compromise between hit rate and PAI:

`PPAI = (n / N) / (a / A)^alpha`

where `0 <= alpha <= 1`.

- `alpha = 0` → PPAI equals hit rate
- `alpha = 1` → PPAI equals PAI

The application allows alpha to be adjusted between 0 and 1.

### Recapture Rate Index (RRI)

For the same places selected from T1:

`RRI = PAI_T2 / PAI_T1`

Interpretation:

- `RRI = 1` — relative concentration is unchanged
- `RRI > 1` — relative concentration increased in T2
- `RRI < 1` — relative concentration decreased in T2

---

## Data validation

The tool checks for common input problems, including:

- missing required columns
- missing `unit_id`
- duplicate `unit_id`
- missing or invalid area values
- zero or negative area
- negative crime counts
- zero total crime

All area values must use the same measurement unit.

---

## Privacy

CSV files are parsed and analysed within the browser using JavaScript. The application does not intentionally upload the supplied data to a backend service.

The page currently loads external JavaScript libraries from a CDN:

- [Chart.js](https://www.chartjs.org/)
- [Papa Parse](https://www.papaparse.com/)

If the application is used in an environment where external network requests are restricted, these libraries can instead be stored locally within the repository.

---

## Running locally

Clone the repository:

```bash
git clone https://github.com/routineactivity/pai-tool.git
cd pai-tool
```

Then open `index.html` in a browser.

For development, you can also serve the folder locally, for example with Python:

```bash
python -m http.server 8000
```

and open:

```text
http://localhost:8000
```

---

## GitHub Pages

The repository can be hosted directly with GitHub Pages because the application is entirely client-side.

In the repository:

1. Open **Settings** → **Pages**.
2. Select **Deploy from a branch**.
3. Select the `main` branch and `/ (root)`.
4. Save.

GitHub Pages will serve the root `index.html` as the application homepage.

---

## References

- Chainey, S., Tompson, L. & Uhlig, S. (2008). *The Utility of Hotspot Mapping for Predicting Spatial Patterns of Crime*. Security Journal, 21, 4–28.
- Penalized Predictive Accuracy Index (PPAI): https://www.mdpi.com/2220-9964/10/9/597
- PAI and Recapture Rate Index comparison: https://www.tandfonline.com/doi/abs/10.1080/07418825.2014.904393
- Additional discussion of prediction evaluation measures: https://www.mdpi.com/2673-4591/39/1/24

---

## Scope

This tool provides descriptive and predictive evaluation measures for aggregated spatial crime data. Results depend on the spatial units, time periods, crime definitions and priority rule chosen by the analyst.

Comparisons should therefore be made using consistent geography and measurement periods wherever possible.
