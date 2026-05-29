# `news-reader`

`news-reader` is a tool to read the latest Redrose Linux news, inspired by Gentoo. It is written in C (originally in Nim).

## Usage

To read the latest article, simply run `news-reader`. If you want to read a specific article, use the `read` command, however in shells you will likely have to use backslashes due to the syntax:

```bash
#                yyyy (d.m)
news-reader read 2026\(9.5\)
```

## Usage (maintainers)

To create a new article, clone the github repo https://github.com/redroselinux/news-reader and go to the `news/` directory. Create a new file in the `yyyy(d.m)` format and set the URL of that file (raw) in `news/latest`.

The syntax of an article:

```
TITLE: My article
ACTION-NEEDED: false

;
My article content!
;
```

## Building from source

As in many Redrose Linux tools, you use the `make_car_package.sh` script. Building requires GCC or another C compiler and make installed.
