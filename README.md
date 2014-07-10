# Overview

This is an enhanced SNMP network interface collector that provides:

* Faster, threaded collection, addressing [this issue.](https://github.com/BrightcoveOS/Diamond/issues/263)
* Seperation of the discovery and polling phases to improve performance.
* Discovery and collection of CPU metrics for Cisco and Cisco IOS-XE devices.
* Discovery and collection of VLAN and Port Channel interfaces.

## Requirements

* Requires a newer version of pysnmp. Tested on 4.2.5
* Requires Python > 2.6.5. Tested on 2.6.5.

## Instalation

* You'll need an `/etc/diamond/collectors/SNMPInterfacePoll.d/' directory to drop the OID flat file into.
* On Ubuntu, place the collector directories and modules in `/usr/share/diamond/collectors/` as you would typically. 
* You'll have to create two configuration files. On Ubuntu they live in `/etc/diamond/collectors/` 

First, SNMPInterfaceDiscovery.conf containing each device you want to discover interfaces for.
Set the interval infrequently. This will only detect interface state changes and save the OID 
in a flat file for regular polling. 
```
enabled = True
interval = 14400

[devices]
[[switch1]]
community = public
host = 192.168.10.10
port = 161

[[switch2]]
community = public
host = 192.168.10.11
port = 161 
```
Second, SNMPInterfacePoll.conf in /etc/diamond/collectors/
This is a hacky config file with a dummy device to enable the polling collector and
set it's interval. The polling collector will gather which OID to poll from the 
flat file generated by the discovery collector.
```
enabled = True
interval = 60

[devices]
[[dummy]]
community = community
host = 127.0.0.1
port = 161
```

## Performance

This collector gathers ~ 6000 SNMP metrics from ~ 700 interfaces in my environment in around 15 seconds. It should be run on subtible system with
decent I/O performance. 