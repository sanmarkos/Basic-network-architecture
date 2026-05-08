# Office Router Configuration

```
hostname Office-Router

interface g0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface g0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

interface g0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0

interface g0/0.40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.0

interface g0/0.50
 encapsulation dot1Q 50
 ip address 192.168.50.1 255.255.255.0

interface g0/0.60
 encapsulation dot1Q 60
 ip address 192.168.60.1 255.255.255.0

interface g0/0.70
 encapsulation dot1Q 70
 ip address 192.168.70.1 255.255.255.0

interface g0/0.99
 encapsulation dot1Q 99
 ip address 192.168.99.1 255.255.255.0

interface s0/1/0
 ip address 200.1.1.2 255.255.255.252

ip route 0.0.0.0 0.0.0.0 200.1.1.1
```
