
# Mixed Signal 10-Bit Potentiometric Digital to Analog Converter Based on SCL180nm Process Node { msvsddac }

The project aims to design a 10-bit Potentiometric Digital to Analog Converter using Cadence tools with scl 180nm technology 0node.

## 1.Inroduction
In real world, most of the data available is in the form of analog in nature. We have two types of converters analog to digital converter and digital to analog converter. These two converting interfaces are essential to obtain the required operations of a processor to manipulate the data of digital electronic equipment and an analog electric equipment. Digital to Analog Converter (DAC) is a device that transforms digital data into an analog signal in order to interact with the real world. The digital signal is represented with a binary code, which is a combination of bits 0’s and 1’s. The digital data can be produced from a microprocessor, Field Programmable Gate Array (FPGA), or Application Specified Integrated Circuit (ASIC). There are two commonly used DAC conversions – Weighed resistors method and R-2R ladder network method. Applications of a DAC: audio amplifier, video encoder, display electronics, data acquisition systems, calibration, Digital potentiometer.

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



## Circuit

![image](https://user-images.githubusercontent.com/110079648/217048054-f2a88a23-05a2-4873-bdb1-9d0fda6e9fbd.png)

Simulation

![Screenshot from 2023-02-06 18-32-43](https://user-images.githubusercontent.com/110079648/217050109-f2e4390b-ee6c-49be-b992-bf2eb81c48c3.png)







