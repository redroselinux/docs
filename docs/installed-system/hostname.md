# Hostname

## Setting the hostname

To set the hostname, use the `hostname` command or manually edit `/etc/hostname`.

## `hostname-reload-daemon`

The hostname reloading daemon writes the content of `/etc/hostname` to the kernel's file where it stores the hostname (`/proc/sys/kernel/hostname`) every 60 seconds.

Its source code can be found in the Redrose Linux Github repository at `rootfs/filesystem/usr/sbin/hostname-reload-daemon`.
