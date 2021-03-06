---
title: Domain decomposition
---

## Domain decomposition

### Domain decomposition

One of the most important problems in designing a parallel code is "Domain Decomposition":

* How will I divide up the calculation between processes?

Design objectives in decomposition are:

* Minimise communication
* Optimise load balance (Share out work evenly)

### Decomposing Smooth Life

We'll go for a 1-d spatial decomposition for smooth life, dividing the domain into
"Stripes" along the x-axis.

If we have an $N$ by $M$ domain, and $p$, processes, each process will be responsible for
$NM/p$ cells, and $NMr^2/p$ calculations.

### Static and dynamic balance

This will achieve perfect **static** load balance: the average work done by each process
is the same. If we were not solving in a rectangular grid, this would have been harder.

However, since the calculation can (perhaps) be done quicker when the domain is empty,
and our field will vary as time passes, we will not achieve perfect **dynamic** load balance.

### Communication in Smooth Life

Given that each cell needs to know the state of cells within a range $r=3r_d$, where $r_d$ is
the inner radius of the neighbourhood ring, and $r$ the outer radius, we need to get this
information to the neighbouring sites.

This is *great*: we only need to transfer information to neighbours, not all the other processes.
Such "local" communication results in fast code.

The amount of communication to take place each time step is proportional to $rMp$, but assuming
an appropriate network topology exists, each pair of neighbours can look after their communication
at the same time, so communication will take time proportional to $rMp/p$=$rM$.

### Strong scaling

We therefore expect the time taken for a simulation to vary like: $Mr(k+Nr/p)$. (Where $k$ is a
parameter describing the relative time to communicate one cell's state compared to the time
for calculating one cell )

Thus, we see that for a FIXED problem size, the benefit of parallelism will disappear
and communication will dominate: this is Amdahl's law again.

### Weak scaling

However, if we consider larger and larger problems, growing $N$ as $p$ grows,
then we can stop communication overtaking us. This is a common outcome: *local* problems provide
perfect *weak* scaling (until network congestion or IO problems dominate).

Any NONLOCAL communication, where the total amount of time for communication to take place grows
as the number of processes does (such as a gather, which takes $p$, a reduction, like $ln(p)$,
or an all-to-all, like $p^2$, means that perfect weak scaling can't be achieved.)
