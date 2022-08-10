# Building Bare-Metal Components

The Xilinx `embeddedsw` repository contains a vast collection of bare-metal drivers, libraries, and applications.   As noted in the [Workflow Prerequisites](workflow-prereqs.md) page, the instructions below require the updated & modified version of the `embeddedsw-experimental-dt-support` repository from the [Xilinx GitHub](https://github.com/Xilinx).  The traditional `embeddedsw` repository will not work.

Keep in mind that not all drivers and libraries that are in traditional embeddedsw repo have currently been ported to support the decoupled workflow.  
To be precise any component source folder that exists in traditional embeddedsw repo and doesnt exist in this 'embeddedsw-experimental-dt-support'can be treated as not supported in the decoupled workflow.

Building the bare-metal drivers, libraries, and applications leverages the `generic-machine` type of `MACHINE` variable.  Please see the [Yocto Build page](yocto-build.md) for valid machine type names.

The example below demonstrates how to build a specific device driver (and its example code)

```
#Building Driver Examples
MACHINE=<generic-machine> bitbake mc:<domain machine name>:<Driver example recipe name>
$ MACHINE=zynqmp-generic bitbake mc:cortexa72-versal-baremetal:emacps-example
```
A similar syntax is used to build a bare-metal application.  Note that all **required** bare-metal firmware applications are built as part of the standard [Yocto Project build process](yocto-build.md).
 ```
# Build versal plm system:
$ MACHINE=versal-generic bitbake mc:microblaze-pmc:plm-firmware mc:microblaze-psm:psm-firmware
 ```
Some applications are well-known "template" application such as the standard "Hello World" and "Peripheral Test" applications.  These can also be built directly.
```
#Building Template application
MACHINE=<generic-machine> bitbake mc:<domain machine name>:<Application recipe name>
$ MACHINE=versal-generic bitbake mc:cortexa72-versal-baremetal:hello-world
```
 ```
# Building Peripheral tests for versal
$ MACHINE=versal-generic bitbake mc:cortexa72-versal-baremetal:peripheral-tests
 ```
The table below provides a translation of the various processor domains in the various device families to their corresponding machine name value which should be used when compiling components directly out of the `embeddedsw` repository.

| Domain/Processor Name             | Machine Name Value                | Notes                                                        |
| --------------------------------- | --------------------------------- | ------------------------------------------------------------ |
| Arm Cortex-A53 (Zynq UltraScale+) | `cortexa53-zynqmp-fsbl-baremetal` | Machine `cortexa53-zynqmp-fsbl-baremetal` must be used for building the FSBL |
|                                   | `cortexa53-zynqmp-freertos`       |                                                              |
| Arm Cortex-A72 (Versal)           | `cortexa72-versal-baremetal`      |                                                              |
|                                   | `cortexa72-versal-freertos`       |                                                              |
| Arm Cortex-R5 (Zynq UltraScale+)  | `cortexr5-zynqmp-baremetal`       |                                                              |
|                                   | `cortexr5-zynqmp-fsbl-baremetal`  |                                                              |
|                                   | `cortexr5-zynqmp-freertos`        |                                                              |
| Arm Cortex-R5 (Versal)            | `cortexr5-versal-baremetal`       |                                                              |
|                                   | `cortexr5-versal-freertos`        |                                                              |
| MicroBlaze (PMU)                  | `microblaze-pmu`                  |                                                              |
| MicroBlaze (PLM)                  | `microblaze-pmc`                  |                                                              |
| MicroBlaze (PSM)                  | `microblaze-psm`                  |                                                              |

