# My MiTV-AXSO0 — what was done

TV: Xiaomi MiTV-AXSO0 (dangal), Android TV 9, firmware PI.6036 at 192.168.1.7.

## Applied

- `tools/disable.ps1 -TvHost 192.168.1.7 mytv/debloat.txt` — ~30 Xiaomi/Google
  telemetry, ad and preload packages DISABLED (not uninstalled).
- `launcher/build-and-install.ps1 -TvHost 192.168.1.7` — built and installed the
  launcher (`com.gathin.tvhome`) as Home; stock `com.google.android.tvlauncher` disabled.
- `pm uninstall com.spocky.projengmenu` — Projectivy launcher uninstalled.
- Kept: Chromecast built-in, Netflix/Prime/YouTube/Hotstar, all your installed apps.
- Disabled on request: voice assistant (mic button dead), Mi Cast / SmartShare /
  Miracast, firmware update service.
- Recovery record (auto-saved by disable.ps1):
  `C:\Users\ACER\.cleanyourtv\recovery-192.168.1.7-1ccded45.json` — keep it, it is
  the only copy of the exact pre-debloat state.

## Undo everything

Reconnect ADB (Developer options → USB debugging on, `adb connect 192.168.1.7:5555`), then:

```
tools\restore.ps1 -TvHost 192.168.1.7 mytv\debloat.txt
```

That re-enables every package and hands Home back to the stock launcher. To remove
the new launcher too: `adb -s 192.168.1.7:5555 shell pm uninstall com.gathin.tvhome`.

## Re-enable one app

```
adb -s 192.168.1.7:5555 shell pm enable <package>
# then comment the line out of mytv/debloat.txt so a re-apply keeps it on
```

## Change the dock or wallpaper

Dock apps live in `launcher/app/src/main/res/values/config.xml` (`dock_apps`),
then re-run `launcher/build-and-install.ps1 -TvHost 192.168.1.7`. Reordering tiles
also works on the TV: launcher Settings → reorder. Themes (`themes/`) can be pushed
without a rebuild via `tools/theme.ps1`.

## Firmware update, when wanted

```
adb -s 192.168.1.7:5555 shell pm enable com.xiaomi.mitv.updateservice
# check for the update in Settings, then disable it again after
```

## Notes

- `com.google.android.leanbacklauncher` re-enables itself at every boot on this
  firmware — expected, it is not Home and does nothing.
- Live TV app is `com.android.tv` (hidden from the grid, reachable via Inputs → Live TV).
