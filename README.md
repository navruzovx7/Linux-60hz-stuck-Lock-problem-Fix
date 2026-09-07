# CachyOS + Hyprland — Monitor Hz Quick Fix

## 1. Check your monitor name, resolution and refresh rate

Run:

```bash
hyprctl monitors all
```

Look for:

```text
Monitor eDP-1
1920x1080@60.00100
availableModes: 1920x1080@60.00Hz 1920x1080@144.00Hz
```

![alt text](images/image-3.png)

In this example:

* Monitor: `eDP-1`
* Resolution: `1920x1080`
* Target refresh rate: `144 Hz`

> ⚠️ **Your values may be different.** Use the monitor name, resolution and refresh rate shown on **your own system**.

---

## 2. Open the monitor config

```bash
nano ~/.config/hypr/config/monitors.lua
```

Find the existing `hl.monitor({ ... })` section.

For example:

```lua
hl.monitor({
    output = MONITOR1,
    mode = "preferred",
    position = "auto",
    scale = "auto",
})
```
![alt text](images/image-1.png)

Delete that section and replace it with the correct one below.

---

## 3. Pick the example that matches your monitor

### Example 1 — 1920×1080 @ 144 Hz

```lua
hl.monitor({
    output   = "eDP-1",
    mode     = "1920x1080@144.0",
    position = "0x0",
    scale    = 1.5,
    vrr      = 0,
})
```

### Example 2 — 2560×1440 @ 165 Hz

```lua
hl.monitor({
    output   = "DP-1",
    mode     = "2560x1440@165.0",
    position = "0x0",
    scale    = 1.0,
    vrr      = 0,
})
```

### Example 3 — 1920×1200 @ 120 Hz

```lua
hl.monitor({
    output   = "eDP-1",
    mode     = "1920x1200@120.0",
    position = "0x0",
    scale    = 1.0,
    vrr      = 0,
})
```

![alt text](images/image-14.png)

> ⚠️ **These are only examples.** If your monitor is different, replace:
>
> `eDP-1` → your monitor name
> `1920x1080` → your resolution
> `144.0` → your desired Hz
> `1.5` → your preferred scale
>
> The exact values can be found with:
>
> ```bash
> hyprctl monitors all
> ```

---

## 4. Remove conflicting monitor settings

Check for other monitor configurations:

```bash
grep -RniE 'monitor|preferred|60\.0' ~/.config/hypr/
```

If you find an old configuration such as:

```ini
monitor=eDP-1,1920x1080@60.0,0x0,1.5
```

remove it or change it to your desired refresh rate.

Also check:

```bash
~/.config/hypr/monitors.conf
```

Make sure another configuration is not forcing your monitor back to `60 Hz`.

---

## 5. Reload Hyprland

```bash
hyprctl reload
```

Then check:

```bash
hyprctl monitors all
```

You should now see your desired refresh rate, for example:

```text
1920x1080@144.00Hz
```

Done. ✅

---

# Fastest Version

```text
1. hyprctl monitors all
        ↓
2. Find monitor name + resolution + desired Hz
        ↓
3. nano ~/.config/hypr/config/monitors.lua
        ↓
4. Replace hl.monitor({ ... }) with the correct configuration
        ↓
5. Remove conflicting 60 Hz / preferred monitor settings
        ↓
6. hyprctl reload
        ↓
7. hyprctl monitors all
        ↓
8. Desired Hz shown → DONE ✅
```

> ⚠️ **Important:** Do not blindly copy the examples if your monitor has different specifications. `eDP-1`, `1920x1080`, `144 Hz`, and `1.5` are only examples based on one configuration. Always check `hyprctl monitors all` first.
