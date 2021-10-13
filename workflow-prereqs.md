# Decoupled Workflow Prerequisites

This page details the host build system prerequisite requirements for using the decoupled build workflow for Xilinx Zynq UltraScale+ MPSoC and Versal ACAP devices.

# Xilinx Toolchain Dependencies

For the hardware EDA persona, the following Xilinx EDA tools are required:

* Xilinx Vivado ML 2021.1 (or later)

# Host Machine Dependencies

The decoupled workflow environment has been primarily tested on Ubuntu-based operating systems but should work on Red Hat compatible machines as well so long as they are supported by the Yocto Project build system.

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

http://docs.yoctoproject.org/3.3.3/singleindex.html#compatible-linux-distribution
http://docs.yoctoproject.org/3.3.3/singleindex.html#required-git-tar-python-and-gcc-versions

# Required Source Code Repositories

The decoupled workflow requires a mix of new and updated repositories.  The details are noted below.

| Repository Name                    | Repository URL                                               | Branch Name                  |
| ---------------------------------- | ------------------------------------------------------------ | ---------------------------- |
| SDTGen (Generate System Devicetree) | [https://github.com/Xilinx/system-device-tree-xlnx](https://github.com/Xilinx/system-device-tree-xlnx) | xilinx-v2021.1-sdt-experimental                       |
| Experimental `embeddedsw`          | [https://github.com/Xilinx/embeddedsw-experimental-dt-support](https://github.com/Xilinx/embeddedsw-experimental-dt-support) | xilinx-v2021.1-sdt-experimental                       |
| Lopper                             | [https://github.com/devicetree-org/lopper](https://github.com/devicetree-org/lopper) | v0.2021.x                       |
| Yocto Manifests                    | [http://github.com/Xilinx/yocto-manifests.git](http://github.com/Xilinx/yocto-manifests.git) | 2021-experimental-dt-support |
| U-Boot                             | [https://github.com/Xilinx/u-boot-xlnx](https://github.com/Xilinx/u-boot-xlnx) | Master                       |
| Linux                              | [https://github.com/Xilinx/linux-xlnx](https://github.com/Xilinx/linux-xlnx) | Master                       |
| Arm Trusted Firmware / TF-A        | [https://github.com/Xilinx/arm-trusted-firmware](https://github.com/Xilinx/arm-trusted-firmware) | Master                       |
|                                    |                                                              |                              |
|                                    |                                                              |                              |

**NOTE: The U-Boot, Linux, and TF-A repositories are the standard release with no decoupling modifications required**

