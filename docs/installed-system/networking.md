# Networking

## Ethernet / QEMU

Connection with Ethernet is currently handled using Busybox `udhcpc` (we plan to get rid of Busybox in Redrose). If your Ethernet cable is connected, the system will automatically connect to the network. This is handled by the `dhcp` service.

If you are in QEMU (or GNOME Boxes, virt-manager, etc.), you do not need to setup Wi-Fi. QEMU handles network by redirecting your connection to the VMs Ethernet.

## Wi-Fi

Redrose Linux alpha-0.5 does not support Wi-FI, however, alpha-0.6 which will be released soon (early July 2026) will have `iwd` built in. [Check our roadmap for more information.](/roadmap.md)
