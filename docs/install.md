# Redrose Linux Installation

Before installing, note: this distribution is work in progress. The installer is simple, but not easier than installing Ubuntu.

## System requirements

> **Note**<br>
> Redrose Linux was not tested on UEFI systems. It works on BIOS systems (tested in QEMU and on some random old computers).
> Also, as of May 2026, it uses the `defconfig` kernel configuration, which can also cause some issues on new hardware.

- RAM: ~700MB (for Live ISO)
- Disk: ~1-2GB
- Flash drive size: ~500MB
- Ventoy support: untested, likely yes
- Architecture: x86_64 (obviously)

## Obtaining a disk image

### Using a prebuilt image

To install Redrose Linux, first download a disk image from the downloads page:

* ISO download: [https://redroselinux.org/downloads](https://redroselinux.org/downloads)
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
#       input file                        output      show progress
sudo dd if=redroselinux/redrose_linux.iso of=/dev/sda status=progress
```

After flashing:

* Insert USB
* Reboot
* Enter BIOS/Boot menu (Esc / Enter / Del / F9 depending on system)
* Select USB device

---

## Using the installer

![GRUB menu](#/images/GRUB_menu.png)

Boot the installer by pressing the ENTER key, and waiting for it to load. Then pick an install mode - guided or manual.

### Guided

#### Default settings

| Setting         | Default        |
| --------------- | -------------- |
| Keyboard layout | us             |
| Locale          | us             |
| Timezone        | UTC            |
| Username        | redrose        |
| User password   | redrose        |
| Root password   | redrose        |
| Hostname        | iuseredrosebtw |
| Coreutils       | GNU            |

---

#### Installation drive rules

Do not select a partition - it will not work.

* Drives: `/dev/sda`, `/dev/nvme0n1`
* Partitions: `/dev/sda1`, `/dev/nvme0n1p1`

The default selection is the largest drive found.

#### What the installer does

> **Warning**<br>
> All data will be erased.

The installer will:

* wipe disk
* create partitions
* copy system files (from `/rootfs.tar.gz`)
* install GRUB
* configure users, hostname, etc.
* ask which coreutils to use
* optionally chroot (default: no)
* optionally open shell (default: no)
* unmount system
* finish install

![Partitioning](#/images/Drive_selection_in_the_installer.png)

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

On BIOS, create partitions like this:
```bash
sgdisk -n 1:1M:+1M -t 1:ef02 -c 1:\"BIOS boot\" $drive
sgdisk -n 3:0:0 -t 3:8300 -c 3:\"Redrose Linux\" $drive # use part no. 3 here, because redrose sometimes expects your drive to be partition 3
```

On UEFI, replace the BIOS boot command with:

```bash
sgdisk -n 2:0:+512M -t 2:ef00 -c 1:\"EFI System\" $drive
```

Run the `partprobe` command after finishing.

Next format the main partition using `mke2fs`:
```bash
mke2fs -F /dev/sda3
```

On BIOS, leave the boot partition as-is. On UEFI, format it with FAT32:
```bash
mkfs.vfat -F32 /efi/partition/thing
```

#### Copy the rootfs files

Mount your root partition on `/mnt`:
```bash
mkdir -p /mnt
mount /drive/part/3 /mount
```

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
- from live
  ```bash
/mnt/usr/bin/mkfstab /mnt -o /mnt/etc/os-release
  ```
Run `nullinitrd` to generate the initramfs.

#### Install the bootloader, aka. GRUB

To install GRUB, use the `grub-install` command in a chroot. On BIOS, just pass the device name to it:
```bash
grub-install $drive
```
