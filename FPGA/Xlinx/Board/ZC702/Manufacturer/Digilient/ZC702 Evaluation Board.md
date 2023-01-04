## 2. 1 Boot Mode Configuration
There are different dedicate configuration modes to select the device boot from which read only memory located.
![[Pasted image 20221201100652.png]]

as showing from this figure, the jumper locate in the red bounding box highlighted area from the board.
![[JP1T6.jpg]]

jumpers on MIO\[5\] - MIO\[3\] can be switched to connect with 3.3V to boot from different mode.
- MIO\[5\] - MIO\[3\] off -> debug only
- MIO\[5\] on -> boot from Quad-SPI memory
- MIO\[5\] + MIO\[4\] on -> boot from SD-Card

![[Pasted image 20221201101329.png]]
