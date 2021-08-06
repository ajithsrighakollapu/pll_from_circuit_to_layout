# A two day work shop on designing the phase locked loop using sky130nm - techology

![](introduction.png)

# Table of contents

- [Overview](#Overview)
- [DAY 1: PLL theory and Lab overview](#Day1)
  -  [Introduction to PLL](#PLL)
  -  [PLL Components](#PLLComponents)
  -  [circuit level functional block diagrams](#circuit_level_functional_blocks_of_PLL)
  -  [charge pump responses](#charge_pump_responses)
  -  [phase_frequency_detectors](#Phase_Frequency_Detectors)
  -  [Voltage controlled Oscillator](#Voltage_Controlled_Oscillator)
  -  [Important Definations Related To PLL:](#Important_Definations_Related_To_PLL)
  -  [Tool Setup](#Tool_Setup)
  -  [Development Flow](#Development_Flow)
  -  [PDK’S AND Specifications](#PDKs_and_Specifications)
  -  [Tools,dependicies installation](#Tools_dependicies_installation)



  
  
# Overview

The repository completely deals with Phase locked Loops from design stage to tapeout stage using the open source tools (i.e **ngspice** for circuit level and transcient anslysis, **magic** for layout design and parasitic extraction and **Caravel** which is a **standard SOC** with on chip resources to control read/write operations,supply voltages to **user dedicated space**(where we can place our PLL)) 

# Day1
The Day1 is completly about the design and working priciple of PLL and about tools which we are using to design the PLL

# PLL
- A pll is basically a control system which maintains its output signal in phase with input reference signal which is 
coming from an external source(quartz crystal).
- The main idea of this workshop is to design a PLL for clock signal where the output signal should not have any 
phase and frequency distortions with respect to reference signal


![](spectrum1.png)

# PLLComponents
- The main design components of the PLL are:
  - phase frequency detector
  - charge pump
  - low pass filter
  - voltage controlled oscillator
  - frequency divider
- The **phase frequency detector** finds the difference between the **reference signal** and the **output signal** from VCO and it will enable or disable the up or down signals correspondingly which were connected to the charge pump through “D” flip flops.
- The AND operation to the outputs of “D” flip flops are given to the CLR(clear) input pin of the flip flop in order to make sure the output of “ D “ flip flop as zero when both outputs are same.  
- charge pump is one of the main component in the design which is used to produce the constant voltage to the input of the VCO in order to generate the output signal with the desired frequency.
- The response of the VCO is linear relation with input voltage,In our design we can control the VCO externally also by using the Multiplexer where the control signal could be to enable or disable the charge pump.

# circuit_level_functional_blocks_of_PLL
## Frequency Phase Detector Circuit
![](frequency_phase_detector1.png)
## Charge Pump Circuit
![](charge_pump1.png)
## Frequency Divider Circuit
![](fdd1.png)

# charge_pump_responses
## Positive Charge Pump
- If the average active time of the up signal is more 
than the down signal then the voltage across the charge 
pump is 
![](positive_Chargepump.png)

## Negative Charge Pump
- if the average active time of the down signal is more 
than the up signal then the voltage across the charge 
pump is
![](negative_chargepump.png)

- The main disadvantage of the charge pump is, it will get charged with leakage current also, without the presence 
of up and down signals , we can improve its performance by additional circuits(including the current mirrors)

# Phase_Frequency_Detectors
- while desigining the PHASE FREQUENCY 
DETECTOR we need to be very careful about the
dead zone which is the very small difference 
between the vref and vout signals 
- In the phase frequency detector the down signal
will get activated if leading the ref signal
- In the same manner the up signal will get 
activated if the out signal is lagging the ref signal
- Here in the charge pump design we have added
the RC series circuit in order to make the system 
stable
As we know 
I=dq/dt
and Q=CV
so I=d(CV)/dT
Hence 
dV/dT=I/C
- so total voltage across the charge pump will be 
equal to V= integration from  time 0 to t(till the 
operation of the of the charge pump) “t” can be 
more than the time period.
# State Diagram
![](state_diagram.png)
# Transfer Function Of The Charge Pump
![](derivation1.png)
# Transfer Fucntion Of The VCO
![](derivation2.png)

- Here on observing the transfer function 
of charge pump with only a single 
capacitor it includes a integrator in its T.F
- And we know the transfer function of 
VCO also had an integrator
- so on observing the complete open 
loop transfer function we will have S^2  
in its denomination which explicitly says 
the presence of oscillations(poles lies on 
the y axis)means system is not completly   
stable so in order to improve the stability 
of the system we are placing the Series 
RC combination at the 
- The Cx value should be =C/10 to 
achieve the higher stability.


# Voltage_Controlled_Oscillator
- The below diagram shows the ring oscillator
- The time period of the ring oscillator with 3 inverters 
is 6(delay of inverter)
- By using the starving mechanism we are generating the 
output frequency 
-Initially the charge pump voltage is used to control the
current through the curent mirrors as the Ring oscillator 
is connected in the series circuit of the current mirror, the
current through the ring oscillator determines the outout
generated frequency
## Ring Oscillator
![](ring_oscillator.png)
## Ring Oscillator with Starving Current circuit
![](current_mirrors.png)


# Important_Definations_Related_To_PLL
## Lock in range:
Once the PLL is locked, it can track the frequency change in the incoming signals. The range of frequencies over which the PLL can maintain the lock with incoming signal is called the lock-in range
## Capture range: 
The frequency range the PLL is able to lock-in from an unlocked state is called capture range
## Settling time: 
The time taken to attain the lock state from an unlocked state

# Tool_Setup
## npsice software
- ngspice for transistor level circuit simulation
- command to install ngspice: sudo apt-get install ngspice
- ngspice directly simulates the circuit file and plots the outputs.
- we need to download the sky130 primitive libraries considering the fact that PLL is designed for sky130nm mode
to run the file type: ngspice <circuit_file_name>

## magic software
- Magic software is for layout design and parasitics extraction
- Here we can modify and we can also extract the parasitics, GDS write features
- We need the technology file for 130nm mode
- To run the file type: magic -T  <technology_file_from_pdk> <circuit_file_name>


# Development_Flow
- specifications: mentioned by the client
- spice-circuit level design: designing the circuit which is satisfying the specification mentioned by the client
- pre layout simulations:checking the subcircuit level outputs and interconnecting them
- layout development: developing the layout by satisfying the design rules 
- parasitics extraction: in order to achieve the original behaviour of the design we need to simulate the design with parasitics , so here we are extracting the parasitics
- post layout simulation: checking out design performance over the designed layout
- Note: we need to do a lot of changes in the individual steps in order to meet the specifications


# PDKs_and_Specifications
- All the informations about the trasistor like(area,configurations,parameter)  are available in the PDK kit
- The pdk kit is just like ibis file in PCB level simulations
- The characteristics of the transistor are available in this kit
  - The pdk contains:
  - io-input-output 
  - pr-primitives(spice): we are using this in our PLL design
  - sc-standard cells
  - hd-high density
  - hs-high speed
  - lp-low power
  - hdll- high density low leakage

- specifications:
  - corner -’TT’ (Typical-Typical) which 
  - corresponds to doping
  - corner -’FS’- fast nmos and slow pmos
  - supply voltage -1.8v
  - Room temparature
  - VCO mode(where the control voltage is given directly to the VCO pin)
  - The range of input frequencies for which the PLL should work Fmin=5MHz;
  - Fmax=12.5MHz
  - Multiplier- 8X.
  - jitter-20ns(which means the time difference between the signals).

# Tools_dependicies_installation



