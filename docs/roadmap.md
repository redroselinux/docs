# Roadmap

This page contains our plans of what to do in Redrose.

## Current

- Fix `iwd` [done, must finish some extra stuff then release alpha0.6]
- Fix the `news-reader` sidebar on our website showing old articles
- Rewrite the installer
  - Remove the need for Busybox everywhere 
  - Fix all little quirks of the current installer
- Rewrite the ISO builder in C++
  - Handle package dependencies cleanly
  - Handle chroot execution cleanly
  - Optimize as much as possible
    - Asynchronous package install (saving multiple seconds)
    - Use `pgzip` (saving multiple seconds)
- Fix stuff like fonts in console and all little design and UX quirks
- Rewrite `redrose-runit` in C++ with a better codebase and better deps, etc 
- Try to support as much hardware as possible
- Add a tool to mirror our repos on your own servers

## Plans for the future

- Use `ly` as the DM in Redrose
- Make a fetcher for https://github.com/bitwire-it/ipblocklist that automatically blocks the IPs

## Some ideas that are kinda crazy

- A full server with an API with Ktor for news-reader
- Reimplementation of `libsystemd` with runit alternatives.
- "The" code editor for developing Redrose (ehh??)
