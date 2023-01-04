XSDK cmake error building libmetal 

Hi folks,

  I've come across an issue where building a BSP that includes the rfdc and libmetal, throws an error in the SDK log, as shown below. Keep in mind this is running an unsupported Ubuntu Focal

````MARKDOWN
ERROR: [Hsi 55-1545] Problem running tcl command ::sw_libmetal_v2_4::generate : Failed to generate cmake files.
    while executing
"error "Failed to generate cmake files.""
    (procedure "::sw_libmetal_v2_4::generate" line 77)
    invoked from within
"::sw_libmetal_v2_4::generate libmetal"
````


Enabling more logging in SDK in Window/Preferences/Xilinx SDK/Log Information Level, and setting it to Debug, yields more log output and shows the root cause:

12:11:30 DEBUG : (XSDB Server)/opt/Xilinx/SDK/2019.1/tps/lnx64/cmake-3.3.2/bin/cmake: error while loading shared libraries: libidn.so.11: cannot open shared object file: No such file or directory child process exited abnormally

So, libidn is a shared library and cmake expects an older version to be in the system. In Fedora 31 the library is in /usr/lib64/libidn.so.12. Adding a soft link works around the issue:

`$ sudo ln -s /usr/lib64/libidn.so.12 /usr/lib64/libidn.so.11`

After this workaround, cmake and thus SDK builds the BSP correctly.


