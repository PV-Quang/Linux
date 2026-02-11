#### Ubuntu

```shell
network:
  version: 2
  ethernets:
    ens33:
      dhcp4: false
      dhcp6: false
      addresses:
        - 50.0.0.11/24
    ens34:
      dhcp4: false
      dhcp6: false
      addresses:
        - 40.0.0.11/24
      routes:
        - to: default
          via: 40.0.0.1
      nameservers:
        addresses:
          - 40.0.0.250
        search:
          - pvq.lab
```

#### Debian
> Cấu hình static network `vi /etc/network/interfaces`
```shell
allow-hotplug ens192 
iface ens192 inet static 
address 192.168.10.10 
netmask 255.255.255.0 
gateway 192.168.10.1 
```

```shell
systemctl restart networking
```
<img width="595" height="286" alt="screenshot_1770796642" src="https://github.com/user-attachments/assets/f403283e-f988-4318-aa37-27a867a94880" />

