## Project Structure
```
Project
|   Readme.md
└── SKL.out             // executable output of the program
└── SKL_Connect.c       // Init Flow of the component and TCP Server
    └── ZED_Function.c  // Basic Function for different component
    └── ZED_Function.h
```
---
## Update [[FPGA]] Environment
1. Source the [[petalinux]] environment
2. Generate the hardware xsa file
3. Modify the update.sh to navigate the hardware file
4. Run update.sh to re-generate the Linux image
5. Modify the peripheral in the pop-up window if need, including [[root-fs]], [[kernel]], general options 

## System Pre-requirement
1. [[GCC]] Compiler installation
2. Reserve Memory for [[ADC]]
3. Allow to host [[TCP]] server at port 500
4. Change local IP to 192.168.200.1

## User Adjustable Parameter
- ADC Sample Size (SKL_Connect.c:L11)

    `#define ADC_Length 20000`

## Way to compile Program

Type following command in terminal:

`gcc ./SKL_Connect.c -o ./SKL.out`