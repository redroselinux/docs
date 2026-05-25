# fuel

`fuel` is a source-based package manager compiling packages for Car. It does not have a package because of its file layout; you will need to clone it and compile the binary.

## Building from source

First, you will need to download the repo:

```bash
git clone https://github.com/redroselinux/fuel --depth 1
cd fuel
```

`fuel` is a `dub` project, meaning you need a D compiler installed. We prefer using `ldc` as it produces the smallest binaries:

```bash
dub build --build=release --compiler=ldc
```

It is also recommended to strip the binary:

```bash
strip --strip-unneeded ./fuel
```

## Basic usage

Run `./fuel -h` to show a help message.

### `build`

Build a package in the `fuelpkgs/` directory. Example:

```bash
./fuel build libcp
```

## Creating packages
### `fuelpkgs/` structure

`fuelpkgs/` is a directory containing sub-directories named as the packages, with a file called `metadata` in each of them. When a package is compiled, it is created at `fuelpkgs/package/package.tar.zst`, and the working directory is created at `fuelpkgs/package/package-compiling`.

### Formatting (`{{{var}}}`)
You can include a variable inside of another one by using `{{{}}}`, for example:
```
PackageSource = "https://ftp.gnu.org/gnu/{{{PackageName}}}/{{{PackageName}}}-{{{PackageVersion}}}.tar.gz"
```

### `PackageName`
A required field containing the name of the package.

### `PackageVersion`
A required field containing the current version of the package.

### `PackageSource`
A required field containing the URL to a tarball of the source code. We recommend using `{{{}}}` formatting, so you can easily change versions.

### `PackageSourceSig`
An optional field containing the URL to a GnuPG signature of the source code. We recommend using `{{{}}}` formatting, so you can easily change versions.

### `CompilingWeight`
Set how big is the package for compiling. This will be used when we make `fuel` just rebuild all packages someone marks as outdated.

- `tiny` - 6 tiny packages will compile at once
- `small` - 3
- `small-to-medium` - 2 
- `medium` - 2
- `medium-to-large` - 1
- `huge` - 1

### `PackageDeps`
Package dependencies, divided by whitespaces. This gets written into the metadata of the package (`/car`).

### `BuildSystem`

#### `autoconf`

The basic syntax is:

```
autoconf CONFIGUREOPTS <<RedroseStandardConfigureOpts>> >> MAKEOPTS -j451 >>
```

<small>find the numetal ref (hint: -j utf-8)</small>

`RedroseStandardConfigureOpts` currently stands for `--prefix=/usr --disable-systemd --disable-selinux --disable-apparmor`.

#### `custom`
Simply run shell commands:

```
custom make
```

### `BuildSystem.InstallCommand`
The command to install the package into a staging directory. `--DESTINATION--` expands to the staging dir.
