# EuropeanMV Dataset: common events in an active distribution energy network

## Overview
<p align="justify">
This repository presents the simulation environment based on the Task Force C6.04 from April 2014 of the Council on Large Electric Systems (in french, CIGRE), which is a global community committed to the collaborative development and sharing of power system expertise proposed in 2014, this CIGRE Task Force, named “Benchmark Systems for Network Integration of Renewable and Distributed Energy Resources”, is a common basis for testing containing both European and North American benchmark network.
<p align="justify">
In order to emulate a distribution system in a trustworthy way, it was opted to use the medium voltage distribution network benchmark, with European configuration and as fewer adjustments as possible, i.e, the configuration and parameters proposed by CIGRE were narrowly followed, except by the fact that some renewable distributed generation units were added to the system, in order to broach the new era of energy systems, with high penetration of renewable energy.
<p align="justify">
The European medium voltage (MV) distribution system has a three-phase feeder that can be operated both meshed or radially. The feeder includes numerous laterals at which transformers can be connected. The nominal voltage is 20 kV and frequency is 50 Hz. Basically, every node is a load node or a generation node and details about them are presented in the sequence. Maybe one of the most significant changes in the original structure of MV benchmark is the complete usage of overhead lines. 
<p align="justify">
In the original configuration, the system has predominantly underground cables, meantime for High Impedance Fault (HIF) investigation, it makes more sense to deal with overhead lines, once that most HIFs’ characteristics are based on conductor-ground touch. In this sense, the system is adapted to contain overhead lines. Another nuanced modification performed is concerned to the system’s grounding. So, a grounding transformer at the end of the substation is added to the network. Finally, detailed distributed generation units are inserted in the system: these units are coupled through inverters controlled by maximum power point tracking. The aforementioned system is fully modelled and implemented in Matlab/Simulink environment.

![image](https://github.com/dionatancieslak/CIGRE-EuropeanMV/assets/14805079/017551f1-0c5c-4b76-a8ae-e5b2703c7153)
<p align="center"> Figure 1 - European MV distribution system.

<p align="justify">
The European MV distribution network considered to all the following developments is shown in Figure 5. It consists in an 11-nodes system connected to the main grid through a power transformer, as mentioned, the network operates in 20 kV and 50 Hz. The switch S1 is liable to change the operational mode of the grid (from connected to islanded and vice-versa). Also, switches S2 and S3 change the topology of the network, from radial to mesh. All the other relevant details and parameters information needed to reproduce the experiments are xtracted from the CIGRE technical brochure.

## Simulation Files

<p align="justify">
The nodes of the system are modelled as three-phase voltage-current measurement devices, that measures instantaneous three-phase voltages and currents in the circuit. Voltages are selected to be measured as phase-to-ground, while the measured current is the current that flows through the node. All the data are sampled with a frequency of 12.8 kHz, which represents 256 samples per cycle. The system, considering three distributed generation units with detailed models, is simulated during 1.2 s, in the discrete mode, with a sampling time of 5 µs. The measures are stored in a .mat file with dimension 15000 x 111, where:

- columns 0-35 (medium voltage side - 20 kV): Vbus0A, Vbus0B, Vbus0C, ..., Vbus11C
- columns 36-71 (medium voltage side - 20k V): Ibus0A, Ibus0B, Ibus0C, ..., Ibus11C
- columns 72-101 (low voltage side - 0.4 kV): Vbus1LVA, Vbus1LVB, Vbus1LVC, Vbus3LVA, ..., Vbus11LVC (does not contain nodes 0 and 2)
- columns 102-110 (low voltage side - 0.4 kV, all generation nodes): Ibus3LVA, Ibus3LVB, Ibus3LVC, ..., Ibus7LVC

Files considered in the simulations are presented in "simulation_classes.rar" and consists in:

- classes: model and scripts of all considered classes (faulty and non-faulty).
    - 1.CBS (capacitor bank switching model)
    - 2.DGS (distributed generation switching model)
    - 3.HIF (high impedance fault model)
    - 4.LIF (low impedance fault model)
    - 5.LOV (load variation model)
    - 6.NOP (normal operation model)
    - 7.TRE (transformer energization model)

<p align="justify">
Each class model has its own parameter definition, so, in each simulation, the parameters are randomly selected among each interval of values, as can be see in the Table ahead.

![image](https://github.com/dionatancieslak/CIGRE-EuropeanMV/assets/14805079/08e06bf2-ce02-4d8d-b6b5-743e817c26a5)
<p align="center"> Table 1 - Parameters considered in the simulations.

- MG_original: model of CIGREE European MV Network and the renewable energy sources model.
    - *class*.slx (microgrid Simulink model)
    - MG_sim_*class*.m (script to control simulations)
    - myModel_init.mat (initialization microgrid parameters)
    - power_PVarray_grid_det_CIGRE.slx (PV model with parameters)
    - power_wind_dfig_det_CIGRE.slx (WT model with parameters)
    - randomValue.m (function used in the MG_sim file)
    - saveData.m (function used in the MG_sim file)

## Data Processing Files

<p align="justify">
The waveforms obtained in the simulation step can be processed by Fourier approach, i.e, by calculating the correspondent harmonic content of each measure. The .m files are processed in Python and the scripts needed are available in "processing_data.rar", and consists in:

- DFT.py (function to calculate DFT of a time series, recursively)
- ResamplePhasor.py (function to resample arrays according to a desired number of phasors per fundamental cycle)
![image](https://github.com/dionatancieslak/CIGRE-EuropeanMV/assets/14805079/1d2ec81f-776c-4b8b-a81f-b734fc5b1ea6)
<p align="center"> Figure 2 - Example of a resampled phasor series.

- EstimatesPhasor.py (script that estimated the ith phasors of an waveform input)
![image](https://github.com/dionatancieslak/CIGRE-EuropeanMV/assets/14805079/42494e38-44fe-47ca-bf49-eb76bf7a0a30)
<p align="center"> Figure 3 - Example of an estimated phasor with i = 1, 2 and 3.

## Example of Dataset

<p align="justify">
The original dataset took an average of 45 minutes to be simulated and, in order to build a homogeneous dataset, each class of event is simulated 30 times at each location (Table 1), where for every simulation, random values of class-specific parameters are picked up, totaling 2700 examples, with a simulation elapsed time of around 2025 hours, in two computers Intel i5-3230M @ 2.6GHz with 8 GB of RAM, totalling more than 30 GB of simulated data.

By repository limitations, a small dataset ("reduced_dataset.rar", hosted in mega.nz) is added in order the motivate preliminary investigations of the reader.

https://mega.nz/file/7WIyQRxR#M6t06k9NSkD_yZ6SulgMNGU34vX5iCp6bQE0yMKTgHk
