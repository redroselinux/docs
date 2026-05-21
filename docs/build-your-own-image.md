# Build your own Live ISO

To build your own Redrose Linux ISO image, you need to install some tools, clone the source and start the building process.

## Downloading the source code

Our Github repo is located at [https://github.com/redroselinux/redroselinux](https://github.com/redroselinux/redroselinux), so to clone the repo, run:

```bash
git clone https://github.com/redroselinux/redroselinux --depth=1
```

Using `--depth=1` makes the download much faster by not downloading the entire Git history of our repo; it's pretty large.

## Installing dependencies

Run `make dep` (which also runs every time before compilation) to check if you have all the required programs.
A list is not maintained here because every time we would (not) require a dependency, we will have to update both the Makefile and the documentation, which would be very annoying.

## Configuring the compilation

The `rootfs/rootfs_strap_packages` file contains all packages that are installed into the rootfs. All packages downloaded are cached in `strap_packages/`, so every time you need to update a package, you must delete the old version.

This file is used in the `Makefile`, which calls `strap.py`. Example usage of this script:
```bash
echo "example" | python3 strap.py
```
This command will install `example` into the `rootfs/filesystem` dir (which is the default)/

You can also add parameters to the package field:
```
-- comments too!
example
example:rootfs/filesystem
example:rootfs/filesystem:recompress
```
Adding `recompress` will recompress the package directory with gzip, for the coreutils selection feature. This command will create `rootfs/filesystem/example.tar.gz`.

The entire `rootfs/rootfs_strap_packages` file is written to `rootfs/filesystem/etc/redrose-strap`, every time you input something to `strap.py`, line-by-line.

## Starting compilation

Simply run the `make` command. This also spins up a VM; if you do not want this, run `make no-vm`.
