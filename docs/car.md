# Car

Car is a package manager made for Redrose Linux. It is created to be very minimalistic and is written in Nim.

## Most used commands
### `install`
Install packages. Supports Car `.tar.zst`, Pacman `.pkg.tar.zst`, AppImage, and DPKG `.deb` packages.

```bash
car install example
```
Aliases: `i`, `get`

### `delete`
Delete packages based on the `/etc/car/saves` files.

```bash
car delete example
```

### `list`
(3.16+) List all installed packages with their versions.

```bash
car list
```

### `tires`
(3.16+) List files belonging to an installed package.

```bash
car tires example
```

### `addrepo`
(3.16+) Add a repository/mirror to the mirror list. Mirrors are colon-separated in `/etc/car/mirror`.

```bash
car addrepo https://example.com/repo
```

### `listup`
Update the packagelist. Do not confuse with `update`.
Equivalent of `apt update` / `pacman -Sy`. Supports multiple mirrors separated by `:` in `/etc/car/mirror`.

```bash
car listup
```

To enable the propriertary repo, create `/etc/car_propiertary.lock`.

### `update`
Update the packagelist, then upgrade all packages. In newer versions of Car, an update can delete or add packages based on what the maintainer says.
Equivalent of `apt update && apt upgrade` / `pacman -Syu`.

```bash
car update
```

In newer versions of Car, an update creates the `/etc/car/update` file, following our curated rolling-release cycle, updated on Friday. To force re-updating, delete this file.

#### Adding updates (for maintainers)
To create an update (done every Friday), you add a line starting with `UPDATE:`.
A blank update will just contain `:::` after that. What is between the first two `:` is the description. It must be original since it is written to the `/etc/car/update` file.
The second field is for packages to be added (divided by `,`) and the third one is for packages to be deleted.
The fourth one is an additional description. Example:

```
UPDATE:Standard Friday update, 29.05.2026:::No breaking changes.
```

### `why`
Why is a package installed?

```bash
car why example
```

### `search`
Search for a package.

```bash
car search h
```

### `brake` / `release`
Stop a package from being updated / let it be updated again.

```bash
car brake example
car release example
```

### `init`
Look at the [initialization](/car.md#Initialization) section.

### `clearcache`
Clear all cache.

```bash
car clearcache
```

## Package format
Car uses a format similar to `.pkg.tar.zst` from `pacman`. A package has to be a zstd-compressed tarball, with a file named `car` inside of it. This is the metadata file.

Fields:
* `version`
  The version of the package.
  ```
version 0.1
  ```
* `dep`
  A dependency of the package. Each dependency gets collected and in the end it does matter that all the packages get installed. Man, cars try so hard they burn themselves down, oh, the wastelands of today. We need to unshatter the car or let it fade. I love linkin park
  ```
dep libffi
  ```
* `exec`
  Execute a command that may unshatter the car.
  ```
exec /bin/postinst
exec rm /bin/postinst
  ```
  If a command fails to execute, the user will get warned that the installation failed partially.

All the other files get copied into the root folder. This is an example file tree, from the `dbus` package:
```
├── car                  <- the metadata file
├── etc
│   ├── ...
│   └── sv               <- make sure your packages services go to sv, not service
│       └── dbus
│           ├── log
│           │   └── run
│           └── run
└── ...
```

Installing a package saves all the files inside of the tarball into a file in `/etc/car/saves`.

## Initialization

The `car init` command creates all important files that Car needs to function. It is run during installation of the system. To force init even when it was already done, use it with the `--force` flag.

## Building from source

Car is a Nimble project, so you will need Nim and Nimble installed (they require a C compiler). Clone the repo:

```bash
git clone https://github.com/redroselinux/car
cd car
```

Then run the build command:

```bash
nimble build
```

# car-repo-init

## Building from source

Quick install; no package for now
```bash
git clone https://github.com/redroselinux/car-repo-init --depth 1
cd car-repo-init
dub build
sudo cp ./car-repo-init /usr/bin
cd ..
rm -rf car-repo-init
```

## Quick overview
`car-repo-init` is a tool for generating package repository listings from Car packages.

It scans a directory for `.tar.zst` files, extracts each one, reads the `car` metadata, and makes `packagelist` and `packagelist_long` files.

## Usage

```bash
car-repo-init gen -p https://example.com/repo/
```

Flags:
- `-p` (required) - URL prefix for the packages
- `-o` (optional, default: `packagelist`) - output file name
  The long packagelist name is derived from this; if `-o` is `smth`, the long packagelist goes to `smth_long`
- `-d` (optional, default: `.`) - directory to scan for `.tar.zst` files

## Output

- `packagelist` - package name, version
- `packagelist_long` - also includes dependency information

## Generating a website

The `gen-site.sh` script converts `packagelist_long` into an HTML page
with repository info (currently unused in Redrose sites):

```bash
./gen-site.sh packagelist_long index.html
```
