# Module 1: Logic - Worked Example (Toy Instance)

### 1. Problem Setup
Assume a small-scale instance with the following parameters:
* **Invigilators ($n=3$):** $I = \{CB_1, CB_2, CB_3\}$
* **Shifts ($m=2$):** $J = \{CT_1, CT_2\}$
  * $CT_1$ and $CT_2$ occur at the exact same time (Overlap constraint applies).
  * $CT_1$ requires exactly $k=2$ invigilators.
  * $CT_2$ requires exactly $k=1$ invigilator.
* **Availability:** Invigilator $CB_1$ is busy during shift $CT_1$.

### 2. Decision Variables
Let $x_{i,j}$ be a Boolean variable where $x_{i,j} = True$ if invigilator $i$ is assigned to shift $j$, and $False$ otherwise. This generates a total of 6 variables:
* Variables for $CT_1$: $x_{1,1}, x_{2,1}, x_{3,1}$
* Variables for $CT_2$: $x_{1,2}, x_{2,2}, x_{3,2}$

### 3. Rules and CNF Encoding
Based on the problem logic, we encode the constraints into Conjunctive Normal Form (CNF) clauses:

**Rule 1: Availability**
* Since $CB_1$ is busy during $CT_1$, $x_{1,1}$ must evaluate to False.
* Clause: 
$$(\neg x_{1,1})$$

**Rule 2: Overlap**
* Since $CT_1$ and $CT_2$ overlap in time, an invigilator cannot be assigned to both simultaneously.
* Clauses (applied to all 3 invigilators):
$$(\neg x_{1,1} \vee \neg x_{1,2})$$
$$(\neg x_{2,1} \vee \neg x_{2,2})$$
$$(\neg x_{3,1} \vee \neg x_{3,2})$$

**Rule 3: Capacity**
*For shift $CT_1$ (Exactly $k=2$ out of $n=3$):*
* **At-most-2:** If we select any group of $k+1 = 3$ invigilators, at least 1 must be rejected.
$$(\neg x_{1,1} \vee \neg x_{2,1} \vee \neg x_{3,1})$$
* **At-least-2:** If we select any group of $n-k+1 = 2$ invigilators, at least 1 must be accepted.
$$(x_{1,1} \vee x_{2,1})$$
$$(x_{1,1} \vee x_{3,1})$$
$$(x_{2,1} \vee x_{3,1})$$

*For shift $CT_2$ (Exactly $k=1$ out of $n=3$):*
* **At-most-1:** If we select any group of $k+1 = 2$ invigilators, at least 1 must be rejected.
$$(\neg x_{1,2} \vee \neg x_{2,2})$$
$$(\neg x_{1,2} \vee \neg x_{3,2})$$
$$(\neg x_{2,2} \vee \neg x_{3,2})$$
* **At-least-1:** If we select any group of $n-k+1 = 3$ invigilators, at least 1 must be accepted.
$$(x_{1,2} \vee x_{2,2} \vee x_{3,2})$$

### 4. SAT Solver Trace (Deduction Steps)
When these clauses are fed into a SAT solver, it applies unit propagation to find the satisfying assignment:
1. **From Availability:** The unit clause $(\neg x_{1,1})$ forces **$x_{1,1} = 0$**.
2. **From $CT_1$ At-least-2:** Substituting $x_{1,1}=0$ into $(x_{1,1} \vee x_{2,1})$ forces **$x_{2,1} = 1$**. Similarly, $(x_{1,1} \vee x_{3,1})$ forces **$x_{3,1} = 1$**. 
   *(Result: $CT_1$ is successfully assigned to $CB_2$ and $CB_3$)*.
3. **From Overlap:** Since $x_{2,1}=1$, the clause $(\neg x_{2,1} \vee \neg x_{2,2})$ forces **$x_{2,2} = 0$**. Since $x_{3,1}=1$, the clause $(\neg x_{3,1} \vee \neg x_{3,2})$ forces **$x_{3,2} = 0$**.
4. **From $CT_2$ At-least-1:** Substituting $x_{2,2}=0$ and $x_{3,2}=0$ into $(x_{1,2} \vee x_{2,2} \vee x_{3,2})$ forces **$x_{1,2} = 1$**.
   *(Result: $CT_2$ is successfully assigned to $CB_1$)*.

### 5. Final Model Output
The SAT solver returns `Satisfiable = True` with the following model, satisfying all logical constraints perfectly:
```json
{
    "satisfiable": true,
    "model": {
        "x_1,1": 0,
        "x_2,1": 1,
        "x_3,1": 1,
        "x_1,2": 1,
        "x_2,2": 0,
        "x_3,2": 0
    },
    "unsat_core": null
}