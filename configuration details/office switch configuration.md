# office switch configuration.md
```hostname Office-Core-SW

vlan 10
 name HR

vlan 20
 name IT

vlan 30
 name Finance

vlan 40
 name Printers

vlan 50
 name Servers

vlan 60
 name DMZ

vlan 70
 name Guest

vlan 99
 name Management

interface g1/0/1
 switchport mode trunk

interface g1/0/2
 switchport mode access
 switchport access vlan 10

interface range g1/0/3-4
 switchport mode access
 switchport access vlan 20

interface range g1/0/5-6
 switchport mode access
 switchport access vlan 30

interface g1/0/7
 switchport mode access
 switchport access vlan 40

interface g1/0/8
 switchport mode access
 switchport access vlan 50

interface g1/0/9
 switchport mode access
 switchport access vlan 60
