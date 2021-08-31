# PYNUCTRAN
## A Python Library for Stochastic Nuclear Transmutation Solver.
#
#

Initially developed, designed and proposed by M. R. Omar for the purpose of simulating various nuclear transmutations such as decays, fissions as well as neutron absorptions. PYNUCTRAN helps physicists to avoid cumbersome numerical issues of solving the nuclide depletion equations (also known as the Bateman's equations. These issues include the stiffness of the Batemans equations due to complex decay chain. To date, there are many numerical depletion solvers available such as CRAM, TTM etc.  Interestingly, the designed stochastic solver is easier to code, alas, it consumes computational power. Fortunately, thanks to the current computing technology progress, such that the computational resource is not a problem anymore. Future research involves parallelizing PYNUCTRAN to reduce CPU time to further boost the computing speed.

## Features

- Capable of simulating complex depletion chains.
- PYNUCTRAN simulates the actual transmutations processes, thus, it can be used to verify other analytical/numerical depletion codes.
- Create a plot of various isotope concentrations.
- Helps nuclear physics students to understands transmutations processes through simulations. They can create, study and design any depletion chains and simulate the transmutations.

If you don't prefer dealing with complicated mathematical methods to solve the Bateman's equation, you could try PYNUCTRAN. As long as you have the computational power!

## Summary of the Stochastic Transmutation Method

PYNUCTRAN works by creating a massive amount of simulated nuclides in the computer memory. Also, the simulation time is divided into several time steps. At each time step, PYNUCTRAN iterates over each of these simulated nuclides and decides whether each of these nuclides undergoes a removal process or not. The removal process includes the decay, fission and absorptions and any other user-defined removal methods. The term 'removal' is used because if the removal process occurs, it mutates the nuclide species, and the isotope is now transformed (removed) into a new product(s) depending on the removal method. If removal occurs, the nuclide is removed from the simulation and its product(s) will be stored for the iteration during the next time steps.

PYNUCTRAN characterizes nuclides into various isotope species. Each isotope species may consist of a set of removal methods. For example, U-238 disappears from the system via fission or decay. Here, these removal methods occur at a specific rate, λ, per unit second. For a decay, λ is the decay constant, whereas, for other removal methods involving neutrons, λ is the reaction rate. The reaction rate is obtained using the neutron flux computed from transport codes, such as MCNP, OpenMC and et cetera.

During the iteration, PYNUCTRAN selects the most probable event, i.e. nothing happens? fission? decay? Such a decision is made by using a random number in [0,1) and based on the probabilistic approach proposed by M. R. Omar. Here, the random selections of the removal methods are based on Poisson statistics, and it assumes a constant λ within the preceding time steps. Once a removal method is selected, then the code will again decide whether the selected removal method is actually occurring or not.

## Some Python Examples
#
_Installing PYNUCTRAN from PyPI repository._
```bash
pip install pynuctran
```
_Importing PYNUCTRAN library to your Python code._
```python
from pynuctran.solver import *
```
_Defining an isotope and add it to the simulation._
```python
from pynuctran.solver import *

# Creates an isotope (Thorium-234)
Th234 = Isotope('Th234', 'Thorium-234', 90, 234)
# Create a removal method with decay type. The specified half-life must be in seconds.
half_life_Th234 = 2.082240E+06
# Calculate the decay constant: λ = ln(2)/t_halflife
lambda_Th234 = np.log(2.0) / half_life_Th234
# Create the removal method using the Removal() class constructor.
Th234_RM = Removal('Decay', lambda_Th234, RemovalType.Decay)
# Add the decay product. Here, the decay product is Pa-234 and the yield is 100%. 
# Here, it is compulsory to create another isotope -> Pa234. If you do not wish to monitor the product, leave the field blank, i.e. ''.
Th234_RM.AddDaughters(100.0, 'Pa234', '')
# Add the removal method to the created isotope Th234.
Th234.AddRemoval(Th234_RM)
```

_Running the simulation._
```python
# Creates a simulation Test, with time interval 100secs over 200 time steps.
MySimulation = Physics(Id='Test', TimeInterval=100, Steps=200)
# Add the created isotopes.
MySimulation.AddIsotope(Th234)
MySimulation.AddIsotope(Pa234)
# Generates the initial nuclides at (t=0sec). Here, we create 5000 Th234 nuclides.
MySimulation.GenerateNuclides(Th234, 5000)
# Executes the simulation.
MySimulation.Run()
# Plots the concentration curve over time steps. 
MySimulation.PlotConcentrations(Color={'Th234': '#23d212', 'Pa234':'#11211f'})
```

_Sample output_

<img src="https://user-images.githubusercontent.com/33319386/131448902-fd33d3fd-ac21-493f-bda4-551ddba6dbe8.png" alt="drawing" width="200"/>

Sample PYNUCTRAN output using ```Physics.PlotConcentration()``` for Lago & Rahnema (2017) benchmark test #5. [doi: http://dx.doi.org/10.1016/j.anucene.2016.09.004]


## License
(c) M.R.Omar, School of Physics, Universiti Sains Malaysia, 11800 Penang, Malaysia.

Permission is hereby granted,  free of charge,  to any person  obtaining  a copy of  PYNUCTRAN and associated documentation files (the "Code"), to deal in the Library without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Code, and to permit persons to whom the Library is furnished to do so,  subject to the following conditions:

The  above  copyright  notice  and  this permission notice  shall  be  included  in  all copies or substantial portions of the Software.

THE CODE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS  OR IMPLIED, INCLUDING  BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT  SHALL  THE AUTHORS  OR COPYRIGHT  HOLDERS  BE LIABLE FOR ANY CLAIM,  DAMAGES OR  OTHER LIABILITY,  WHETHER  IN  AN  ACTION  OF CONTRACT,  TORT  OR OTHERWISE,  ARISING FROM,  OUT OF OR IN CONNECTION WITH THE CODE OR THE USE OR OTHER DEALINGS IN THE CODE.
