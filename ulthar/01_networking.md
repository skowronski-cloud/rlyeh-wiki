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
