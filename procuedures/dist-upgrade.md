# dist upgrade

## intro steps

divide nodes and hypervisors into following groups ans store them in some sort of tracker file

1. full rebuild - fully stateless nodes
2. clustered rebuild - members of clusters that can be rebuuilt one by one without data loss
3. statefull nodes - export/import of some data required
4. in-place upgrade - mostly hypervisors that cannot be deleted and re-created

then, in `group_vars/all.yml` update `lxd_source.alias`

proceed AZ by AZ

## preparing backup destination inside LXC storage pool

target pool; `var_lib` dir is for backups made inside node

```bash
mkdir -p /mnt/backups/
lxc storage volume create default backups  size=256GB
lvchange --debug --verbose -Kay vg0/custom_default_backups  
mount /dev/mapper/vg0-custom_default_backups /mnt/backups/
mkdir -p /mnt/backups/var_lib
chmod 777 /mnt/backups/var_lib
```

intermediate pool, needed as place where LXC tools dump data; size must be at least of largest backed up container disk

```bash
lxc storage volume create default backups_internal  size=128GB
lxc config set storage.backups_volume storage.backups_volume: default/backups_internal
```

## generic instructions for attaching backup disk to nodes

purpose is to attach disk and dump only crucial data outside node (e.g. tarball of `/var/lib/prometheus`)

```bash
lxc config device add ${node} backups disk source=/mnt/backups path=/mnt/backups

lxc config device remove ${node} backups
```
## generic instructions for destroy & rebuild

from HV console:

```bash
export node=...
lxc stop ${node}
lxc delete ${node}
```

then from ansible operator machine execute full build commands:

```bash
./play ...-bootstrap.yml
./play ...-configure.yml
./play ...-deploy.yml
./play ...
````

## full rebuild

### special notes for SMI until it has backup instance

1. download https://inventory.skowronski.cloud/ansible/inventory.txt to ansible project dir
2. in `play` script replace `_inventory.py` with `inventory.txt`
3. after rebuild, revert changes in `play`

### optional backup for low AZs

first, *preparing backup destination inside LXC storage pool* is needed on each HV

log into parent HV and execute

```bash
export node=...
lxc export ${node} /mnt/backups/${node}.tgz
```

### destroy & rebuild

follow *generic instructions for destroy & rebuild*

## clustered rebuild

mostly the same case as *full rebuild*, but pay attention to `XYZ_stack_0-rebuild.md` for special steps for cluster joining as they are almost never ansibled

## statefull nodes

### data export

firstly, determine actions needed - location of workking directory for the service, for example `/var/lib/prometheus`

then, from HV follow *generic instructions for attaching backup disk to nodes*

after that log in to node and make a tarball:

```bash
systemctl stop ${service_unit}
tar Pczf /mnt/backups/var_lib/${node}.tgz /${working_dir}
```

### destroy & rebuild

follow *generic instructions for destroy & rebuild*

### data import

follow *generic instructions for attaching backup disk to nodes* again as the instance is new

the log in to node and execute

```bash
systemctl stop ${service_unit}
cd /
tar vxzf /mnt/backups/var_lib/${node}.tgz
systemctl start ${service_unit}
````

after that, detach backups volume using *generic instructions for attaching backup disk to nodes*

## in-place

if possible, perform backup of rootfs (for Hetzner Cloud use snapshot, for baremetal - use backups volume to dump `/`)

then log in to machine and execute:

``` bash
apt update
apt upgrade
apt dist-upgrade
do-release-upgrade -d
```
