## 2. 1 Boot Mode Configuration
There are different dedicate configuration modes to select the device boot from which read only memory located.
![[Pasted image 20221201100652.png]]

as showing from this figure, the jumper locate in the red bounding box highlighted area from the board.
![[JP1T6integer
C_M_AXIS_TDATA_WIDTH
integer
C_M_START_COUNT
[1:0]
IDLE
wire
i_CMOS_Clk
wire [11:0]
i_CMOS_Data
wire
i_ADC_Work
wire
INIT_AXI_TXN
wire
i_Trigger
wire
i_Mode
wire
M_AXIS_ACLK
wire
M_AXIS_ARESETN
wire
M_AXIS_TREADY
wire
o_ADC_Done
wire [7:0]
o_LED
wire
M_AXIS_TVALID
wire [C_M_AXIS_TDATA_WIDTH-1 : 0]
M_AXIS_TDATA
wire [(C_M_AXIS_TDATA_WIDTH/8)-1 : 0]
M_AXIS_TSTRB
wire
M_AXIS_TLAST

.jpg]]

jumpers on MIO\[5\] - MIO\[3\] can be switched to connect with 3.3V to boot from different mode.
- MIO\[5\] - MIO\[3\] off -> debug only
- MIO\[5\] on -> boot from Quad-SPI memory
- MIO\[5\] + MIO\[4\] on -> boot from SD-Card

![[Pasted image 20221201101329.png]]
