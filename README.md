protein folding using Monte-Carlo and analyzing the structure
using Monte Carlo and analyzing the structure
I wrote this code in a simpler form before, and this code is more complex. If it is not necessary, please use the previous code.







More Complex Energy Function: The energy function was improved by using the Lennard-Jones potential to calculate the energy between atoms. This provides a more realistic simulation compared to a simple distance-based energy function.

Increased Simulation Settings Flexibility: Added adjustable parameters for the number of steps, temperature, and maximum displacement of atoms. This allows for more control over the simulation process.

Advanced Reporting: Enhanced the reporting during the simulation by printing the energy every 100 steps. This provides more insight into the progress of the simulation.

Parallel Simulation: Utilized ThreadPoolExecutor to run multiple Monte Carlo simulations in parallel. This increases the efficiency and speed of the simulation by taking advantage of multiple CPU cores.

Saving Simulation Results: The folded structures from each parallel simulation are saved to separate PDB files, allowing for further analysis and comparison.
