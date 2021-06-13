# Networking

## Networking layout

* Upstream is `enp9s0` with IP `144.76.27.245`.
* Internal network adapter for VMs that has VPN tunnel for replication to backup servers and administration is `net10` with IP `10.0.0.1/8`
* IP numbering is as follows: `10.LOCATION.CATEGORY.SEQUENTIAL_ID`
  * `LOCATION` is ID of physical location or HV; 0 is for Rlyeh, 1 is for future backup server in Helsinki, 255 is for admin VPN clients
  * `CATEGORY` is arbitrary number for server category, e.g. webservers have 1, MySQL servers have 2...
  * `SEQUENTIAL_ID` is global id of server in given category, even across different `LOCATION`s 
  * DNS aliases are `${CATEGORY_NAME}${SEQUENTIAL_ID`, where `CATEGORY_NAME` is word alias of `CATEGORY` ID from IP numbering, and `SEQUENTIAL_ID` is the one from IP numbering as well


## Packages to install
```
apt install bridge-utils iptables
```

## netplan config - `/etc/netplan/01-netcfg.yaml`

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp9s0:
      addresses:
        - 144.76.27.245/32
        - 2a01:4f8:191:13f8::2/64
      routes:
        - on-link: true
          to: 0.0.0.0/0
          via: 144.76.27.225
      gateway6: fe80::1
      nameservers:
        addresses:
          - 213.133.100.100
          - 213.133.98.98
          - 213.133.99.99
          - 2a01:4f8:0:1::add:9999
          - 2a01:4f8:0:1::add:9898
          - 2a01:4f8:0:1::add:1010
  bridges:
    net10:
      addresses:
        - 10.0.0.1/8
```

## NAT script - `/usr/local/bin/nat-setup`
```bash
#!/bin/bash
logger "Setting up NAT for net10"
echo 1 > /proc/sys/net/ipv4/ip_forward
iptables -t nat -A POSTROUTING -s '10.0.0.0/8' -o enp9s0 -j MASQUERADE
```

## NAT service - `/etc/systemd/system/nat-setup.service`
```
[Unit]
Description=NAT setup

[Service]
Type=oneshot
ExecStart=/usr/local/bin/nat-setup

[Install]
WantedBy=network.target
```

### Enable&start
```bash
systemctl start  nat-setup
systemctl enable nat-setup
systemctl status nat-setup
```