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
* Logic: A shift $j$ needs *exactly* $k$ invigilators out of the total $n$ available staff[cite: 1]. Because CNF format only understands basic AND/OR logic, we cannot write "exactly $k$" directly. Instead, we must break it down into two simpler rules: **"Maximum $k$"** and **"Minimum $k$"**[cite: 2].

* Rule 1: At-most-$k$ 
  If a shift only needs $k$ people, we cannot allow $k+1$ people to work. This means if we randomly pick *any group of $k+1$ people*, at least one person in that group **must be rejected**.
  *CNF Formula:* For every possible combination of $k+1$ invigilators, we write a clause where everyone has a NOT ($\neg$) sign (meaning at least one person is not assigned):
  $$\bigvee_{m=1}^{k+1} \neg x_{i_m, j}$$

* Rule 2: At-least-$k$ 
  If we need $k$ people, we cannot have too many people taking the day off. Specifically, if we look at a large group of $n - k + 1$ people, it is mathematically impossible for all of them to be absent. At least one person from this large group **must be assigned** to work.
  *CNF Formula:* For every possible combination of $n - k + 1$ invigilators, we write a clause with positive signs (meaning at least one person is assigned):
  $$\bigvee_{m=1}^{n-k+1} x_{i_m, j}$$