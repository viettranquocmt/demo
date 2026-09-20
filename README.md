* Problem Setup
    * Invigilators ($n=3$): $I = \{CB_1, CB_2, CB_3\}$
    * Shifts ($m=2$): $J = \{CT_1, CT_2\}$
    * $CT_1$ and $CT_2$ occur at the exact same time (Overlap constraint applies).
    * $CT_1$ requires exactly $k=2$ invigilators.
    * $CT_2$ requires exactly $k=1$ invigilator.
    * Availability: Invigilator $CB_1$ is busy during shift $CT_1$.

* Decision Variables
Let $x_{i,j}$ be a Boolean variable where $x_{i,j} = True$ if invigilator $i$ is assigned to shift $j$, and $False$ otherwise. This generates a total of 6 variables:
    * Variables for $CT_1$: $x_{1,1}, x_{2,1}, x_{3,1}$
    * Variables for $CT_2$: $x_{1,2}, x_{2,2}, x_{3,2}$

* Rules and CNF Encoding
Based on the problem logic, we encode the constraints into Conjunctive Normal Form (CNF) clauses:

    * Rule 1: Availability
        * Since $CB_1$ is busy during $CT_1$, $x_{1,1}$ must evaluate to False.
        * Clause: 
        $$(\neg x_{1,1})$$

    * Rule 2: Overlap
        * Since $CT_1$ and $CT_2$ overlap in time, an invigilator cannot be assigned to both simultaneously.
        * Clauses (applied to all 3 invigilators):
        $$(\neg x_{1,1} \vee \neg x_{1,2})$$
        $$(\neg x_{2,1} \vee \neg x_{2,2})$$
        $$(\neg x_{3,1} \vee \neg x_{3,2})$$

    * Rule 3: Capacity
        * For shift $CT_1$ ($k=2$ out of $n=3$):
            * At-most-2 (select 3, reject $\ge 1$):
            $$(\neg x_{1,1} \vee \neg x_{2,1} \vee \neg x_{3,1})$$
            * At-least-2 (select 2, accept $\ge 1$):
            $$(x_{1,1} \vee x_{2,1})$$
            $$(x_{1,1} \vee x_{3,1})$$
            $$(x_{2,1} \vee x_{3,1})$$
        * For shift $CT_2$ ($k=1$ out of $n=3$):
            * At-most-1 (select 2, reject $\ge 1$):
            $$(\neg x_{1,2} \vee \neg x_{2,2})$$
            $$(\neg x_{1,2} \vee \neg x_{3,2})$$
            $$(\neg x_{2,2} \vee \neg x_{3,2})$$
            * At-least-1 (select 3, accept $\ge 1$):
            $$(x_{1,2} \vee x_{2,2} \vee x_{3,2})$$

* SAT Solver Trace (Unit Propagation)
    * Step 1 (Availability): $(\neg x_{1,1}) \Rightarrow \mathbf{x_{1,1} = 0}$.
    * Step 2 ($CT_1$ At-least-2): Substituting $x_{1,1}=0$ into $(x_{1,1} \vee x_{2,1})$ and $(x_{1,1} \vee x_{3,1}) \Rightarrow \mathbf{x_{2,1} = 1}, \mathbf{x_{3,1} = 1}$.
    * Step 3 (Overlap): Substituting $x_{2,1}=1, x_{3,1}=1$ into their respective overlap clauses $\Rightarrow \mathbf{x_{2,2} = 0}, \mathbf{x_{3,2} = 0}$.
    * Step 4 ($CT_2$ At-least-1): Substituting $x_{2,2}=0, x_{3,2}=0$ into $(x_{1,2} \vee x_{2,2} \vee x_{3,2}) \Rightarrow \mathbf{x_{1,2} = 1}$.
