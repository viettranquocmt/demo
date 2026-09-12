1. Variable 
- We define a boolean variable $x_{i,j}$ to represent the statement $Assign(i, j)$.  
* If $x_{i,j}$ evaluates to $True$, invigilator $i$ is assigned to shift $j$.
* If $x_{i,j}$ evaluates to $False$, invigilator $i$ is not assigned to shift $j$.
2. Availability
* Logic: If invigilator $i$ is busy during shift $j$, they cannot be assigned to that shift.  
* First-order formula: $\forall i\forall j: Busy(i, j) \rightarrow \neg Assign(i, j)$.  
* CNF Translation: Whenever $Busy(i, j)$ is true, we force that $x_{i,j}$ must be false. This forms a unit clause:
$$(\neg x_{i,j})$$
3. Overlap
* Logic: An invigilator cannot be assigned to two different shifts that overlap in time.  
* First-order formula: $\forall i\forall j\forall k: Overlap(j, k) \wedge Assign(i, j) \rightarrow \neg Assign(i, k)$.  
* CNF Translation: This rule dictates that invigilator $i$ cannot be assigned to both shift $j$ and shift $k$ simultaneously. For every pair of overlapping shifts $j$ and $k$, and for every invigilator $i$, we generate a clause containing two negative literals:
$$(\neg x_{i,j} \vee \neg x_{i,k})$$
4. Capacity
Logic: Each shift requires exactly $k$ invigilators. In propositional logic, "exactly $k$" cannot be written as a single simple clause. It is decomposed into two separate bounds: "at most $k$" and "at least $k$". Let $n$ be the total number of available invigilators.  
*At-most-$k$ bound: We must prevent any group of $k+1$ invigilators from being assigned to the same shift. For every possible combination of $k+1$ invigilators out of the total pool $n$, at least one person in that specific group must not be assigned. This generates clauses consisting entirely of negative literals:
$$\bigvee_{m=1}^{k+1} \neg x_{i_m, j}$$
*At-least-$k$ bound: We must ensure that enough people are assigned. If we evaluate any group of $n - k + 1$ invigilators, it is logically impossible for all of them to be unassigned (otherwise, the remaining available pool would be strictly smaller than $k$). Therefore, in every combination of $n - k + 1$ invigilators, at least one person must be assigned. This generates clauses consisting entirely of positive literals:
$$\bigvee_{m=1}^{n-k+1} x_{i_m, j}$$