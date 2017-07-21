<img src="http://www.omniosce.org/OmniOSce_logo.svg" height="128">

# Building OmniOSce

This package contains scripts to help set up and build a copy of
OmniOSce on your own server or within a non-global zone.

Each version of OmniOS can only be built on the same version so if you want
to build bloody, you need a machine running up-to-date bloody and the same
for a stable branch such as r151022 - you will need a machine running the
same release version.

You will also need a Github account and to have forked the `illumos-omnios`
and `omnios-build` repositories before proceeding. The username for the
github account must be provided as an argument to the setup script.

### Example build zone setup

```
# dladm create-vnic -l igb0 omni0

# zonecfg -z omni
omni: No such zone configured
Use 'create' to begin configuring a new zone.
zonecfg:omni> create
zonecfg:omni> set brand=lipkg
zonecfg:omni> set zonepath=/data/zone/omni
zonecfg:omni> set fs-allowed=ufs
zonecfg:omni> set limitpriv=default,dtrace_user,dtrace_proc
zonecfg:omni> set ip-type=exclusive
zonecfg:omni> add net
zonecfg:omni:net> set physical=omni0
zonecfg:omni:net> end
zonecfg:omni> verify
zonecfg:omni> exit

# zoneadm -z omni install
A ZFS file system has been created for this zone.
Sanity Check: Looking for 'entire' incorporation.
       Image: Preparing at /data/zone/omni/root.

   Publisher: Using omnios (https://pkg.omniosce.org/r151022/core).
...

# zlogin omni

# ipadm create-if omni0
# ipadm create-addr -T static -a local=x.x.x.x/y omni0/v4
# route -p add default x.x.x.x
# echo 'nameserver 80.80.80.80' > /etc/resolv.conf
# cp /etc/nsswitch.{dns,conf}

# zfs create -o mountpoint=/build data/zone/omni/ROOT/build

# cd /root
# git clone https://github.com/citrus-it/omni.git
# cd omni
# ./setup /build <github username>

# omni build_oi
# omni build_omnios

```

