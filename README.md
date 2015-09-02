unnamed qemu runner
==========

Author: Peter Maloney

Goals and features
==========

- Simple
- Easy to modify it (it's a script, not some big huge C program)
- Config is simple name=value syntax, no xml or anything complicated. And it's bash, so you can code some conditionals or whatever you want.
- config script supports comments

TODO
==========
this is a disorganized repo... I pushed from somewhere without all the newest files
give it a name and rename stuff... maybe call it qrun
convert this to an actual markdown syntax
vnc port is always 5900, only one vm supported ... I probably already fixed that but it's not in this version
add a --dry-run option, so it'll print the command without running
document options and defaults for variables like disk1_type, cdType, net1_type, vgaType

Installation
==========

$ git checkout TODO: put url here
$ mkdir runner-conf

Optionally put "runner" in $PATH

Usage
==========
Either put "runner" in $PATH, or use absolute path in the shebang (first line in config script), or run with "/path/to/runner /path/to/configfile"

make a config file like this:
$ cd runner-conf
$ vim vm1

-------------------------
#!/usr/bin/env runner

disk1_file=/dev/somevolumegroup/somelogicalvolume
disk1_format=raw
disk1_type=ide

cdFile=/dev/sr0
cdType=ide

ram=4096
cpus=4

# blank means auto-generate
net1_mac=
net1_type=e1000

vgaType=vmware
-------------------------


then run your config file:

./vm1

assumptions - not bugs
==========

using -q35 always; this works with even windows xp 32 bit... probably not a problem for anyone.
it assumes if running with $DISPLAY set, you want the gui, else vnc
config file is bash... you can't change it with the shebang
defaults file contains some other things you might call assumptions... such as the bridge name br0
