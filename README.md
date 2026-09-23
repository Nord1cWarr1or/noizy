# Noizy

**English** | **[Русский](https://github.com/Nord1cWarr1or/noizy/blob/master/README.ru.md)**

Noizy is a system-tray application that plays looping background sounds — noise, rain, ocean, wind, thunder, and more. It is a fork of [dwisiswant0/noizy](https://github.com/dwisiswant0/noizy) by Dwi Siswanto, a drop-in replacement for the Background Sounds feature of the Apple Hearing app, for Windows, Linux, and macOS. The fork exists because upstream has been inactive since November 2023: issue #15 and several dependency updates remain unmerged there, and this repository carries the fixes.

## Improvements over upstream

- **Reset now clears the sound checkboxes.** Pressing Reset stopped all sounds, but the menu kept showing every checkbox checked ([dwisiswant0/noizy#15](https://github.com/dwisiswant0/noizy/issues/15)). The cause is in the bundled `fyne.io/systray` pre-release of July 2023: on Linux it only emitted the dbusmenu `LayoutUpdated` signal, which desktop panels treat as a structural change and do not re-read item properties from. The fork updates `fyne.io/systray` to v1.12.2, which also emits `ItemsPropertiesUpdated` — the signal panels actually apply ([fyne-io/systray#116](https://github.com/fyne-io/systray/pull/116), [fyne-io/systray#72](https://github.com/fyne-io/systray/issues/72)).

Nothing else was dropped or changed relative to upstream master.

## Requirements

- [Go](https://go.dev/doc/install) 1.21+ with CGO enabled and a C compiler for the target platform.
- ALSA library on Linux: `libasound2-dev` on Debian-based distributions, `alsa-lib-devel` on RedHat-based ones.
- Audio Toolbox framework on macOS (ships with Xcode).
- A system tray that supports StatusNotifier/AppIndicator on Linux; see the [systray platform notes](https://github.com/fyne-io/systray#platform-notes).

For other platforms, see the [oto prerequisites](https://github.com/ebitengine/oto#prerequisite).

## Installation

The [Go module path](https://pkg.go.dev/github.com/dwisiswant0/noizy) still points at the upstream repository, so `go install` cannot install this fork. Build from source:

```bash
git clone https://github.com/Nord1cWarr1or/noizy.git
cd noizy
make build
```

The binary lands in `./bin/noizy`. To install upstream instead (which does support `go install`):

```bash
CGO_ENABLED=1 go install github.com/dwisiswant0/noizy@latest
```

On Windows (cmd), set the variable first:

```bat
set CGO_ENABLED=1 && go install github.com/dwisiswant0/noizy@latest
```

### Make targets

| Target | Result |
|--------|--------|
| `make build` | `./bin/noizy` for the current platform; version injected via ldflags, path trimmed |
| `make build-windows` | same, with `-H=windowsgui` so no console window opens |
| `make clean` | removes `./bin` |

## Usage

Run the binary. The app lives entirely in the system tray and has no window.

| Menu item | Action |
|-----------|--------|
| Background Sounds | submenu tree with ten categories and 30 sounds |
| Noizy &lt;version&gt; | version line (disabled) |
| Reset | stops every playing sound and clears the checkboxes |
| Quit | stops all sounds and exits |

Clicking a sound toggles it; any number of sounds can play at the same time. On `SIGHUP` the app resets, on `SIGINT`/`SIGTERM` it quits. Diagnostics go to stderr, with `slog` debug output included.

| Category | Sounds |
|----------|--------|
| Noise Sounds | Brown Noise, Pink Noise, White Noise |
| Water Sounds | Brook, Creek, Stream, Close Waterfall, Distant Waterfall |
| Coastal & Ocean | Calm Shore, Shore, Wild Shore, Ocean Waves, Large Waves |
| Rain Sounds | Rain Drops, Pouring Rain |
| Thunderstorms | Distant Thunder, Closer Thunder |
| Types of Winds | Coastal Wind, Forest Wind, Autumn Breeze |
| Nature Sounds | Birds, Frogs, Summer Night, Heat Wave |
| Social Settings | Coffee House, Cocktail Voices |
| Relaxation | Meditation Time, Wind Chimes |
| Other | Bonfire, Fan Noise |

The binary is large (around 170 MB) because all 30 OGG files are embedded; OGG was chosen over MP3 for better quality at the same size. A [demo](https://github.com/dwisiswant0/noizy/assets/25837540/4be24840-436f-49ed-9397-af93eac23516) recorded against upstream shows the tray menu in action.

## Contributing

1. Fork the repository.
2. Create a feature branch (`git checkout -b fix/my-fix`).
3. Commit your changes.
4. Push and open a Pull Request against [Nord1cWarr1or/noizy](https://github.com/Nord1cWarr1or/noizy).

CI runs [golangci-lint](https://golangci-lint.run) (`golangci-lint run --timeout=5m`) and semgrep (`semgrep ci` with the `auto` ruleset); run them locally before pushing.

## License

Noizy is released under the [GNU GPL v3](/LICENSE). The fork is distributed under the same license, in compliance with the upstream GPL v3.

The sounds embedded in the repository are copyrighted by [Stéphane Pigeon](https://stephanepigeon.com) and are intended solely for personal use.

## Acknowledgments

- **Dwi Siswanto ([dwisiswant0](https://github.com/dwisiswant0))** — author of the original Noizy.
- **Stéphane Pigeon** — author of the sounds.
