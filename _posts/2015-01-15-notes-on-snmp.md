---
layout: post
category : note
tagline: "Things i want to remember about snmp"
tags : [notes, snmp]
---
{% include JB/setup %}

# Digging into SNMP results

## Find which ID relates to a switch interface 
    
    $ snmpwalk -c public -v 2c HOSTNAME IF-MIB::ifDescr | grep "Ethernet111/1/38"
    IF-MIB::ifDescr.527305024 = STRING: Ethernet111/1/38

## Use that ID to determine the operational status of the port 

    $ snmpwalk -c public -v 2c HOSTNAME IF-MIB::ifOperStatus.527305024
    IF-MIB::ifOperStatus.527305024 = INTEGER: down(2)

## Use that id to find the administrative status of the port
    
    $ snmpwalk -c public -v 2c HOSTNAME IF-MIB::ifAdminStatus.527305024
    IF-MIB::ifAdminStatus.527305024 = INTEGER: up(1)
