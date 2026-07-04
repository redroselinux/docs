# FAQ

## Q: Why does this distro exist?
**A:** We have a [whole page dedicated to that](/why.md).

## Q: Is this distro ready for use? Is it for begginners?
**A:** Not at all. It is made for moderately advanced users and currently has a lot of bugs and issues since it exists for only around half a year.

## Q: Why do you not use systemd?
**A:** Many reasons. To start off, a program should do ONE thing WELL. Systemd does a lot of things (if they are done well.. that's up to you). Also, is systemd easy to set up? No. Is configuring systemd easy? No. Could systemd use a bit of performance improvements? Yes.

On the other side, we do understand that systemd is the standard. This is why we have a [simple PoC for a systemctl-like command](https://github.com/redroselinux/ireallyloverunit). We plan to make expand it, and when someone runs systemctl, it tells them that this is not a systemd system and runs the translated runit command compatible with Redrose Linux. 

## Q: How to report a bug?
**A:** Go to our [Github Issues](https://github.com/redroselinux/redroselinux/issues) page. If you want to contribute, simply fork some repo and make changes. We have a [contribution guide](/contributing.md) for the main repo.

## Q: Does the installer require an internet connection?
**A:** No. The installer lets you pick your coreutils, select options, and install, without network.
