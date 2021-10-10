# rlyeh LXD

## LXD init
execute `lxd init`

* `Would you like to use LXD clustering? (yes/no) [default=no]:` -> *default*
* `Do you want to configure a new storage pool? (yes/no) [default=yes]: ` -> **no** (see later creation of pool on preconfigured LVM)
* `Would you like to connect to a MAAS server? (yes/no) [default=no]:` -> *default*
* `Would you like to create a new local network bridge? (yes/no) [default=yes]:` -> *default*
* `What should the new bridge be called? [default=lxdbr0]:` -> *default*
* `What IPv4 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]:` -> **10.0.0.1/16**
* `Would you like LXD to NAT IPv4 traffic on your bridge? [default=yes]` -> *default*
* `What IPv6 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]:` -> *default*
* `Would you like the LXD server to be available over the network? (yes/no) [default=no]` -> **yes**
* `Address to bind LXD to (not including port) [default=all]:` -> *default*
* `Port to bind LXD to [default=8443]:` -> *default*
* `Trust password for new clients:` -> see `rlyeh-secrets/plain/lxd_init.txt`
* `Would you like stale cached images to be updated automatically? (yes/no) [default=yes] ` -> *default*
* `Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]: ` -> *default*


## lxd storage pool - attaching to system VG
```
lxc storage create default lvm source=vg0 lvm.vg.force_reuse=true lvm.use_thinpool=true lvm.thinpool_name=lxd-pool
```

it should create something like:

```
NAME                      MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINT
nvme1n1                   259:0    0 953.9G  0 disk  
├─nvme1n1p1               259:2    0   512M  0 part  
│ └─md0                     9:0    0   511M  0 raid1 /boot
└─nvme1n1p2               259:3    0 953.4G  0 part  
  └─md1                     9:1    0 953.2G  0 raid1 
    ├─vg0-root            253:0    0    32G  0 lvm   /
    ├─vg0-lxd--pool_tmeta 253:1    0     1G  0 lvm   
    │ └─vg0-lxd--pool     253:3    0 919.2G  0 lvm   
    └─vg0-lxd--pool_tdata 253:2    0 919.2G  0 lvm   
      └─vg0-lxd--pool     253:3    0 919.2G  0 lvm   
nvme0n1                   259:1    0 953.9G  0 disk  
├─nvme0n1p1               259:4    0   512M  0 part  
│ └─md0                     9:0    0   511M  0 raid1 /boot
└─nvme0n1p2               259:5    0 953.4G  0 part  
  └─md1                     9:1    0 953.2G  0 raid1 
    ├─vg0-root            253:0    0    32G  0 lvm   /
    ├─vg0-lxd--pool_tmeta 253:1    0     1G  0 lvm   
    │ └─vg0-lxd--pool     253:3    0 919.2G  0 lvm   
    └─vg0-lxd--pool_tdata 253:2    0 919.2G  0 lvm   
      └─vg0-lxd--pool     253:3    0 919.2G  0 lvm   
```
