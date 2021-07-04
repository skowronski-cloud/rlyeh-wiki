# Networking

## Networking layout

* Upstream is `enp9s0` with IP `144.76.27.245`.
* Internal network adapter for VMs that has VPN tunnel for replication to backup servers and administration is `net10` with IP `10.0.0.1/8`
* IP numbering is as follows: `10.LOCATION.CATEGORY.SEQUENTIAL_ID`
  * `LOCATION` is ID of physical location or HV; 0 is for Rlyeh, 1 is for future backup server in Helsinki, 255 is for admin VPN clients
  * `CATEGORY` is arbitrary number for server category, e.g. webservers have 1, MySQL servers have 2...
  * `SEQUENTIAL_ID` is global id of server in given category, even across different `LOCATION`s 
  * DNS aliases are `${CATEGORY_NAME}${SEQUENTIAL_ID`, where `CATEGORY_NAME` is word alias of `CATEGORY` ID from IP numbering, and `SEQUENTIAL_ID` is the one from IP numbering as well


## NET_PLAN

### Location
* 0 - Rlyeh
* 201 - amaterasu

### Categories
* 0 - reserved
* 1 - web public
* 2 - mysql db
* 3 - psql db
* 4 - dns
* 5 - influx db
* 6 - sandbox
* 7 - infra
* 8 - web private
* 254 - remote clients
* 255 - remote clients

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
```


## Firewall - install ogniochron 