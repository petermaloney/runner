# unnamed qemu runner

Author: Peter Maloney

Goals and features
==========

- Simple
- Easy to modify it (it's a script, not some big huge C program)
- Config is simple name=value syntax, no xml or anything complicated. And it's bash, so you can code some conditionals or whatever you want.
- config script supports comments

TODO
==========
- for the "qemu-bridge" file and "qemu-bridge-*", first look in same dir as conf, then in same as runner
- give it a name and rename stuff... maybe call it qrun

Installation
==========

```
$ git clone git@github.com:petermaloney/runner.git
$ mkdir runner-conf
$ cp runner/defaults runner-conf/
$ vim runner-conf/defaults
    (edit the defaults if you like)
```

Optionally put "runner" in $PATH

Usage
==========
Either put "runner" in $PATH, or use absolute path in the shebang (first line in config script), or run with "/path/to/runner /path/to/configfile"

make a config file like this:
```
$ cd runner-conf
$ vim vm1
```

```
#!/usr/bin/env runner

disk1_file=/dev/somevolumegroup/somelogicalvolume
disk1_format=raw
disk1_type=ide

cd1_file=/dev/sr0
cd1_type=ide

ram=4096
cpus=4

# blank mac means auto-generate and write in this file on the first run
net1_mac=
net1_type=e1000

vgaType=vmware
```

Example for passing through a graphics card and some other PCI device (eg. usb controller).
```
#don't set vgaType if setting vgaPci
#vgaType=vmware

romDir=/mnt/archive/software/vgarom/Sapphire.HD6770.1024
romFile="$romDir"/Sapphire.HD6770.1024.120105.rom
vgaPci=05:00.0

add_vfio 00:14.2
```


```
$ chmod u+x vm1
```

then run your config file (as root):

```
# ./vm1
```

Configuration Options
==========

disk1_file:
- path to file or raw device, eg. /dev/mapper/vms-vm1disk2
- ceph rbd with usual syntax, rbd:{pool}/{image}[:option=value], eg. rbd:rbd/vm-101-disk-1:id=peter

disk1_type:
- virtio
- ide

disk1_format:
- raw
- qcow2

cd1_type:
- virtio
- ide

net1_type:
- e1000e
- virtio

vgaType - any qemu type you set is passed through to qemu (std,qxl,cirrus,vmware,etc.), except these which are handled specially, and vmware is the default if blank:
- vnc
- spice

assumptions - not bugs
==========

- using -q35 always; this works with even windows xp 32 bit... probably not a problem for anyone.
- it assumes if running with $DISPLAY set, you want the gui, else vnc
- config file is bash... you can't change it with the shebang
- defaults file contains some other things you might call assumptions... such as the bridge name br0

assumptions - and maybe bugs
==========

- currently tested on manjaro qemu 2.3.0-7 and 2.4.0-2; maybe some quirks or qemu command syntax are not from qemu but from Manjaro
