# Redrose Linux Installation

Before installing, note: this distribution is work in progress. The installer is simple, but not easier than installing Ubuntu.

## System requirements

> **Note**<br>
> Redrose Linux currently supports BIOS systems only (tested in QEMU and on some random old computers). UEFI systems are not supported yet. NVMe drives are not supported either.

- RAM: ~1GB
- Disk: ~1-2GB
- Flash drive size: ~500MB
- Ventoy support: untested, likely yes
- Architecture: x86_64 (obviously)

## Obtaining a disk image

### Using a prebuilt image

To install Redrose Linux, first download a disk image from the downloads page:

* ISO download: [https://redroselinux.org/download](https://redroselinux.org/download)
* Beta VM launcher: use for quick testing
  ```bash
  curl -s https://redroselinux.org/vm_launcher.sh | sh
  ```

SHA256 hashes are available in the GitHub releases page:
[https://github.com/redroselinux/redroselinux/releases](https://github.com/redroselinux/redroselinux/releases)

![SHA256 sums](#/images/SHA256_sums.png)

To verify:

1. Remove `sha256:` prefix
2. Run:

```bash
sha256sum -c "hash"
```

### Build your own Live ISO

* [Build your own ISO (advanced)](#/build-your-own-image.md)

## Installing

### USB preparation

If you are using a VM, you can skip this step.

Check devices using the `lsblk` command and check if the drive is 100% the one you want to overwrite. In this example, `/dev/sda` is used.

---

Now, we can flash the ISO image to our drive.

> **Warning**<br>
> This is destructive. All data on the target drive will be erased.

```bash
#       input file                        output      show progress   faster copying
sudo dd if=redroselinux/redrose_linux.iso of=/dev/sda status=progress bs=100M
```

After flashing:

* Insert USB
* Reboot
* Enter BIOS/Boot menu (Esc / Enter / Del / F9 depending on system)
* Select USB device

---

## Using the installer

Boot the installer by pressing the ENTER key, and waiting for it to load. You will then be asked to choose between a guided and a manual installation.

### Guided

The installer asks for everything and shows the default value in the prompt - pressing ENTER picks the default.

#### Default settings

| Setting         | Default        |
| --------------- | -------------- |
| Keyboard layout | us             |
| Timezone        | Europe/London  |
| Username        | redrose        |
| User password   | redrose        |
| Root password   | redrose        |
| Hostname        | iuseredrosebtw |
| Coreutils       | GNU            |
| GRUB            | installed      |

> **Note**<br>
> The keyboard layout and timezone are asked, but not applied yet. This is planned for a future release.

> **Note**<br>
> To skip creating a user account, set the username to `root` and answer yes to the following prompt.

#### What the installer does

> **Warning**<br>
> All data on the selected drive will be erased.

The installer will:

* wipe the selected drive
* create partitions (a boot partition and a root partition)
* create filesystems (ext4 root)
* mount the root filesystem
* extract the base system (from `/rootfs.tar.gz`)
* install busybox and the chosen coreutils
* create the user and set passwords
* set the hostname
* initialize Car
* generate the initramfs
* install GRUB (unless you declined)
* generate `/etc/fstab`
* set up D-Bus

Before anything happens, you get a summary of your choices and a confirmation prompt.

If a step fails, you can choose whether to continue anyway or reboot.

After the installation finishes, you can optionally chroot into the new system or open a shell in the live environment. Pressing ENTER then reboots the machine.

Reboot after completion.

If you still see the Live ISO GRUB menu after rebooting, select `Boot existing OS`.

### Manual

To manually install Redrose Linux on your computer, boot the Live ISO and pick manual installation. 

This guide is unfinished, but it is enough for creating a simple bootable system.

#### (QoL) Install Busybox on the Live ISO

Run these commands, so you do not have to worry about prefixing all commands with `busybox`:
```bash
mkdir /usr /usr/bin /usr/sbin /sbin
busybox --install
export PATH=$PATH:/usr/bin:/sbin:/usr/sbin
```
Skipping this step is fine, but you will have to prefix most commands with `busybox`.

#### Identifying the target drive

Use the `fdisk` command:
```bash
fdisk -l
```
This outputs a list of drives and information about them.

This tutorial will use the `$drive` variable, so set it:
```bash
drive=/dev/sda # PICK YOUR ACTUAL DRIVE!
```

#### Erasing the drive

We will use the `sgdisk` command. This command does not require the `busybox` prefix even without installing; it is an external tool.

```bash
sgdisk --zap-all $drive
```

> In some places `$drive` is not used because we have to specify a specific partition. The problem is some drives end with `p1`, some end with `1` so we let you decide.

#### Creating partitions and filesystems

Here, we will also use the `sgdisk` command to create partitions. However, we need to know if we are installing on a BIOS or UEFI system.

If this command fails, you are using BIOS:
```bash
ls /sys/firmware/efi
```
Otherwise, you are on UEFI.

Partitioning uses the same scheme as the guided installer: partition 1 is root, partition 2 is the boot partition.

On BIOS, create partitions like this:
```bash
sgdisk --new=2:0:+1M -t 2:ef02 -c 2:\"BIOS boot\" $drive
sgdisk -n 1:0:0 -t 1:8300 -c 1:\"Redrose Linux\" $drive
```

On UEFI, replace the BIOS boot command with:
```bash
sgdisk --new=2:0:+512M -t 2:ef01 -c 2:\"EFI System\" $drive
```

Run the `partprobe` command after finishing.

Next, format the root partition:
```bash
mkfs.ext4 -F /dev/sda1
```

On BIOS, leave the boot partition as-is. On UEFI, format it with FAT32:
```bash
mkfs.vfat -F 32 /dev/sda2
```

#### Copy the rootfs files

Mount your root partition on `/mnt`:
```bash
mkdir -p /mnt
mount /drive/part/1 /mnt
```

> **Warning**<br>
> The reason $drive was not used above is that different types of drives have different blockdev names for partitions.

The rootfs tarball is located in `/rootfs.tar.gz`. 
```bash
gzip -dc /rootfs.tar.gz | tar -xvf - -C /mnt --strip-components=1
```

After this command finished, run `ls /mnt` to check if the files got extracted correctly.

Install busybox:
```bash
chroot /mnt /bin/sh -c '/bin/busybox --install'
```

If you also wish to install a different implementation of `coreutils` and `findutils`, extract these files to `/mnt`:
- (GNU) `/coreutils-gnu/*.tar.gz`
- (uutils) `/coreutils-uutils/*.tar.gz`

#### Generating important files

Run `mkfstab` to generate `/etc/fstab`:
- from chroot
  ```bash
mkfstab /
 ```
- or from live
  ```bash
/mnt/usr/bin/mkfstab /mnt -o /mnt/etc/fstab
  ```
Run `nullinitrd` to generate the initramfs.

#### Install the bootloader, aka. GRUB

To install GRUB, use the `grub-install` command in a chroot. On BIOS, just pass the device name to it:
```bash
grub-install $drive
```

## Using the installed system

Some basic setup can be found in [this category](/installed-system/index.md).
