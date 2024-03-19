## SKL Radar [[FPGA]] - [[SPI]]
---
## Core: ZYNQ7000 - xc7z020clg484-1 (Zedboard)

---
### Register Recommended Setup
| Register (Offset) | Function | Bit 7 | Bit 6 | Bit 5 | Bit 4 | Bit 3 | Bit 2 | Bit 1 | Bit 0 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 0x00 |  SPI Status & Control | nDone | N/A | N/A | N/A | Send Data | Receive | Load Data | Reset |
| 0x04 | Output Data buffer | Data[7] | Data[6] | Data[5] | Data[4] | Data[3] | Data[2] | Data[1] | Data[0] |
| 0x08 | Read Data buffer | Data[7] | Data[6] | Data[5] | Data[4] | Data[3] | Data[2] | Data[1] | Data[0] |
| 0x0C | Transmit Limit |  |  |  |  |  |  |  |  |

#### Ports:
- i_Clk: Clock for logic
- i_Cmd_Lim: Setup how many bytes of data to send before CS set
- i/o_StatusReg: Input / Output of the Status register, updated by user embedded program / logic
- i_TxBuffer: Byte to load into send query
- o_RxBuffer: Byte read from MISO
- o_SPL_Clk: SPI Clock
- i_SPI_MISO: SPI MISO
- o_SPI_MOSI: SPI MOSI
- o_LED_Temp: 8Bit LED Debug

#### Clock Configuration & Parameter:
- Parameter:
    - CLKS_PER_HALF_BIT: counter for set / reset clock, default 9
- i_Clk: 250MHz
---

#### Tasks (Function):
- t_preChange: Setup ready to change the SPI Clock
- t_Change: Toggle the SPI Clock. If the transmit limit reached, the logic will setup counter delay to set SPI CS
- t_reset: Reset the control parameter to default value
- t_ReadinTX: Load the transmit data to the buffer from user

#### Logic state (reference to comment):
1. Wait the r_CmdAccept set, when previous SPI communicate done and ready for next communication start
2. Logic load in data / start the SPI communication according to the status register
3. Start the SPI communication with the start up countdown
4. Place the bits of data to dataline
5. Set the end counter according to next data or end transmition

#### Special Mention:
- Register data initialize is done in initial block since the TX byte array initialize is need.
- always(*) block is used to assign the register to output port or debug.
---