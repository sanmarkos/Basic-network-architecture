# DHCP Configuration
```
! =========================
! DHCP CONFIGURATION
! =========================

! Exclude gateway and reserved IPs
ip dhcp excluded-address 192.168.10.1 192.168.10.20
ip dhcp excluded-address 192.168.20.1 192.168.20.20
ip dhcp excluded-address 192.168.30.1 192.168.30.20
ip dhcp excluded-address 192.168.40.1 192.168.40.20
ip dhcp excluded-address 192.168.50.1 192.168.50.20
ip dhcp excluded-address 192.168.60.1 192.168.60.20
ip dhcp excluded-address 192.168.70.1 192.168.70.20

ip dhcp excluded-address 172.16.10.1 172.16.10.20
ip dhcp excluded-address 172.16.20.1 172.16.20.20
ip dhcp excluded-address 172.16.30.1 172.16.30.20


! =========================
! OFFICE VLAN DHCP POOLS
! =========================

ip dhcp pool HR
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool IT
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool FINANCE
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool PRINTERS
 network 192.168.40.0 255.255.255.0
 default-router 192.168.40.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool SERVERS
 network 192.168.50.0 255.255.255.0
 default-router 192.168.50.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool DMZ
 network 192.168.60.0 255.255.255.0
 default-router 192.168.60.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool GUEST_WIFI
 network 192.168.70.0 255.255.255.0
 default-router 192.168.70.1
 dns-server 192.168.50.10
 domain-name markos.local


! =========================
! WAREHOUSE VLAN DHCP POOLS
! =========================

ip dhcp pool WAREHOUSE_PC
 network 172.16.10.0 255.255.255.0
 default-router 172.16.10.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool WAREHOUSE_WIFI
 network 172.16.20.0 255.255.255.0
 default-router 172.16.20.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool WAREHOUSE_PRINTER
 network 172.16.30.0 255.255.255.0
 default-router 172.16.30.1
 dns-server 192.168.50.10
 domain-name markos.local
```
