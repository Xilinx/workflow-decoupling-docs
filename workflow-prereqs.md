# Decoupled Workflow Prerequisites

This page details the host build system prerequisite requirements for using the decoupled build workflow for Xilinx Zynq UltraScale+ MPSoC and Versal ACAP devices.

# Xilinx Toolchain Dependencies

For the hardware EDA persona, the following Xilinx EDA tools are required:

* Xilinx Vivado ML 2023.1
* Xilinx Vitis 2023.1

# Host Machine Dependencies

The decoupled workflow environment has been primarily tested on Ubuntu-based operating systems but should work on Red Hat compatible machines as well so long as they are supported by the Yocto Project build system and pre requisites satisfy as per Yocto project documentation.
https://docs.yoctoproject.org/4.0.3/singleindex.html#compatible-linux-distribution

Ubuntu operating systems must be newer than Ubuntu 18.04 LTS.

 For the software developer persona, the following packages are required:

* `gawk`
* `wget`
* `git-core`
* `diffstat`
* `unzip`
* `texinfo`
* `gcc-multilib`
* `build-essential`
* `chrpath`
* `socat`

To install these packages, the following commands can be used to simplify things.

**UBUNTU**

`sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat`

**RED HAT / FEDORA**

`yum install gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat` 

Also note that if you are building in an environment with `$HOME` or scratchpad directories mounted to NFS shares that the `$TMPDIR` variable is set to a local `/tmp`. 

# Required Source Code Repositories

The decoupled workflow requires a mix of new and updated repositories.  The details are noted below.

| Repository Name                    | Repository URL                                               | Branch Name                  |
| ---------------------------------- | ------------------------------------------------------------ | ---------------------------- |
| SDTGen (Generate System Devicetree) | [https://github.com/Xilinx/system-device-tree-xlnx](https://github.com/Xilinx/system-device-tree-xlnx) | xlnx_rel_v2023.1_sdt_experimental_beta                       |
| Experimental `embeddedsw`          | [https://github.com/Xilinx/embeddedsw-experimental-dt-support](https://github.com/Xilinx/embeddedsw-experimental-dt-support) | xlnx_rel_v2023.1_sdt                   |
| Lopper                             | [https://github.com/devicetree-org/lopper](https://github.com/devicetree-org/lopper) | v0.2023.x                      |
 | Yocto Manifests                    | [http://github.com/Xilinx/yocto-manifests.git](http://github.com/Xilinx/yocto-manifests.git) | xlnx-rel-v2023.1 |
| U-Boot                             | [https://github.com/Xilinx/u-boot-xlnx](https://github.com/Xilinx/u-boot-xlnx) | xlnx_rel_v2023.1                       |
| Linux                              | [https://github.com/Xilinx/linux-xlnx](https://github.com/Xilinx/linux-xlnx) | xlnx_rel_v2023.1                       |
| Arm Trusted Firmware / TF-A        | [https://github.com/Xilinx/arm-trusted-firmware](https://github.com/Xilinx/arm-trusted-firmware) | xlnx_rel_v2023.1                       |
|                                    |                                                              |                              |
|                                    |                                                              |                              |

**NOTE: The U-Boot, Linux, and TF-A repositories are the standard release with no decoupling modifications required**

