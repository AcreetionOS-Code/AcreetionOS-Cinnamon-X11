# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

This is the **X11 (X.Org Server) edition** of AcreetionOS Cinnamon — one of three sibling display-server variants in the wider `AcreetionOS-Cinnamon` workspace (the others are `XLibre/` and `Wayland/`). See [`../CLAUDE.md`](../CLAUDE.md) for the workspace-level overview, including the rationale for the self-managed AcreetionOS repositories and how the three variants relate.

AcreetionOS is an **Arch Linux–based** distribution that pulls from its own curated repositories at `iso.acreetionos.org:8448` instead of upstream Arch — this is the deliberate stability mechanism. Do **not** re-add upstream Arch repos: per `Changes.md` it breaks the AcreetionOS keyring.

This repo is an `archiso` profile. Its build output is a bootable live ISO labeled `AcreetionOS` (year-month).

## What makes this variant different from XLibre / Wayland

- Uses standard upstream **X.Org Server** packages: `xorg-server`, `xorg-server-common`, `xf86-video-amdgpu` / `intel` / `nouveau`, `xf86-input-libinput`.
- Includes additional project assets not in the XLibre variant: web pages (`index.html`, `contact.html`, `developers.html`, `ermin.html`, etc.), `chatbot-ui/`, `peertube/`, `installer/`, `Issues/`, and the `.gitlab-ci.yml` build pipeline.
- `packages.x86_64` explicitly includes `pacman`; the XLibre list does not.
- `pacman.conf` enables `[acreetionOSREPO]` (→ `repo/$arch`) as the active AcreetionOS repo; the XLibre variant uses `[acreetionOSREPO-main]` instead.

## Build Commands

- **Full build:** `./build.sh` — runs `./refresh.sh -j && ./mkarchiso.sh`, then `sudo rm -rf ./work`
- **mkarchiso only:** `./mkarchiso.sh` — invokes `mkarchiso` with the `AcreetionOS_XL` label override and writes the ISO to `../ISO/` (note: the script's `-L` flag currently says `AcreetionOS_XL` — check before relying on it for X11 labeling)
- **Clean build artifacts:** `./refresh.sh` — `rm -rf work/ out/`
- **Recover after a failed build:** `./umount.sh` — lazy-unmounts virtual filesystems under `work/x86_64/airootfs/` before removing `work/`
- **Stamp build info:** `./generate-build-info.sh` — writes commit/date/user into `airootfs/etc/acreetion-build`
- **Apply Cinnamon overlay patches:** `./patch-cinnamon.sh` — copies `airootfs/cinnamon-configs/cinnamon-stuff/{usr,bin}/*` over `airootfs/usr/`
- **Build the colorized mkarchiso C wrapper** (optional): `make` (binary `mkarchiso_wrapper`); `sudo make install` to `/usr/local/bin/`

Builds require sudo (archiso needs loop-device access for squashfs). `work/` and `out/` are gitignored.

## CI Pipeline

`.gitlab-ci.yml` defines a 3-stage pipeline (`prepare` → `build` → `publish`) tagged for a self-hosted shell runner (`acreetionos`) on the Dell Precision 7910 build server. Triggers: pushes to branch `acreetionos`, tags, web-triggered, or scheduled. The publish stage copies the ISO to `/srv/http/iso`, updates the `AcreetionOS-latest.iso` symlink, and prunes to the 3 most recent builds. Be aware that any push to `acreetionos` triggers a full ISO build.

## Architecture (this profile)

- **`profiledef.sh`** — ISO metadata, boot modes (BIOS syslinux + UEFI ia32/x64 GRUB, ESP and El Torito), squashfs+xz+BCJ compression, and the explicit `file_permissions` map. **Any new executable script added to `airootfs/usr/bin/` or `airootfs/usr/local/bin/` must be listed here with `0:0:755`**, or archiso will not set it executable in the ISO.
- **`packages.x86_64`** — Package list installed into the live system.
- **`pacman.conf`** — Build-time repo configuration. Active: `[acreetionOSREPO]`, `[personal]`. Both at `iso.acreetionos.org:8448`. Sets `IgnorePkg = v4l2loopback-dkms`, `OverwriteFiles = *`, `ParallelDownloads = 25`.
- **`bootstrap_packages.x86_64`** — Minimal package set for the bootstrap tarball.
- **`airootfs/`** — Overlay copied verbatim onto the live root. Custom install/post-install scripts live in `airootfs/usr/bin/` and `airootfs/usr/local/bin/` (`calamares.sh`, `postinstall.sh`, `preinstall`, `stormos-final`, `setup-displays.sh`, `wifi-connection`, `fixkeys.sh`, `dd.sh`). Cinnamon customizations stage in `airootfs/cinnamon-configs/` and are merged into `airootfs/usr/` via `patch-cinnamon.sh`.
- **`grub/`**, **`syslinux/`**, **`efiboot/`** — Bootloader configurations.

## Installer

The live ISO ships **Calamares** (`calamares-git` + `calamares-config` from the custom repo) as the graphical installer, launched via `airootfs/usr/bin/calamares.sh`.

## Notes when editing

- This is one of three parallel variants. A change here is **not** automatically reflected in `../XLibre/` or `../Wayland/`; mirror manually if intended for those.
- `mkarchiso` (the binary at the root) is a vendored copy of the archiso script; the C source `mkarchiso.c` + `Makefile` produce a separate *colorized wrapper*, not a replacement for the script.
- The non-archiso subprojects in this directory (`chatbot-ui/`, `peertube/`, `installer/`, web pages, `Issues/`) are not consumed by `mkarchiso` — only what `profiledef.sh` and `airootfs/` reference ends up in the ISO.
