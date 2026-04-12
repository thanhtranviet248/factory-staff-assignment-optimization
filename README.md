# Factory Staff Assignment Optimization

This project implements a mixed-integer linear programming (MILP) model to optimize weekly staff assignment in a three-shift manufacturing environment using Gurobi in Python. The model assigns workers to days and shifts such that shift-specific labor demands are satisfied while respecting individual working-time regulations and minimizing total labor utilization.

## 1. Problem description

We consider a factory operating 7 days per week with 3 shifts per day (morning, afternoon, evening). For each day–shift pair, a required number of workers is given (e.g., Monday: $(6,4,3)$; Tuesday: $(5,5,5)$; Wednesday: $(6,4,4)$; Thursday: $(5,5,5)$; Friday: $(5,5,3)$; Saturday: $(6,5,4)$; Sunday: $(5,5,4)$), and the total workforce size is 20 workers.

Each worker is subject to the following rules: 

- A worker can work at most one shift per day.
- Each worker must have at least 2 leave days per week (i.e., at most 5 working days).
- If a worker works the evening shift on day $j$, they are not allowed to work the morning shift on day $j+1$.
- The planning task is to assign workers to day–shift positions such that all daily shift demands are met and the total number of worker–shift assignments is minimized while complying with these labor constraints.

## 2. Mathematical formulation

### 2.1 Sets and indices

We define the following sets:

- The set of workers is $I = \{1,2,\dots,20\}$.
- The set of days in the planning horizon is $J = \{1,2,\dots,7\}$.
- The set of shifts per day is $K = \{1,2,3\}$ (for example, $1$ = morning, $2$ = afternoon, $3$ = evening).
- Indices $i \in I$, $j \in J$, and $k \in K$ denote, respectively, a worker, a day, and a shift.

### 2.2 Parameters

The parameter $d_{jk}$ denotes the required number of workers on day $j$ in shift $k$ for all $j \in J, k \in K$. These values are derived from the production plan for the week.

### 2.3 Decision variable

The binary decision variable $x_{ijk}$ indicates whether worker $i$ is assigned to day $j$ in shift $k$, that is $x_{ijk} \in \{0,1\}$ for all $i \in I, j \in J, k \in K$.

### 2.4 Objective function

The objective is to minimize the total number of worker–shift assignments over the planning horizon, given by \$\min \sum_{i \in I} \sum_{j \in J} \sum_{k \in K} x_{ijk}.$


### 2.5 Constraints

The model includes the following types of constraints:

- Demand satisfaction requires that, for each day–shift pair $(j,k)$, the number of assigned workers equals the required demand, i.e. $\sum_{i \in I} x_{ijk} = d_{jk} \text{ for all } j \in J, k \in K.$
- Daily workload constraints ensure that each worker can work at most one shift per day, i.e. $\sum_{k \in K} x_{ijk} \le 1 \text{ for all } i \in I, j \in J.$
- Weekly workload constraints limit each worker to at most 5 working days per week, which in this setting is equivalent to $\sum_{j \in J} \sum_{k \in K} x_{ijk} \le 5 \text{ for all } i \in I.$
- Rest-period constraints prohibit an evening-to-morning consecutive shift for the same worker, enforced by $x_{ij3} + x_{i(j+1)1} \le 1 \text{ for all } i \in I, j \in \{1,2,3,4,5,6\}.$ - Integrality conditions require $x_{ijk} \in \{0,1\} \text{ for all } i \in I, j \in J, k \in K.$
