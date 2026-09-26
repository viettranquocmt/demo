* Decision Variables
  Let $x_{i,j} \in \{0, 1\}$ be a binary variable where $x_{i,j} = 1$ if invigilator $i$ is assigned to shift $j$, and $0$ otherwise.

* 1. Capacity Constraint
  * Description: Each shift $j$ must be assigned exactly $k_j$ invigilators.
  * Equation:
    $$\sum_{i \in I} x_{i,j} = k_j \quad \forall j \in J$$

* 2. No Double-Booking (Overlap) Constraint
  * Description: An invigilator $i$ cannot be assigned to two overlapping shifts ($j$ and $k$) simultaneously.
  * Inequality:
    $$x_{i,j} + x_{i,k} \le 1 \quad \forall i \in I, \forall (j,k) \in Overlap$$

* 3. Availability Constraint
  * Description: If invigilator $i$ is marked as busy during shift $j$, they cannot be assigned to that shift.
  * Equation:
    $$x_{i,j} = 0 \quad \forall i \in I, \forall j \in J \text{ where } Busy(i, j) \text{ is True}$$