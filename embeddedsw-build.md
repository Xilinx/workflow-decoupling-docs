# Building Bare-Metal Components

The Xilinx `embeddedsw` repository contains a vast collection of bare-metal drivers, libraries, and applications.   As noted in the [Workflow Prerequisites](workflow-prereqs.md) page, the instructions below require the updated & modified version of the `embeddedsw-experimental-dt-support` repository from the [Xilinx GitHub](https://github.com/Xilinx).  The traditional `embeddedsw` repository will not work.

Keep in mind that not all drivers and libraries that are in traditional embeddedsw repo have currently been ported to support the decoupled workflow.  
To be precise any component source folder that exists in traditional embeddedsw repo and doesnt exist in this 'embeddedsw-experimental-dt-support'can be treated as not supported in the decoupled workflow.

Building the bare-metal drivers, libraries, and applications leverages the `generic-machine` type of `MACHINE` variable.  Please see the [Yocto Build page](yocto-build.md) for valid machine type names.

The example below demonstrates how to build a specific device driver (and its example code)

```
#Building Driver Examples
MACHINE=<generic-machine> bitbake mc:<domain machine name>:<Driver example recipe name>
$ MACHINE=xlnx-versal-vck190-reva-x-ebm-01-reva bitbake mc:cortexa72-0-versal-baremetal:emacps-example
```
A similar syntax is used to build a bare-metal application.  Note that all **required** bare-metal firmware applications are built as part of the standard [Yocto Project build process](yocto-build.md).
 ```
# Build versal plm system:
$ MACHINE=xlnx-versal-vck190-reva-x-ebm-01-reva bitbake mc:microblaze-0-pmc:plm-firmware mc:microblaze-0-psm:psm-firmware
 ```
Some applications are well-known "template" application such as the standard "Hello World" and "Peripheral Test" applications.  These can also be built directly.
```
#Building Template application
MACHINE=<generic-machine> bitbake mc:<domain machine name>:<Application recipe name>
$ MACHINE=xlnx-versal-vck190-reva-x-ebm-01-reva bitbake mc:cortexa72-0-versal-baremetal:hello-world
```
 ```
# Building Peripheral tests for versal
$ MACHINE=xlnx-versal-vck190-reva-x-ebm-01-reva bitbake mc:cortexa72-0-versal-baremetal:peripheral-tests
 ```
The table below provides a translation of the various processor domains in the various device families to their corresponding machine name value which should be used when compiling components directly out of the `embeddedsw` repository.

| Domain/Processor Name             | Machine Name Value                  | Notes                                                        |
| --------------------------------- | ---------------------------------   | ------------------------------------------------------------ |
| Arm Cortex-A53 (Zynq UltraScale+) | `cortexa53-0-zynqmp-fsbl-baremetal` | Machine `cortexa53-0-zynqmp-fsbl-baremetal` must be used for building the FSBL |
|                                   | `cortexa53-0-zynqmp-freertos`       |                                                              |
|                                   | `cortexa53-0-zynqmp-baremetal`      |                                                              |
|                                   | `cortexa53-1-zynqmp-freertos`       |                                                              |
|                                   | `cortexa53-1-zynqmp-baremetal`      |                                                              |
|                                   | `cortexa53-2-zynqmp-freertos`       |                                                              |
|                                   | `cortexa53-2-zynqmp-baremetal`      |                                                              |
|                                   | `cortexa53-3-zynqmp-freertos`       |                                                              |
|                                   | `cortexa53-3-zynqmp-baremetal`      |                                                              |
| Arm Cortex-A72 (Versal)           | `cortexa72-0-versal-baremetal`      |                                                              |
|                                   | `cortexa72-0-versal-freertos`       |                                                              |                                
|                                   | `cortexa72-1-versal-baremetal`      |                                                              |
|                                   | `cortexa72-1-versal-freertos`       |                                                              |                               
| Arm Cortex-R5 (Zynq UltraScale+)  | `cortexr5-0-zynqmp-baremetal`       |                                                              |
|                                   | `cortexr5-0-zynqmp-fsbl-baremetal`  |                                                              |
|                                   | `cortexr5-0-zynqmp-freertos`        |                                                              |
|                                   | `cortexr5-1-zynqmp-baremetal`       |                                                              |
|                                   | `cortexr5-1-zynqmp-freertos`        |                                                              |
| Arm Cortex-R5 (Versal)            | `cortexr5-0-versal-baremetal`       |                                                              |
|                                   | `cortexr5-0-versal-freertos`        |                                                              |
|                                   | `cortexr5-1-versal-baremetal`       |                                                              |
|                                   | `cortexr5-1-versal-freertos`        |                                                              |
| MicroBlaze (PMU)                  | `microblaze-0-pmu`                  |                                                              |
| MicroBlaze (PLM)                  | `microblaze-0-pmc`                  |                                                              |
| MicroBlaze (PSM)                  | `microblaze-0-psm`                  |                                                              |

