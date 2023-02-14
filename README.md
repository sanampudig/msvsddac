
# Mixed Signal 10-Bit Potentiometric Digital to Analog Converter Based on SCL180nm Process Node {msvsddac}

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









