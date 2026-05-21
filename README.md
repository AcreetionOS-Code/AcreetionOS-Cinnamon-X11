# AcreetionOS Cinnamon — X11 Edition

Welcome to the **X11 (X.Org Server) edition** of AcreetionOS Linux with the Cinnamon desktop environment. This is the flagship, recommended-for-most-users build of the AcreetionOS Cinnamon family.

Download ISOs: <https://acreetionos.org>

## About AcreetionOS

AcreetionOS Linux is a community-driven, lightweight, and versatile distribution. It is currently **based on Arch Linux**, with the long-term goal of becoming a parallel\* distribution with its own base. The focus is simplicity, ease of use, stability, and up-to-date packages.

Unlike vanilla Arch, AcreetionOS does not consume the upstream Arch repositories at runtime. Instead it ships **self-managed repositories** hosted at `iso.acreetionos.org:8448` (`[acreetionOSREPO]`, `[personal]`). Curating the package set ourselves is how we offer end users a more stable, predictable experience on top of an Arch-style rolling base.

## About this edition (X11)

This edition uses the **X.Org Server** — the long-standing X Window System (X11) that has been the predominant display system for Linux and Unix since 1984. It is maintained by the X.Org Foundation under the MIT License.

Why pick this edition:

- **Maximum compatibility.** X11 has decades of application, toolkit, and driver support. If something is going to work on Linux, it works on X11.
- **Mature drivers.** Uses the standard `xf86-video-*` and `xf86-input-libinput` driver stack.
- **Network transparency, accessibility tools, screen sharing, and legacy app support** are all well-trodden paths on X11.

Pick a different edition if:

- You want active upstream X server development with newer features like client namespacing — see the **[XLibre edition](../XLibre/)**.
- You want the modern, secure, per-frame-perfect display protocol — the **[Wayland edition](../Wayland/)** is reserved for that (not yet released).

## Features

- Lightweight Arch-based system with the Cinnamon desktop
- Curated package set from AcreetionOS's own repositories
- Calamares graphical installer
- Both BIOS (SYSLINUX) and UEFI ia32/x64 (GRUB) boot support
- x86_64

## Getting Started (End Users)

1. Download the latest ISO from <https://acreetionos.org>.
2. Write it to a USB drive with [Etcher](https://etcher.balena.io/#download-etcher), [Rufus](https://rufus.ie/en/), or [Ventoy](https://ventoy.net/en/index.html).
3. Boot from USB and follow the on-screen installation steps.

> **Ventoy users:** you must use **GRUB Mode 2**. Plain GRUB will not boot the ISO correctly.

## Building the ISO yourself

This repository **is** an `archiso` profile. Build it on an Arch (or AcreetionOS) host with `archiso` installed:

```bash
./build.sh           # refreshes work/, runs mkarchiso, cleans up
# or, step by step:
./refresh.sh         # rm -rf work/ out/
./mkarchiso.sh       # runs mkarchiso with the AcreetionOS label
./umount.sh          # if a previous build left bind mounts in work/
```

The resulting ISO is written to `../ISO/`. Builds require sudo (loop devices for squashfs). Pushes to the `acreetionos` branch are also built and published automatically via `.gitlab-ci.yml` on the project's self-hosted runner.

For deeper architectural notes (profile layout, `airootfs/`, file-permission gotchas, etc.) see [`CLAUDE.md`](./CLAUDE.md) in this directory, or [`../CLAUDE.md`](../CLAUDE.md) for the workspace-level overview.

## Contributing

Contributions are welcome. See the [Contributor Guide](https://github.com/AcreetionOS/AcreetionOSDocumentationPlan/blob/main/documentation/contributor-guide.md). For bug reports and feature requests, [open an issue](https://github.com/cobra3282000/acreetionos/issues).

## Roadmap

The current goal is a stable release by the end of 2026. Track progress on the [project roadmap](https://github.com/cobra3282000/acreetionos/projects).

## Project Information

- **Status:** In active development
- **License:** GPL-3.0
- **Maintainers:** Darren Clift (@cobra3282000), Natalie Spiva (@spivanatalie64)

---

\* *Parallel distribution: a distribution similar to, but with a different goal set than, its upstream.*
