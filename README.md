
# Mixed Signal 10-Bit Potentiometric Digital to Analog Converter Based on SCL180nm Process Node {msvsddac}


## 1. Introduction to Potentiometric Digital to Analog Converter
The project aims to design a 10-bit Potentiometric Digital to Analog Converter using Cadence tools with scl 180nm technology node.

Here are the specifications from [SPECS DOCUMENT](https://github.com/xzlashutosh/avsddac_3v3/blob/master/potentiometricDAC_IP.pdf)

Digital-to-Analog-Converter (DAC) systems are ubiquitous. They are needed to interface digital or discrete signals -- typically binary such as VDD and GND, to the physical, analog world that uses a continuous range of signals. DACs and their counterpart Analog-to-Digital-Converter (ADC)s are thus found in most electrical systems. Practical applications are found in process control, programmable voltage sources, communication systems, data acquistions systems and many more. Commercial DACs range from 4-bit DAC to ultra high precision 24-bit DAC (and higher) to fast 22M Samples/Sec and 16-bit precise DAC. In this repository I present the building blocks of a 10-bit DAC with requirements that are typical for integration in modern Systems-on-a-Chip (SoC). Various analog designs can found such as an inverter, up/down shifters, analog switch and various n-bit DAC's. They are presented including their Virtuoso simulation behavior using 130nm scl 180nm technology node.

This project is part of the **Mixed Signal design of ASIC's Course in IIIT Bengaluru Masters program** taught by VLSI System Design Founder, **Professor Kunal Gosh**. 


# Theory
### Operating principle
![DAC principle](https://user-images.githubusercontent.com/6376127/192501680-db959d55-320e-46c6-97cc-4cb83330bd93.jpeg)

The operating principle of a DAC shown in Fig. 1 is to convert a digital input code to an analog output signal. The input code open or close switches such that the correct output signal is created from a positive voltage reference or source typically called VREF. The negative reference can be ground or a negative voltage reference. The output signal can be a voltage or a current. The simplest DAC, a 1-bit DAC is an analog switch and switches between VREF and ground (or negative VREF). 

The smallest signal increments, the stepsize also called delta's or [1 Least Significant Bit (LSB)](https://wiki.analog.com/university/courses/tutorials/cmos-dac-chapter) can be uniform (linear DAC) or logarithmic (log DAC) but should always be [monotonic](https://en.wikipedia.org/wiki/Monotonic_function). 

### Characteristics
To analyze and compare DACs often four DC errors (static error characteristics)  ([Chapter 2, page 2.15 from the Data Conversion Handbook](https://www.analog.com/en/education/education-library/data-conversion-handbook.html)) are computed using the sampled and ideal transfer function:

- **Offset and Gain error.**

![Offset and Gain characteristic](https://user-images.githubusercontent.com/6376127/192576544-6de542f6-be5a-40da-b5ad-0e7f7ac6de77.png)

Image credits: [Data Conversion Handbook](https://www.analog.com/en/education/education-library/data-conversion-handbook.html)

The offset or shift error can be seen when the transition from DAC output value to the next one is consistently off by a certain value. To calculate the offset error we take the sampled DAC output at t=0 and take the difference to the ideal DAC output at t=0. The gain or slope error can be seen when the DAC output has its offset error removed and is compared to the ideal slope. To calculate the gain error: sampled DAC output at t=n - sampled DAC output at t=0 - (2<sup>^n</sup> - 1 LSB). 

- **Differential non-linearity (DNL).**
![DNL characteristic](https://user-images.githubusercontent.com/6376127/192559984-543719f2-49e3-4ed1-9ed9-41088e65bb19.png)

Image credits: [Original source All About Circuits, adapted from the Data Conversion handbook](https://www.allaboutcircuits.com/technical-articles/understanding-dnl-and-inl-specifications-of-a-digital-to-analog-converter/)

How much is the difference between the sampled step *height* at t=x+1 and the previous sampled step *height* at t=x compared to the ideal step height of 1 LSB? The difference or deviation per input code is measured in LSB units. 1 LSB = FS/2<sup>^n</sup>. For example, the DAC output at input code `010` has a height of 3.5 LSB while the DAC output at `001` is 1 LSB. The difference is 2.5 LSB. Compared to the ideal step size this is an increase (error) of +1.5 LSB. The DNL can be plotted over the entire input code range. If the DNL has only positive terms over the entire input range, the DAC is considered to be monotonic over the full scale (FS) output range, a desired property.

- **Integral non-linearity (INL).**
![INL characteristic](https://user-images.githubusercontent.com/6376127/192577443-1349215a-b577-4949-98f0-1a0d3819ead6.png)

Image credits: [Data Conversion Handbook](https://www.analog.com/en/education/education-library/data-conversion-handbook.html)

How much is the difference between the sampled step *height* at t=x and the ideal step *height* at t=x? The difference or deviation per input code is measured in LSB units. For example, in the DNL image at code `011` we eyeball that the sample is 4.5 LSB, while the previous sample was 3.5 LSB which is a perfect 1 LSB increase and thus a DNL error of 0. However, when we compute the INL we see that the ideal sample height is not 4.5 LSB but 3 LSB, meaning a INL error of + 1.5 LSB. The INL can be plotted over the entire input code range. For both the INL and the DNL the compensated sample output should be used which can be calculated as sample output - offset error - (n/(2<sup>^n</sup>-1 LSB)) * gain error ([see example from UiO IN5220 course material](https://www.uio.no/studier/emner/matnat/ifi/IN5220/v21/timeplan/in5220_v21_ex03_sol.pdf))

In SPICE simulation the transfer function can be aqcuired using a transient analysis, plotting time on the x-axis and DAC output on the y-axis. The DAC input or binary code start at all zero and and is incremented by 1 bit at fixed periods using a PWL or Pulse signal generator. The simulation time should be long enough to reach a steady state (DC) such that we can sample it. With Xschem, the INL and DNL metrics can be directly calculated and visualised from the raw simulated data, saving an export step to data processors and graphing tools such as Excel or Matlab. 

### Architecture
![Complete DAC](https://user-images.githubusercontent.com/6376127/192506303-bdb3a85e-4050-44f3-8655-eef9c75d7447.jpeg)

A complete DAC as show in Fig. 2 typically involves a output buffer stage using an integrated opamp, a precise voltage reference and I/O multiplexer using SPI or I2C with input buffer. Without the [sample&hold (SH) output buffer stage](https://www.analog.com/media/cn/training-seminars/tutorials/mt-090.pdf) the DAC is classified as a potentiometric DAC or *digital potentiometer*. Fabricated high precision DACs are [laser trimmed](https://en.wikipedia.org/wiki/Laser_trimming) and have [ESD I/O pins](https://www.ti.com/lit/ds/symlink/dac161s055.pdf) to protect the pins from unwanted ESD events.

### Design
Several DAC core designs have been published, each with trade-offs in terms of silicon area, speed, precision and other metrics. The presented overview is by no means comprehensive and only discusses resistive DAC (as opposed to [capacitive DAC](https://www.uio.no/studier/emner/matnat/ifi/IN5220/v21/timeplan/in5220_v21_ex04_sol.pdf)) designs based on voltage reference sources and with voltage output. A good comprehensive and practical reference is the [Data Conversion Handbook](https://www.analog.com/en/education/education-library/data-conversion-handbook.html) (2005) edited by Walt Kester from Analog Devices.

- Resistor string ([thermometer/fully decoded](https://www.researchgate.net/publication/266008269_MT-014_TUTORIAL_Basic_DAC_Architectures_I_String_DACs_and_Thermometer_Fully_Decoded_DACs)) design. This design requires 2<sup>^n</sup> resistors and thus requires a large area. For large n (>10 bit) it becomes hard to correctly match the resistors. The resistors have a process dependent minimum size such that process variance will not effect it much (the matching problem). Typically a decoder block is added to switch the 2<sup>^n</sup> transistors with just n inputs. A variant to this decoder design is the hierarchical design used in this work where the decoder is integrated in lower level DAC blocks (left most design). It offers good DNL.

![Resistor string architectures](https://user-images.githubusercontent.com/6376127/192521415-2a7db1e6-20f0-49a8-9b1a-962bccb07373.png)
Image credits: [UiO IN5220 course material](https://www.uio.no/studier/emner/matnat/ifi/IN5220/v21/timeplan/in5220_v21_06_dac.pdf) 

- Binary weighted (encoded) design. This design reduces the amount resistors from 2<sup>^n</sup> to just n. The trade-off is that each an increase in variation of resistors from just 1 in resistor string to n in binary weighted designs. Binary weighted designs also offer the worse DNL characteristic. 
![Binary Weighted architecture](https://user-images.githubusercontent.com/6376127/192524723-82377320-a10c-4dd4-b0fa-39abf8acf887.png)
Image credits: [UiO IN5220 course material](https://www.uio.no/studier/emner/matnat/ifi/IN5220/v21/timeplan/in5220_v21_06_dac.pdf) 

- R-2R (encoded) design. This design reduces the amount of resistors from 2<sup>^n</sup> to just 2n and decreases the variation in resistors from n to 2 compared to binary weighted designs.
![R-2R architecture](https://user-images.githubusercontent.com/6376127/192525477-65bf20dd-fb80-45ba-8922-417671c30629.png)
Image credits: [UiO IN5220 course material](https://www.uio.no/studier/emner/matnat/ifi/IN5220/v21/timeplan/in5220_v21_06_dac.pdf) 

- Segmented (hybrid) design. In a segmented or hybrid design two or more DACs are combined to mitigate some of the issues such as area or resistor variance. Segmented DAC designs typically have one DAC architecture handling the least significant bits (LSB) and another handling the rest (the most significant bits, MSB). Segmented DACs allow for very high speed designs and offer good trade-offs between area, power and performance. 
![Segmented architecture](https://user-images.githubusercontent.com/6376127/192537404-765f921f-ac82-4c44-ab7e-bd05697d52e7.png)
Image credits: [Walt Kester, introduction to segmented DAC](https://www.researchgate.net/publication/238727450_Basic_DAC_Architectures_III_Segmented_DACs)

- PWM DAC https://www.edn.com/phased-array-pwm-dac/

# Related work
- 1st generation DAC (2020) by Ashutosh Sharma. [Blog](https://www.vlsisystemdesign.com/10bit-dac-osu180nm/) [Repository](https://github.com/xzlashutosh/avsddac_3v3)
- 2nd generation DAC (2021) by Shalini Kanna, Harshitha Basavaraju, Skandha Deepsita, Kunal Ghosh. [Repository](https://github.com/vsdip/avsddac_3v3_sky130_v1)
- 3rd generation DAC (2022) by Steven Bos. This work

# Main contributions
- Comparison of SPICE simulation engines (ngspice, xyce serial, xyce parallel, hspice) 
- Schematic redesign of analog switch with improved transmission gate, up/down shifter, 5V transistor type, transistor L/W sizing
- Schematic redesign of n-bit DACs with uniform resistor ladder and transistor gate capacitance
- Layout redesign of analog switch 
- Layout redesign of n-bit DACs
- Static and dynamic power analysis
- Improved INL and DNL characteristics
- Integration of design in Efabless caravan (analog harness)
- Documentation
- New features: 
  - Enable/disable component switch to save power when unused
  - Triggerable output stage to create pulse trains
  - Bipolar DAC enabling both positive and negative voltage steps 
    
# Open source tools used and installation
- XSCHEM for schematic drawing and graphing. [Website](http://repo.hu/projects/xschem/xschem_man/xschem_man.html). [Repository](https://github.com/StefanSchippers/xschem)
- XYCE serial/parallel for simulation. [Website](https://xyce.sandia.gov/). [Repository](https://github.com/Xyce/Xyce) 
- MAGIC for layout. [Website](http://opencircuitdesign.com/magic/). [Repository](https://github.com/RTimothyEdwards/magic))
- KLAYOUT for GDS viewing. [Website](https://www.klayout.de/). [Repository](https://github.com/KLayout/klayout)
- OPENPDK for patched open PDK [Website](http://opencircuitdesign.com/open_pdks/index.html). [Repository](https://github.com/RTimothyEdwards/open_pdks)
- OPENLANE w/ OPENROAD for toolchain HDL to GDSII. [Website](https://openlane.readthedocs.io/en/latest/). [Repository](https://github.com/The-OpenROAD-Project/OpenLane) 
- EFABLESS ANALOG CARAVEL harness for on-chip debugging and control. [Website](https://github.com/efabless/caravel_user_project_analog/blob/main/docs/source/index.rst). [Repository](https://github.com/efabless/caravel_user_project_analog )  

Please refer to the websites or github repo's to install the tools. The installation process gets updates regularly so the website or repo are the best source of information. Most tools are installed on linux platforms. With Windows Subsystem for Linux (WSL) we can run Linux with Windows and retain full Windows Explorer (file managmenent) functionality and high CPU/GPU performance compared to a virtual machine. Docker containers such as [FOSS-ASIC-TOOLS](https://github.com/efabless/foss-asic-tools) offer to install all the above tools with just one installation which is convenient for most users. In my case I build each tool individually from source code as I needed newer versions that were not available yet. 

# IP Design Specs
![IP block](https://user-images.githubusercontent.com/6376127/192647277-81dd892c-05ba-43ed-8eb2-6ade996fda49.png)

| Parameter| Description| Min | Typ | Max | Unit | Condition |
| :---:  | :-: | :-: | :-: | :---:  | :-: | :-: |
|RL|Load resistance| 50|||Mohm|T=-40 to 85C|
|CL|Load capacitance|||1|pF|T=-40 to 85C|
|VDDA|Analog supply| |3.3||V|T=-40 to 85C|
|VDD|Digital supply voltage||1.8||V|T=-40 to 85C|
|VREFH|Reference voltage high|||3.3|V|T=-40 to 85C|
|VREFL|Reference voltage low|-3.3|||V|T=-40 to 85C|
|RES|Resolution| |10||bit|T=27C|
|VFS|Full Scale Voltage|-3.3| |3.291627| V |T=27C|
|URATE|Update rate||1 || MSamples/Sec |T=27C|

| Parameter| Pre-layout | Post-Layout |
| :---:  | :-: | :-: |
|DNL| -0.x LSB to 0.x LSB | -0.x LSB to 0.x LSB |
|INL| -0.x LSB to 0.x LSB| -0.x LSB to 0.x LSB |
|Gain Error| x | x |
|Offset Error| xE-07 V | xE-07 V |

# Comparison of SPICE simulators

Work in progress. 

- NGSPICE SERIAL (open source)
- NGSPICE PARALLEL (open source)
- XYCE SERIAL (open source)
- XYCE PARALLEL (open source)
- HSPICE SERIAL (commercial)
- HSPICE PARALLEL (commercial) NOT TESTED


## Table of Contents
- [1. Introduction to Potentiometric Digital to Analog Converter](#1-introduction-to-potentiometric-digital-to-analog-converter)
- [2. Potentiometric DAC Architecture Design](#2-potentiometric-dac-architecture-design)
- [3. Specification List](#3-specification-list)
- [4. EDA Tools Used](#4-eda-tools-used)
- [5. Pre-layout and Simulations](#5-pre-layout-and-simulations)
  * [A. Switch](#a-switch)
  * [B. 2-Bit DAC subcircuit](#b-2-bit-dac-subcircuit)
  * [C. 3-Bit DAC subcircuit](#c-3-bit-dac-subcircuit)
  * [D. 4-Bit DAC subcircuit](#d-4-bit-dac-subcircuit)
  * [E. 5-Bit DAC subcircuit](#e-5-bit-dac-subcircuit)
  * [F. 6-Bit DAC subcircuit](#f-6-bit-dac-subcircuit)
  * [G. 7-Bit DAC subcircuit](#g-7-bit-dac-subcircuit)
  * [H. 8-Bit DAC subcircuit](#h-8-bit-dac-subcircuit)
  * [I. 9-Bit DAC subcircuit](#i-9-bit-dac-subcircuit)
  * [J. 10-Bit-DAC](#j-10-bit-dac)
  * [Vout v/s Digital Code Graph for 10-Bit DAC](https://github.com/xzlashutosh/avsddac_3v3#vout-vs-digital-code-graph-for-10-bit-dac)
  * [INL(LSB) v/s Digital Code Graph for 10-Bit DAC](https://github.com/xzlashutosh/avsddac_3v3#inllsb-vs-digital-code-graph-for-10-bit-dac)
  * [DNL(LSB) v/s Digital Code Graph for 10-Bit DAC](https://github.com/xzlashutosh/avsddac_3v3#dnllsb-vs-digital-code-graph-for-10-bit-dac)
- [6. Layout and Simulations](#6-layout-and-simulations)
  * [A. Switch Layout](#a-switch-layout)
  * [B. Resistor Layout](#b-resistor-layout)
  * [C. Capacitor Layout](#c-capacitor-layout)
    + [Modification done in osu180nm to make use of a capacitor as a device -](#modification-done-in-osu180nm-to-make-use-of-a-capacitor-as-a-device--)
    + [Limitation of osu180 PDK -](#limitation-of-osu180-pdk--)
  * [D. 2-Bit DAC Subcircuit Layout](#d-2-bit-dac-subcircuit-layout)
  * [E. 3-Bit DAC Subcircuit Layout](#e-3-bit-dac-subcircuit-layout)
  * [F. 4-Bit DAC Subcircuit Layout](#f-4-bit-dac-subcircuit-layout)
  * [G. 5-Bit DAC Subcircuit Layout](#g-5-bit-dac-subcircuit-layout)
  * [H. 6-Bit DAC Subcircuit Layout](#h-6-bit-dac-subcircuit-layout)
  * [I. 7-Bit DAC Subcircuit Layout](#i-7-bit-dac-subcircuit-layout)
  * [J. 8-Bit DAC Subcircuit Layout](#j-8-bit-dac-subcircuit-layout)
  * [K. 9-Bit DAC Subcircuit Layout](#k-9-bit-dac-subcircuit-layout)
  * [L. 10-Bit-DAC Layout](#l-10-bit-dac-layout)
  * [Vout v/s Digital Code Graph for 10-Bit DAC](#vout-v-s-digital-code-graph-for-10-bit-dac-1)
  * [INL(LSB) v/s Digital Code Graph for 10-Bit DAC](#inl-lsb--v-s-digital-code-graph-for-10-bit-dac-1)
  * [DNL(LSB) v/s Digital Code Graph for 10-Bit DAC](#dnl-lsb--v-s-digital-code-graph-for-10-bit-dac-1)
- [7. Instructions to get started with the design](#7-instructions-to-get-started-with-the-design)
  * [For Pre-Layout Simulation -](#for-pre-layout-simulation--)
  * [For Post-Layout Simulation -](#for-post-layout-simulation--)
- [8. Author](#8-author)
- [9. Acknowledgments](#9-acknowledgments)
- [10. Contact Information -](#10-contact-information--)

## 2. Potentiometric DAC Architecture Design

The basic idea is to divide the voltage into N different voltage values in the range of Vref+ and Vref- for an N-Bit DAC. The design used here to achieve this is the simple resistor string DAC which consists of resistors in series. These resistors are then connected to various switches in such a fashion that it routes the exact voltage to the output.

The problem of the largeness of the circuit is reduced by building hierarchical subcircuits of switch, 2 Bit, 3 Bit, 4 Bit,....., 9 Bit DAC, and then two 9 Bit DAC is used to build the 10-Bit potentiometric DAC.

Have a look at the simplified architecture for potentiometric-DAC given below

<p align="center">
  <img width="7000" height="700" src="https://github.com/xzlashutosh/avsddac_3v3/blob/master/subcircuits/An%20overview%20of%2010-Bit%20PotDAC.png">
</p>

Given below is the block diagram of the DAC - 

<p align="center">
  <img width="7000" height="700" src="https://github.com/xzlashutosh/avsddac_3v3/blob/master/subcircuits/overview%20of%20design.png">
</p>

## 3. Specification List

| Parameter| Description| Min | Type | Max | Unit | Condition |
| :---:  | :-: | :-: | :-: | :---:  | :-: | :-: |
|RL|Load resistance| 50|||Mohm|T=-40 to 85C|
|CL|Load capacitance|||1|pF|T=-40 to 85C|
|VDDA|Analog supply| |3.3||V|T=-40 to 85C|
|VDD|Digital supply voltage||1.8||V|T=-40 to 85C|
|VREFH|Reference voltage high|||3.3|V|T=-40 to 85C|
|VREFL|Reference voltage low|0|||V|T=-40 to 85C|
|RES|Resolution| |10||bit|T=27C|
|VFS|Full Scale Voltage|0| |3.291627| V |T=27C|

| Parameter| Pre-layout | Post-Layout |
| :---:  | :-: | :-: |
|DNL| -0.999893345 LSB to 2.03065020 LSB | -1.182952606 LSB to 2.380283181 LSB |
|INL| -1.953038429 LSB to 0.527216491 LSB| -3.698306813 LSB to 0.181125461 LSB |
|Gain Error| 0 | 0 |
|Offset Error| 2.12E-07 V | 2.14E-07 V |

## 4. EDA Tools Used 
The design has been built using open-source EDA tools. The library used is osu180nm. 

I have used [eSim](https://esim.fossee.in/downloads) to build schematic, [ngSpice](http://ngspice.sourceforge.net/download.html) to run simulations and verify the circuit. [Magic](http://opencircuitdesign.com/magic/) has been used to lay-out the circuit.

## 5. Pre-layout and Simulations
The complete circuit of 10-Bit potentiometric DAC is built hierarchically using the following subcircuits.

### A. Switch
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Pre-Layout%20and%20Simulation/Switch%20-%20prelayout.JPG)

### B. 2-Bit DAC subcircuit
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/subcircuits/2-Bit_DAC.png)

### C. 3-Bit DAC subcircuit
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/subcircuits/3-bit_DAC.png)

### D. 4-Bit DAC subcircuit
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/subcircuits/4-bitDAC.png)

### E. 5-Bit DAC subcircuit
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/subcircuits/5_bit_dac.png)

### F. 6-Bit DAC subcircuit
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/subcircuits/6_bit_dac.png)

### G. 7-Bit DAC subcircuit
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/subcircuits/7_bit_dac.png)

### H. 8-Bit DAC subcircuit
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/subcircuits/8_bit_dac.png)

### I. 9-Bit DAC subcircuit
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/subcircuits/9_bit_dac.png)

### J. 10-Bit-DAC
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/subcircuits/10_bit_dac.png)

#### The source details at Vref+ = 3.3V and Vref- = 0V are given [here](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Pre-Layout%20and%20Simulation/SourceDetails.txt).


### Vout v/s Digital Code Graph for 10-Bit DAC

![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Pre-Layout%20and%20Simulation/prelayout%20-%20output%20waveform.png)


### INL(LSB) v/s Digital Code Graph for 10-Bit DAC

![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Pre-Layout%20and%20Simulation/INL(LSB).png)

### DNL(LSB) v/s Digital Code Graph for 10-Bit DAC

![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Pre-Layout%20and%20Simulation/DNL(LSB).png)

### For pre-layout simulation please follow [this](https://github.com/xzlashutosh/avsddac_3v3#for-pre-layout-simulation--).

## 6. Layout and Simulations 

### A. Switch Layout
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/LayoutImages/sw.JPG)
 

### B. Resistor Layout
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/LayoutImages/resistor.JPG)

value = 253 Ω

### C. Capacitor Layout
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/LayoutImages/capacitor.JPG)

value = 3.12nF

#### Modification done in osu180nm to make use of a capacitor as a device - 
 add 
 
        device capacitor None glass poly,pc 9000 73000 

in the #devices section inside the osu180nm.tech node.

#### Limitation of osu180 PDK - 
As the capacitor layer, or poly2 none are present in osu180nm, the capacitor can not be built in a straight forward way using the osu PDK.The above given modifications in the technology node were done to simulate the characteristics of the circuit. Here, defining the capacitor in a certain manner gives the required characteristics in the circuit.

### D. 2-Bit DAC Subcircuit Layout
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/LayoutImages/2.JPG)


### E. 3-Bit DAC Subcircuit Layout
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/LayoutImages/3.JPG)

### F. 4-Bit DAC Subcircuit Layout
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/LayoutImages/4.JPG)
 

### G. 5-Bit DAC Subcircuit Layout
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/LayoutImages/5.JPG)


### H. 6-Bit DAC Subcircuit Layout
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/LayoutImages/6.JPG)

### I. 7-Bit DAC Subcircuit Layout
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/LayoutImages/7.JPG)


### J. 8-Bit DAC Subcircuit Layout
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/LayoutImages/8.JPG)


### K. 9-Bit DAC Subcircuit Layout
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/LayoutImages/9.JPG)


### L. 10-Bit-DAC Layout
![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/LayoutImages/10.JPG)

size = 635.1 x 684.8 microns

#### The source details are [here](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/toPasteInEnd.txt).


### Vout v/s Digital Code Graph for 10-Bit DAC

![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/OutputWaveform.png)


### INL(LSB) v/s Digital Code Graph for 10-Bit DAC

![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/INL(LSB)-postLayout.png)

### DNL(LSB) v/s Digital Code Graph for 10-Bit DAC

![Alt Text](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/DNL(LSB)-postLayout.png)

### For post-layout simulation please follow [this](https://github.com/xzlashutosh/avsddac_3v3#for-post-layout-simulation--).


## 7. Instructions to get started with the design
### For Pre-Layout Simulation - 
1. Download eSim from the given [website](https://esim.fossee.in/downloads).
2. Download [this](https://github.com/xzlashutosh/avsddac_3v3/tree/master/Pre-Layout%20and%20Simulation) folder.
3. Keep the contents of [subcircuits folder](https://github.com/xzlashutosh/avsddac_3v3/tree/master/subcircuits) inside the eSim subcircuits folder (C:\FOSSEE\eSim\library\SubcircuitLibrary).
4. Store the [libary files](https://github.com/xzlashutosh/avsddac_3v3/tree/master/Libraries) in the eSim User Library section (C:\FOSSEE\eSim\library\deviceModelLibrary\User Libraries).
5. Open the 10_bit_dac project in eSim kept in Pre-Layout Simulation [folder ](https://github.com/xzlashutosh/avsddac_3v3/tree/master/Pre-Layout%20and%20Simulation).
6. Run the schematic, extract spice netlist, and simulate the design using ngSpice to view the output. You should get the output as shown above in the [figure](https://github.com/xzlashutosh/potentiometric-DAC/blob/master/subcircuits/10_bit_dac.png).

The simulation may take more than an hour to complete. Kindly keep patience.

### For Post-Layout Simulation - 
1. Download Magic from the given [website](http://opencircuitdesign.com/magic/) for Linux and Mac.
For windows, you will have to install Cygwin Terminal and then Magic can run on the Windows platform also. Kindly look [here](http://opencircuitdesign.com/cygwin/magic.html) for Windows operating system. 

2. Download [this](https://github.com/xzlashutosh/avsddac_3v3/tree/master/Layout%20and%20Simulation) folder or complete repository.
3. Go to the specific directory to run the Magic file.
4. To open the 10BitDac.mag with osu180nm.tech using terminal type - 
       magic -T osu180nm.tech 10BitDac
5. To extract the spice netlist type in tkcon window -

       extract all
       ext2spice 10BitDac.ext
     
6. After you get the extracted netlist, to add the contents of [this](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/toPasteInStarting.txt) file at the beginning of the spice file, and also add the contents of [this](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/toPasteInEnd.txt) file at the end of the spice file to append the pmos,nmos libraries, and simulation parameters.
7. Now, to simulate the layout, run ngspice in another terminal using -

       ngspice 10BitDac.spice
       
8. You should get the output as shown above in the [figure](https://github.com/xzlashutosh/avsddac_3v3/blob/master/Layout%20and%20Simulation/OutputWaveform.png).
  
## 8. Author 
- Ashutosh Sharma, B.Tech, Electronics & Communication Engineering, IIITD&M Kurnool

## 9. Acknowledgments
- Kunal Ghosh, Co-founder VSD Corp. Pvt. Ltd.
- Philipp Gühring, Software Architect, LibreSilicon Association.

I would also like to thank research fellows for extending their help and guidance during the research internship program. Many Thanks to 

- Ankur Sah, M.tech Embedded Systems, NIT Jamshedpur, ankursah5@gmail.com
- Nikhil Shinde, B.E., KJSIEIT Mumbai, shinde.nv@somaiya.edu
- Shubham Negi, B.Tech. Computer Engineering, IIITD&M Kurnool, coe17b030@iiitk.ac.in
- Sheryl Serrao, Undergraduate Student, Mumbai University, sherylcorina@gmail.com

## 10. Contact Information - 
 - Ashutosh Sharma, B.Tech, Electronics, and Communication Engineering, IIITD&M Kurnool, xzlashutosh@gmail.com
 - Kunal Ghosh, Co-founder, VSD Corp. Pvt. Ltd. - kunalghosh@gmail.com
 - Philipp Gühring, Software Architect, LibreSilicon Association - pg@futureware.at





#######################################
The project aims to design a 10-bit Potentiometric Digital to Analog Converter using Cadence tools with scl 180nm technology node.

## 1.Introduction
In real world, most of the data available is in the form of analog in nature. We have two types of converters analog to digital converter and digital to analog converter. These two converting interfaces are essential to obtain the required operations of a processor to manipulate the data of digital electronic equipment and an analog electric equipment. Digital to Analog Converter (DAC) is a device that transforms digital data into an analog signal in order to interact with the real world. The digital signal is represented with a binary code, which is a combination of bits 0’s and 1’s. The digital data can be produced from a microprocessor, Field Programmable Gate Array (FPGA), or Application Specified Integrated Circuit (ASIC). There are two commonly used DAC conversions – Weighed resistors method and R-2R ladder network method. Applications of a DAC: audio amplifier, video encoder, display electronics, data acquisition systems, calibration, Digital potentiometer.


# PRE-LAYOUT SIMULATION

## Circuit 1 bit DAC

![image](https://user-images.githubusercontent.com/110079648/217048054-f2a88a23-05a2-4873-bdb1-9d0fda6e9fbd.png)

> Simulation

![Screenshot from 2023-02-06 18-32-43](https://user-images.githubusercontent.com/110079648/217050109-f2e4390b-ee6c-49be-b992-bf2eb81c48c3.png)


## Circuit 2 bit DAC

![Screenshot from 2023-02-06 23-58-22](https://user-images.githubusercontent.com/110079648/217055276-457ddb2f-9ebd-4837-a794-351125df7535.png)

> Simulation

![Screenshot from 2023-02-06 23-58-15](https://user-images.githubusercontent.com/110079648/217055371-537fb5f0-6a21-4191-bff6-afda59311cb5.png)

## Circuit 3 bit DAC

<img width="957" alt="image" src="https://user-images.githubusercontent.com/110079648/218561977-84dd198e-7a6f-4c06-a443-573b9cdf4f21.png">

> Simulation

<img width="1331" alt="image" src="https://user-images.githubusercontent.com/110079648/218562101-9de84391-b0b1-4f18-9bfd-11b60ac75dcd.png">

## Circuit 4 bit DAC

<img width="1112" alt="image" src="https://user-images.githubusercontent.com/110079648/218562276-08f1dd1c-2465-432d-afc3-7a81e9cc060f.png">

> Simulation

<img width="1330" alt="image" src="https://user-images.githubusercontent.com/110079648/218562358-1e1b755e-05f4-41a3-8e8a-89a0923a3a02.png">

## Circuit 5 bit DAC

<img width="1110" alt="image" src="https://user-images.githubusercontent.com/110079648/218562595-7f2b1b69-5b18-42a6-866b-a065286d9d89.png">

> Simulation

<img width="1324" alt="image" src="https://user-images.githubusercontent.com/110079648/218562691-c7b28881-0d3e-4904-8cbc-2cf2188529df.png">

## Circuit 6 bit DAC

<img width="1130" alt="image" src="https://user-images.githubusercontent.com/110079648/218562802-0df97e68-c20a-4164-a628-64033372d6d2.png">

> Simulation

<img width="1324" alt="image" src="https://user-images.githubusercontent.com/110079648/218562862-947f4f87-ec17-4f40-97b7-8220dc014f8b.png">

## Circuit 7 bit DAC

<img width="1170" alt="image" src="https://user-images.githubusercontent.com/110079648/218562981-53a4165f-11db-4a91-a45e-79c39850464c.png">

> Simulation

<img width="1325" alt="image" src="https://user-images.githubusercontent.com/110079648/218563053-c063dd4b-5e4e-4cc7-b25a-928d19a11fae.png">

## Circuit 8 bit DAC

<img width="1012" alt="image" src="https://user-images.githubusercontent.com/110079648/218563164-e9cd261c-5836-48c4-940b-41c463d87609.png">

> Simulation

<img width="1328" alt="image" src="https://user-images.githubusercontent.com/110079648/218563273-17074702-d429-4609-8841-47f7a0370128.png">

## Circuit 9 bit DAC

<img width="1002" alt="image" src="https://user-images.githubusercontent.com/110079648/218563427-711aeb87-7ee6-4612-b9a5-e12cc5910dbd.png">

> Simulation

<img width="1323" alt="image" src="https://user-images.githubusercontent.com/110079648/218563604-b7feb219-f3a5-4fc8-a8e5-b624dfeb2b31.png">

## Circuit 10 bit DAC

<img width="1011" alt="image" src="https://user-images.githubusercontent.com/110079648/218563748-49f23527-4934-423c-a4df-6dbd3843a089.png">

<img width="826" alt="image" src="https://user-images.githubusercontent.com/110079648/218563940-0e068ada-a0fd-4fb1-8b55-0d8d4742d864.png">

<img width="719" alt="image" src="https://user-images.githubusercontent.com/110079648/218563967-f41b9341-4775-4211-982a-22a6253b4ceb.png">

> Simulation

<img width="1323" alt="image" src="https://user-images.githubusercontent.com/110079648/218563872-b85f1ceb-0b25-4f95-92ce-90a76fdac19f.png">

# POST-LAYOUT SIMULATION

-- Pending















# Archive
## Tool 
Creating Inverter writen in Verilog

![image](https://user-images.githubusercontent.com/110079648/217043527-42724b49-8e2a-4e0d-813b-42e50f559d52.png)

![image](https://user-images.githubusercontent.com/110079648/217045647-d8ee110c-4429-4e80-95d2-e81e0bcccff3.png)

After saving and closing, it will ask to create a symbol based on inputs and outputs to module.

then create a schematic, to thest this block

![image](https://user-images.githubusercontent.com/110079648/217049822-9f85bb26-a0a1-440e-b25b-1334225e4231.png)

create a config file also for this test bench.

![image](https://user-images.githubusercontent.com/110079648/217049140-b882c3d0-1a47-4832-900e-9ad5a603282a.png)


![image](https://user-images.githubusercontent.com/110079648/217046766-649bdbb3-1b69-497f-a20b-7b2b34998437.png)

to simulate launch ADE-L and chage simulator to ams from spectre

![image](https://user-images.githubusercontent.com/110079648/217047144-1bdea6f3-6e8a-4416-97f5-3c8437375f42.png)

# ERROR

![image](https://user-images.githubusercontent.com/110079648/217049589-f05b6fe5-43aa-4fb7-98d7-7ee81010fcbe.png)









