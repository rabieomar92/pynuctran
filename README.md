# PyNUCTRAN
## A Python Library for Stochastic Nuclear Transmutation Solver.

### ```pip install pynuctran```

PyNUCTRAN is a Python library created by M. R. Omar for simulating various nuclear transmutations such as decays, fissions, and neutron absorptions. The code helps physicists avoid cumbersome numerical issues of solving the nuclide depletion equations (also known as the Bateman's equations). These issues include the stiffness of the Batemans equations due to the complex decay chain problems. To date, there are many numerical depletion solvers available such as CRAM, TTM etc.  Interestingly, the proposed stochastic solver is simple to code, but it consumes computational power. Thanks to the current computing technology progress, the computational resource is not a problem anymore. Future research involves parallelizing PyNUCTRAN to reduce CPU time, thus further boosting the computing speed. 

## Features

- Capable of simulating complex depletion chains.
- PyNUCTRAN simulates the actual transmutations processes, thus, it can be used to verify other analytical/numerical depletion codes.
- Create a plot of various isotope concentrations.
- Helps nuclear physics students to understands transmutations processes through simulations. They can create, study and design any depletion chains and simulate the transmutations.

If you don't prefer dealing with complicated mathematical methods to solve the Bateman's equation, you could try PYNUCTRAN. As long as you have the computational power!

## Summary of the Stochastic Transmutation Method

PyNUCTRAN works by creating a massive amount of simulated nuclides in the computer memory. Also, the code divides the simulation time into discrete timesteps. In each step, PyNUCTRAN iterates over each of these simulated nuclides. Here, the code decides whether each of these nuclides undergoes a removal process or not. The removal process includes the decay, fission and absorptions and any other user-defined removal methods. As the removal process occurs, it mutates the nuclide species. At this point, the removal event transforms the isotope (removed) into one or more daughter nuclides, depending on the removal method. If removal occurs, the code removes the nuclide from the simulation, and its product(s) will be stored for the iteration during the next timestep.

PyNUCTRAN characterizes nuclides into various isotope species. Each isotope species may consist of a set of removal methods. For example, U-238 disappears from the system via fission or decay. Here, these removal methods occur at a specific rate, λ, per unit second. For decay events, λ is the well-known decay constant. For other removal methods involving neutrons, λ is the reaction rate. The reaction rate is obtained using the neutron flux computed from transport codes, such as MCNP, OpenMC and et cetera.

During the iteration, PyNUCTRAN selects the most probable event and tests whether the selected removal event occurs. Such a selection utilizes a random number ![\small](https://latex.codecogs.com/svg.latex?\small&space;\gamma\in\[0,1\)), and it applies the probabilistic approach proposed by M. R. Omar. Suppose that a nuclide of an isotope consists of ```n``` removals. Thus the probability of selecting the k-th removal is given by

![\Medium \bg_black x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}](https://latex.codecogs.com/svg.latex?\normal&space;p_k\(\Lambda_1,\Lambda_2,...,\Lambda_n\)=\prod_{j=1}^{n}\(\delta_{kj}+\(-1\)^{\delta_{kj}}e^{-\Lambda_j}\)) 

where ![\small](https://latex.codecogs.com/svg.latex?\small&space;\Lambda_k=\lambda_k\Delta{t}) with ![\small](https://latex.codecogs.com/svg.latex?\small&space;\Delta{t}) as the time interval between steps. Then, then the k-th removal is selected if the following condition is satisfied

![\Medium \bg_black x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}](https://latex.codecogs.com/svg.latex?\normal&space;\sum_{j=1}^{k-1}p_j<\gamma\sum_{j=1}^{n}p_j\le\sum_{j=1}^{k}p_j) 

Here, the random selections of the removal methods follow the Poisson process, and it assumes a constant λ within the preceding time steps. Once a removal method is selected, then the code will again decide whether the selected removal event is occurring or not. A new random number ![\small](https://latex.codecogs.com/svg.latex?\small&space;\gamma\in\[0,1\)) is sampled and the removal event occurs if the following condition is met:

![\Large \bg_black x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}](https://latex.codecogs.com/svg.latex?\normal&space;\gamma>\(1-e^{-\Lambda_k}\)) 


## Some Python Examples

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

# Proceed creating more isotopes (if any)...
...
...
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

<img src="https://user-images.githubusercontent.com/33319386/131448902-fd33d3fd-ac21-493f-bda4-551ddba6dbe8.png" alt="drawing" width="400"/><img src="https://user-images.githubusercontent.com/33319386/131520815-03ec0513-8f56-4d58-9ee2-1186614e3408.png" alt="drawing" width="400"/>

Sample PYNUCTRAN output using ```Physics.PlotConcentration()``` for Lago & Rahnema (2017) benchmark test #5. [doi: http://dx.doi.org/10.1016/j.anucene.2016.09.004]


## License (MIT)
(c) M.R.Omar, School of Physics, Universiti Sains Malaysia, 11800 Penang, Malaysia.

Permission is hereby granted,  free of charge,  to any person  obtaining  a copy of  PYNUCTRAN and associated documentation files (the "Code"), to deal in the Library without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Code, and to permit persons to whom the Library is furnished to do so,  subject to the following conditions:

The  above  copyright  notice  and  this permission notice  shall  be  included  in  all copies or substantial portions of the Software.

THE CODE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS  OR IMPLIED, INCLUDING  BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT  SHALL  THE AUTHORS  OR COPYRIGHT  HOLDERS  BE LIABLE FOR ANY CLAIM,  DAMAGES OR  OTHER LIABILITY,  WHETHER  IN  AN  ACTION  OF CONTRACT,  TORT  OR OTHERWISE,  ARISING FROM,  OUT OF OR IN CONNECTION WITH THE CODE OR THE USE OR OTHER DEALINGS IN THE CODE.

## Library Documentation

Before reading this section, the reader must have the basic idea of object-oriented programming (OOP). This section uses the standard OOP terms to ensure effective explanation. Of course, thanks to the simplicity of the method implemented in PyNUCTRAN. Consequently, the library consists of a small number of classes and structures. You will discover that the library only has 600++ lines of Python code, which is a lot less than the state-of-the-art Monte Carlo transport codes. Also, identifiers are stylized using the CamelNotation, just like most Microsoft-oriented codes. It improves the code readability without any fancy underscores. However, some identifiers do not use camel notation, especially those for temporary data storage.

The library consists of four (4) classes and one (1) enumeration.
```
Classes: Physics, Isotope, Removal, Nuclide
Enums: RemovalType
```


### ```class RemovalType(Enum)```
An enumeration distinguishing the nuclide removal types. There are only four (4) possible values:
```python
RemovalType.Fission = 0
RemovalType.Absoption = 1
RemovalType.Decay = 2
RemovalType.NoRemoval = 3
```
### The ```Removal``` class
The purpose of this class is to handles the information of a removal method.

##### ```Removal.Removal(Id: str, Lambda: float, Type: RemovalType)```
A class constructor that defines a nuclide removal method. This class is a data structure that stores the parameters of a specific removal. For instance, a beta decay of isotope A is a removal method, where the decay event removes isotope A from the system by mutating A into other species. The class constructor takes three (3) parameters ```Id```, ```Lambda``` and ```Type```. ```Id``` is a string identifier of the removal method; ```Lambda``` is the removal rate (per second); and ```Type``` is the removal type.

##### ```Removal.AddDaughters(Yield: float, NuclideIdA: str, NuclideIdB: str)```
Adds the daughter products to the removal method. ```Yield``` is the probability (in per cent) of producing the daughter pair during the removal event; ```NuclideIdA``` is the string identifier of the first product, i.e. U238, Pu240...; ```NuclideIdB``` is the string identifier of the second product. Only a pair of isotopes is accepted. If the removal produces only one product, set '' for the second product. If both product Ids are empty strings, '', then the code does not create the product after the removal event. 
##### ```Removal.SampleDaughters() -> tuples(Isotope, Isotope)```
Randomly selects one of the daughter pairs based on the specified product yield. The method returns a pair of isotopes tuple ```(Isotope, Isotope)``` representing the sampled daughter pair.
#####  ```Removal.Id: str```
Gets or sets the string identifier of the removal method.
##### ```Removal.Lambda: float```
Gets or sets the removal rate (per unit second).
##### ```Removal.Type: RemovalType```
Gets or sets the removal type.
##### ```Removal.Daughters: list```
Stores a Python list containing the daughter pair parameters: ```[[Yield, NuclideIdA, NuclideIdB],[..,..,..],[..,..,..],...].```

### The ```Isotope``` class
An ```Isotope``` class is a data structure that stores various information of an isotope species.
##### ```Isotope.Isotope(Id: str, Name: str, Z: float, A: float)```
A class constructor that defines an Isotope. ```Id``` is a string identifier of the isotope, i.e. 'U235','Pu240', etc. Also, ```Id``` must be short and simple because it could serve as a reference to the daughter(s) of other removal methods (if any). ```Name``` is a string specifying the full name of the isotope; ```Z``` is the proton number; and ```A``` is the nucleus number.
##### ```Isotope.AddRemoval(Method: Removal)```
Adds a removal method, ```Method```, to the isotope.
##### ```Isotope.Id: str```
Gets or sets the string identification of the isotope.
##### ```Isotope.Name: str```
Gets or sets the name of the isotope.
##### ```Isotope.Z: float```
Gets or sets the proton number of the isotope.
##### ```Isotope.A: str```
Gets or sets the mass number of the isotope.
##### ```Isotope.Removals: list(Removal)```
A list of removal methods experienced by the isotope.

### The ```Nuclide``` class
##### ```Nuclide.Nuclide(NIsotope: Isotope, CreationStep: int)```
A class constructor that creates a nuclide of species ```NIsotope```. ```CreationStep``` is an integer specifying the time step where it is created. ```CreationStep``` enables the computation of the average isotope lifetime in the simulation.
##### ```Nuclide.NId: int```
Gets the identification number. This number is automatically generated during nuclide's creation.
##### ```Nuclide.Isotope: Isotope```
Gets or sets the isotope species of the nuclide.
##### ```Nuclide.CreationStep: int```
Gets or sets the time step it was created.

### The ```Physics``` class
Handles the necessary routines that helps to simulate the transmutation processes.
##### ```Physics.Physics(Id: str, TimeInterval: float, Steps: int)```
A class constructor that defines a new simulation. ```Id``` is a string identifier of the simulation; ```TimeInterval``` is the simulation time interval (in seconds) between time successive time steps; and ```Steps``` is the total number of time steps to simulate.
##### ```Physics.AddIsotope(NewIsotope: Isotope)```
Adds a new isotope, ```NewIsotope: Isotope```, to the simulation.
##### ```Physics.GenerateIsotopes(NuclideIsotope: Isotope, Count: int)```
Generates and adds nuclides of the same species, ```NuclideIsotope```, into the source table. Here, ```Count``` is the total number of nuclides to generate.
##### ```Physics.SelectRemoval(SelectedIsotope: Isotope) -> Removal```
Samples a nuclide removal method (Also selects the NoRemoval event).```SelectedIsotope``` is the isotope species of the simulated nuclide. This function returns the selected removal method.
##### ```Physics.DoesRemovalHappens(rm: Removal) -> bool```
Decides whether a removal process occurs. ```rm``` is the removal of the nuclide species. Returns ```True``` if the removal occurs, ```False``` otherwise.
##### ```Physics.ProcessRemoval(Type: RemovalType, NuclideToProcess: Nuclide)```
Processes a removal event, i.e. kills the removed nuclide and add new nuclides in the source table. ```RemovalType``` is the type of the undergoing removal; ```NuclideToProcess``` is the nuclide subjected to the removal event.
##### ```Physics.UpdateStep()```
Updates the current time step and processes the source table shift.
##### ```Physics.IterateOverNuclides(self, CurrentStep: int)```
A subroutine containing the loop that iterates over the entire source nuclides table. During each time step, the nuclide source table contains the nuclides that are waiting for their fate. Here, the fate is either to be removed from the system or not. Therefore, PyNUCTRAN iterates over these nuclides to decide and simulate their removal fate. ```CurrentStep``` is an integer specifying the current time step.
##### ```Physics.Run(Seed: int = 0)```
Begins executing the simulation from ```timestep = 0``` to ```Steps```. ```Seed``` is the seed number of Python's pseudo-random number generator (PRNG). Use the same seed number if you need to maintain the reproduce the same result.
##### ```Physics.PlotConcentrations(self, Ids: list = [], Range: tuple = (0, 0), Normalize: bool = False, Color: dict = {})```
Plots the concentration curves over time steps. ```Ids``` is the list of isotope identification strings, specifying which isotope concentration to include in the plot; ```Range``` is the time step range. The default is from 0 to the specified maximum steps. ```Normalize``` is a boolean specifying whether the concentrations should be normalized or not. A normalized concentration is equals to [the nuclide count] / [total initial nuclides]. ```Color```: A python dictionary specifying the curve colours that differentiate the isotopes. The key of the dictionary is equivalent to the isotope Id defined in the simulation. See ```Isotope.Id```.
##### ```Physics.Isotopes: dict(Isotopes)```   
A list of isotopes defined in the simulation.
##### ```Physics.NuclidesTable: dict```
A Python dictionary consists of two arrays, array A and B, for storing the source nuclides and the product nuclides. 
##### ```Physics.Id: str```
Stores the string identification of the simulation.
##### ```Physics.Dt: float```
Gets or sets the time interval.
##### ```Physics.Steps: int```
Gets or sets total number of steps to simulate.
##### ```Physics.SourceTable: str```
A string specifying  the key of the current  source table.
##### ```Physics.ProductTable: str```
A string specifying the key of the current product table.
##### ```Physics.IsotopesToMonitor: list```
A list of  isotope  identifications  specifying the isotopes should be tallied  during  the simulation.
##### ```Physics.OutputSkip: int```
The number of steps  skipped  for console printing. Note: The ```Physics.DisplayProgressOnly``` property  must be set to ```False```.
##### ```Physics.IsotopeConcentrations: dict```
A dictionary containing the concentrations of all isotopes over the entire simulated time steps. The keys of the dictionary are equivalent to the isotope Id defined in the simulation. The rows of the dictionary correspond to the time step.
  ```                                          
   [Isotope1]  [Isotope2]  [Isotope3] ...
   c1-1        c2-1        c3-1
   c1-2        c2-2        c3-2
   c1-3        c2-3        c3-3
   :           :           :
   :           :           :
```
##### ```Physics.ProcessedCount: int```
The total number of nuclides simulated during the entire simulation.
##### ```Physics.InitialNuclidesCount: dict```
A dictionary storing the total number of initial nuclides (when the time step = 0).
##### ```Physics.IsotopeLifetime: dict```
A dictionary that stores the average lifetime of all isotopes defined in the simulation. 
##### ```Physics.CountTable: dict```
A dictionary that stores the CURRENT step's total number of nuclides.






