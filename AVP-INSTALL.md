# Installing Tiberian Dawn on Apple Vision Pro

This is the native visionOS target of Tiberian Dawn for iPad, macOS and Vision Pro, an unofficial, community-made port of *Command & Conquer: Tiberian Dawn* (1995) based on [Vanilla Conquer](https://github.com/TheAssemblyArmada/Vanilla-Conquer) and the source code released by Electronic Arts. It is an arm64 command-window prototype built from the same engine, Metal renderer, audio, importer, saves and controls as the iPad and Mac apps, with gaze-and-pinch controls. (The iPad app can also run on Vision Pro in Apple's *Designed for iPad* mode; this guide covers the native target.)

## What you need

- A Mac with Xcode and the visionOS SDK, and CMake 3.28 or newer
- An Apple development team to sign with
- Apple Vision Pro
- Your own C&C Gold **GDI and Nod** disc images. Data from the Remastered Collection, The First Decade or The Ultimate Collection isn't supported.

## Your game files

This repository contains no game, disc images or original data files.

1. On first launch, choose the GDI and Nod ISO files together in the system document picker.
2. The app validates both discs, extracts the data locally, and doesn't keep the ISO files. You can select a previously prepared data directory instead.

## Build from source

There's no public visionOS download; the native target is built from source. Start from a checkout of this repository with its SDL2 submodule (`git submodule update --init --recursive` if `third_party/SDL2` is empty).

For an unsigned device compile, which checks the build without installing it:

```sh
./scripts/build-visionos-device.sh
```

It applies the SDL2 iPadOS and visionOS patches in `patches/` to `third_party/SDL2`, configures the `visionos-device` CMake preset and compiles the `TiberianDawn` target.

## Install on Apple Vision Pro

Signing stays local. Set `VISIONOS_DEVELOPMENT_TEAM` in an untracked `CMakeUserPresets.json` (the preset turns signing off otherwise), and never commit a team ID, provisioning profile, certificate or credential. A user preset modelled on `resources/CMakeUserPresets.json.example` looks like this (set `VISIONOS_BUNDLE_IDENTIFIER` too if Xcode says `org.tiberiandawn.visionos` isn't available to your team):

```json
{
  "version": 6,
  "configurePresets": [
    {
      "name": "visionos-device-local",
      "inherits": "visionos-device",
      "cacheVariables": {
        "VISIONOS_BUNDLE_IDENTIFIER": "com.example.tiberian-dawn-vision",
        "VISIONOS_DEVELOPMENT_TEAM": "YOUR_TEAM_ID"
      }
    }
  ]
}
```

Then prepare SDL2 and configure it:

```sh
./scripts/prepare-visionos-dependencies.sh
cmake --preset visionos-device-local
```

Open `build/visionos-device/TiberianDawnApple.xcodeproj`, select the `TiberianDawn` scheme and your Apple Vision Pro, and press Run.

## Notes

- **Controls:** look at a unit, command or menu entry and pinch once to click it. Pinch-drag from empty terrain to draw a selection box. On visionOS 26 or newer, look toward an edge of the tactical map to scroll it (Look to Scroll). You can also look near a map edge and hold the pinch for 0.4 seconds to scroll. Hold the pinch over the central game area for the classic secondary click or cancel, and pinch once to skip a skippable cutscene. Mouse, trackpad, hardware keyboard and supported controllers also work.
- The **Visual Controls** dialog turns Look to Scroll on or off and sets scroll speed, edge sensitivity and selection tolerance.
- The native target is a prototype. It has launched on a physical Apple Vision Pro, imported the Gold data and entered gameplay; the README notes that comfort and the reworked gameplay gestures still need one more physical-headset re-test.
- EA has not endorsed and does not support this product. Command & Conquer and related names are Electronic Arts trademarks.
