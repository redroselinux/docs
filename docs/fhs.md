# Filesystem Hierarchy Structure

Redrose Linux uses a very simple FHS. All of the `/usr` and `/` paths are symlinked, where `/` folders are symlinks to the `/usr` ones.

- `/`: the root of the filesystem; everything is located under this directory.
- `/bin` `/usr/bin`: programs for normal users (`cp`, `rm`, `mkdir`, `cat`, `ls`...).
- `/sbin` `/usr/sbin`: programs for system administration (`fdisk`, `grub-install`, `fsck`...).
- `/lib` `/usr/lib`: shared libraries used by dynamically linked programs.
- `/lib64` `/usr/lib64`: 64-bit shared libraries on systems that separate them from `/usr/lib`.
- `/etc`: system-wide configuration files.
- `/home`: user home directories.
- `/root`: home directory of the root user.
- `/tmp`: temporary files. programs may delete files here at any time. (tmpfs)
- `/var`: variable data such as logs, caches, and databases.
- `/run`: runtime state files, sockets, and PID files. contents are cleared on boot. (tmpfs)
- `/proc`: virtual filesystem exposing kernel and process information. (procfs)
- `/sys`: virtual filesystem exposing hardware and kernel interfaces. (sysfs)
- `/dev`: device files representing hardware and virtual devices. (devtmpfs)
- `/mnt`: temporary mount point for manually mounted filesystems.
- `/media`: mount points for removable media such as USB drives and CDs.
- `/srv`: data served by system services.
- `/boot`: bootloader files, kernel and initrd images, etc.

> **Where is /opt?**
> We already have /usr/local, and writing to the standard directories is already simpler.

## `/etc`

### `sv/`
A directory with runit services. This directory contains the actual service files, and `/etc/service` contains the symlinks for exanbled services.

### `os-release`
Redrose has no `/usr/lib/os-release`; only `/etc/os-release` exists. This is because `/usr/lib` should be for system libraries!

### `redrose-strap`
A file copied while bootstrapping the rootfs. It contains all packages from `rootfs/rootfs_strap_packages` in the Redrose source tree.

### `car/`
Configuration files for our package manager, [Car](/car.md).

### `car_propiertary.lock`
A file created when propriertary software is enabled. The name contains a typo, however it cannot be removed as it is already being used; we are planning to use a symlink.

### `repro.car`
A file containing a list of all installed Car packages. It should also be renamed but the reason it is not is again, it is used already. This one in particular is used ever since Car 0.1 was created.

### `mtab`
A symlink to `/proc/mounts`.
