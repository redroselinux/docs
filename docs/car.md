# Car

Car is a package manager made for Redrose Linux. It is created to be very minimalistic and is written in Nim.

## Most used commands
### `install`
Install packages.

```bash
car install example
```
Aliases: `i`, `get`

### `delete`
Delete packages based on the `/etc/car/saves` files.

```bash
car delete example
```

### `listup`
Update the packagelist. Do not confuse with `update`.
Equivalent of `apt update` / `pacman -Sy`.

```bash
car listup
```

### `update`
Update the packagelist, then upgrade all packages. In newer versions of Car, an update can delete or add packages based on what the maintainer says.
Equivalent of `apt update && apt upgrade` / `pacman -Syu`.

```bash
car update
```

In newer versions of Car, an update creates the `/etc/car/update` file, following our curated rolling-release cycle, updated on Friday. To force re-updating, delete this file.

### other
`why` - why is a package installed? (NEW, may not be available)<br>
`cleanbuild` - rebuild a package, replaced with `fuel` (deprecated)<br>
`search` - search for a package<br>
`init` - look at the [inicialization](/car.md#Inicialization) section<br>

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
├── car
├── etc
│   ├── dbus-1
│   │   ├── session.conf
│   │   └── system.conf
│   └── sv                            <- make sure your packages services go to sv, not service
│       └── dbus
│           ├── log
│           │   └── run
│           └── run
├── run
│   └── dbus
├── usr
│   ├── bin
│   │   ├── ...
│   ├── include
│   │   └── dbus-1.0
│   │       └── dbus
│   │           ├── ...
│   ├── lib64
│   │   ...
│   ├── libexec
│   │   └── dbus-daemon-launch-helper
│   └── share
│       ├── ...
└── var
    └── lib
        └── dbus
```

Installing a package saves all the files inside of the tarball into a file in `/etc/car/saves`.

## Inicialization

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
