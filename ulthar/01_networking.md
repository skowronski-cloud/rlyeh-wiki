# ulthar networking

## install hc_utils and let it work
```
curl https://packages.hetzner.com/hcloud/deb/hc-utils_0.0.3-1_all.deb -o /tmp/hc-utils_0.0.3-1_all.deb -s && apt install /tmp/hc-utils_0.0.3-1_all.deb
```

## vSwitch - rlyeh (robot/dedicated) part must be executed before ulthar (console/cloud) part

1. first you must complete *rlyeh (robot/dedicated) part* and have vSwitch
2. go to https://console.hetzner.cloud/projects/191112/networks and select/create network
3. configure network as **172.16.0.0/24**
4. add first subnet **172.16.0.0/28** and attach **ulthar** VM with **172.16.0.2** IP
5. add second subnet **172.16.0.16/28** with **enable vSwitch** checkbox

## masquerade
masquerade is managed by this script instead of LXC NAT as XFRM (netfilter routing) does not work well with LXC's network

below is ansible managed

### `/opt/fix_masquerade.sh`
```
#!/bin/bash
MY_NET=10.1.0
REMOTE_NET=10.0.0

ping -c1 -W1 "${REMOTE_NET}.1"
if [[ $? -eq 1 ]]; then
  # this is one from LXD
  iptables-legacy -t nat -D POSTROUTING -s "${MY_NET}.0/16" ! -d "${MY_NET}.0/16" -m comment --comment "generated for LXD network lxdbr0" -j MASQUERADE

  # remove old fix to avoid duplicates
  iptables-legacy -t nat -D POSTROUTING -s "${MY_NET}.0/16" ! -d 10.0.0.0/8 -m comment --comment "generated for LXD network lxdbr0" -j MASQUERADE

  # this is proper one
  iptables-legacy -t nat -A POSTROUTING -s "${MY_NET}.0/16" ! -d 10.0.0.0/8 -m comment --comment "generated for LXD network lxdbr0" -j MASQUERADE
fi
```
### crontab
```
#Ansible: /opt/fix_masquerade.sh
* * * * * /opt/fix_masquerade.sh 1>/dev/null 2>/dev/null
```
