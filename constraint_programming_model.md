# Group Lunch Planner – Constraint Programming Model

A constraint programming (CP) model for planning group lunches among users, incorporating preferences, group incentives, dietary needs, and time/cost constraints.

---

## ***Model Parameters***

$$K = number\ of\ users$$

$$R = number\ of\ restaurants$$

$$L = number\ of\ locations$$

$$L_{r} = location\ index\ of\ restaurant\ r$$

$$P_{k,r} = preference\ score\ for\ user\ k\ on\ restaurant\ r$$

$$S_{k,r} = similarity\ score\ for\ user\ k\ on\ restaurant\ r$$

$$D_{r1,r2} = dissimilarity\ score\ between\ restaurant\ r1\ and\ r2\ $$

$$hd = number\ of\ history\ days$$

$$H_{kd} = restaurant\ visited\ by\ user\ k\ on\ day\ d$$

$$B_{i} = group\ incentive\ given\ to\ a\ group\ of\ size\ i$$

$${rh}_{r} = 1\ if\ restaurant\ r\ is\ Halal,\ otherwise\ 0$$

$${kh}_{k} = 1\ if\ user\ k\ is\ Muslim,\ otherwise\ 0$$

$$et = eating\ time\ range,\ including\ queue\ time$$

$${rt}_{r} = travel\ time\ to\ and\ from\ restaurant\ r$$

$${tx}_{k}^{s} = allocated\ start\ time\ for\ user\ k$$

$${tx}_{k}^{e} = allocated\ end\ time\ for\ user\ k$$

$${ty}_{g}^{s} = start\ time\ for\ group\ g$$

$$c_{k} = total\ spending\ of\ user\ k\ over\ all\ history\ days$$

$$c_{r} = price\ level\ of\ restaurant\ r$$

$$C_{\max} = maximum\ average\ cost$$

### Note:

-   Every restaurant is assigned a unique index
    $r \in \lbrack 1,R\rbrack$ and a location index
    $l \in \lbrack 1,L\rbrack$. Hawker stalls in the same hawker centre
    or food court are assigned unique restaurant indexes but share the
    same location index for the purpose of computing group incentive
    bonuses.

-   $P_{k,r}$, $S_{k,r}$, $D_{r1,r2}$, $B_{i}$ should be appropriately
    weighted and pre-computed beforehand.

-   Instead of strictly enforcing a minimum group size, group incentive
    values are designed to encourage the formation of larger groups.

## ***Decision Variables***

$$x_{k} = Restaurant\ assignment\ for\ user\ k$$

$$y_{g} = Location\ assignment\ for\ group\ g$$

$$z_{k} = Group\ assignment\ for\ user\ k$$

$${xl}_{k} = Location\ assignment\ for\ user\ k$$

## ***Objective Function***

$$max\lbrack\frac{1}{K}(w_{p}\sum_{k = 1}^{K}P_{k,x_{k}} + w_{s}\sum_{k = 1}^{K}S_{k,x_{k}} - w_{d}\sum_{d = 1}^{hd}{\sum_{k = 1}^{K}D_{x_{k},H_{k,d}}} + w_{g}\sum_{g = 1}^{K}B_{count(\{ z_{k}:k = 1,2,...,K\},g)})\rbrack$$

## ***Constraints***

**1. Restaurant-Location Consistency**

Ensure that the location of user k's assigned restaurant corresponds to
their assigned location.

$$L_{x_{k}} = {xl}_{k}\ \ \ \ \forall k$$

**2. Group-Location Consistency**

Ensure that the location assigned to user k's group g matches the
location assigned to user k.

$$y_{z_{k}} = {xl}_{k}\ \ \ \ \forall k$$

**3. Dietary Requirement**

Muslim user must be assigned to a Halal restaurant.

$$
{rh}_{x_{k}} \geq {kh}_{k}\ \ \ \ \forall k
$$

**4. Time Feasibility**

Each user must individually satisfy the time constraint: start time +
round-trip travel time + eating time $\leq$ end time.

$${tx}_{k}^{s} + et + {rt}_{x_{k}} \leq {tx}_{k}^{e}\ \ \ \ \forall k$$

**5. Group Synchronisation**

Each user's start time must match the start time of their assigned
group.

$${tx}_{k}^{s} = {ty}_{z_{k}}^{s}\ \ \ \ \forall k$$

**6. Cost Constraint**

The combined cost of the current day (today) and all history days must
not exceed the maximum allowed average.

$$c_{k} + c_{x_{k}} \leq (1 + hd) \times C_{\max}\ \ \ \ \forall k$$

