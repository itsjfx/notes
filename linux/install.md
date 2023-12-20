# Linux Install

https://xkcd.com/538/
![[../assets/xkcd-538.png]]
These are rough notes written for me, hope it helps somebody.

I'm currently running Arch Linux on my desktop, but this would be applicable to Fedora with a minimal install (somewhat) etc.

I wanted my Linux install to have these things:
* `UEFI` and `GPT`
* `systemd-boot` instead of `GRUB`
* `btrfs` with subvolumes
  * trying out `zstd:1`
	  * [default on Fedora](https://github.com/rhinstaller/anaconda/blob/c6e2e850b84f50ecfa956b3ab3dbfcf02b47fd87/data/profile.d/fedora.conf#L19)
  * <https://man.archlinux.org/man/btrfs.5#COMPRESSION_LEVELS>
* support for `btrfs` snapshots in the future if I want them
* `LUKS2` encrypted main partition
* unencrypted `/boot`
	* see xkcd
* no LVM
	* similar to <https://fedoraproject.org/wiki/Changes/BtrfsByDefault#Detailed_Description>
* dual boot with Windows (not working !!!!!)
* secure boot (not done right now)
	* <https://wiki.archlinux.org/title/dm-crypt/Encrypting_an_entire_system#LUKS_on_a_partition_with_TPM2_and_Secure_Boot>

Based off:
* https://wiki.archlinux.org/title/installation_guide
* https://wiki.archlinux.org/title/dm-crypt/Encrypting_an_entire_system#LUKS_on_a_partition
* https://itsfoss.com/install-arch-linux/
* https://github.com/egara/arch-btrfs-installation
* https://fogelholk.io/installing-arch-with-lvm-on-luks-and-btrfs/
* https://gist.github.com/OdinsPlasmaRifle/e16700b83624ff44316f87d9cdbb5c94

## TODO

* zram and swap file? or just swap file and zswap?
* swap in general
* suspend to disk
* snapshots and restore
* dual boot with Windows working properly
* systemd command aliases in rofi
  * `systemctl reboot --firmware-setup`
  * `systemctl reboot --boot-loader-entry=x.conf`
* systemd boot updating

## Arch Installation

Follow the [Arch Installation Guide](https://wiki.archlinux.org/index.php/Installation_guide), check here for steps changes

### Pre-installation/Set the console keyboard layout and font

Ignore this

### Pre-installation/Partition the disks

#### Partition

Based on:
* <https://wiki.archlinux.org/title/dm-crypt/Encrypting_an_entire_system#LUKS_on_a_partition>
* <https://github.com/egara/arch-btrfs-installation>

* Where `BOOT_PARTITION` is boot partition
* Where `ROOT_PARTITION` is root/data partition

```
+-----------------------+------------------------+
| Boot partition        | LUKS2 encrypted root   |
|                       | partition              |
|                       |                        |
| /boot                 | /                      |
|                       |                        |
|                       | /dev/mapper/root       |
|                       |------------------------|
| /dev/BOOT_PARITTION   | /dev/ROOT_PARTITION    |
+-----------------------+------------------------+
```

1. Provision the drive with `fdisk DRIVE`, find with `fdisk -l`
2. Delete existing partitions (use `d`) ? I think 3. handles this
3. Type `g` to create a `GPT` drive
4. Make EFI partition
   1. Press `n`
   2. Partition type: Primary
   3. Partition number: 1
   4. First sector: continue
   5. Last sector: `+512M`
   6. Press `t` to change the type, press `L` to list types
   7. Select the type for `EFI System`
      1. The types seem to differ per motherboard (?), so double check
   8. Don't worry about making/marking as bootable
5. Make primary partition
   1. Press `n`
   2. Partition type: Primary
   3. Partition number: 2
   4. First sector: continue
   5. Last sector: continue
   6. Press `t` to change the type, press `L` to list types
   7. Select the type for `Linux filesystem`
      1. The types seem to differ per motherboard (?), so double check
6. Press `w` to write to the drive

#### Validate drives

Validate with `fdisk -l`

Look for the following:
* Disklabel type: `GPT`

It should look like:

```
Disk /dev/nvme1n1: 1.82 TiB, 2000398934016 bytes, 3907029168 sectors
Disk model: Samsung SSD 980 PRO 2TB
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: x

Device           Start        End    Sectors  Size Type
/dev/nvme1n1p1    2048    1050623    1048576  512M EFI System
/dev/nvme1n1p2 1050624 3907028991 3905978368  1.8T Linux filesystem
```

#### Setup LUKS & btrfs

##### Make LUKS container and initialise btrfs

* Following <https://github.com/egara/arch-btrfs-installation> but substituting `/dev/mapper/root`
* You should be able to follow any guide and do the same

```
cryptsetup -y -v luksFormat /dev/ROOT_PARTITION
cryptsetup open /dev/ROOT_PARTITION root
mkfs.btrfs -L arch /dev/mapper/root
mount /dev/mapper/root /mnt
```

Check mapping works as intended
```
umount /mnt
cryptsetup close root
cryptsetup open /dev/ROOT_PARTITION root
mount /dev/mapper/root /mnt
```

##### Setup BTRFS subvolumes

```
cd /mnt
btrfs subvolume create _active
btrfs subvolume create _active/rootvol
btrfs subvolume create _active/homevol
btrfs subvolume create _snapshots
```

##### Mount them

* For the boot partition, `systemd-boot` throws a warning if your permissions are too open, somebody on reddit suggested these masks:
  * <https://www.reddit.com/r/archlinux/comments/15slhtp/deleted_by_user/>

```
cd ..
umount /mnt
mount -o subvol=_active/rootvol,compress=zstd:1,discard=async /dev/mapper/root /mnt
mkdir /mnt/{home,boot,btrfs_root}
mount -o fmask=0137,dmask=0027 /dev/BOOT_PARTITION /mnt/boot
mount -o subvol=_active/homevol,compress=zstd:1,discard=async /dev/mapper/root /mnt/home
mount -o subvol=/,compress=zstd:1,discard=async /dev/mapper/root /mnt/btrfs_root
```

##### Swap File

TODO
need to exclude from COW

### Installation/Select the mirrors

These get copied, so set them up properly now

```
pacman -Syy
pacman -S reflector
reflector -c "AU" --save /etc/pacman.d/mirrorlist
```

### Installation/Install essential packages

* Where `CPU_MICROCODE` is `intel-ucode` or `amd-ucode` or `whatever else`

```
pacstrap -K /mnt base linux linux-firmware neovim networkmanager dhclient sudo CPU_MICROCODE
```

### Configure the system/Fstab

1. Run `genfstab -U /mnt >> /mnt/etc/fstab`
2. In `/mnt/etc/fstab` remove `subvolid=` references on each `btrfs` mount to help with snapshot rollback in the future

It should look like this:
```
# Static information about the filesystems.
# See fstab(5) for details.

# <file system> <dir> <type> <options> <dump> <pass>
# /dev/mapper/root LABEL=arch
UUID=x	/         	btrfs     	rw,relatime,compress=zstd:1,ssd,discard=async,space_cache=v2,subvol=/_active/rootvol	0 0

# /dev/nvme0n1p1
UUID=x      	/boot     	vfat      	rw,relatime,fmask=0137,dmask=0027,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro	0 2

# /dev/mapper/root LABEL=arch
UUID=x	/home     	btrfs     	rw,relatime,compress=zstd:1,ssd,discard=async,space_cache=v2,subvol=/_active/homevol	0 0

# /dev/mapper/root LABEL=arch
UUID=x	/btrfs_root	btrfs     	rw,relatime,compress=zstd:1,ssd,discard=async,space_cache=v2,subvol=/	0 0
```

### Configure the system/Localization

Make sure to uncomment `en_US.UTF-8 UTF-8` and do the rest of the steps, ignore console keyboard layout

### Configure the system/Network configuration

In addition to `/etc/hostname`, make an `/etc/hosts` with the following:

```
127.0.0.1 localhost
::1 localhost
127.0.0.1 HOSTNAME
```

### Configure the system/Initramfs & Configure the system/Boot loader

### systemd-boot and LUKS initramfs

TODO `systemd-boot` updating

See the following:
* <https://wiki.archlinux.org/title/systemd-boot>
* <https://wiki.archlinux.org/title/dm-crypt/Encrypting_an_entire_system#Configuring_mkinitcpio>

1. Setup these hooks: `HOOKS=(base systemd autodetect modconf kms keyboard block sd-encrypt filesystems fsck)`
   1. `systemd` `keyboard` `sd-encrypt` are the important ones
   2. Add `sd-vconsole` if using non-default console font/non-US keyboard
2. I've put `btrfs` in `MODULES`, it can probably go in `HOOKS` after `sd-encrypt`
   1. `MODULES=(btrfs)`
3. Run `bootctl install`
4. Re-generate the initramfs via `mkinitcpio -P`

#### Edit bootloader config

Edit `/boot/loader/loader.conf`

and put
```
default arch.conf
timeout 10
console-mode max
editor no
```

#### Add a loader for arch

TODO modify options correctly

* Edit `esp/loader/entries/arch.conf`
* Where `CPU_MICROCODE` is `intel-ucode` or `amd-ucode` or `whatever else`
* **Validate the below files are in the `/boot` folder, if not then do the below**
    * `vmlinuz-linux`: `linux`
    * `CPU_MICROCODE`: `package from earlier`
    * `initramfs-linux.img`: `mkinitcpio -P`
* **Make sure that your `initrd CPU_MICROCODE` is BEFORE `initrd initramfs-linux.img` otherwise you'll get a failure**
* Where UUID is your UUID of your root partition
  * You can use `:read ! blkid /dev/ROOT_PARTITION` in `vim` to get it, or the same command in a terminal and type it out

```
title Arch Linux
sort-key 01
linux /vmlinuz-linux
initrd /CPU_MICROCODE.img
initrd /initramfs-linux.img
options rd.luks.name=UUID=root root=/dev/mapper/root rootflags=subvol=_active/rootvol rds.luks.options=discard rw quiet splash
```

#### Repeat above for fallback-initramfs

Same config, but reference `/initramfs-fallback` instead

### Configure the system/Root password

Don't forget to do this

### Reboot

Reboot and that's the end of the Arch Installation

## Reboot and run bootstrap

Other people: please don't use this script

Not tested (sorry future me):
```
curl -fL https://raw.githubusercontent.com/itsjfx/dotfiles/master/scripts/bootstrap/run_bootstrap.sh >run_bootstrap.sh

bash run_bootstrap.sh 
```

## Copy root files

Copy the root files `~/.root-files/` over as they will setup X11 and part of the NVIDIA stuff below correctly

TODO have a script to do this and run diffs

## NVIDIA steps

After installing NVIDIA drivers from bootstrap there's a couple of things needed

#### Remove nouveau/kms from mkinitcpio

I'm not sure if this is needed if you _don't_ install `nouveau` but listed here https://wiki.archlinux.org/title/NVIDIA#Installation

Remove `kms` from `mkinitcpio.conf` and regenerate the initramfs

#### Setup DRM kernel mode setting

* [1.3 DRM kernel mode setting](https://wiki.archlinux.org/title/NVIDIA#DRM_kernel_mode_setting)
    * Should be done in `~/.root-files/etc/modprobe.d/nvidia.conf`
    * Latest drivers are >545 in Arch so no need to set anything in the kernel parameters
    * To check:
        * `cat /sys/module/nvidia_drm/parameters/modeset` and `fbdev` should return `Y`
* [1.3.1.1 mkinitcpio](https://wiki.archlinux.org/title/NVIDIA#mkinitcpio) Add early loading to the kernel
    * Follow these steps
    * https://wiki.archlinux.org/title/Kernel_mode_setting#mkinitcpio
    * Add `MODULES=(... nvidia nvidia_modeset nvidia_uvm nvidia_drm ...)` to the `mkinitcpio.conf`
    * Re-generate the initramfs
* [1.3.1.3 pacman hook](https://wiki.archlinux.org/title/NVIDIA#pacman_hook) pacman hook
    * Should be done in `~/.root-files/etc/pacman.d/hooks/nvidia.hook`

`sudo sysfsutils | less` you should see multiple entries from `nvidia` from earlier
## Setup Windows dual boot

I couldn't get this working:
* https://forum.endeavouros.com/t/tutorial-add-a-systemd-boot-loader-menu-entry-for-a-windows-installation-using-a-separate-esp-partition/37431
* https://wiki.archlinux.org/title/systemd-boot#Boot_from_another_disk

TODO: read https://wiki.archlinux.org/title/Dual_boot_with_Windows and see if any of the tips help. Seems like good prereading.
* check fast shutdown in Windows
* check fast startup in BIOS

Frozen screen on "Windows Boot Manager"
## Snapshots

TODO
