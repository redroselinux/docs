# Roadmap

This page contains our plans of what to do in Redrose.

- Fix `iwd` [work in progress, almost done]
- Add tools for Car like a better builder than fuel [[work in progress]](https://github.com/redroselinux/cartools)
  - Make the builder an alternative PM for Redrose making us a hybrid distro
- Rewrite the installer in Go with [Bubble Tea](https://github.com/charmbracelet/bubbletea) or [Huh](https://github.com/charmbracelet/huh)
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
