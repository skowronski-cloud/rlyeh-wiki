# rlyeh networking

## Packages to install before
```
apt install bridge-utils iptables
```

## vSwitch - rlyeh (robot/dedicated) part must be executed before ulthar (console/cloud) part

1. go to https://robot.your-server.de/vswitch?sortcol=id&sorttype=down
2. create vSwitch with VLAN ID **4090**
3. select rlyeh in *Add servers to vSwitch*
4. go to *ulthar (console/cloud) part* and after that one is complete verify that there's note in the vSwitch stating:
   `This vSwitch is connected to cloud network #XXX, Subnet 172.16.0.16/28, Gateway 172.16.0.17.`


## netplan config - `/etc/netplan/01-netcfg.yaml`

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp9s0:
      addresses:
        - 144.76.27.245/32
        - 144.76.27.254/32
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
  vlans:
    vlan.4090:
      id: 4090
      link: enp9s0
      addresses: [172.16.0.18/28]
      routes:
        - on-link: true
          to: 172.16.0.0/24
          via: 172.16.0.17
```
