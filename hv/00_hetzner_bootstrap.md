# Bootstrapping from Hetzner rescue image

Execute `installimage`, select `Ubuntu`, then `20.10`. In `install.conf` editor put this:

````
HOSTNAME hv.skowronski.cloud
IMAGE /root/.oldroot/nfs/install/../images/Ubuntu-2010-groovy-64-minimal.tar.gz

DRIVE1 /dev/nvme0n1
DRIVE2 /dev/nvme1n1
SWRAID 1
SWRAIDLEVEL 1

PART /boot ext3 512M
PART lvm   vg0  all

LV   vg0   root /   ext4 32G
```

After install is finished, reboot and add SSH keys - current personal `ssh-ed25519` and one for Semaphore.

Since Hetzner does not have image for Ubuntu 21.04 there's need to execute `do-release-upgrade`.