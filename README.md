# Homework 0

## Bio

I am Mario Ortega and I am a second year PhD student in the Nuclear Engineering department at UC Berkeley. My research focus is on deterministic methods in numerical radiation transport and analytical solutions of the radiation transport equation. My research focuses specifically on criticality eigenvalue problems. Criticality is the ability of a system to sustain a nuclear chain reaction. With the focus on eigenvalue problems, numerical linear algebra is a necessary component of any numerical method. I hope to learn in CS267 how to write algorithms and methods that can take advantage of parallelization.

## A Parallel Radiation Transport Problem

Radiation effects are important when modeling the design of nuclear reactors, problems in nuclear security, and other applications in thermal and plasma physics. The radiation transport equation is a function of seven variables, three spatial variables, energy, time, and direction (which requires two angles to specify). Analytical solutions only exists for the simplest of cases and require removing energy dependence, time dependence, higher spatial dimensions, etc. In order to solve the integro-differential equation quadrature is used to approximate the integration over direction while the spatial domain is discretized. On this spatial mesh, domain decomposition can be used to solve the equation on each mesh cell, calculate the boundary terms of each cell, and then pass this information along to be used as the source terms for neighboring mesh cells. 3D problems require terabytes of memory due to all the material cross sections and source terms necessary to characterize the domain and the solution of the equation on some domain requires teraflops. These problems are usually solved on the machines of the Department of Energy's (DOE) Advanced Scientific Computing Initiative (ASCI) program.

The solution algorithm can be decomposed into four steps:
1. Compute source and coupling terms
2. Place source terms on the right-hand side
3. For each angle and energy, compute the radiation flux in one cell
4. Do this until solution is converged in all cells.

This method of decoupling source terms and solving for the flux in each cell is called a sweep and it effectively uncouples each cell. This is where we can use a parallel machine to calculate the flux in each cell indepdently before recomputing the source terms. The straightforward approach to parallelizing the algorithm is to assign individual energies and angles to each processor. However, this has its drawbacks as energy is better treated together so as to keep fluxes contiguous so improve cache performance. Second, assigning each processor one angle might leave some processors unused as quadrature sets used in radiation transport degrade with increasing numbers of angles used. Finally, the cross section and interaction data needed for each cell usually is too much for one single processor to hold. Algorithms are currently being studied to increase the utility of parallelization for radiation transport problems.

Currently, Sandia National Laboratories is testing algorithms on two machines at Sandia: the Intel Tflops (ASCI Red) and the CPlant machine. The ASCI Red is a traditional massively parallel supercomputer using 333 MHz Pentiums. The CPlant is a commodity cluster using 500 MHz DEC Alpha processors. These machines are not on the TOP500 list.

The algorithms tested have shown speedups and seems to scale with the number of processors though it is only about 70% efficient at 256 processors as the ideal efficiency. More research is underway to determine where the bottlenecks are in the algorithms and how to remove them.


