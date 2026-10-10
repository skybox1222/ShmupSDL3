# ShmupSDL3
A free, open-source horizontal shmup running on Direct3D 11, with SDL3 bindings written by hand via P/Invoke.# ShmupSDL3

A side-scrolling **shoot 'em up** written in **C#** with **SDL3**, rendered in 2D on **Direct3D 11**.
SDL3 bindings are hand-written via **P/Invoke**, no external wrapper.

> Shoot, dodge, grab power-ups, take down the bosses. The more points you score, the tougher it gets.

You tube Video:
https://youtu.be/kFPhv03JSmY?si=9MfxChbBa4LS8Wf7

---

## Features

- Ship with smooth banking tilt when moving up/down
- **21 enemy types** with different behaviors (linear, wave, zig-zag, kamikaze, snipers, turrets, tanks, patrols, and more)
- **Boss fights** with a 3-shot aimed fan burst and particle-based engine flames
- **Upgradable weapons** up to level 5
- **Three kinds of defense**: energy shield, orbiting spheres, super bomb
- Particle system (explosions, organic Perlin-noise smoke, engine flames, auras)
- **Parallax** starfield (3 layers), nebulae and **animated volumetric fog** (3D Perlin noise)
- Audio via **SDL3_mixer**: overlapping sound effects and looping background music
- All assets are optional: if a file is missing, the game uses a drawn placeholder or simply skips that element (no crash)

---

## How to play

### Controls

| Key | Action |
|-----|--------|
| ← ↑ → ↓ | Move the ship |
| `SPACE` (hold) | Shoot (one shot every 0.35 s) |
| `ENTER` | Launch a super bomb |
| Close the window | Quit |

### Goal

Survive as long as you can and get the highest score. There are no extra lives: when your energy reaches zero the ship explodes and, after the death sequence, **the game restarts from scratch** (score, weapons and bonuses reset).

### Energy and damage

- Ship energy: **100**
- Colliding with an enemy or the boss, or getting hit by an enemy bullet, deals damage, followed by a short invulnerability window (0.5 s)
- The **energy shield**, when active, absorbs damage instead of your energy

### Score

| Event | Points |
|-------|--------|
| Successful hit | +1 |
| Enemy killed | +5 |
| Enemy killed by super bomb | +5 |
| Power-up collected | +15 |
| Boss defeated | +200 |

### Enemy waves

A new wave arrives every **4 seconds**. Some enemies are tougher (2 to 5 hits to destroy) and some shoot back.

### Power-ups

Every **50 enemies killed** drops a bonus. The type is picked at random:

| Power-up | Effect |
|----------|--------|
| **Weapon** | Raises the weapon level (1 to 5 parallel bullets) |
| **Orbiting spheres** | Unlocked after collecting 5 power-ups with the weapon at max level. Up to 3 spheres orbit the ship and instantly destroy any enemy they touch |
| **Energy shield** | ~30% chance. Dedicated bar (100) that absorbs damage; explodes when depleted |
| **Super bomb** | Destroys all enemies on screen, clears enemy bullets and removes 25% of the boss's health. You start with 2, max 5 |

### Boss

- Appears every **3000 points**
- 1000 health points, each of your hits deals 10 damage (100 hits total, or use super bombs)
- Fires an aimed 3-shot fan burst every 1.1 seconds
- Its defeat comes with a long chain of explosions

---

## Requirements

- **Windows** x64 (the project uses Direct3D 11; if unavailable it falls back to the renderer auto-detect)
- **.NET 6 SDK or later** (uses top-level statements; .NET 8 recommended)
- Native SDL3 libraries (x64):
  - `SDL3.dll`
  - `SDL3_image.dll`
  - `SDL3_mixer.dll`

### Where to download the libraries

From the official GitHub releases:

- SDL3: https://github.com/libsdl-org/SDL/releases
- SDL3_image: https://github.com/libsdl-org/SDL_image/releases
- SDL3_mixer: https://github.com/libsdl-org/SDL_mixer/releases

Download the `win32-x64` builds and copy the DLLs into the output folder (next to the executable).

> Note: SDL3_mixer 3.x has a completely different API from SDL2_mixer (`MIX_Mixer`, `MIX_Audio`, `MIX_Track`...). You need the 3.x versions.

---

## Build and run

```bash
git clone https://github.com/<your-username>/ShmupSDL3.git
cd ShmupSDL3
dotnet build -c Release
```

Then copy into the output folder (e.g. `bin/Release/net8.0/`):

1. `SDL3.dll`, `SDL3_image.dll`, `SDL3_mixer.dll`
2. the `assets/` folder

and run `ShmupSDL3.exe`.

### Project configuration

The code uses `unsafe` blocks (direct keyboard state and pixel reads), so your `.csproj` needs:

```xml
<PropertyGroup>
  <OutputType>Exe</OutputType>
  <TargetFramework>net8.0</TargetFramework>
  <Nullable>enable</Nullable>
  <ImplicitUsings>enable</ImplicitUsings>
  <AllowUnsafeBlocks>true</AllowUnsafeBlocks>
  <PlatformTarget>x64</PlatformTarget>
</PropertyGroup>

<!-- Automatically copy assets and DLLs to the output folder -->
<ItemGroup>
  <None Update="assets\**\*" CopyToOutputDirectory="PreserveNewest" />
  <None Update="*.dll" CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

To hide the console window, set `<OutputType>WinExe</OutputType>` (log to a file if you still need diagnostics).

---

## Assets

The game looks for files in the `assets/` folder next to the executable.

| File | Used for |
|------|----------|
| `PlShip1.png` | Player ship |
| `bullet.png` | Player bullet |
| `enbullet.png` | Enemy bullet |
| `nm1.png` … `nm4.png` | Enemy sprites |
| `powerup.png` | Weapon power-up |
| `enshield.png` | Energy shield power-up |
| `orbit.png` | Orbiting spheres |
| `pickup.png` | Super bomb power-up |
| `bigship.png` | Boss (see below) |
| `shoot.mp3` | Shooting sound |
| `boom.mp3` | Explosion |
| `superbomb.mp3` | Super bomb |
| `music.mp3` | Background music (infinite loop) |

Supported formats: images through SDL3_image (PNG, JPG, ...), audio through SDL3_mixer (WAV, MP3, OGG, FLAC, ...).

### Boss texture and magenta markers

The `bigship.png` texture can contain **pure magenta pixels (255, 0, 255)**: the game detects them on load, makes them transparent and uses them as **emission points for the engine flames**. Just draw the markers where you want the flames on the sprite. Without `bigship.png` the boss does not appear.

### Asset license

The files in `assets/` (graphics, **sound effects and music**) are provided for **non-commercial use only** and are **not covered by the code license**.

If you want to sell or commercially distribute the game or a derivative of it, **you must replace all assets** with your own files or files with a suitable license.

- **Sound effects:** from [Pixabay](https://pixabay.com/) and still subject to the [Pixabay Content License](https://pixabay.com/service/license-summary/).
- **Music:** generated by the author with [Suno](https://suno.com/) and subject to Suno's terms.

---

## Technical notes

### Architecture

```
ShmupSDL3/
├── Program.cs      # Game loop, input, game logic, rendering, enemies, particles
├── SDL3.cs         # Minimal P/Invoke bindings for SDL3 (window, renderer, input, textures)
├── SDL3Mixer.cs    # Minimal P/Invoke bindings for SDL3_mixer (audio)
└── assets/         # Sprites and sounds
```

### Rendering

- **1280×480** window, SDL renderer forced to `direct3d11` with auto-detect fallback
- Blend mode enabled for semi-transparent rectangles and textures
- **60 FPS** target with delta time (frame-rate independent movement); displayed FPS smoothed with an exponential moving average
- **Parallax** with 3 star layers plus a nebula bank
- **Volumetric fog**: time-animated 3D Perlin noise, written by hand into a low-resolution RGBA32 texture (`SDL_TEXTUREACCESS_STREAMING`) and upscaled with linear filtering for the soft look
- **Particle system**: explosions, smoke (noise texture), engine flames (player, enemies, boss), shield aura, power-up effects

### Input

Keyboard state is read directly with `SDL_GetKeyboardState` (continuous movement without per-key event handling). The super bomb only detects the **press edge** of ENTER, not the held state.

### Audio

- `MIX_PlayAudio` for effects: "fire and forget", multiple overlapping plays of the same sound without cutting each other off
- Music on a dedicated `MIX_Track` with infinite loop (`SDL_mixer.play.loops = -1`) and its own gain
- Short effects loaded with `predecode: true` to avoid decoding latency

### P/Invoke bindings

The bindings in `SDL3.cs` and `SDL3Mixer.cs` are intentionally minimal: they only contain the functions the game uses. A few useful notes:

- `SDL_Event` is a large union: it is mapped with `StructLayout.Explicit` (128 bytes), reading only the needed fields
- In SDL3 many functions return `bool` (1 byte), so the `DllImport`s use `[return: MarshalAs(UnmanagedType.I1)]`
- `SDL_PIXELFORMAT_RGBA32` guarantees R,G,B,A byte order on little-endian platforms (required to write pixels from the CPU)
- `SDL_ConvertSurface` brings images to a known format, since `IMG_Load` can return different formats depending on the file

### Enemies

All derive from the abstract `Enemy` class (position, size, `Health`) and implement their own movement: `LinearEnemy`, `CirclingEnemy`, `WaveEnemy`, `FastEnemy`, `BouncingEnemy`, `ArmoredCirclingEnemy`, `ZigZagEnemy`, `AmbushEnemy`, `LoopEnemy`, `SeekerEnemy`, `SniperEnemy`, `SpiralEnemy`, `TurretDroneEnemy`, `DasherEnemy`, `SentinelEnemy`, `SentryEnemy`, `OrbiterGuardEnemy`, `PulsarEnemy`, `PatrolEnemy`, `BorderPatrolEnemy`, `TankEnemy`.

To add a new enemy: create a class extending `Enemy`, set `Health` and override the movement/shooting logic, then add it to the wave selection.

### Easy-to-tweak parameters

Constants at the top of `Program.cs`:

| Constant | Meaning |
|----------|---------|
| `WindowW` / `WindowH` | Window size |
| `ShipSpeed` | Ship speed (px/s) |
| `FireCooldown` | Time between shots |
| `TimeBetweenWaves` | Seconds between waves |
| `DamagePerHit` | Damage per hit taken |
| `BossScoreInterval` | Points between bosses |
| `BossMaxHealth` | Boss health |
| `MaxWeaponLevel`, `MaxBombs`, `MaxOrbShields` | Upgrade limits |
| `MusicVolume`, `ShootVolume` | Volumes |

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `DllNotFoundException: SDL3` (or `SDL3_image` / `SDL3_mixer`) | Copy the x64 DLLs next to the executable |
| `BadImageFormatException` | Architecture mismatch: build and use 64-bit DLLs |
| Errors about `unsafe` blocks | Enable `AllowUnsafeBlocks` in the `.csproj` |
| No audio | Check that `SDL3_mixer.dll` is present and the files in `assets/` exist |
| Sprites replaced by rectangles | Files in `assets/` are not found: make sure the folder is in the output directory |
| Boss never appears | `assets/bigship.png` is missing |

---

## Roadmap

- [ ] More elaborate boss movement and collisions
- [ ] Main menu and game over screen
- [ ] High score saved to file
- [ ] Gamepad support
- [ ] More bosses and levels

---

## License

- **Source code:** MIT (see the `LICENSE` file)
- **Assets (graphics, sound effects, music):** non-commercial use only, excluded from the MIT license. See [Asset license](#asset-license).

SDL3, SDL3_image and SDL3_mixer are distributed under the zlib license by their respective authors.

## Credits

- Code: Matteo P ( skybox1212@gmail.com )
- Sound effects: [Pixabay](https://pixabay.com/)
- Music: generated with [Suno](https://suno.com/)
- Graphics: _authors and licenses_
- [SDL](https://libsdl.org/) — Simple DirectMedia Layer
