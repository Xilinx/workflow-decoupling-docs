# OpenAMP Project Build Infrastructure                                             
                                                                                   
This document shows how to build OpenAMP DTB's, Linux and RPU applications via Xilinx Decoupling Flow
                                                                                   
# Setting Up the System Devicetree                                                 
                                                                                   
Before proceeding be sure to place the system devicetree files provided by the hardware developer persona in a location accessible by the user which will be running the Yocto builds.
                                                                                   
For Versal ACAP users, pdi should already be located in the system devicetree directory. Zynq UltraScale+ MPSoC designs require the `psu_init` files (`psu_init.h` and `psu_init.c`). Check if these files are there in the same directory as `system-top.dts`. If not then copy them manually.
                                                                                   
# Building OpenAMP with Yocto                                                      
                                                                                   
## Required configurations                                                         
                                                                                   
Use manifest git://github.com/Xilinx/yocto-manifests.git and tag xlnx-rel-v2022.1_update2_sdt_experimental_beta. Follow the instructions mentioned in this [page](yocto-build.md) till [yocto project configuration part.](yocto-build.md#configuring-the-localconf-for-bare-metal-components)                                          
                                                                                                                                                                       
### Yocto command to build OpenAMP Firmware, Linux device tree and OpenAMP Linux apps 
                                                                                   
Append the following to conf/local.conf in addition to what we got from dt-processor.sh                                       
                                                                                   
```                                                                                                                                                               
# Flag to ensure that OpenAMP decoupling recipes are picked up                     
OPENAMP_DECOUPLING="1"

# OpenAMP deocupling recipes can pick up a system device tree independently of the Yocto-variable SYSTEM_DTFILE
# as this variable will be overwritten depending on the target.                 
OPENAMP_DTFILE = "<absolute path to SDT>" # e.g. "/proj/yocto/mhatle/DTB-FLOW/current/vck190-sdt/system-top.dts"
                                                                                
# enable linux apps and open-app package                                        
# as part of this, firmware will be built, installed an deployed as well        
# rpmsg-echo-test package is for kernel-space demo.
IMAGE_INSTALL:append = " open-amp-demos open-amp rpmsg-echo-test rpmsg-mat-mul rpmsg-proxy-app "
                                                                                
# there is a race-condition issue with xilinx-firmware decoupling recipes. for now prevent this with this flag for open-amp recipe.
PARALLEL_MAKE:open-amp = "-j 1"                                                 
                                                                                
$ bitbake petalinux-image-minimal                                               
```                                                                             
                                                                                
1) For Booting on Hardware for Versal, the Xilinx BOOT.BIN can be found at < yocto project tmp_dir>/deploy/images/versal-generic/boot.bin. Note that this boot.bin doesn't have the openamp dtb. openamp.dtb needs to be either loaded at u-boot or a separate bin needs to created with the generated openamp dtb.
                                                                                
2) OpenAMP Linux DTB can be found at                                             
```                                                                             
 <yocto project tmp_dir>/deploy/images/<versal or zynqmp>-generic/openamp-lopper-output.dtb
```                                                                             
This should be placed in the systest board's TFTP directory                     
                                                                                
3) open-amp recipe will install the provided rpsmg packages in the rootfs.
                                                                                
4) OPENAMP_DECOUPLING yocto recipe flag is switch to say that, for Linux platform, whether to use decoupling flow or not.By default this is off ("0")
                                                                                
#### Yocto local.conf information for rpc demos                                 
Append the following to local.conf                                              
```                                                                             
XLNX_CMAKE_BSP_VARS = " -DUNDEFINE_FILE_OPS "                                   
```                                                                             
See UG1186 for documentation on this symbol.                                    
                                                                                
#### Example YAML Overlays                                                      
Linked below is the location of the Versal and ZU+ overlays.                    
                                                                                
Versal: https://gitenterprise.xilinx.com/Yocto/meta-openamp/blob/2022/meta-xilinx-standalone-experimental/recipes-openamp/open-amp/overlays/openamp-overlay-versal.yaml#L69
                                                                                
ZU+: https://gitenterprise.xilinx.com/Yocto/meta-openamp/blob/2022/meta-xilinx-standalone-experimental/recipes-openamp/open-amp/overlays/openamp-overlay-zynqmp.yaml#L63
                                                                                
You can also set your own copy of the overlay and add it to the local.conf as follows.
```                                                                                    
OPENAMP_OVERLAY:zynqmp = <path to overlay> # set for both linux AND R5 targets if building the project for zynqmp
OPENAMP_OVERLAY:versal = <path to overlay> # set for both linux AND R5 targets if building the project for versal
```                                                                             
   
                                                                               
### YAML Fields and Configurations                                              
                                                                                
#### OpenAMP with RPMsg kernelspace vs. userspace                               
                                                                                
   The decoupling recipes allow a user to specify if the demo is for RPMsg kernelspace or RPMsg userspace.
                                                                                
By default the demos are for (Linux) RPMsg userspace flow. To change this, change the Yocto overlay's value for "openamp-xlnx-native" to false. Below is an example of changing it in YAML
                                                                                
```                                                                             
   rpmsg-relation:                                                              
                compatible: openamp,rpmsg-v1                                    
                openamp-xlnx-native: true # true means to use native OpenAMP implementation. Here change this to false
...                                                                             
```                                                                             
                                                                                
####   RPU Configuration                                                        
   Setting the "cpumask" property in the R5 cluster denotes the RPU configuration. Below is a snippet from YAML and a table showing how to generate RPU cluster with configuration for R5 in lockstep and split mode.
                                                                                
Relevant Lopper commit: https://github.com/devicetree-org/lopper/commit/69d88490a6e2a21d80d529b56a5c8f2e1754c28d
                                                                                
```                                                                             
    openamp_r5_0_cluster:                                                       
    compatible:                                                                 
    - "openamp,domain-v1"                                                       
    cpus:                                                                       
    - cluster: cpus_r5                                                          
    cpumask: 0x1                                                                
    mode:                                                                       
    secure: true                                                                
```                                                                             
                                                                                
   Above the cpumask value of '0x1' is for RPU cluster R5 0 as shown in the table below.
                                                                                
The field 'cpumask' is used to denote RPU configuration. Below documents supported values and resulting RPU configuration. If another value is put in the lopper build will report the error.
                                                                                
   | RPU Target Configuration | Value | Supported |                             
   | :----------------------: | ----- | --------- |                             
   | RPU 0                    | 0x1   | Yes       |                             
   | RPU 1                    | 0x2   | No        |                             
   | Lockstep                 | 0x0   | Yes       |                             
     
