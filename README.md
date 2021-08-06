# A two day work shop on designing the phase locked loop using sky130nm - techology

![](introduction.png)

# Table of contents

- [Overview](#Overview)
- [DAY 1: PLL theory and Lab overview](#Day1)
  -  [Introduction to PLL](#PLL)
  -  [PLL Components](#PLLComponents)
  -  [circuit level functional block diagrams](#circuit_level_functional_blocks_of_PLL)
  -  [charge pump responses](#charge_pump_responses)
  -  [Voltage controlled Oscillator](#VCO)
  
  
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


# VCO
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

