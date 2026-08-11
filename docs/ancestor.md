# Ancestor

A build system for packages in Redrose Linux.

## Installation

...is simple.

```bash
sudo car install ancestor
```

Or, build manually:

```bash
git clone https://github.com/redroselinux/ancestor/ --depth 1 && cd ancestor
dub build --build=release
sudo cp ancestor /usr/bin
```

## Building packages

To build a package, you use the `ancestor build` command and optionally use the `--verbose` flag. You will need to clone the packages repo:

```bash
git clone https://github.com/redroselinux/ancestor-builds --depth 1 /var/ancestor
# you should clone into /var/ancestor or set your own ANCESTOR_PATH.
```

If you use a manifest file path with `ancestor build`, it uses that manifest. If you enter a package name, it searches in `ANCESTOR_PATH` (an enviroment variable) which has the same syntax has the UNIX `PATH` envvar. The default value is:

```
/var/ancestor:pkg/
```

## Writing a package manifest

First, create a directory in the packages repo:

```
pkg/
    my-package/
```

Now, in this directory, create a `my-package.manifest` file and optionally a `README` that gets shown to the user every time they build your package.
The basic syntax of the `.manifest` file is mostly `key :: type :: args`, as you can see in this example:

```
-- lua-like comments!!

author :: mostypc123 :: mostypc123@redroselinux.org
package :: ancestor :: 0.1
download :: git :: https://github.com/redroselinux/ancestor :: master
build :: dub_single_bin :: ancestor
install_cmd :: auto
```

As you can see, unlike the old Redrose build system Fuel, you do not have to use a semicolon (`;`) for separating lines!


### property `author`

This gets appended to your `/car` manifest file in a `name <email>` format!

```
author :: your_nick_should_be_one_word :: me@example.com
```

If you want to stay anonymous, simply use:

```
author :: anonymous :: drop-all@redroselinux.org
```

### property `package`

```
package :: my-package :: 0.1
```

The first value after the property name is the package name, and the second one is the package version.

### property `download`

This manages downloading of the package source.

```
download :: type :: <type-specific args>
```

#### type `gnu_ftp`

```
download :: gnu_ftp :: file_ext
```

Project name and version derived from set variables.

#### type `git`

```
-- fetches with --depth 1 and no checkout
download :: git :: url
-- checkouts the selected branch/tag
download :: git :: url :: checkout
-- checkout can be 'auto' to derive from set version
```

You can use URL shortcuts: `hub:` is replaced with `https://github.com/`, `lab:` is replaced with `https://gitlab.com/` and `cberg:` is replaced with `https://codeberg.org/`.

#### type `manual`

```
download :: manual: :: url
```

### optional property `manual_extract`

Extract command must contain '<placeholder>' to indicate the file to extract and `<dir>` to indicate the dir to extract to. These are replaced by the parser with actual paths.

Use this property when you need to manually extract something for whatever reason (like a manual download or unsupported file extension).

```
manual_extract :: command
```

### property `build`

#### type `manual`

The build commands are a dynamic array which you can append to, meaning that you can add prebuild commands by using `build :: manual` before the actual build.

Same for post-build commands.

```
build :: manual :: command_to_append
```

#### type `autotools_make`

TODO: add an `autotools_make_seperate_builddir` type for GCC/GNU binutils/whatever style build systems.

```
build :: autotools_make :: flags
```

#### type `dub_single_bin`

If you don't know: `dub` is the Dlang build system.

```
build :: dub_single_bin :: bin_name
```

### type `go_single_bin` and `cargo_single_bin`

Works basically the same as `dub_single_bin`.

### property `install_cmd`

The command that stages the files. Also a dynamic array of commands that you can append to!

#### type `auto`

Automatically derived from whatever build system is used. Cannot be used if your build system is `manual`.

```
install_cmd :: auto
```

#### type `manual`

Pretty obvious, innit?

```
install_cmd :: manual :: cmd
```

### property `car_post_inst`

Yet another dynamic array, I love eating your heap memory.

Translates to `exec` lines in Car metadata.

```
car_post_inst :: command1 :: command2
```

### property `car_dep`

Translates to `dep` lines in Car metadata.

```
car_dep :: dep1 dep2
```
