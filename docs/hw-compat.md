# Hardware Compatibility

This page lists some hardware models and their compatibility with Redrose Linux. You can help improve this page by testing hardware that you own and [submitting a PR on GitHub](https://github.com/redroselinux/docs) adding an entry to the table below.

| Model               | Status | Notes |
|---------------------|--------|-------------------------------------------------------------------------|
| *any UEFI computer* | ❌     | Hangs on `Loading initramfs` |
| *NVMe drives*       | ❌     | `nullinitrd` does not support them and Redrose does not either |
| HP ProBook 455 G8   | ❌     | See issue [#25](https://github.com/redroselinux/redroselinux/issues/25) |
| ThinkCentre E73     | ✔️     | |
