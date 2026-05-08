# Warehouse router configuration
```
hostname Warehouse-Router

interface g0/0.110
 encapsulation dot1Q 110
 ip address 172.16.10.1 255.255.255.0

interface g0/0.120
 encapsulation dot1Q 120
 ip address 172.16.20.1 255.255.255.0

interface g0/0.130
 encapsulation dot1Q 130
 ip address 172.16.30.1 255.255.255.0

interface s0/1/0
 ip address 200.1.1.10 255.255.255.252

ip route 0.0.0.0 0.0.0.0 200.1.1.9
```
