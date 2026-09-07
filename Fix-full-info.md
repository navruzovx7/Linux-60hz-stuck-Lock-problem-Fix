# Hyprland 144Hz Fix on CachyOS

> A practical guide for fixing monitor refresh-rate issues on **CachyOS + Hyprland**, especially when a display supports 144Hz but keeps falling back to 60Hz after login or reboot.

![Hyprland](https://img.shields.io/badge/Hyprland-Wayland-blue)
![CachyOS](https://img.shields.io/badge/CachyOS-Linux-green)
![Refresh Rate](https://img.shields.io/badge/Refresh%20Rate-144Hz-orange)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

---

## Overview

Some laptops and monitors expose multiple refresh-rate modes, for example:

```text
1920x1080@60Hz
1920x1080@144Hz
```

Hyprland may detect both modes correctly while still starting the display at **60Hz**.

This can be especially confusing when:

* `hyprctl monitors all` shows `144Hz` as an available mode.
* A GUI display utility lets you select `144Hz`.
* The display temporarily switches to `144Hz`.
* After restarting Hyprland or logging in again, it returns to `60Hz`.

This repository documents a reliable way to diagnose and fix that configuration problem.

---

## The Problem

Check the currently active monitor:

```bash
hyprctl monitors all
```

You may see:

```text
Monitor eDP-1

1920x1080@60.00100

availableModes:
1920x1080@60.00Hz
1920x1080@144.00Hz
```
![alt text](image-6.png)

This means the hardware and Hyprland are both aware of the 144Hz mode.

The problem is usually **not the panel**.

Instead, another Hyprland configuration rule may be applying:

```text
preferred
```

or explicitly selecting:

```text
1920x1080@60
```

after your desired 144Hz configuration.

---

# Why This Happens

Hyprland supports explicit monitor configurations such as:

```text
monitor = DP-1, 1920x1080@144, 0x0, 1
```

and, with the Lua configuration interface:

```lua
hl.monitor({
    output = "DP-1",
    mode = "1920x1080@144",
    position = "0x0",
    scale = 1,
})
```

The important part is: nano ~/.config/hypr/config/monitors.lua

```text
mode = "1920x1080@144"
```

Using:

```text
mode = "preferred"
```

![alt text](image-7.png)


does **not necessarily mean the highest refresh rate**.

If the preferred mode exposed by the display is 60Hz, Hyprland can start at 60Hz even though 144Hz is available.

---

# Diagnosis

## 1. Check available modes

Run:

```bash
hyprctl monitors all
```

Look for:

```text
availableModes:
1920x1080@60.00Hz
1920x1080@144.00Hz
```

If 144Hz appears here, the display is exposing the mode correctly.

---

## 2. Search your Hyprland configuration

Search for monitor-related settings:

```bash
grep -RniE 'monitor|eDP-1|preferred|60\.0|144\.0' ~/.config/hypr/
```

You may discover multiple monitor configuration files.

For example:

```text
~/.config/hypr/hyprland.conf
~/.config/hypr/monitors.conf
~/.config/hypr/monitors.lua
~/.config/hypr/config/monitors.lua
```

Having multiple configuration layers is important to investigate.

---

# CachyOS + Hyprland Lua Configuration

If your Hyprland setup uses Lua, the monitor configuration may look like this:

```lua
hl.monitor({
    output   = "eDP-1",
    mode     = "1920x1080@144.0",
    position = "0x0",
    scale    = 1.5,
    vrr      = 0,
})
```

For a 1920×1080 laptop panel supporting 144Hz, this explicitly requests:

* Resolution: `1920x1080`
* Refresh rate: `144Hz`
* Position: `0x0`
* Scale: `1.5`
* VRR: disabled

---

# The Common Configuration Trap

A configuration such as:

```lua
hl.monitor({
    output = MONITOR1,
    mode = "preferred",
    position = "auto",
    scale = "auto",
})
```

can override the intended refresh-rate configuration.

If the display's preferred mode is 60Hz, the result may be:

```text
1920x1080@60Hz
```

even though:

```text
1920x1080@144Hz
```

is available.

### Fix

Replace the generic configuration with an explicit mode:

```lua
hl.monitor({
    output   = "eDP-1",
    mode     = "1920x1080@144.0",
    position = "0x0",
    scale    = 1.5,
    vrr      = 0,
})
```

---

# Avoid Duplicate Monitor Rules

Do not configure the same monitor in multiple places unless you understand the loading order.

For example, avoid having all of these simultaneously:

```ini
monitor = eDP-1, 1920x1080@144, 0x0, 1.5
```

```ini
monitor = eDP-1, preferred, auto, 1.5
```

```ini
monitor=eDP-1,1920x1080@60.0,0x0,1.5,vrr,0
```

Instead, keep one authoritative configuration.

For Lua-based configurations:

```lua
hl.monitor({
    output   = "eDP-1",
    mode     = "1920x1080@144.0",
    position = "0x0",
    scale    = 1.5,
    vrr      = 0,
})
```

---

# Applying the Configuration

After editing the configuration:

```bash
hyprctl reload
```

Then verify:

```bash
hyprctl monitors all
```

You should see:

```text
1920x1080@144.00000
```

instead of:

```text
1920x1080@60.00100
```

---

# Troubleshooting Checklist

If 144Hz still does not stick, check the following.

### Check the monitor name

```bash
hyprctl monitors all
```

Example:

```text
Monitor eDP-1
```

Use the exact output name:

```text
eDP-1
```

---

### Check available refresh rates

```bash
hyprctl monitors all
```

Make sure 144Hz appears under:

```text
availableModes
```

---

### Search for conflicting configurations

```bash
grep -RniE 'monitor|preferred|60\.0|144\.0' ~/.config/hypr/
```

Look for:

```text
preferred
```

or:

```text
1920x1080@60
```

---

### Check the active configuration

```bash
hyprctl monitors
```

Compare:

```text
1920x1080@60Hz
```

with:

```text
1920x1080@144Hz
```

---

### Reload Hyprland

```bash
hyprctl reload
```

Then check again:

```bash
hyprctl monitors all
```

---

# GUI Tools

GUI tools such as `nwg-displays` can be useful for discovering and configuring monitor layouts.

However, if the setting is overwritten by another Hyprland configuration file, selecting 144Hz in a GUI may not be enough to make the setting persistent.

The important step is identifying **which configuration layer is ultimately configuring the monitor**.

---

# Example Configuration

For a laptop with:

```text
Output: eDP-1
Resolution: 1920x1080
Refresh rates: 60Hz / 144Hz
Scale: 1.5
```

a working Lua configuration is:

```lua
hl.monitor({
    output   = "eDP-1",
    mode     = "1920x1080@144.0",
    position = "0x0",
    scale    = 1.5,
    vrr      = 0,
})
```

Verify the result:

```bash
hyprctl monitors all
```

Expected:

```text
Monitor eDP-1

1920x1080@144.00000

availableModes:
1920x1080@60.00Hz
1920x1080@144.00Hz
```

---

# Important: `preferred` vs `144Hz`

One of the key lessons from this issue is:

```text
preferred
```

and:

```text
highest refresh rate
```

are not necessarily the same thing.

If you specifically want 144Hz, explicitly request:

```text
1920x1080@144
```

instead of relying on:

```text
preferred
```

---

# Useful Commands

### List monitors

```bash
hyprctl monitors
```

### List all monitor modes

```bash
hyprctl monitors all
```

### Reload Hyprland

```bash
hyprctl reload
```

### Find monitor configuration

```bash
grep -RniE 'monitor|eDP-1' ~/.config/hypr/
```

### Find refresh-rate conflicts

```bash
grep -RniE '60\.0|144\.0|preferred' ~/.config/hypr/
```

---

# Scope

This repository focuses on:

* CachyOS
* Hyprland
* Wayland
* Laptop internal displays (`eDP-*`)
* External displays (`DP-*`, `HDMI-*`)
* Refresh-rate configuration
* Hyprland Lua configurations
* Persistent monitor configuration problems

It is **not** limited to 144Hz. The same approach can be used for:

```text
60Hz
75Hz
120Hz
144Hz
165Hz
240Hz
360Hz
```

provided that the monitor exposes the requested mode.

---

# Contributing

If you encounter a similar problem on another laptop, GPU, monitor, or Hyprland configuration, contributions are welcome.

Useful information to include in an issue:

```bash
hyprctl monitors all
```

```bash
hyprctl version
```

```bash
grep -RniE 'monitor|preferred|60\.0|144\.0' ~/.config/hypr/
```

Please remove personal information before posting configuration files or logs.

---

# License

This project is licensed under the MIT License.

See [LICENSE](LICENSE) for details.

---

## Credits

* [Hyprland](https://hyprland.org/) — Wayland compositor
* [CachyOS](https://cachyos.org/) — Linux distribution
* [nwg-displays](https://github.com/nwg-piotr/nwg-displays) — GUI display configuration utility

---

## References

* [Hyprland Monitor Configuration](https://wiki.hypr.land/configuring/core/monitors/)
* [Hyprland Configuration Documentation](https://wiki.hypr.land/)
* [GitHub README Documentation](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-readmes)

> If your display supports 144Hz but Hyprland keeps returning to 60Hz, don't immediately blame the hardware. Check **which monitor rule is actually being applied**.
