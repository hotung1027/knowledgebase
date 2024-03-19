## SKL Radar [[FPGA]] - [[ADC]]
---
## Core: ZYNQ7000 - xc7z020clg484-1 (Zedboard)
## ADC: ADS41B29

---
## FPGA Input Timing Constraints
![[Pasted image 20221228112432.png]]
![[Pasted image 20221207174513.png]]

---

| LED  | Function                                |
| ---- | --------------------------------------- |
| 0    | M_AXIS_TREADY                           |
| 1    | AXIS_TVALID                             |
| 2    | (read_pointer < NUMBER_OF_OUTPUT_WORDS) |
| 3    | TX_DONE                                 |
| 4    | INIT_AXI_TXN                            |
| 5    | i_Trigger                               |
| 7-:2 | mst_exec_state                          | 


### IP Register & Port
Interface Type : [[AXI4]], [[AXI4-Stream]]
- Control interface:  [[AXI4 GP]] interface slave mode
- Data interface: [[AXI4 HP]] Stream inteface master mode
- Input port: 12 bit ADC interface
- Output port: [[SPI]] communication interface

| Register (Offset) | Function | Bit 7 | Bit 6 | Bit 5 | Bit 4 | Bit 3 | Bit 2 | Bit 1 | Bit 0 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 0x00 |  SPI Status & Control | nDone | N/A | N/A | N/A | Send Data | Receive | Load Data | Reset |
| 0x04 | Output Data buffer | Data[7] | Data[6] | Data[5] | Data[4] | Data[3] | Data[2] | Data[1] | Data[0] |
| 0x08 | Read Data buffer | Data[7] | Data[6] | Data[5] | Data[4] | Data[3] | Data[2] | Data[1] | Data[0] |
| 0x0C | Transmit Limit |  |  |  |  |  |  |  |  |
| 0x10 | ADC Status | N/A | N/A | N/A | N/A | N/A | N/A | N/A | ADC Done |
| 0x14 | ADC Control | N/A | N/A | N/A | N/A | N/A | N/A | N/A | ADC Enable |
![[Pasted image 20230113222308.png]]
#### Ports:
- i_Trigger: External Trigger of ADC, trigger on falling edge
- i_Mode: Debug External Control, data will be sequential data instead of ADC sampled data when logic low 

#### Clock Configuration:
- m00_axis_aclk: 250MHz
- i_CMOS_Clk: 250MHz
- s00_axi_aclk: 100MHz

#### Operation Method:
- IO Trigger connected to IP and PS Input pin, PS will set 0x14 ADC Enable bit to 1 when ready to read data.
- Data will be sample once the i_Trigger have the falling edge
- Currently the data amount is fixed by define the value in IP
- Data will be writen into RAM through DMA
- PS will set the ADC Enable bit to 0 and wait until ready signal through user control

---
### PS Configuration
#### Preripheral Enabled:
- [[USB]] (Uart)
- [[Ethernet]]
- [[Stream to Memory Map (S2MM)]]

#### Ethernet setup
IP Address: 192.168.1.10

Protocal: [[TCP]]/IP

### FPGA Configuration
---
#### 1.Memory Buffer Size
During design the FPGA, the size of buffer should be consider for matching the bandwidth required storing data from ADC.

In Below, Width of Buffer Length Register $x$ presenting the a buffer size of $2^x$ bits for the DMA.
For example, we will receive about 5000 data point, each are 12 bit size, these require 3 digit in the register, which is inefficient to align cache line, so we pad the data point to 4 digit size in the register. Therefore, we require 5000 * 16 bit size of buffer, which is 80000 bits >= 2^16 bits,
![[Pasted image 20230103153724.png]]