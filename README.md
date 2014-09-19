clgpustress
===========

Heavy OpenCL GPU stress tester (version 0.0.4.1)

### IMPORTANT CAUTION!!!!!

THIS PROGRAM IS VERY DANGEROUS FOR GRAPHICS CARD AND CAN OVERHEAT OR DAMAGE YOUR GRAPHICS CARD! PLEASE USE CAREFULLY
THIS PROGRAM. I RECOMMEND TO RUN THIS PROGRAM ON ALL STOCK PARAMETERS (CLOCKS, VOLTAGES, ESPECIALLY GPU MEMORY CLOCK).

THIS PROGRAM IS INFLUENCED BY PRIMEGRID GENEFER AND ALSO IS VERY SENSITIVE FOR ANY OVERCLOCKING,
BUT MUCH BETTER LOADS GPU CORE. MOREOVER MUCH BETTER BURNS GRAPHICS CARD THAN FURMARK!

THIS PROGRAM WAS TESTED ONLY ON RADEON HD 7850 AND CAN BEHAVES INCORRECTLY ON OTHER GRAPHICS CARDS.

YOU ARE USING THIS SOFTWARE ONLY ON YOUR OWN RISK!

### Available binaries

Binaries are at http://files.nativeboinc.org/offtopic/clgpustress/.

### Program behaviour

By default program finds ALL GPU devices from all platforms and runs stress for them.
You can choose particular device with using '-L' option. Also you can select OpenCL platform
by using '-A', '-N' or '-E' options (also, you can combine these options to select many platforms).
By default program calibrates test for performance and memory
bandwidth. While running tests program checks result with previously computed results on the device.
If results mismatches program terminates stress test for failed device.
By default program terminates stress testing when any device fails. You can adds '-f' option
to force continue stress testing for other devices.

### Software requirements:

- popt library
- OpenCL support
- cl.hpp (OpenCL C++ support)
- compiler with C++11 support
- Linux (currently this system is supported).

### Building program

Enter command:

make

To clean project enter command:

make clean

OPENCLDIR variable defined in Makefile may be changed for successful compilation.
If you have AMDAPP or OPENCL directory in other place than /opt/AMDAPP you must change
OPENCLDIR variable in Makefile file.

### Memory requirements

Program prints size of memory required in the device memory.
Standard tests requires 64 * blocksNum * workFactor * maxComputeUnits * maxWorkGroupSize bytes in
device memory.

You can get maxComputeUnits and maxWorkGroupSize from 'clinfo' or from other
OpenCL diagnostics utility.

Option '-I' chooses standard method with decoupled input and output which requires
double size of memory on the device. By default program uses single buffer for input and output.

Program needs also host memory: 192 * blocksNum * workSize bytes for buffers.

### Usage

Examples of usage:

- print help: ./gpustress -?
- simplest usage: ./gpustress
- run stress: ./gpustress -G -W512 -S32 -B2 -T0
- run stress only on AMD devices: ./gpustress -A
- run stress only on NVIDIA devices: ./gpustress -N
- run stress only on Intel devices: ./gpustress -E
- run stress only on first device from first platform: ./gpustress -L 0:0
- run stress only on second device from second platform: ./gpustress -L 1:1

If option '-j' is not specified then program automatically calibrates test for device for performance and memory bandwidth.

#### Parameters for the tests

Now you can specify following parameters for tests:

- workFactor - controls work size: (workitems number: workFactor * maxComputeUnits * maxWorkGroupSize)
- blocksNum - number of blocks processed by single workitem (can be in 1-16)
- passIters - number of iterations of the execution kernel in single pass
- kitersNum - number of iteration of core computation within single memory access
- inputAndOutput - enables input/output mode
- builtinKernel - kernel of the test (0-2). tests are described in supported tests section

#### Specifiyng devices to testing:

GPUStress provides simple method to select devices. To print all available devices you can
use '-l' option:

./gpustress -l

gpustress prints all OpenCL devices, also prints their the platform id and the device id.

GPUStress allows to select devices from specified the OpenCL platform, by using following options:

- '-A' - choose only devices from AMD (AMDAPP) platform
- '-N' - choose only devices from NVIDIA (NVIDIA CUDA) platform
- '-E' - choose only devices from Intel platform

You can combime these options to choose many platforms.
By default gpustress chooses devices from all  platforms.

Moreover gpustress allows to choose devices of particular type:

- '-C' - choose only CPU devices
- '-G' - choose only GPU devices
- '-a' - choose only accelerators

You can combine these option to choose devices of many types.
By default gpustresss chooses only GPU devices.

The custom devices are not supported, because doesn't supports for the OpenCL compiler.

Moreover, you can choose a particular devices from a particular platforms with using option '-L'.
Parameter of this option is comma-separated list of the platform id and the device id
separated by using colon. Following example:

./gpustress -L 0:0,1:1,1:2,1:3

choose first device from first platform; second,third,fourth device from second platform.

#### Specifying configuration for particular devices

In easiest way, you can choose one value for all devices by providing a single value.

You can choose different values for particular devices for following parameters:
workFactor, blocksNum, passItersNum, kitersNum, builtinKernel, inputAndOutput.
Values are in list that is comma separated, excepts inputAndOutput where is sequence of
the characters ('1','Y','T' - enables; '0','N','F' - disables). Moreover parameter of '-I' option
is optional (if not specified program assumes that inputAndOutput modes will be
applied for all devices).

Examples:

gpustress -L 0:0,0:1 -W 512,4 -B 2 -T 1 -I YN

chooses for all devices blocksNum=2, builtinKernel=1, for first device: workFactor=512, inAndOut=yes
; for second device: workFactor=4, inAndOut=no.

If value's list will be shorter than list of the choosen devices then
last provided value from list will be choosen for remaining devices.

For a determining the order of the choosen devices, you can use '-c' option to get that order.

### supported tests

Now, tests has been built in program.

Tests list:

- 0 - standard with local memory checking
- 1 - standard without local memory checking
- 2 - polynomial walking
