# ParsedConfig Schema - Directed Graph

```
                                    ┌──────────────────┐
                                    │  ParsedConfig    │
                                    └────────┬─────────┘
                  ┌─────────────────────────┼─────────────────────────┐
                  │                         │                         │
          ┌───────▼────────┐        ┌──────▼──────┐        ┌─────────▼─────────┐
          │ binding_modes  │        │    gaps     │        │     general       │
          │ Vec<...>       │        │ GapsConfig  │        │ GeneralConfig     │
          └───────┬────────┘        └──────┬──────┘        └────────┬──────────┘
                  │                        │                        │
          ┌───────▼────────────┐   ┌───────▼────────────┐   ┌──────▼──────────┐
          │BindingModeConfig   │   │ scale_with_dpi     │   │ cursor_jump      │
          ├────────────────────┤   │ inner_gap          │   │ focus_follows... │
          │ • name             │   │ outer_gap          │   │ toggle_workspace │
          │ • display_name     │   │ • top              │   │ startup_commands │
          │ • keybindings ─┐   │   │ • right            │   │ shutdown_commands│
          │   Vec<...>     │   │   │ • bottom           │   │ config_reload... │
          └────────────────┘   │   │ • left             │   │ hide_method      │
                               │   │ single_window...   │   │ show_all_in...   │
                               │   └────────────────────┘   └──────────────────┘
                               │
                        ┌──────▼────────────────┐
                        │ KeybindingConfig      │
                        ├───────────────────────┤
                        │ • bindings[]          │
                        │ • commands[]          │
                        │   Vec<InvokeCommand>  │
                        └───────────────────────┘


                  ┌─────────────────────────┬─────────────────────────┐
                  │                         │                         │
          ┌───────▼────────┐        ┌──────▼──────┐        ┌─────────▼─────────┐
          │  keybindings   │        │window_behav.│        │ window_effects    │
          │  Vec<...>      │        │WindowBehav..│        │WindowEffectsConfig│
          └───────┬────────┘        └──────┬──────┘        └────────┬──────────┘
                  │                        │                        │
        ┌─────────▼───────────┐   ┌────────▼───────────┐   ┌────────▼─────────┐
        │KeybindingConfig     │   │ initial_state      │   │ focused_window    │
        │ (same as above)     │   │ state_defaults ────┼──┤ other_windows     │
        └─────────────────────┘   │   WindowStateD...  │   │ WindowEffectConfig│
                                  └────────────────────┘   └────────┬─────────┘
                                          │                         │
                        ┌─────────────────▼──────────────┐  ┌───────▼──────────┐
                        │ WindowStateDefaultsConfig     │  │ WindowEffectConfig│
                        ├──────────────────────────────┤  ├──────────────────┤
                        │ • floating                   │  │ • border           │
                        │   FloatingStateConfig        │  │ • hide_title_bar   │
                        │   - centered                 │  │ • corner_style     │
                        │   - shown_on_top             │  │ • transparency     │
                        │ • fullscreen                 │  └──────┬───────────┘
                        │   FullscreenStateConfig      │         │
                        │   - maximized                │  ┌──────┴──────────────────────────┬────────────┐
                        │   - shown_on_top             │  │                                 │            │
                        └──────────────────────────────┘  │                                 │            │
                                                    ┌─────▼────────┐  ┌──────▼──────┐  ┌───▼──┐  ┌──▼─────┐
                                                    │ BorderEffect │  │HideTitleBar │  │Corner│  │Transpa-│
                                                    │ Config       │  │EffectConfig │  │Effect│  │ency    │
                                                    ├──────────────┤  ├─────────────┤  │Config│  │Config  │
                                                    │ • enabled    │  │ • enabled   │  │      │  │        │
                                                    │ • color      │  │             │  │ • en │  │ • en   │
                                                    │   (RGBA)     │  │             │  │ • st │  │ • opa  │
                                                    └──────────────┘  └─────────────┘  └──────┘  └────────┘


                  ┌─────────────────────────┬─────────────────────────┐
                  │                         │                         │
          ┌───────▼────────┐        ┌──────▼──────┐        ┌─────────▼─────────┐
          │  window_rules  │        │ workspaces  │        │    (empty)        │
          │  Vec<...>      │        │  Vec<...>   │        │                   │
          └───────┬────────┘        └──────┬──────┘        │                   │
                  │                        │               │                   │
        ┌─────────▼────────────┐  ┌────────▼──────┐       │                   │
        │WindowRuleConfig      │  │WorkspaceConfig│       │                   │
        ├──────────────────────┤  ├───────────────┤       │                   │
        │ • commands[]         │  │ • name        │       │                   │
        │ • match_window[]     │  │ • display_... │       │                   │
        │ • on[]               │  │ • bind_to_... │       │                   │
        │ • run_once           │  │ • keep_alive  │       │                   │
        └──────┬───────────────┘  └───────────────┘       │                   │
               │                                          │                   │
        ┌──────▼──────────────┐                           │                   │
        │WindowMatchConfig    │                           │ ❌ NO KEYBINDINGS │
        ├─────────────────────┤                           │                   │
        │ • window_process    │                           │                   │
        │   MatchType         │                           │                   │
        │ • window_class      │                           │                   │
        │   MatchType         │                           │                   │
        │ • window_title      │                           │                   │
        │   MatchType         │                           │                   │
        └──────┬──────────────┘                           │                   │
               │                                          │                   │
        ┌──────▼──────────────┐                           │                   │
        │    MatchType        │                           │                   │
        │  (enum options)     │                           │                   │
        ├─────────────────────┤                           │                   │
        │ • Equals            │                           │                   │
        │ • Includes          │                           │                   │
        │ • Regex             │                           │                   │
        │ • NotEquals         │                           │                   │
        │ • NotRegex          │                           │                   │
        └─────────────────────┘                           │                   │
                                                          └───────────────────┘
```

## Legend

- **Boxes**: Configuration structs
- **Vec<...>**: Indicates an array/vector
- **├─**: Child properties
- **│**: Connection/containment
- **Indentation**: Nesting depth

## Key Observations

1. **Keybindings appear in TWO places**:
   - `ParsedConfig.keybindings` (root level, global)
   - `BindingModeConfig.keybindings` (mode-specific)

2. **WorkspaceConfig is a LEAF node** - it has no complex children, only simple properties

3. **Window Effects are deeply nested** under `ParsedConfig → window_effects → WindowEffectConfig` with four distinct effect types

4. **Window Rules** support flexible matching with MatchType enum

5. **Commands** (Vec<InvokeCommand>) can appear in:
   - KeybindingConfig
   - GeneralConfig (startup, shutdown, reload)
   - WindowRuleConfig

