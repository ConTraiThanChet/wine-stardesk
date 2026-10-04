# wine-stardesk

Unofficial fork of [Bottles' Soda](https://github.com/bottlesdevs/wine) runner (`soda-11.0-10`) with fixes needed to run StarDesk under Wine/Bottles on Linux.

This repo contains no Wine source. Like upstream Soda, it is a CI recipe: GitHub Actions clones Valve's Wine (experimental bleeding edge, commit `0509a0b`), applies [wine-tkg](https://github.com/Frogging-Family/wine-tkg-git) and the patches in [wine-tkg-userpatches/](wine-tkg-userpatches/), then builds and packages the runner.

## Changes from Soda

- **`newdev.DiUninstallDevice`** — implemented ([newdev-implement-diuninstalldevice.mypatch](wine-tkg-userpatches/newdev-implement-diuninstalldevice.mypatch)). StarDesk's backend service calls this on startup; the missing export made it fail.
- Release renamed to `soda-stardesk-<version>` so it can be installed alongside the official Soda runner.
- PGO is disabled for push builds, because the PGO baseline artifact lives in the upstream repo. A PGO candidate can still be built via `workflow_dispatch` once a baseline run exists here.

All other Soda patches are kept unchanged.

## Install in Bottles

1. Download `soda-stardesk-11.0-10-x86_64.tar.xz` (or `aarch64`) from [Releases](https://github.com/ConTraiThanChet/wine-stardesk/releases).
2. Extract it into the Bottles runners directory:
   - Flatpak: `~/.var/app/com.usebottles.bottles/data/bottles/runners/`
   - Native: `~/.local/share/bottles/runners/`
3. Restart Bottles, then select the runner in the bottle's settings (**Settings → Runner**).

## Notes for StarDesk

- Use DXVK and StarDesk's software decoding. Hardware decoding does not work: StarDesk needs D3D11 shared textures (`GetSharedHandle`), which wined3d does not implement.
- Start the StarDesk service and client from the same bottle (same wineserver).

## Building

Builds only run in GitHub Actions ([build-soda.yml](.github/workflows/build-soda.yml)):

- Push to the `soda` branch: builds x86_64 and aarch64 and publishes the `soda-stardesk-11.0-10` release.
- `workflow_dispatch`: builds a test candidate (optional PGO), uploaded as an artifact only.

## Credits

[Bottles](https://github.com/bottlesdevs) (Soda), [Valve](https://github.com/ValveSoftware/wine), [Frogging-Family wine-tkg](https://github.com/Frogging-Family/wine-tkg-git), and the [Wine project](https://www.winehq.org/). Wine is licensed under the LGPL 2.1+.
