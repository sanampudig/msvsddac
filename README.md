
# Mixed Signal 10-Bit Potentiometric Digital to Analog Converter Based on SCL180nm Process Node {msvsddac}


## 1. Introduction to Potentiometric Digital to Analog Converter
The project aims to design a 10-bit Potentiometric Digital to Analog Converter using Cadence tools with scl 180nm technology node.

Here are the specifications from [SPECS DOCUMENT](https://github.com/sanampudig/msvsddac/blob/main/potentiometricDAC_IP.pdf)

Digital-to-Analog-Converter (DAC) systems are ubiquitous. They are needed to interface digital or discrete signals -- typically binary such as VDD and GND, to the physical, analog world that uses a continuous range of signals. DACs and their counterpart Analog-to-Digital-Converter (ADC)s are thus found in most electrical systems. Practical applications are found in process control, programmable voltage sources, communication systems, data acquistions systems and many more. Commercial DACs range from 4-bit DAC to ultra high precision 24-bit DAC (and higher) to fast 22M Samples/Sec and 16-bit precise DAC. In this repository I present the building blocks of a 10-bit DAC with requirements that are typical for integration in modern Systems-on-a-Chip (SoC). Various analog designs can found such as an inverter, up/down shifters, analog switch and various n-bit DAC's. They are presented including their Virtuoso simulation behavior using 130nm scl 180nm technology node.

This project is part of the **Mixed Signal design of ASIC's Course in IIIT Bengaluru Masters program** taught by VLSI System Design Founder, **Professor Kunal Gosh**. 

## Table of Contents
- [1. Introduction to Potentiometric Digital to Analog Converter](#1-Introduction-to-Potentiometric-Digital-to-Analog-Converter)
- [2. Operating principle](#2-Operating-principle)
- [3. Characteristics](#3-Characteristics)
- [4. Potentiometric DAC Architecture Design](#4-Potentiometric-DAC-Architecture-Design)
- [5. IP Design Specs](#5-IP-Design-Specs)
- [6. Pre-layout and Simulations](#6-pre-layout-and-simulations)
  * [A. 1-Bit DAC subcircuit](#a-1-bit-dac-subcircuit)
  * [B. 2-Bit DAC subcircuit](#b-2-bit-dac-subcircuit)
  * [C. 3-Bit DAC subcircuit](#c-3-bit-dac-subcircuit)
  * [D. 4-Bit DAC subcircuit](#d-4-bit-dac-subcircuit)
  * [E. 5-Bit DAC subcircuit](#e-5-bit-dac-subcircuit)
  * [F. 6-Bit DAC subcircuit](#f-6-bit-dac-subcircuit)
  * [G. 7-Bit DAC subcircuit](#g-7-bit-dac-subcircuit)
  * [H. 8-Bit DAC subcircuit](#h-8-bit-dac-subcircuit)
  * [I. 9-Bit DAC subcircuit](#i-9-bit-dac-subcircuit)
  * [J. 10-Bit-DAC](#j-10-bit-dac)
  * [Vout v/s Digital Code Graph for 10-Bit DAC](#Vout-vs-Digital-Code-Graph-for-10-Bit-DAC)
  * [DNL(LSB) v/s Digital Code Graph for 10-Bit DAC](#DNL-LSB-vs-Digital-Code-Graph-for-10-Bit-DAC)
  * [INL(LSB) v/s Digital Code Graph for 10-Bit DAC](#INL-LSB-vs-Digital-Code-Graph-for-10-Bit-DAC)
- [7. Post-layout and Simulations](#6-post-layout-and-simulations)
- [8. Contributors ](#8-Contributors)
- [9. Acknowledgments](#9-Acknowledgments)
- [10. Contact Information](#10-Contact-Information)
- [11. References](#11-References)



## 2. Operating principle
![DAC principle](https://user-images.githubusercontent.com/6376127/192501680-db959d55-320e-46c6-97cc-4cb83330bd93.jpeg)

The operating principle of a DAC shown in Fig. 1 is to convert a digital input code to an analog output signal. The input code open or close switches such that the correct output signal is created from a positive voltage reference or source typically called VREF. The negative reference can be ground or a negative voltage reference. 

The smallest signal increments, the stepsize also called delta's or [1 Least Significant Bit (LSB)](https://wiki.analog.com/university/courses/tutorials/cmos-dac-chapter) can be uniform (linear DAC) or logarithmic (log DAC) but should always be [monotonic](https://en.wikipedia.org/wiki/Monotonic_function). 

## 3. Characteristics
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

## 4. Potentiometric DAC Architecture Design

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

## 5. IP Design Specs
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


## 6. Pre-layout and Simulations

### A. 1-Bit DAC subcircuit

![image](https://user-images.githubusercontent.com/110079648/217048054-f2a88a23-05a2-4873-bdb1-9d0fda6e9fbd.png)

> Simulation

![Screenshot from 2023-02-06 18-32-43](https://user-images.githubusercontent.com/110079648/217050109-f2e4390b-ee6c-49be-b992-bf2eb81c48c3.png)


### B. 2-Bit DAC subcircuit

![Screenshot from 2023-02-06 23-58-22](https://user-images.githubusercontent.com/110079648/217055276-457ddb2f-9ebd-4837-a794-351125df7535.png)

> Simulation

![Screenshot from 2023-02-06 23-58-15](https://user-images.githubusercontent.com/110079648/217055371-537fb5f0-6a21-4191-bff6-afda59311cb5.png)

### C. 3-Bit DAC subcircuit

<img width="957" alt="image" src="https://user-images.githubusercontent.com/110079648/218561977-84dd198e-7a6f-4c06-a443-573b9cdf4f21.png">

> Simulation

<img width="1331" alt="image" src="https://user-images.githubusercontent.com/110079648/218562101-9de84391-b0b1-4f18-9bfd-11b60ac75dcd.png">

### D. 4-Bit DAC subcircuit

<img width="1112" alt="image" src="https://user-images.githubusercontent.com/110079648/218562276-08f1dd1c-2465-432d-afc3-7a81e9cc060f.png">

> Simulation

<img width="1330" alt="image" src="https://user-images.githubusercontent.com/110079648/218562358-1e1b755e-05f4-41a3-8e8a-89a0923a3a02.png">

### E. 5-Bit DAC subcircuit

<img width="1110" alt="image" src="https://user-images.githubusercontent.com/110079648/218562595-7f2b1b69-5b18-42a6-866b-a065286d9d89.png">

> Simulation

<img width="1324" alt="image" src="https://user-images.githubusercontent.com/110079648/218562691-c7b28881-0d3e-4904-8cbc-2cf2188529df.png">

### F. 6-Bit DAC subcircuit

<img width="1130" alt="image" src="https://user-images.githubusercontent.com/110079648/218562802-0df97e68-c20a-4164-a628-64033372d6d2.png">

> Simulation

<img width="1324" alt="image" src="https://user-images.githubusercontent.com/110079648/218562862-947f4f87-ec17-4f40-97b7-8220dc014f8b.png">

### G. 7-Bit DAC subcircuit

<img width="1170" alt="image" src="https://user-images.githubusercontent.com/110079648/218562981-53a4165f-11db-4a91-a45e-79c39850464c.png">

> Simulation

<img width="1325" alt="image" src="https://user-images.githubusercontent.com/110079648/218563053-c063dd4b-5e4e-4cc7-b25a-928d19a11fae.png">

### H. 8-Bit DAC subcircuit

<img width="1012" alt="image" src="https://user-images.githubusercontent.com/110079648/218563164-e9cd261c-5836-48c4-940b-41c463d87609.png">

> Simulation

<img width="1328" alt="image" src="https://user-images.githubusercontent.com/110079648/218563273-17074702-d429-4609-8841-47f7a0370128.png">

### I. 9-Bit DAC subcircuit

<img width="1002" alt="image" src="https://user-images.githubusercontent.com/110079648/218563427-711aeb87-7ee6-4612-b9a5-e12cc5910dbd.png">

> Simulation

<img width="1323" alt="image" src="https://user-images.githubusercontent.com/110079648/218563604-b7feb219-f3a5-4fc8-a8e5-b624dfeb2b31.png">

### J. 10-Bit DAC subcircuit

<img width="1011" alt="image" src="https://user-images.githubusercontent.com/110079648/218563748-49f23527-4934-423c-a4df-6dbd3843a089.png">

<img width="826" alt="image" src="https://user-images.githubusercontent.com/110079648/218563940-0e068ada-a0fd-4fb1-8b55-0d8d4742d864.png">

<img width="719" alt="image" src="https://user-images.githubusercontent.com/110079648/218563967-f41b9341-4775-4211-982a-22a6253b4ceb.png">

> Simulation

<img width="1323" alt="image" src="https://user-images.githubusercontent.com/110079648/218563872-b85f1ceb-0b25-4f95-92ce-90a76fdac19f.png">

### Vout vs Digital Code Graph for 10-Bit DAC

### DNL-LSB vs Digital Code Graph for 10-Bit DAC


### INL-LSB vs Digital Code Graph for 10-Bit DAC

## 7. Post-layout and Simulations

## 8. Contributors 

- **Sanampudi Gopala Krishna Reddy** 
- **Kunal Ghosh** 



## 9. Acknowledgments


- Kunal Ghosh, Director, VSD Corp. Pvt. Ltd.
- Steven Bos
## 10. Contact Information

- Sanampudi Gopala Krishna Reddy, Postgraduate Student, International Institute of Information Technology, Bangalore  svgkr7@gmail.com
- Kunal Ghosh, Director, VSD Corp. Pvt. Ltd. kunalghosh@gmail.com

## 11. References
- [kunalg123/avsddac_workshop](https://github.com/kunalg123/avsddac_workshop#readme)







