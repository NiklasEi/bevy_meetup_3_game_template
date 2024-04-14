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
- Platform specific files for packaging
- Minimal structure with two crates

---

# Automate what you can

- Simple CI pipeline
- Build pipeline for all target platforms
- Publish pipelines for web, android[^1] and iOS[^2]

<img class="workflows" alt="Modules in bevy_game_template" src="/workflows.png" width="350"/>


[^1]: https://www.nikl.me/blog/2023/github_workflow_to_publish_android_app/
[^2]: https://www.nikl.me/blog/2023/github_workflow_to_publish_ios_app/
---

# Extendable

- Embrace Bevy plugins for code organisation
- It's a workspace; You can add more crates
- Plugins are organized by domain

---

# Project structure

Try to cut plugins by domain

<img alt="Modules in bevy_game_template" src="/modules.png" width="450"/>

---

# Project structure - example audio.rs

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

# Project structure - example (old; Bevy 0.5) loading.rs

```rust
pub struct LoadingPlugin;
impl Plugin for LoadingPlugin {
    fn build(&self, app: &mut AppBuilder) {
        app.add_system_set(SystemSet::on_enter(GameState::Loading).with_system(start_loading.system()))
           .add_system_set(SystemSet::on_update(GameState::Loading).with_system(check_state.system()));
    }
}
struct LoadingState { handles: Vec<HandleUntyped> }

pub struct AudioAssets { pub flying: Handle<AudioSource> }
pub struct TextureAssets { pub texture_bevy: Handle<Texture> }

fn start_loading(mut commands: Commands, asset_server: Res<AssetServer>) {
    let handles = vec![asset_server.load_untyped("flying.ogg"), asset_server.load_untyped("sprite.png")];
    commands.insert_resource(LoadingState { handles });
}

fn check_state(mut commands: Commands, mut state: ResMut<State<GameState>>, server: Res<AssetServer>, loading_state: Res<LoadingState>) {
    if LoadState::Loaded != server.get_group_load_state(loading_state.handles.iter().map(|handle| handle.id)) { return; }
    commands.insert_resource(AudioAssets { flying: server.get_handle("flying.ogg") });
    commands.insert_resource(TextureAssets { texture_bevy: server.get_handle("sprite.png") });
    state.set(GameState::Menu).unwrap();
}
```

---

# Some patterns deserve their own crate

Cutting "internal" plugins by domain makes it easy to move them to other crates

![Plugin bevy_asset_loader on GitHub](/bevy_asset_loader_github.png)

---

# Project structure - loading.rs with bevy_asset_loader

```rust
pub struct LoadingPlugin;
impl Plugin for LoadingPlugin {
    fn build(&self, app: &mut App) {
        app.add_loading_state(
            LoadingState::new(GameState::Loading)
                .continue_to_state(GameState::Menu)
                .load_collection::<AudioAssets>()
                .load_collection::<TextureAssets>(),
        );
    }
}

#[derive(AssetCollection, Resource)]
pub struct AudioAssets {
    #[asset(path = "audio/flying.ogg")]
    pub flying: Handle<AudioSource>,
}
#[derive(AssetCollection, Resource)]
pub struct TextureAssets {
    #[asset(path = "textures/bevy.png")]
    pub bevy: Handle<Image>,
    #[asset(path = "textures/github.png")]
    pub github: Handle<Image>,
}
```

---
layout: cover
---

# cross-platform things in code

---

# No console on Windows [^1]

```rust
#![cfg_attr(not(debug_assertions), windows_subsystem = "windows")]
```

[^1]: https://bevy-cheatbook.github.io/platforms/windows.html#disabling-the-windows-console
---

# Icons everywhere

* `build.rs` file for exe icon [^1]
* `window.set_window_icon()` to set the icon in the task bar/window on Windows and X11 Linux [^2]

[^1]: https://bevy-cheatbook.github.io/platforms/windows.html#setting-the-exe-icon
[^2]: https://bevy-cheatbook.github.io/window/icon.html