# rlyeh VPN

## note on key type selection
VPN uses RSA4096 key because even if macOS/iOS support ECDSA remote auth, it is configurable using `CertificateType` param which is not exposed in any user configurable object except MDM profiles (see https://gary-nebbett.blogspot.com/2019/10/establishing-vpn-connection-from-ios-to.html?m=0). 

This generates following error on client, which breaks connection without telling server:
```
[IKE_AUTH R resp1 ###-###] Initiator packet authentication method 
    Payload Type = Auth
    Authentication Protocol = ECDSA256
    Authentication Data = 64:{length = 64, bytes = ### } does not match proposal RSASignature
IKEv2Session[1, ###-###] Failed to process IKE Auth (EAP) packet (connect)
IKEv2Session[1, ###-###] Failed to process IKE Auth packet (connect)
```

## cmds
```
apt install strongswan libcharon-extra-plugins libstrongswan-extra-plugins
```

## `/opt/fix_masquerade.sh` - needed because LXD network keeps overwriting rules
```bash
#!/bin/bash
iptables-legacy -t nat -D POSTROUTING -s 10.0.0.0/16 ! -d 10.0.0.0/16 -m comment --comment "generated for LXD network lxdbr0" -j MASQUERADE
iptables-legacy -t nat -A POSTROUTING -s 10.0.0.0/16 ! -d 10.0.0.0/8 -m comment --comment "generated for LXD network lxdbr0" -j MASQUERADE
```

## `/etc/ipsec.conf`
```
config setup
  charondebug="ike 1, knl 1, cfg 1"
  uniqueids=no
  strictcrlpolicy=no
conn ikev2-vpn
  auto=add
  compress=no
  type=tunnel
  keyexchange=ikev2
  fragmentation=yes
  forceencaps=yes
  dpdaction=clear
  dpddelay=300s
  rekey=no
  left=144.76.27.245,144.76.27.254
  leftid=@rlyeh_ipsec.skowronski.cloud
  leftcert=server-cert.pem
  leftsendcert=always
  leftsubnet=10.0.0.0/8
  right=%any
  rightauth=eap-mschapv2
  rightdns=1.1.1.1,9.9.9.9
  rightsendcert=never

conn ikev2-vpn-yggdrasil
   also=ikev2-vpn
   rightid=yggdrasil
   eap_identity=yggdrasil
   rightsourceip=10.0.255.200/32

conn ulthar-to-rlyeh
  authby=secret
  type=tunnel
  auto=route
  left=172.16.0.18
  leftsubnet=10.0.0.1/16
  right=172.16.0.2
  rightsubnet=10.1.0.1/16
  ike=aes256-sha2_256-modp1024!
  keyexchange=ikev2
  esp=aes256-sha2_256!
  ikelifetime=1h
  rightupdown=/opt/fix_masquerade.sh
  leftupdown=/opt/fix_masquerade.sh
```

## secrets
* `/etc/ipsec.secrets`                  <- `../rlyeh-secrets/plain/ipsec/ipsec.secrets`
* `/etc/ipsec.d/private/server-key.pem` <- `../rlyeh-secrets/plain/ipsec/rlyeh_ipsec.key`
* `/etc/ipsec.d/certs/server-cert.pem`  <- `../rlyeh-secrets/plain/ipsec/rlyeh_ipsec.crt`
* `/etc/ipsec.d/cacerts/ca-cert.pem`    <- `../rlyeh-secrets/plain/DS_CA.crt`
