# Ansible playbooks for building Cisco IOS-based network
The goal behind the playbooks here is to demonstrate simple examples for automating Cisco IOS-based network. The assumed network environment can be set up by the containerlab [here](../cisco-clab/README.md).

## Included contents
### Playbooks
|Name     |Description|
|:--------|:----------|
|`pb_gather_ssh_keys.yml`|Gather SSH public keys from Cisco devices.|
|`pb_build_network.yml`|Configure networks on IOS devices.|
|`pb_collect_facts.yml`|Collect IOS device facts.|
|`pb_net_validate.yml`|Validate network reachability on IOS devices.|
|`pb_op_cmds.yml`|Retrieve operational data from IOS devices.|
|`pb_write_config.yml`|Save running configurations on IOS devices.|

## Prerequisites
### Basic requirements for Ansible
Any control node:
- ansible core 2.16+

Ansible collections:
- cisco.ios

In order for functioning some playbooks, you need to install the following Python libraries.
```
$ pip3 install ansible-pylibssh netaddr
```

### Inventory
Each `ansible_host` in `inventory/ansible-inventory.yml` needs to match the corresponding containerlab, assuming that the ansible control node runs on the same host where the containerlab runs on.

## Usage
### Gather SSH public keys for remote Cisco devices
`pb_gather_ssh_keys.yml` can gather public keys from all remote Cisco devices and store them to the file, which is defined as `hosts_file` variable.
```
$ ansible-playbook pb_gather_ssh_keys.yml
```

### Configure networks on IOS devices
`pb_build_network.yml` consists of three plays, each of them is attached tags, `lan`, `l3_core` and `wan` respectively.

`lan` tag instructs to configure basic system paramaters like DNS and NTP, and to configure interfaces and VLANs, on access and core switches.
```
$ ansible-playbook pb_build_network.yml --tags lan
```
`l3_core` tag instructs to add core switch specific configurations like L3 VLAN interfaces and VRRP on core switches. It also configures OSPF on core side.
```
$ ansible-playbook pb_build_network.yml --tags l3_core
```
`wan` tag instructs to configure basic system paramaters like DNS and NTP on WAN routers, and also to configure interfaces, routes and OSPF.
```
$ ansible-playbook pb_build_network.yml --tags wan
```
### Collect IOS device facts
`pb_collect_facts.yml` can collect the basic facts from running Cisco devices and generate structured reports for the current state regarding the devices.
```
$ ansible-playbook pb_collect_facts.yml
```

### Validate network reachability on IOS devices
`pb_net_validate.yml` can validate network reachability via `ping` accross devices.
```
$ ansible-playbook pb_net_validate.yml
```
You can also validate by pinging from servers to VRRPs.
```
$ docker exec -it clab-cisco-campus-data01 bash
data01:/# ping 10.1.10.254  ! VRRP virtual gateway in VLAN 10

$ docker exec -it clab-cisco-campus-voice01 bash
voice01:/# ping 10.1.20.254  ! VRRP virtual gateway in VLAN 20
```

### Retrieve operational data from IOS devices
`pb_op_cmds.yml` can execute oprrational commandes on IOS devices and store these outputs to text files. It also saves the running configuration on the devices to `configs` directory.
```
$ ansible_playbook pb_op_cmds.yml
```

### Save running configurations
`pb_write_config.yml` copies the running config to the startup config on each IOS device.
```
$ ansible-playbook pb_write_config.yml
```
