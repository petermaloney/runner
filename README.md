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
- for the "qemu-ifup" file and "qemu-ifup-*", first look in same dir as conf, then in same as runner
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
- virtio-scsi (supports discard/fstrim)
- virtio
- ide

disk1_format:
- raw
- qcow2

disk1_cache:
- from qemu manual: cache is "none", "writeback", "unsafe", "directsync" or "writethrough" and controls how the host cache is used to access block data

cd1_type:
- virtio
- ide

net1_type:
- e1000e
- virtio

vgaType - any qemu type you set is passed through to qemu (std,qxl,cirrus,vmware,etc.), except these which are handled specially, and vmware is the default if blank:
- vnc
- spice

with vnc, there's also:
vncport - the number to add to 5900 to get the listen port (default 0)

with spice, there's also:
spice_port - port to listen to for SPICE clients (default 5900)
spice_addr - address to listen on for SPICE clients (default 127.0.0.1)

romFile - file path to a VGA rom file to use instead of qemu default which is to use what's on the hardware; with some configurations (presumably kernel,qemu,hw,seabios versions) this could improve stability problems when rebooting guests

vgaPci - pci address of the GPU to add

vga_pci_add_bus - controls adding bus,addr options for the vfio-pci line for vgaPci:
- 0 - does not add it
- 1 - (default) adds it

drive_type - applies to both diskN_type and cdN_type:
- ide (default)

balloon:
- 1 = enable qemu virtio-balloon-pci
- 0 = disabled (default)

biosbin:
- path to bios file

ovmf:
- 1 = enable ovmf
- 0 = disabled (default)

ovmf_dir:
- directory to store copies of the ovmf code (bios bin) and vars images (default = ${confdir}/ovmf)

ovmf_code:
- location of the original ovmf code bin file (default = /usr/share/ovmf/ovmf_code_x64.bin)

ovmf_vars:
- location of the original ovmf vars bin file (default = /usr/share/ovmf/ovmf_vars_x64.bin)

monitor_stdio:
- 1 = enable stdio monitor (default)
- 0 = disabled

monitor_qmp:
- 1 = enable qmp monitor (for use with a qmp client, eg. qmp-shell -H /var/run/runner/qmp/${vmname}.qmp)
- 0 = disabled (default)

assumptions - not bugs
==========

- using -q35 always; this works with even windows xp 32 bit... probably not a problem for anyone.
- it assumes if running with $DISPLAY set, you want the gui, else vnc
- config file is bash... you can't change it with the shebang
- defaults file contains some other things you might call assumptions... such as the bridge name br0

assumptions - and maybe bugs
==========

- currently tested on artixlinux qemu 6.2.0; maybe some quirks or qemu command syntax are not from qemu but from this
