# Berlin Tram Network — Graph Coloring

Minimum graph coloring of the Berlin tram network using two approaches:
a **greedy algorithm** and a **MILP formulation** solved with CBC.

---

## Problem

The Berlin tram network has **22 routes**:
`M1, M2, M4, M5, M6, M8, M10, 12, M13, 16, M17, 18, 21, 27, 37, 50, 60, 61, 62, 63, 67, 68`

Two routes **conflict** if they share a stop or overlap in service area.
The goal is to assign a **minimum number of colors** (time slots / frequencies) to the routes such that no two conflicting routes share the same color — a classic **minimum graph coloring** problem.

---

## Files

| File | Description |
|------|-------------|
| `berlin-tram-2024 (5).csv` | 22×22 conflict matrix (upper-triangular, with route labels) |
| `code_berlin.ipynb` | Greedy graph coloring — fast, no solver required |
| `berlin_tram_graph_coloring_pyomo_cbc.ipynb` | Optimal MILP solution using Pyomo + CBC solver |
| `result.xlsx` | Output: route-to-color assignment |
| `berlin-tram-2024-grey.pdf` | Original tram network reference document |

---

## CSV Format

The conflict matrix CSV (`berlin-tram-2024 (5).csv`) has:
- **Row 0**: `route` label header
- **Row 1**: Column route name headers (`M1, M2, ...`)
- **Rows 2–23**: One row per route; a cell value of `1` indicates a conflict between the row route and the column route

Only the **upper triangle** is filled; the matrix is symmetric.

---

## Approaches

### 1. Greedy Coloring — `code_berlin.ipynb`

A sequential greedy algorithm assigns each route the lowest-indexed color that does not conflict with already-assigned routes.

**No external solver needed.**

```
Route: M1     Color: Yellow
Route: M2     Color: Yellow
Route: M4     Color: Green
...
Total colors used: 7
```

### 2. Optimal MILP — `berlin_tram_graph_coloring_pyomo_cbc.ipynb`

A **Binary MILP** formulated in [Pyomo](http://www.pyomo.org/) and solved with [CBC](https://github.com/coin-or/Cbc):

**Variables:**
- `x[r, c] = 1` if route `r` is assigned color `c`
- `y[c] = 1` if color `c` is used

**Objective:** Minimize `Σ y[c]`

**Constraints:**
1. Every route receives exactly one color
2. Conflicting routes cannot share a color
3. A color is active whenever any route uses it

---

## Results

| Method | Colors Used |
|--------|-------------|
| Greedy | 7 |
| MILP (Optimal) | 7 |

**Color assignments:**

| Color | Routes |
|-------|--------|
| Yellow | M1, M2, 16, 18, 37, 61 |
| Green | M4, M17, 50, 62 |
| Blue | M5, 12, 21, 63 |
| Orange | M6, 60 |
| Pink | M8, 67 |
| Grey | M10, 27, 68 |
| Red | M13 |

---

## Setup

### Requirements

```bash
pip install pyomo pandas numpy matplotlib openpyxl
brew install cbc          # macOS
# or: sudo apt install coinor-cbc   # Ubuntu/Debian
```

### Run

Open either notebook in JupyterLab / VS Code and run all cells:

```bash
jupyter lab
```

---