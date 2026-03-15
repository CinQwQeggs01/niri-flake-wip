# niri-flake (WIP Blur Fork)

> [!WARNING]
> This is a temporary experimental fork for NixOS users who want to try out the blur/frosted glass effect before it lands in the niri mainline.
> Once [wip/branch](https://github.com/niri-wm/niri/tree/wip/branch) is merged into niri's main branch, this fork will no longer be maintained.
> If a similar WIP branch appears in the future, this fork may be updated again.

## Differences from Upstream

This fork is based on [sodiboo/niri-flake](https://github.com/sodiboo/niri-flake) with the following changes:

- Replaced the `niri-unstable` source with [niri-wm/niri wip/branch](https://github.com/niri-wm/niri/tree/wip/branch) to enable blur/background effect support
- Added `background-effect` option to the `window-rules` Nix module

For all other configuration options and module usage, please refer to the upstream project:
👉 [sodiboo/niri-flake](https://github.com/sodiboo/niri-flake)

---

## Usage

### 1. Add the flake input

Replace the upstream niri-flake with this fork in your `flake.nix`:
```nix
inputs = {
  niri-flake.url = "github:cinqwqeggs01/niri-flake-wip";
};
```

### 2. Use the WIP build of niri

Make sure your configuration points `package` to `niri-unstable` (the wip/branch build):
```nix
programs.niri.package = pkgs.niri-unstable;
```

---

## Background Effect Configuration

`background-effect` can be configured inside `window-rules` with the following fields:

| Field | Type | Description |
|-------|------|-------------|
| `blur` | `bool` | Enable blur behind the window |
| `xray` | `bool` | Enable xray mode (efficient blur, suited for tiled windows) |
| `noise` | `float` | Pixel noise intensity to reduce color banding from blur (recommended: `0.02`) |
| `saturation` | `float` | Background color saturation (`0` = desaturated, `1` = normal, `>1` = boosted) |

### Enable blur for all floating windows
```nix
programs.niri.settings.window-rules = [
  {
    matches = [ { is-floating = true; } ];
    background-effect = {
      blur = true;
      xray = false;
      noise = 0.02;
      saturation = 1.1;
    };
  }
];
```

### Enable blur for a specific application
```nix
programs.niri.settings.window-rules = [
  {
    matches = [ { app-id = "kitty"; } ];
    background-effect = {
      blur = true;
      noise = 0.02;
      saturation = 1.0;
    };
  }
];
```

### Xray blur for tiled windows, regular blur for floating windows
```nix
programs.niri.settings.window-rules = [
  # Floating windows: regular blur
  {
    matches = [ { is-floating = true; } ];
    background-effect = {
      blur = true;
      xray = false;
      noise = 0.02;
      saturation = 1.1;
    };
  }
  # Tiled windows: xray blur (better performance)
  {
    matches = [ { is-floating = false; } ];
    background-effect = {
      blur = true;
      xray = true;
    };
  }
];
```

---

## Notes

- Blur only works if the application itself supports a transparent background (e.g. Kitty, Alacritty, Ghostty)
- In non-xray mode, blur will temporarily disappear during window open/close animations and while dragging tiled windows — this is a known upstream limitation
- This fork does not provide a binary cache. The first build will compile niri from source, which may take a while

---

## License

Same as upstream. See [LICENSE](./LICENSE).
