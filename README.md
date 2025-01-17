# atv-bootloader

(forked from original code exported from Google Code)

This bootloader works by embedding a Linux kernel/initrd into "mach kernel" that boot.efi loads. Right now it's very simplistic and will boot to login prompt. **user = root, password = root.**

`parted` is present as well as hfsplus tools for creating gpt partitions and formating them as ext2/3 and hfs/hfsplus. Also present is `kexec` to bootload another kernel. Typical usage is:

```
kexec --load vmlinuz --initrd=initrd.gz --command-line="root=/dev/sdb2 video=vesafb"
kexec -e
```

atv-bootloader does not require an efi enabled kernel nor the patched `imac_fb` framebuffer, only a `vesafb` is required for console output. Boot params are set in `com.apple.Boot.plist`. 

There are some special switches: 

* If `"video=imac_fb"` is detected, atv-bootloader will assume a patched imac_fb is present. 
* `"video=efifb"` assumes the 2.6.24 efifb frame buffer driver is present. 
* `"video=vesafb"` assumes the vesafb frame buffer.

Of special note, some installers are doing funny things like assuming a video bios (it's hiding in the AppleTV), these will hang and do bad things.  So you might have to perform the actual Linux install on a doner box. Or if you only have Windows, burn a Linux LiveCD and boot your Windows box from that.

## How To Use

Make a recovery partition. 25MB in size is plenty. Copy boot.efi, mach_kernel and the contents of recovery.tar.gz into it. sync, unmount, stick it in the AppleTV, reboot (might need to hold IR remote "menu" and "-" buttons down. This should boot to a Linux login prompt. *See [LinuxUSBPenBoot](https://github.com/lemonjesus/atv-bootloader/wiki/LinuxUSBPenBoot) for more complete instructions.*

## How To Build
The first thing I did after forking this was dockerize the build environment for reproducable builds. It pulls the toolchain from The Internet Archive and installs it. The `Makefile` should Just Work™, producing a `mach_kernel` correctly.

I have not looked into how to build the kernel that it loads, but it seems pretty straight forward.

## Uses
* Make a Linux based patchstick. Linux permissions and user IDs are similar to darwin. So you could create a bash stript that copies sshd into the AppleTV. Linux run the bash script but it moves and mods darwin stuff.

* Make a kexec bootloader. Make the recovery bits like normal, add a script at the end of "s/etc/init.d/rcS" that mounts your root partition and kexec into it.


## Special Thanks
* Special thanks to the original devleoper: `davilla`
* Special thanks to "Edgar (gimli) Hucek" for the 1st boot and original code (mach_linux_boot) and James McKenzie of Mythic-Beasts for mb_boot_tv.

