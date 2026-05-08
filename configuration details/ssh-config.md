# SSH configuraion in Office Router/Switch
```
hostname Office-Router

ip domain-name markos.local

username admin secret admin123

crypto key generate rsa

line vty 0 4
 login local
 transport input ssh
```
