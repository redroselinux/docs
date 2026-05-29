# Contributing Guide

This guide will explain our code style and rules.

## Basic info

If you are a new contributor, check some Issues with the `good first issue` tag. These issues are great for new contributors. Our codebase is pretty small anyway so reading it should be simple.

To build it, download the source:

```
git clone https://github.com/redroselinux/redroselinux
cd redroselinux
```

Next up, familiarize yourself with the instructions shown when running

```
make help
```

and when you're ready to build it, run

```
make
```

or 

```
make no-vm
```

Compiling requires a few tools, and the Makefile tells you what you are missing before compiling.
One important thing though is that you need to have our package manager Car installed (even though the bootstrapper tries to avoid it if you are missing it), and you have to initialize it. Run 'car' to show the help message.
Also, the bootstrapper can fetch the packagelist but cannot update it. It should be fine for a single compile, though.

You can build it on Linux only. Did you try to build it on Windows?

## AI

BTW, we use Coderabbit for code reviews.

If you use AI for assistance, that is fine, but include a comment like this one in `src/installer/main.c`:
```c
// was ai used in this file? yes (minor assistance)
```

**This comment needs to be on top of the file after or before imports.**
If you forget to add it, we will ask you to add it, nothing crazy.

If you did use AI for more than just a bit of assistance, put in this:
```c
// was ai used in this file? yes (functions: this, that, that, then, h)
```

If you did not AT ALL, include:
```c
// was ai used in this file? no
```
I, the maintainer of this project KNOW how AI code looks. If you very clearly LIE about this, your PR gets closed with no second chances no matter how cool the feature is.

### Boundaries between minor and major assistance

We define minor assistance as using AI for small, non-critical tasks, such as formatting, code suggestions, or helping with repetitive boilerplate. You can usually distinguish it from larger AI contributions by comparing prompts:

- This is a good example of using AI, even I, the maintainer of this project do this A LOT.
  > Please finish this migration for the rest of the code: [...]

- If you do this then we might not even accept the PR.
  > add feat [...], pls maek it woekrk

If AI was used for more than minor assistance, you must list the functions you used it in, in the AI comment.

## Code Style

- For all languages, use 4 spaces per 1 tab.
  Counts for this repo only, in other repos where Nim is used, use 2 spaces.

### C

You must use the K&R style in C. 
The code style of C may seem messy here because it is an older codebase.

When you create a function:

```c
void my_function() {}
```

...NEVER EVER make a newline after the ).

If your function takes a lot of arguments, put each argument in its own line and indent one more time for the code, like in this func from `src/installer/postinstgen.c`:

```c
int gen_postinst_scripts(
    char* drive,
    char* username,
    char* userpassword,
    char* rootpassword,
    char* hostname) {
        // the code
}
```
Note: this function does not exist in the code anymore.

### D

You must use the so-called Egyptian brackets. This means you put the bracket one line after function definition, of for loop or whatever block.

## Submitting PRs

When you are submitting a PR, please, please explain what you did. I do not want a PR like:

```py
H #56
______________________________
No description provided.

-2157 +0
```

A good example:

```py
Added a Finished section to the installer #57
______________________________
Right now, it just says Press ENTER, but we should have a finished section, so I added it.
Included here:
- src/installer/tui.c/finished_header()
- src/installer/postinstgen.c/gen_postinst_scripts()
```

If a maintainer makes a PR that does not have this kind of description, it is fine, because we know what we are pushing.
