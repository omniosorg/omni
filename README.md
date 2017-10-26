<img src="http://www.omniosce.org/OmniOSce_logo.svg" height="128">

# Building OmniOSce

This package contains scripts to help set up and build a copy of
OmniOSce on your own server or within a non-global zone.

Each version of OmniOS can only be built on the same version so if you want
to build bloody, you need a machine running up-to-date bloody and the same
for a stable branch such as r151022 - you will need a machine running the
same release version.

You will also need a Github account and to have forked the `illumos-omnios`,
`omnios-build`, and `kayak` repositories before proceeding. The username for the
github account must be provided as an argument to the setup script.

### Quick start (building in global zone)

> It can be useful to create a dedicated ZFS filesystem for the build
> area (as shown below) but this step is optional.

```
# zfs create -o mountpoint=/build rpool/build
# chown <builduser> /build

# su - <builduser>

% pkg install git
% git clone https://github.com/omniosorg/omni.git
% cd omni
% ./setup /build <github username>

% omni update_illumos
% omni update_omnios

% omni build_illumos && omni build_omnios
```

### Example build zone setup

Building within a zone also works well with a couple of additional privileges
over the default set as shown in the example below. It is however not
possible to generate release media from within a zone.

To create a zone suitable for building:

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
zonecfg:omni> commit
zonecfg:omni> exit

# zoneadm -z omni install
A ZFS file system has been created for this zone.
Sanity Check: Looking for 'entire' incorporation.
       Image: Preparing at /data/zone/omni/root.

   Publisher: Using omnios (https://pkg.omniosce.org/r151022/core).
...

# zoneadm -z omni boot
# zlogin omni

# ipadm create-if omni0
# ipadm create-addr -T static -a local=x.x.x.x/y omni0/v4
# echo x.x.x.x > /etc/defaultrouter
# echo 'nameserver 80.80.80.80' > /etc/resolv.conf
# cp /etc/nsswitch.{dns,conf}
# svcadm restart routing-setup

# zfs create -o mountpoint=/build data/zone/omni/ROOT/build

```

The process for then building illumos-omnios and OmniOS itself is the
same as in the global zone.

