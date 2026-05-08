# Access list configuration in office router
```
officerouter#show access-lists
Extended IP access list 100
    10 permit ip 192.168.10.0 0.0.0.255 192.168.40.0 0.0.0.255
    20 deny ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255
    30 deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
    40 permit ip any any (4 match(es))
Extended IP access list 110
    10 deny icmp 192.168.60.0 0.0.0.255 192.168.10.0 0.0.0.255 echo
    20 deny icmp 192.168.60.0 0.0.0.255 192.168.30.0 0.0.0.255 echo
    30 permit ip any any (112 match(es))
Extended IP access list 120
    10 deny ip 192.168.70.0 0.0.0.255 192.168.0.0 0.0.255.255
    20 permit ip any any
Standard IP access list 1
    10 permit 172.16.0.0 0.0.255.255
    20 permit 192.168.0.0 0.0.255.255 (76 match(es))
Extended IP access list 101
    10 deny ip 172.16.0.0 0.0.255.255 192.168.0.0 0.0.255.255
    20 permit ip 172.16.0.0 0.0.255.255 any
    30 deny ip 192.168.0.0 0.0.255.255 172.16.0.0 0.0.255.255 (21 match(es))
    40 permit ip 192.168.0.0 0.0.255.255 any
```
