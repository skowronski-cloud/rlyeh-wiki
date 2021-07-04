# LXD

## LXD init
```
root@hv ~ # lxd init
Would you like to use LXD clustering? (yes/no) [default=no]: 
Do you want to configure a new storage pool? (yes/no) [default=yes]: no
Would you like to connect to a MAAS server? (yes/no) [default=no]: 
Would you like to create a new local network bridge? (yes/no) [default=yes]: 
What should the new bridge be called? [default=lxdbr0]: 
What IPv4 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]: 10.0.0.1/8
Would you like LXD to NAT IPv4 traffic on your bridge? [default=yes]: yes
What IPv6 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]: 
Would you like the LXD server to be available over the network? (yes/no) [default=no]: yes
Address to bind LXD to (not including port) [default=all]: 
Port to bind LXD to [default=8443]: 
Trust password for new clients: 
Again: 
Would you like stale cached images to be updated automatically? (yes/no) [default=yes] 
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]: 
root@hv ~ # 
```

`Trust password for new clients: ` -> see `rlyeh-secrets/plain/lxd_init.txt`

## lxd storage pool - attaching to system VG
```
lxc storage create default lvm source=vg0 lvm.vg.force_reuse=true lvm.use_thinpool=true lvm.thinpool_name=lxd-pool
```

->

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
