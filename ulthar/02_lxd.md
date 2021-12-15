# ulthar LXD

## LXD init
execute `lxd init`

* `Would you like to use LXD clustering? (yes/no) [default=no]:` -> *default*
* `Do you want to configure a new storage pool? (yes/no) [default=yes]: ` -> **yes**
* `Would you like to connect to a MAAS server? (yes/no) [default=no]:` -> *default*
* `Would you like to create a new local network bridge? (yes/no) [default=yes]:` -> *default*
* `What should the new bridge be called? [default=lxdbr0]:` -> *default*
* `What IPv4 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]:` -> **10.1.0.1/16** `<--`
* `Would you like LXD to NAT IPv4 traffic on your bridge? [default=yes]` -> **no** `<--`
* `What IPv6 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]:` -> *default*
* `Would you like the LXD server to be available over the network? (yes/no) [default=no]` -> **yes** 
* `Address to bind LXD to (not including port) [default=all]:` -> *default*
* `Port to bind LXD to [default=8443]:` -> *default*
* `Trust password for new clients:` -> see `rlyeh-secrets/plain/lxd_init.txt`
* `Would you like stale cached images to be updated automatically? (yes/no) [default=yes] ` -> *default*
* `Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]: ` -> *default*