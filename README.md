# Factory Staff Assignment Optimization

This project implements a mixed-integer linear programming (MILP) model to optimize weekly staff assignment in a three-shift manufacturing environment using Gurobi in Python. The model assigns workers to days and shifts such that daily shift-specific labor demands are satisfied while respecting individual working-time regulations and minimizing total labor utilization.

## 1. Problem description

We consider a factory operating 7 days per week with 3 shifts per day (morning, afternoon, evening). For each day–shift pair, a required number of workers is given (e.g., Monday: $(6,4,3)$; Tuesday: $(5,5,5)$; …; Sunday: $(5,5,4)$), and the total workforce size is 20 workers.

Each worker is subject to the following constraints: (i) a worker can work at most one shift per day; (ii) each worker must have at least 2 leave days per week (i.e., at most 5 working days); (iii) if a worker works the evening shift on day $j$, they are not allowed to work the morning shift on day $j+1$. The planning task is to assign workers to day–shift positions such that all daily shift demands are met and the total number of worker–shift assignments is minimized, while complying with these labor rules.

## 2. Mathematical formulation

### Sets

- $I$ is the set of workers, $I = \{1, 2, 3, \dots, 20\}$.
- $J$ is the set of days in the planning horizon, $J = \{1, 2, 3, \dots, 7\}$.
- $K$ is the set of shifts in a day, $K = \{1, 2, 3\}$ (e.g., 1 = morning, 2 = afternoon, 3 = evening).

### Indices and parameters

- $i \in I$ indexes workers.
- $j \in J$ indexes days.
- $k \in K$ indexes shifts.
- $d_{jk}$ is the required number of workers on day $j$ in shift $k$.

### Decision variable

- $x_{ijk}$ is a binary variable indicating whether worker $i$ is assigned to day $j$ in shift $k$, i.e. $x_{ijk} \in \{0,1\}$ for all $i \in I$, $j \in J$, $k \in K$.

### Objective function

Minimize the total number of worker–shift assignments over the planning horizon:
\[
\min \sum_{i \in I} \sum_{j \in J} \sum_{k \in K} x_{ijk}.
\]

### Constraints

1. **Demand satisfaction:** For each day–shift pair $(j,k)$, the number of assigned workers must match the required demand:
\[
\sum_{i \in I} x_{ijk} = d_{jk}
\quad \forall j \in J, k \in K.
\]

2. **At most one shift per day per worker:** Each worker can work at most one shift per day:
\[
\sum_{k \in K} x_{ijk} \le 1
\quad \forall i \in I, j \in J.
\]

3. **Maximum five working days per week:** Since a worker can work at most one shift per day, limiting them to at most 5 shifts per week enforces at least 2 leave days:
\[
\sum_{j \in J} \sum_{k \in K} x_{ijk} \le 5
\quad \forall i \in I.
\]

4. **No evening-to-morning consecutive shift:** If a worker $i$ works the evening shift (shift 3) on day $j$, they cannot work the morning shift (shift 1) on day $j+1$:
\[
x_{ij3} + x_{i(j+1)1} \le 1
\quad \forall i \in I, \forall j \in \{1, 2, 3, 4, 5, 6\}.
\]

5. **Integrality:** All decision variables are binary:
\[
x_{ijk} \in \{0,1\}
\quad \forall i \in I, j \in J, k \in K.
\]
