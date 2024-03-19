The Processing System 7 core generates fabric clocks based on your selections. 
````
create_clock -name clk_fpga_0 -period "20" [get_pins "PS7_i/FCLKCLK[0]"] set_input_jitter clk_fpga_0 0.6 
````
The clocks are asynchronous, so you should constrain them appropriately