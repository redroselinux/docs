# `mkfstab`

`mkfstab` is a tool to generate the `/etc/fstab` file containing information about mounts. It still quite an unfinished tool, written in Rust.

## Usage

A command to generate `fstab` from `/` into `/etc/fstab` would look like this:

```bash
mkfstab /
```

The command may ask for confirmation if `/etc/fstab` already exists. You can use the `--yes` or `--no` flags to go over confirmation prompts, or use the `-o` flag to change the output file.

Careful usage:

```bash
mkfstab / --no -o /etc/test-fstab
```

You can then review the output, created in `/etc/test-fstab`.
 
## Building from source

The source code of `mkfstab` is located in the main Redrose Linux repository, [https://github.com/redroselinux/redroselinux](https://github.com/redroselinux/redroselinux), in the directory `src/mkfstab/`.

The dependencies for building are Rust, Cargo (for building the program only), POSIX sh, tar, Zstandard, and fakeroot (for building a package).

To build a package, just run:
```bash
sh make_package.sh
```
Or build a single binary:
```bash
cargo build --release
```
Debug build:
```bash
cargo build
```
