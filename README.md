# Committee Selection Optimizer

> Combinatorial optimization project implementing exact (CPLEX/OPL) and metaheuristic (Greedy, Local Search, GRASP) solvers for a constrained committee selection problem.

---

## Problem

Given a pool of **N candidates** distributed across **D departments**, select a committee that:

- Fills exact per-department quotas `n[p]`
- Avoids selecting **incompatible** pairs (`m[i,j] = 0`)
- Enforces a **bridge rule**: two low-compatibility members (`0 < m[i,j] < 0.15`) can only coexist if there is a third member who is highly compatible (`> 0.85`) with both
- **Maximizes** average pairwise compatibility across all committee members

---

## Algorithms

| Solver | Type | Description |
|---|---|---|
| **Greedy** | Constructive | Iteratively picks the candidate with highest average compatibility to current members |
| **Greedy Local Search** | Constructive + Improvement | Greedy construction followed by repeated swap-based local search |
| **GRASP** | Metaheuristic | Multi-restart: randomized greedy construction (RCL with parameter `α`) + local search; returns best solution across all iterations |
| **CPLEX / OPL** | Exact | ILP model solved with IBM CPLEX — provides optimal solution used as benchmark |

### GRASP parameter tuning

`Heuristic/tuning_alpha_script.py` sweeps `α ∈ [0, 1]` across three instance sizes and plots the relative gap to the CPLEX optimum, helping identify the best balance between greediness and randomness.

---

## Project Structure

```
AMMM_project/
├── CPLEX/
│   └── project.template.mod        # ILP model in OPL for IBM CPLEX
├── Heuristic/
│   ├── main.py                     # Entry point — reads config, runs selected solver
│   ├── datParser.py                # Parser for .dat input files
│   ├── config/config.dat           # Choose solver and input file here
│   ├── solvers/
│   │   ├── solver_Greedy.py
│   │   ├── solver_GreedyLocalSearch.py
│   │   └── solver_GRASP.py
│   ├── project_data/               # 8 small test instances
│   └── tuning_alpha_script.py      # GRASP alpha sweep + plot
└── InstanceGenerator/
    ├── Main.py                     # Entry point for generator
    ├── InstanceGenerator.py        # Generates random .dat instances
    └── config/config.dat           # Generator settings (N, num instances, …)
```

---

## Quick Start

### Requirements

- Python 3.8+
- `numpy`, `matplotlib` (only for instance generation and tuning script)
- IBM CPLEX (optional, for exact solver)

```bash
pip install numpy matplotlib
```

### Run a heuristic solver

1. Edit `Heuristic/config/config.dat`:

```
solver = GRASP;                    # Greedy | GreedyLocalSearch | GRASP
inputDataFile = instance50_0.dat;
verbose = true;
```

2. Run from the `Heuristic/` directory:

```bash
cd Heuristic
python main.py
```

**Example output:**
```
Selected Committee: [3, 7, 12, 15, 21, ...]
Objective value: 0.5489
```

### Generate new instances

Edit `InstanceGenerator/config/config.dat`, then:

```bash
cd InstanceGenerator
python Main.py
```

### Tune GRASP alpha

```bash
cd Heuristic
python tuning_alpha_script.py
```

---

## Input Format (`.dat`)

```
D = 9;
N = 40;
n = [ 3 2 2 1 4 2 2 3 3 ];
d = [ 1 1 1 1 2 2 2 2 3 3 ... ];
m = [
  [ 1.00 0.37 0.46 ... ]
  [ 0.37 1.00 0.51 ... ]
  ...
];
```


## Technologies

- **Python 3** — heuristic solvers and instance generator
- **IBM CPLEX + OPL** — exact ILP solver
- **numpy / matplotlib** — numerical generation and result visualization

---

## Authors

Developed as part of the **AMMM (Algorithms and Mathematical Models in Manufacturing)** course project.
