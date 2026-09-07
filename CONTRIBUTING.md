# Contributing

Thank you for your interest in contributing to **Hyprland 144Hz Fix**.

This project aims to document and improve solutions for monitor refresh-rate and display configuration issues on CachyOS, Hyprland, and other Wayland-based environments.

Contributions are welcome, including:

* Bug reports
* Configuration examples
* Hardware-specific findings
* Troubleshooting steps
* Documentation improvements
* Scripts and diagnostic tools
* Compatibility information

## Before Opening an Issue

Please check the existing issues and documentation first.

When reporting a problem, provide as much relevant technical information as possible.

Useful commands include:

```bash
hyprctl monitors all
```

```bash
hyprctl version
```

```bash
grep -RniE 'monitor|preferred|60\.0|120\.0|144\.0|165\.0|240\.0' ~/.config/hypr/
```

### Include

If relevant, include:

* Distribution and version
* Hyprland version
* GPU model
* Monitor model
* Connection type (`eDP`, `HDMI`, `DisplayPort`, etc.)
* Available refresh rates
* Current refresh rate
* Relevant Hyprland configuration
* Error messages
* Steps to reproduce the issue

### Remove Sensitive Information

Before submitting logs or configuration files, remove:

* Usernames
* Home-directory paths containing personal information
* Hostnames
* IP addresses
* Serial numbers
* Hardware identifiers that you do not want to publish
* Other personally identifiable information

## Pull Requests

### Keep Changes Focused

A pull request should solve one problem or improve one clearly defined area.

Good examples:

* Fix an incorrect Hyprland configuration example.
* Add support information for a specific display.
* Improve troubleshooting documentation.
* Add a diagnostic script.

Avoid unrelated changes in the same pull request.

### Documentation

Documentation should be:

* Clear
* Technically accurate
* Reproducible
* Easy to understand
* Compatible with current Hyprland syntax where possible

If a configuration depends on a specific Hyprland version or distribution, mention it explicitly.

## Configuration Examples

When submitting a configuration example, explain:

1. What hardware or environment it targets.
2. What problem it solves.
3. Where the configuration should be placed.
4. How to apply the configuration.
5. How to verify the result.

For example:

```lua
hl.monitor({
    output   = "eDP-1",
    mode     = "1920x1080@144.0",
    position = "0x0",
    scale    = 1.5,
    vrr      = 0,
})
```

## Commit Messages

Use concise commit messages.

Recommended format:

```text
docs: improve 144Hz troubleshooting
fix: correct Lua monitor configuration
docs: add CachyOS example
feat: add monitor detection script
```

Common prefixes:

* `feat:` — new functionality
* `fix:` — bug fix
* `docs:` — documentation
* `refactor:` — code restructuring
* `test:` — tests
* `chore:` — maintenance

## Pull Request Checklist

Before submitting a pull request, make sure:

* [ ] The change is focused.
* [ ] Documentation is clear.
* [ ] Configuration examples have been tested when possible.
* [ ] No personal information is included.
* [ ] Existing functionality is not unnecessarily changed.
* [ ] The README is updated if the change affects user-facing behavior.

## Code of Conduct

Please be respectful and constructive.

Technical disagreements are welcome, but personal attacks, harassment, and intentionally disruptive behavior are not.

Thank you for helping improve this project.
