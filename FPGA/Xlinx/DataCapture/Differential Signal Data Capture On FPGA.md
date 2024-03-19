# Versal Core Archietecture for ACAP
![[Pasted image 20221206174049.png]]
![[Pasted image 20221206182146.png]]
# Timming  Compensation
![[Pasted image 20221206183811.png]]

# Defining Clocks
The first step in setting up timing constraints is to define the external clocks for both the RX and TX domains. For the RX clock domain, the clock that exists at the actual FPGA input port is the data clock that is output from the ADC. This clock is defined as shown in the SDC line below. 
> The period is set to 4 ns, assuming the ADS4249 is running at 250 MSPS, and the clock is applied to the lvds_rx_clk_p input port defined in the top-level verilog file.
````
create_clock -name ADC_DATA_CLK -period 4.00 [get_ports lvds_rx_clk_p]
````
Next, a virtual clock is created defining the ideal launch clock for the data coming out of the ADC. Since the ADC is a center-aligned interface, the launch clock needs to be advanced by 90° in order to obtain the correct initial setup and hold times. 
> This is shown in the line below, where the waveform parameter defines the rising and falling edge position, respectively, where the default values are 0 ns and 2 ns for a 250-MHz clock. Advancing the clock by 90° is equivalent to having the rising edge occur 1 ns sooner, so a value of –1 ns should work, however, the waveform parameter cannot have negative values. Since the waveform parameter understands that clocks are periodic, and then a value of 3 ns and 5 ns will be understood as –1 ns and 1ns.
````
create_clock -name ADC_LAUNCH_CLK -period 4.00 -waveform {3 5}
````
Next, the ADC latch clock is manually created from the PLL output as shown below, rather than allowing the tool to do it automatically. This way, the name ADC_LATCH_CLK can be used in other statements rather than using the longer, automatically generated PLL clock name. These clock definitions must come before the derive_pll_clocks statement mentioned below.
````
create_generated_clock -name ADC_LATCH_CLK \ -source [get_pins {RX_PLL_inst|altpll_component|auto_generated|pll1|inclk[0]}] \ [get_pins {RX_PLL_inst|altpll_component|auto_generated|pll1|clk[0]}]
````
Adding the two lines below automatically derives the clocks of the PLL that have not already been defined and calculates the clock uncertainty.
````
derive_pll_clocks 
derive_clock_uncertainty
````
![[Pasted image 20221207174223.png]]

>In the figure, the green blocks represent the total skew that the FPGA is allowed to introduce internally and the red blocks represent the maximum and minimum delays that
> the ADC may introduce on the data line
> 
![[Pasted image 20221207174606.png]]
# Constraint Check
The following sections will breakdown the example code in detail. A simplified block diagram of
the examt
Here's a list of things you'll need to do/check next:

1.  Make sure the data clock, DCLK_P / DCLK_N, from the ADC is routed to an LVDS pin-pair on the FPGA that is **clock-capable**.  
2.  Write a **create_clock** constraint for the 150MHz data clock that looks something like the following:
3. ```` create_clock -period 6.667 [get_ports DCLK_P]````
4.  Write the following constraint for the MMCM that you are using.  Later, when you enter phase-shifts in degrees into the Clocking-Wizard, this constraint will ensure that they are properly converted to clock delay/advance in seconds.```
````
set_property PHASESHIFT_MODE LATENCY [get_cells MMCM1/inst/mmcm_adv_inst]
````

1.  Also, when you setup the MMCM using the Clocking-Wizard, place a checkmark in the box called "**Phase Alignment**" on the "Clocking Options" tab.  This will ensure that input and output clock of the MMCM have a known phase relationship.
2.  Write **set_input_delay** constraints for each data, SDAT_P[11 downto 0] / SDAT_N[11 downto 0], LVDS line coming from the ADC.  You can use wildcards (*) as shown below if all these data lines have the same physical length.
````
1.  set_input_delay -clock [get_clocks DCLK_P] -max tco_max [get_ports {SDAT_P[*]}] 2.  set_input_delay -clock [get_clocks DCLK_P] -min tco_min [get_ports {SDAT_P[*]}] 3.  set_input_delay -clock [get_clocks DCLK_P] -clock_fall -max -add_delay tco_max [get_ports {SDAT_P[*]}] 4.  set_input_delay -clock [get_clocks DCLK_P] -clock_fall -min -add_delay tco_min [get_ports {SDAT_P[*]}]​   
````
6.  In the above **set_input_delay** constraints, tco_max and tco_min are the maximum and minimum clock-to-output time for data coming from the ADC.  I have assumed that the physical length of the DCLK_P/DCLK_N traces on the board are the same as the physical length of each SDAT_P[*]/SDAT_N[*] trace.


# Reference
[1] https://support.xilinx.com/s/question/0D52E00006hpOdUSAU/adc-lvds-data-capture?language=en_US