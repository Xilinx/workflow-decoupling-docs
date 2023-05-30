# Building the Vivado Hardware Design

The first step in the decoupled workflow process is the generation of the system devicetree file.  This is the only step that must be completed in the Vivado EDA environment.  If you work in a segregated environment, please share these instructions with your EDA engineer(s).  This is also the only step which is directly dependent upon having Xilinx EDA tooling installed.

The tool which generates the system devicetree file is called SDTGen. It is published on the Xilinx GitHub at [https://github.com/Xilinx/system-device-tree-xlnx.git](https://github.com/Xilinx/system-device-tree-xlnx.git).

# Generate a Vivado Design

Before you can generate a system devicetree file, you must have a complete Vivado design, including generation of the hardware handoff (.xsa) file.  This is required because the SDTGen tool consumes the .xsa file.

This example focuses on generation of collateral for a the ZCU102 Evaluation Board (Zynq UltraScale+ MPSoC device) but references are provided where applicable for generation of components for Versal ACAP devices.

Start by creating a project targeting the ZCU102 board as shown in the picture.

![ZCU102 Selection in Vivado](resources/vivado-zcu102-selection.png)

In this example, a simple Vivado IP Integrator design is used with a collection of IO easily accessible on the ZCU102 Evaluation Board.  Any design using IP from the Vivado IP catalog should be sufficient.

**note: SDTGen does not currently recognize third-party or custom IP**

![Vivado IPI Canvas](resources/vivado-ipi-canvas.png)

If you would like to follow along with this example, the high-resolution PDF and Vivado block diagram Tcl files are provided:

* [Vivado IPI Canvas PDF]
* [Vivado IPI Block Diagram Recreation Tcl file] - see Xilinx [Answer Record 55692](https://support.xilinx.com/s/article/55692) for information on saving and reading BD Tcl files.

When implementing the design, be sure to complete the **Write Bitstream** step.

![Vivado Write Bitstream](resources/vivado-write-bitstream.png)

After completing implementation of the Vivado design, export the design to a hardware handoff file (.xsa) using **File --> Export --> Export Hardware...** in the Vivado pull-down menus.

When exporting, you will be given the option of how to create .xsa file.  Be sure to include the bitstream (or device image for Versal ACAP devices) in the .xsa file as shown.

<img src="resources/vivado-export-xsa-bitstream.png" alt="Vivado Export Include Bitstream" style="zoom: 67%;" />

Note where the .xsa hardware handoff file is stored as it will be needed for generating the system devicetree file via SDTGen.

# Fetch the SDTGen Source Code

The final task required from the hardware design persona is the creation of the system devicetree using the SDTGen tool.  Start by fetching the SDTGen source code.  This may require an additional host system tool (eg, git) on the hardware persona machine.  If the hardware persona is doing their design work on a Linux-based host please see the [Workflow Prerequisites](workflow-prereqs.md) page.  If the hardware persona is working on a Windows-based development machine they can use a tool such as [TortoiseGit](https://tortoisegit.org/) to clone the SDTGen source code.

For Linux-based EDA hosts, fetch the SDTGen source code and fetch the appropriate branch as shown below:

```
$ git clone https://github.com/Xilinx/system-device-tree-xlnx.git
$ git checkout -b xlnx_rel_v2023.1_sdt_experimental_beta origin/xlnx_rel_v2023.1_sdt_experimental_beta
```

The screenshots below shows fetching the SDTGen source code on a Windows-based host using TortoiseGit.

<img src="resources/dtg_clone_tortoisegit.png" alt="TortoiseGit Clone Windows" style="zoom: 80%;" />

<img src="resources/dtg_clone_tortoisegit2.png" alt="TortoiseGit Clone Windows" style="zoom: 80%;" />



# Configure SDTGen

The steps below assume that the hardware persona also has Vitis installed on his machine and added to path. 

Inorder to use system-device-tree-xlnx from cloned path, user need to follow below steps
- Copy the scripts folder from Vitis installed path to your local path.
    cp -rf <path to vitis>/scripts <local folder path where user wants to copy the scripts folder>(Eg: cp -rf /usr/bin/vitis/scripts /tmp/path/)
- Set MYVIVADO environment variable with this path (ex: #export MYVIVADO=/tmp/path/)
- Now open sdtgen.tcl file from the copied scripts folder (ex:/tmp/path/scripts/xsct/sdtgen/sdtgen.tcl) and update it to source your own local device_tree.tcl that is present in cloned system-device-tree-xlnx folder at device_tree/data/device_tree.tcl. 
 
# Generate the System Devicetree (S-DT) File
 
Launch xsct with the following command at the Linux terminal

```
$ xsct
 
```

This bring the SDTGen tool into the xsct Tcl environment.  The first command to use is the `sdtgen set_dt_param` command.  This command has several options that are required.  The details of these options can be found by executing the sdtgen set_dt_param help in the xsct terminal.

```
xsct% sdtgen set_dt_param -help
```

Of particular interest (and required) are:

* `-dir` - the directory where the system devicetree file will be generated
* `-xsa` - the path to the Vivado hardware handoff (.xsa) file
* '-repo - the path to the cloned system-device-tree-xlnx repo

Some arguments carry default values even if not specified:

* `-board_dts` - An optional reference to a known board DTS file.  The default value is "none"
  * Zynq UltraScale+ MPSoC example: `zcu102-rev1.0`
  * Versal ACAP example: `versal-vck190-reva`
* `-debug` - Enable or disable debug mode. The default value is "disabled".  Set to "enable" to enable.
* `-trace` -  Enable or disable SDTGen execution tracing for additional debug.  The default value is "disabled". Set to "enable" to enable.

The `sdtgen set_dt_param` command can be used to set all of the parameters at one time

```
xsct% sdtgen set_dt_param -board_dts <board file> -dir <directory name> -xsa <Vivado XSA file>
```

or it can be used sequentially to set each parameter individually. This can be useful in programmatic execution.

```
xsct% sdtgen set_dt_param -dir output_dts
xsct% sdtgen set_dt_param -xsa design_1_wrapper.xsa
xsct% sdtgen set_dt_param -board_dts zcu102-rev1.0
```

Conversely, the current values of each parameter can be retrieved using the `sdtgen get_dt_param` command in the same manner as the `sdtgen set_dt_param` command.

Once all of the parameters are set properly, the system devicetree file can be generated using the `sdtgen generate_sdt` command.  

 xsct% sdtgen generate_sdt

This command will generate below system devicetree files in the path specified in the `-dir` parameter:

**Zynq UltraScale+ MPSoC**

* `system-top.dts` - This is the top-level devicetree file which includes fundamental system information such as the number of processors present, the memory map, and memory offsets for key peripherals.  All others are referenced using the `#include` directive
* `zynqmp.dtsi` - This is the Zynq UltraScale+ architecture-specific devicetree file which includes information specific to that device family implementation
* `zynqmp-clk-ccf.dtsi` - This devicetree file includes all of the clock tree information for the system
* `pcw.dtsi` - This devicetree file includes the configuration information for all peripherals located in the Zynq UltraScale+ Processing System and specified as part of the PCW IP in Vivado
* `pl.dtsi` - This devicetree file includes the configuration information for all peripherals located in the Zynq UltraScale+ Programmable Logic

**Versal ACAP**

* `system-top.dts` - This is the top-level devicetree file which includes fundamental system information such as the number of processors present, the memory map, and memory offsets for key peripherals.  All others are referenced using the `#include` directive
* `versal.dtsi` - This is the Versal architecture-specific devicetree file which includes information specific to that device family implementation
* `versal-clk.dtsi` - This devicetree file includes all of the clock tree information for the system
* `versal-spp-pm.dtsi` - This devicetree file includes power and clock domain declarations which are needed when separating the design into domain-specific devicetree files
* `pcw.dtsi` - This devicetree file includes the configuration information for all peripherals located in the Versal Processing System and specified as part of the CIPS IP in Vivado
* `pl.dtsi` - This devicetree file includes the configuration information for all peripherals located in the Versal Programmable Logic

 
Along with system device tree files, some additional hw files will be needed during the Yocto Project build and yocto expects these files to be present in the directory same as system device tree files i.e directory specified with -dir option.  Inorder to copy these files to -dir path, use below command at xsct which copies these files from xsa to path specified with -dir.
 
xsct% sdtgen copy_hw_files
 
 For Zynq UltraScale+ MPSoC devices, SDTGen will copy the following hw files in the same directory as the system device tree files:

* `psu_init.tcl`
* `psu_init.html` (The Zynq UltraScale+ configuration in HTML viewable form)
* `psu_init.c`
* `psu_init.h`
* `psu_init_gpl.c`
* `psu_init_gpl.h`
* `system_wrapper.bit`

For Versal ACAP devices, SDTGen will copy the following hw files in the same directory as the system device tree files:

* `<export_file_name>.pdi`


Additionally, devicetree binding information will be located in the `include` sub-directory various peripherals found in the design.

The hardware developer persona must share all of the contents of the path specified in the `--dir` parameter with the software developer persona.

# Notes & Exceptions

The files generated in the same directory as the system device tree files are still needed for use by the software developer persona.  These files (eg, `psu_init.c`, `psu_init.h`, `<export_file_name>.pdi`, etc) should be included in the set of files shared from the hardware developer persona to the software developer persona.

