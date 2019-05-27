```
network:
    ethernets:
        eth0:
            addresses: [172.18.6.6/16]
            gateway4: 172.18.0.1
            dhcp4: no
            optional: true
            nameservers:
                    addresses: [172.18.0.1,223.6.6.6]
    version: 2
```

