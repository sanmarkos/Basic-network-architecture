# NAT configuration in Office Router
```
 =========================
  NAT / PAT CONFIGURATION
 =========================

Allow internal office and warehouse networks for NAT
access-list 1 permit 192.168.0.0 0.0.255.255
access-list 1 permit 172.16.0.0 0.0.255.255

Configure inside interfaces
interface g0/0
 ip nat inside

interface g0/0.10
 ip nat inside

interface g0/0.20
 ip nat inside

interface g0/0.30
 ip nat inside

interface g0/0.40
 ip nat inside

interface g0/0.50
 ip nat inside

interface g0/0.60
 ip nat inside

interface g0/0.70
 ip nat inside

interface g0/0.99
 ip nat inside

Configure WAN/ISP interface
interface s0/1/0
ip nat outside

Enable PAT overload
ip nat inside source list 1 interface s0/1/0 overload

What this does mean

| Feature          | Purpose                                      |
| ---------------- | -------------------------------------------- |
| ACL 1            | Selects internal private networks            |
| ip nat inside    | Marks internal LAN interfaces                |
| ip nat outside   | Marks ISP/public interface                   |
| overload         | Enables PAT (many users share one public IP) |
