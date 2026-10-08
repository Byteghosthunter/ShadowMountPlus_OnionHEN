# ShadowMountPlus 1.7 beta 3 – OnionHEN / M.2 detection patch

This source tree contains a small detection-focused patch based on the supplied
ShadowMountPlus 1.7 beta 3 source.

## What changed

1. **OnionHEN game roots are built-in defaults**
   - `/data/OnionHEN/games`
   - `/mnt/ext0/OnionHEN/games`
   - `/mnt/ext1/OnionHEN/games`
   - `/mnt/usb0..7/OnionHEN/games`

2. **Nested `-app0` folder layout is auto-detected**

   A dump such as:

   `/data/OnionHEN/games/PPSA34015/PPSA34015-app0/sce_sys/param.json`

   is probed automatically even when the global `scan_depth` is only `1`.
   The normal `scan_depth=2` behaviour still works as before.

3. **Valid folder dumps become visible as soon as they are discovered**

   A valid `param.json` source is placed in the library cache before
   mount/registration is attempted. The entry is marked as passive discovery,
   so it does **not** claim lifecycle ownership and is not used to auto-remove
   an installed title if the folder later disappears.

4. **Normally installed games are shown in the Web UI too**

   Titles present in `app.db` but not managed by ShadowMount are added as
   `source_type = installed_pkg` when their install directory exists in one of:

   - `/user/app/<TITLE_ID>`
   - `/mnt/ext0/user/app/<TITLE_ID>`
   - `/mnt/ext0/ps5/user/app/<TITLE_ID>`
   - `/mnt/ext1/user/app/<TITLE_ID>`

   These are shown read-only in the ShadowMount library. The Web UI does not
   offer Copy/Move/Delete Source for these system-managed installs. Uninstall
   remains available.

## Suggested config

If you use custom `scanpath=` entries, remember that they replace the built-in
list. A good setup for OnionHEN + internal + M.2 dumps is:

```ini
scanpath=/data/OnionHEN/games
scanpath=/mnt/ext1/OnionHEN/games
scan_depth=2

api_enabled=1
api_bind_address=0.0.0.0
api_port=10101
```

You do **not** need to add `/mnt/ext1/user/app` as a scan path. Normal PS5/M.2
installs there are read from `app.db` and exposed separately as `installed_pkg`.

## Build

The original repository's `.github/workflows/ps5.yml` is retained. Push this
source tree to a GitHub repository and run **PS5 Build and Release** from the
Actions tab. The build artifact contains `shadowmountplus.elf`.

## Notes

- This is an experimental patch and has not been executed on a PS5 here.
- The supplied environment does not contain the PS5 Payload SDK, so a local
  PS5-target compile could not be completed here. GitHub Actions is the intended
  compile/test route.
- Keep a copy of your original `shadowmountplus.elf` and configuration before
  testing the patched build.
