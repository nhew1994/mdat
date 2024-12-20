This directory has 5 scripts that compute the thermal conductivity
(kappa) of a Lennard-Jones fluid using 5 different methods.  See the
discussion in Section 6.20 of the manual for an overview of the
methods and pointers to doc pages for the commands which implement
them.  Citations for the various methods can also be found in the
manaul.

These scripts are provided for illustration purposes.  No guarantee is
made that the systems are fully equilibrated or that the runs are long
enough to generate good statistics and highly accurate results.

These scripts could easily be adapted to work with solids as well.

-------------

These are the 5 methods for computing thermal conductivity.  The first
4 are non-equilibrium methods; the last is an equilibrium method.

in.langevin = thermostat 2 regions at different temperatures via fix langevin
in.heat = add/subtract energy to 2 regions via fix heat
in.ehex = add/subtract energy to 2 regions via fix ehex
in.mp = use fix thermal/conductivity and the Muller-Plathe method
in.heatflux = use compute heat/flux and the Green-Kubo method

The NEMD systems have 8000 atoms with a box length 2x larger in z, the
non-equilibrium direction.  The G-K system has 4000 atoms and a cubic
box; it also needs to be run longer to generate good statistics.

The scripts were all run on 8 processors.  They all run in a minute or
so and produce the accompanying log files and profile files (for
temperature or heat-flux).

The state point of the LJ fluid is rho* = 0.6, T* = 1.35, and Rcut =
2.5 sigma.  This was chosen to agree with a 1986 paper by D Evans in
Phys Rev A, 34, p 1449, where he computed the thermal conductivity of
a small 108-atom system using a thermostatting method.  Fig 1 in the
paper shows his simulations produced a kappa of around 3.4 for this
system, in agreement with an experimental data point as well.

-------------

Here is how to extract Kappa from the log file output for each method.

The NEMD methods use the formula kappa = dQ * dZ/dTemp where dQ =
energy flux, and dTemp/dZ = temperature gradient.

(1) in.langevin

dQ = 8000 * 0.5*(0.890+0.883) / 100 / 18.82^2 / 2
  8000 atoms
  0.5*(0.890+0.883) = from log file =
    ave of total in/out energy for 2 regions normalized by # of atoms
  100 = 20,000 steps at 0.005 tau timestep = run time in tau
  xy box area = 18.82^2
  divide by 2 since energy flux goes in 2 directions due to periodic z
dTemp = 0.574 from log file for average Temp difference between 2 regions
dZ = 18.82

Kappa = 3.29
# Continue below!
(2) in.heat

dQ = (100*100) / 100 / 18.82^2 / 2
  100*100 = 100 (time in tau) * 100 (energy delta specified in fix heat)
  100 = 20,000 steps at 0.005 tau timestep = run time in tau
  xy box area = 18.82^2
  divide by 2 since energy flux goes in 2 directions due to periodic z
dTemp = 0.748 from log file for average Temp difference between 2 regions
dZ = 18.82

Kappa = 3.55

(3) in.ehex

dQ = (100*100) / 100 / 18.82^2 / 2
  100*100 = 100 (time in tau) * 100 (energy delta specified in fix heat)
  100 = 20,000 steps at 0.005 tau timestep = run time in tau
  xy box area = 18.82^2
  divide by 2 since energy flux goes in 2 directions due to periodic z
dTemp = 0.770 from log file for average Temp difference between 2 regions
dZ = 18.82

Kappa = 3.45

(4) in.mp

dQ = 15068 / 100 / 18.82^2 / 2
  15068 = cumulative delta energy, tallied by fix thermal/conductivity
  100 = 20,000 steps at 0.005 tau timestep = run time in tau
  xy box area = 18.82^2
  divide by 2 since energy flux goes in 2 directions due to periodic z
dTemp = 1.175 from log file for average Temp difference between 2 regions
dZ = 18.82

Kappa = 3.41

(5) in.heatflux

kappa is computed directly within the script, by performing a time
integration of the formulas discussed on the compute heat/flux doc
page - the resulting value prints at the end of the run and is in the
log file

Kappa = 3.88
