# Roadmap

This page contains our plans of what to do in Redrose.

## Current

- Potentially rewrite Car in C as a lib called rac??
- Rewrite the ISO builder in C or C++ or Odin or or or idk
  - Remove the hard busybox dep 
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
