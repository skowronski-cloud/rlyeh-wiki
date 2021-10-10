# ulthar VPN

## `/opt/fix_masquerade.sh` - needed because LXD network keeps overwriting rules
```bash
#!/bin/bash
iptables-legacy -t nat -D POSTROUTING -s 10.1.0.0/16 ! -d 10.1.0.0/16 -m comment --comment "generated for LXD network lxdbr0" -j MASQUERADE
iptables-legacy -t nat -A POSTROUTING -s 10.1.0.0/16 ! -d 10.0.0.0/8 -m comment --comment "generated for LXD network lxdbr0" -j MASQUERADE
```

## `/etc/ipsec.conf`
```
config setup
  charondebug="ike 5, knl 5, cfg 5"
  uniqueids=yes
  strictcrlpolicy=no

conn ulthar-to-rlyeh
  forceencaps=yes
  authby=secret
  type=tunnel
  auto=route
  left=172.16.0.2
  leftsubnet=10.1.0.1/16
  right=172.16.0.18
  rightsubnet=10.0.0.1/16
  ike=aes256-sha2_256-modp1024!
  keyexchange=ikev2
  esp=aes256-sha2_256!
  ikelifetime=1h
  rightupdown=/opt/fix_masquerade.sh
  leftupdown=/opt/fix_masquerade.sh
```
## `/etc/ipsec.secrets`
```
ulthar : EAP ...
```