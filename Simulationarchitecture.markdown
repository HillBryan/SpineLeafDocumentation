---
layout: default
title: Simulation Architecture
nav_order: 3
---

# Simulation Architecture

***

_Table of Contents_
1. [Simulation Pipeline](#simulation-pipeline)
2. [Pre-Simulation](#pre-simulation)
3. [Simulation](#simulation)
4. [Post-Simulation](#post-simulation)

***

### Simulation Pipeline

 - Before the Simulation begins (via bash script ./run_sim) several python scripts
 are ran in order to generate files to be used during the simulation. In addition,
 we have developed python scripts that are ran after the simulation ends to parse
 and analyze the results.

 - The image below shows the simulation pipeline from left (beginning) to right (end).

 - ![](images\pipe.png)

 ***

### Pre Simulation

<br/>
Before the Simulation begins, several python programs are ran to generate files
to be used in the simulation. This can be seen in the image in all the boxes before
`Spine-LeafDCN.exe`. These files are explained in more detail in the `code` section,
 but we will provide a high-level overview here.

1. `RouterConfigurator.py`: This script is responsible for generating the routes, based
off the input, to be used by the simulation. In more detail, this file utilizes INET's
option to [manually override individual routes](https://inet.omnetpp.org/docs/tutorials/configurator/doc/step5.html).
We do this operation because we have developed a custom router module in INET. The output
of this program is `ManualRoutingXML.xml`, the custom routes, and `input.txt`, the command
line input to be used later in the simulation.

2. `nedConfigurator.py`: This script is responsible for generating a new `ned` file.
Think of the `ned` file as the a configuration file that will be used to build the simulation.
In this file, the python script will change the number of `spines`, `leaves`, and `hostsPerleaf`
to equal the command line input. The input of this program is `input.txt`, the command line
input, and the output is `XHost_SpineLeaf.ned`,  the new `ned` file to be used by the simulation.

3. `SendScript.py`: This script is responsible for generating the traffic in the simulation.
There are several parameters used in this script, but these will be discussed in the `script`
section. The traffic is generated by creating a new `ini` file. Think of the `ini` file
as the main component of the simulation, all main configurations are specified in this file.
The input of this script is the command line input. The output is a `ini_generated.ini`, the
newly generated `ini` file, and `size_feq.txt`, traffic frequency data to be used post simulation.

***

### Simulation

<br/>

The actual simulation is what takes the most time in the pipeline but is only one component
in the actual pipeline. In more detail, based on the `simulation time` parameter in `SendScript.py`,
 the traffic generator, the simulator can take hours if specified. The simulation can be ran as a `.exe` file.

 1. `Spine-LeafDCN.exe`: This executable is the actual simulation itself. It is generated when the project is made
 using a `makefile` provided by OMNeT++. The simulation uses any `.ned` and `.ini` files as configuration for the simulation.
 In the command line interface, one will see the different simulation events as text, whereas in the GUI, one will see the
 simulation events as animations. We recommend running from the GUI first to see how the datacenter works and how packets are transferred.

***

### Post-Simulation

<br/>

After the simulation ends, several python scripts are ran to parse, analyze, and graph the results.
Though before these scripts are ran, the `./run_sim` bash script will convert the results file from
either a `.sca` scalar file, or a `.vec` vector file to a `.csv` file. Next several scripts are ran with
this `.csv` file as input. These files are explained in more detail in the `code` section, but we will
 provide a high-level overview here.

1. `ResultsAnalysis.py`: This script takes the `results.csv` as input and produces a bar graph that
shows the traffic distribution across each spine. The purpose of this graph is to show that our Simulation
evenly distributes traffic across the spines, creating a balanced load.

2. `ResultsAnalysisVec.py`: This script takes the `size_freq.txt` file generated by `SendScript.py` as input
and produces a bar graph showing size of traffic and frequency. The purpose of this graph is to show that our
traffic generator produces traffic that is similar to that of a real datacenter. Real datacenter traffic frequency
is hard to come by, but we were able to obtain a reference graph from a [research paper](https://dl.acm.org/doi/pdf/10.1145/1851182.1851192).
