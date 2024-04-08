---
layout: cover
theme: dracula
---

# Learnings from a Bevy game template

---

# A third-party starting point for Bevy apps

- cross-platform
- CI/CD
- extendable

![bevy_game_template on GitHub](/github_bevy_game_template.png)

---

# Cross-platform setup

- Support for all "official" Bevy platforms
- Minimal structure with two crates
- Build files per platform

---

# Automation is everything

- Simple CI pipeline
- Build pipeline for all target platforms
- Publish pipelines for web, android and iOS

---

# Extendable

- Embrace Bevy plugins for code organisation
- Add more crates to the workspace

---

# Project structure

Try to cut plugins by domain

<img alt="Modules in bevy_game_template" src="/modules.png" width="450"/>

---

# Project structure

```rust
pub struct InternalAudioPlugin;

// This plugin is responsible for controlling the game audio
impl Plugin for InternalAudioPlugin {
    fn build(&self, app: &mut App) {
        app.add_plugins(AudioPlugin)
            .add_systems(OnEnter(GameState::Playing), start_audio)
            .add_systems(
                Update,
                control_audio
                    .after(set_movement_actions)
                    .run_if(in_state(GameState::Playing)),
            );
    }
}

#[derive(Resource)]
struct FlyingAudio(Handle<AudioInstance>);

fn start_audio(mut commands: Commands, audio_assets: Res<AudioAssets>, audio: Res<Audio>) { ... }

fn control_audio(actions: Res<Actions>, audio: Res<FlyingAudio>, mut instances: ResMut<Assets<AudioInstance>>) { ... }
```

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