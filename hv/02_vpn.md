# VPN

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

## `/etc/ipsec.conf`
```
config setup
    charondebug="ike 2, knl 2, cfg 0"
    uniqueids=no
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
    left=%any
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

```

## secrets
* `/etc/ipsec.secrets`                  <- `../rlyeh-secrets/plain/ipsec/ipsec.secrets`
* `/etc/ipsec.d/private/server-key.pem` <- `../rlyeh-secrets/plain/ipsec/rlyeh_ipsec.key`
* `/etc/ipsec.d/certs/server-cert.pem`  <- `../rlyeh-secrets/plain/ipsec/rlyeh_ipsec.crt`
* `/etc/ipsec.d/cacerts/ca-cert.pem`    <- `../rlyeh-secrets/plain/DS_CA.crt`
