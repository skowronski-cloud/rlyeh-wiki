# ulthar

## base install
```bash
apt update
apt upgrade
ua attach ...
ua status
ua enable cis
reboot
echo "Port 2137" >> /etc/ssh/sshd_config
systemctl restart sshd
apt install strongswan
```

## `/usr/local/share/ca-certificates/DS_CA.crt`, `/etc/ipsec.d/cacerts/DS_CA.crt`
```
-----BEGIN CERTIFICATE-----
MIIBwTCCAWagAwIBAgIRAJSML1dkcU4NCaajQCT+O8UwCgYIKoZIzj0EAwIwPjEZ
MBcGA1UEChMQc2tvd3JvbnNraS5jbG91ZDEhMB8GA1UEAxMYc2tvd3JvbnNraS5j
bG91ZCBSb290IENBMB4XDTIxMDcyMDE5MTc1NFoXDTMxMDcxODE5MTc1NFowPjEZ
MBcGA1UEChMQc2tvd3JvbnNraS5jbG91ZDEhMB8GA1UEAxMYc2tvd3JvbnNraS5j
bG91ZCBSb290IENBMFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEphGzYO+NOoKU
YuHgg0l4S8/IwgtP2LW6bPujUJdF6wNJfq+9j6WR9lzvT5KzPRDTBEXpArGGpFuF
ZHqeVDOQ7qNFMEMwDgYDVR0PAQH/BAQDAgEGMBIGA1UdEwEB/wQIMAYBAf8CAQEw
HQYDVR0OBBYEFF/NyYoQ+K0Yl5ObbhgH0mzRsVGhMAoGCCqGSM49BAMCA0kAMEYC
IQDtgbI5uxOD1qMeOV4qmLH0ssiiggbad6cvlRqZ8rUo+AIhAIE5qwyUohzJhIrf
Sap9d2Z1ScGHoZVGP8IgJiW3nPLF
-----END CERTIFICATE-----
```

## trust cert
```bash
update-ca-certificates
```

## `/etc/ipsec.conf`
```
conn rlyeh
    auto=start
    right=144.76.27.245
    rightid=rlyeh_ipsec.skowronski.cloud
    rightsubnet=10.0.0.0/8
    rightauth=pubkey
    leftsubnet=10.0.0.0/8
    leftsourceip=10.1.0.2
    leftid=ulthar
    leftauth=eap-mschapv2
    eap_identity=%identity

   dpdaction=restart
   closeaction=restart
```
## `/etc/ipsec.secrets`
```
ulthar : EAP ...
```

## lxd init
```
root@ulthar:~# lxd init
Would you like to use LXD clustering? (yes/no) [default=no]: no
Do you want to configure a new storage pool? (yes/no) [default=yes]: 
Name of the new storage pool [default=default]: 
Name of the storage backend to use (btrfs, dir, lvm, zfs, ceph) [default=zfs]: dir
Would you like to connect to a MAAS server? (yes/no) [default=no]: 
Would you like to create a new local network bridge? (yes/no) [default=yes]: 
What should the new bridge be called? [default=lxdbr0]: 
What IPv4 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]: 10.0.0.1/8
Would you like LXD to NAT IPv4 traffic on your bridge? [default=yes]: 
What IPv6 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]: none
Would you like the LXD server to be available over the network? (yes/no) [default=no]: 
Would you like stale cached images to be updated automatically? (yes/no) [default=yes] yes
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]: 
root@ulthar:~# 
```