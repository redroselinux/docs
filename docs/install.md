# Redrose Linux Installation

Before installing, note: this distribution is work in progress. The installer is simple, but not easier than installing Ubuntu.

> **Note**<br>
> Redrose Linux was not tested on UEFI systems. It works on BIOS systems (tested in QEMU and on some random old computers).
> Also, as of May 2026, it uses the `defconfig` kernel configuration, which can also cause some issues on new hardware.

---

## Obtaining a disk image

### Using a prebuilt image

To install Redrose Linux, first download a disk image from the downloads page:

* ISO download: [https://redroselinux.org/downloads](https://redroselinux.org/downloads)
* Beta VM launcher: use for quick testing
  ```bash
  curl -sSSL https://raw.githubusercontent.com/redroselinux/redroselinux/refs/heads/main/src/redrose-launcher/main.py | python3 
  ```
* [#/build-your-own-image.md](Build your own image (advanced))

SHA256 hashes are available in the GitHub releases page:
[https://github.com/redroselinux/redroselinux/releases](https://github.com/redroselinux/redroselinux/releases)

![SHA256 sums](#/images/SHA256_sums.png)

To verify:

1. Remove `sha256:` prefix
2. Run:

```bash
sha256sum -c "hash"
```

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
sudo dd if=redroselinux/redrose_linux.iso of=/dev/sda status=progess
```

After flashing:

* Insert USB
* Reboot
* Enter BIOS/Boot menu (Esc / Enter / Del / F9 depending on system)
* Select USB device

---

## Using the installer

![GRUB menu](#/images/GRUB_menu.png)

Boot the installer by pressing the ENTER key, and waiting for it to load. Then pick an install mode - guided or manual. Manual installation is completely undocumented. The rootfs is in `/rootfs.tar.gz`.
If you use the guided installation, it should be clear what to do.

---

### Default settings

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

### Installation drive rules

Do not select a partition - it will not work.

* Drives: `/dev/sda`, `/dev/nvme0n1`
* Partitions: `/dev/sda1`, `/dev/nvme0n1p1`

The default selection is the largest drive found.

### What the installer does

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
