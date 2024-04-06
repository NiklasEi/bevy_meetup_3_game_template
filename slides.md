---
layout: cover
theme: dracula
---

# Learnings from a Bevy game template

---

# A cross-platform setup

Game logic in a plugin from the main library
The main binary is the entry point for Web, Windows, macOS and Linux
Separate library crate `mobile` for Android and iOS
---

# Automation is everything

- little time in game jams => get builds out of the way
- build pipeline for all target platforms


---

# general structure

think by domain, not type
player plugin in own mod, not all systems in one, all resources in another


---

# loading state?


---

# Some patterns deserve their own crates

- loading state -> bevy_asset_loader


---

# cross-platform things in code

* Turn off the windows dev console
* `build.rs` file for exe icon
* `window.set_window_icon()` to set the icon in the task bar/window on Windows and Linux

[//]: # (ask to use gnu toolchain so players don't have to install the Microsoft C/C++ Runtime Redistributables?)


---

# excursion: wasm-opt?


---

# excursion: AAB builds?