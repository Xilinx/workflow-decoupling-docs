# Lopper System Definition (YAML) Files

An optional input file to the Lopper tool (in additional to a System Devicetree) is a YAML-based system definition file. This file can provide instructions to Lopper as to how the domain-specific devicetree files should be structured to support either OS-specific constructs or higher-level system interaction such as OpenAMP-based multiprocessing and virtualization-based mapping (eg, Xen Hypervisor passthrough & assignment). Yaml specification can be found [here](https://github.com/devicetree-org/lopper/blob/master/specification/domains-yaml.md)

Though user can represent yaml to perform multple things, the current yaml support in the workflow decoupling is very rudimentary and restricts its usage to define basic Linux and baremetal domains by allocating peripherals and memories to these domains. Sample yaml files are provided in this repo (at resources/peripheral_memory_resource_domain.yaml and resources/versal_peripheral_memory_resource_domain.yaml) for reference on defining domains(Linux and bermetal) and partitioning the peripherals and memory between those domains.

## Domain
A domain constitutes of memory spaces and devices seen by an execution environment such as Linux running in EL1-NS, Xen in EL2-NS, ATF in EL3, TEE in EL1-S and Bare metal on the RPU and so on. It contains all HW related information to configure the relevant OS, FW. A domain is a single address space that includes memory address ranges and the addresses mapped control registers for a set of devices. A domain can include multiple bus masters, such as the CPU and devices using DMAs (e.g an ethernet controller).

## Subsystem
A set of HW resources that includes CPUs, memory-mapped devices, and buses that can have the same life cycle.  As an example, all APU cores and the devices assigned to it could be one subsystem. Similarly RPUs along with the devices assigned to it can constitute a subsystem. Alternatively, if needed APU cores along with the assigned devices and RPU cores along with assigned devices could constitute a single  subsystem. A subsystem can be defined as the union of one or multiple domains.
For example: A domain that comprises Linux (running at EL1-NS) along with the relevant devices can also be defined as a subsystem. Similarly a RPU domain might be identical to a RPU subsystem.

This document covers only the basic baremetal and linux domain use cases, other domain use case like OpenAMP is covered at [this page](openamp-build.md).

#### Yocto Build Infrastructure
Yocto related support for domains is available in xlnx-rel-v2023.1 branch, please use this branch for domain testing.
