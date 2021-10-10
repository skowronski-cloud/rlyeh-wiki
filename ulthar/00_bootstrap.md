# ulthar bootstrapping after system is created from Hetzner Cloud console

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



