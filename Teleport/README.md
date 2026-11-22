# Teleport Device in Verse (UEFN)

**Version 1.0** · Built and tested with UEFN 43.10

A teleport device for your UEFN island, written 100% in Verse.
Place two (or more) teleports with the same ID: a player who walks up to one sees **[E] Teleport**, presses E, dissolves into a particle effect while the screen fades, and reappears at the linked teleport.

## Contents

- [What's included](#whats-included)
- [1. Try the demo](#1-try-the-demo)
- [2. Use it in your own island](#2-use-it-in-your-own-island)
- [3. Setup](#3-setup)
- [4. Device settings](#4-device-settings)
- [5. Customization](#5-customization)
- [6. How it works](#6-how-it-works)
- [7. Notes and limitations](#7-notes-and-limitations)
- [8. Troubleshooting](#8-troubleshooting)

## What's included

| File / Folder | Description |
|---|---|
| `teleport_device.verse` | Verse source (`teleport_device`) |
| `FX/` | 6 Niagara teleport effects (`Systems/NS_FX_Teleport_Blue`, `Green`, `Magenta`, `Pink`, `Red`, `Violet`) with their materials and textures |
| `Sounds/` | Departure and arrival sounds (`teleport_out`, `teleport_in`) and an ambient loop |
| `Teleport.umap` | Ready-to-play demo level with two linked teleports |

## 1. Try the demo

Open the project in UEFN, open `Teleport.umap`, click **Launch Session** and walk up to one of the teleports. Press **E**.

## 2. Use it in your own island

Copy `teleport_device.verse` **and** the `FX` folder into your project's `Content` folder (and the `Sounds` folder if you want the included sounds). Keep them together, at the same level:

```
Content/
├── teleport_device.verse
├── FX/
└── Sounds/
```

The Verse code references the effects in `FX/Systems/` directly. If you move or rename the `FX` folder, or separate it from `teleport_device.verse`, Verse will not compile.

Then click **Verse > Build Verse Code**.

## 3. Setup

### Step 1 - Place the teleports

- Drag `teleport_device` from the Content Browser into your level.
- Place a second `teleport_device` where the player should arrive.
- Give both the **same Teleport ID** (for example 1). That's it: they are linked automatically, no lists or tags needed.

### Step 2 - Orient the arrival point

The player appears **2 m in front** of the destination teleport (the direction of the device's forward arrow) and looks in that direction. Rotate the device so its arrow points where you want the player to face.

### Step 3 - (Optional) Pick an effect and fade color

- **Teleport FX:** Blue, Green, Magenta, Pink, Red, Violet, or **NoFX** for no effect at all.
- **Fade Color:** color of the screen fade the teleporting player sees.

### Step 4 - (Optional) Add sounds

Place up to three **Audio Player** devices anywhere in your level, set their sound (for example the ones in `Sounds/`), and assign them to the teleport:

| Option | Description |
|---|---|
| Ambient Sound | Looping sound at the teleport. Plays while any player is within 50 m. Enable **looping** in the Audio Player. |
| Start Sound | Plays where the player disappears. |
| End Sound | Plays where the player reappears. |

The teleport moves these Audio Players to the right place automatically, so their position in the level does not matter. Volume, attenuation and looping are set on the Audio Player itself.

### Using your own effect

Set **Teleport FX** to **NoFX** and place your own effect at the teleport (a **VFX Spawner** device, a Niagara system or any prop you like). To add your effect to the Teleport FX list instead, add a value to `teleport_fx` and a line to `GetFX()` in the Verse file.

### More than two teleports

With three or more teleports sharing an ID, each one sends you to the **next** one, in a loop (A → B → C → A). The order is the order in which the devices start, so for a fixed route use pairs with different IDs instead.

## 4. Device settings

In the `teleport_device` Details panel:

| Option | Default | Description |
|---|---|---|
| Teleport ID | 1 | Teleports with the same ID are linked. |
| Interact Distance | 250.0 | Distance (cm) at which the [E] Teleport prompt appears and E works. |
| Teleport FX | Blue | Effect at this teleport (ambient, departure and arrival). NoFX = no effect. |
| Fade Color | Light blue | Color of the screen fade. |
| Ambient Sound | none | Optional Audio Player, looping at the teleport. |
| Start Sound | none | Optional Audio Player, played on departure. |
| End Sound | none | Optional Audio Player, played on arrival. |

## 5. Customization

These settings are not in the Details panel. To change them, open `teleport_device.verse`, edit the value in the **Fixed settings** section and click **Verse > Build Verse Code**.

| Setting | Default | Description |
|---|---|---|
| `EffectHeight` | 150.0 | Height (cm) above the device of the effect and the ambient sound. |
| `AmbientDistance` | 5000.0 | The effect and the ambient sound are only active while a player is within this distance (cm). |
| `FXJoinDelay` | 2.0 | Seconds to wait after a player joins before respawning the effect for them. |
| `BurstDuration` | 1.5 | Seconds the departure / arrival effect lasts. |
| `DissolveTime` | 0.5 | Seconds between disappearing and arriving (screen fades in). |
| `ReassembleTime` | 0.7 | Seconds after arriving before the player is visible and can move (screen fades out). |
| `ArrivalDistance` | 200.0 | The player appears this far (cm) in front of the destination device. |
| `ArrivalHeight` | 100.0 | ...and this high (cm) above it. |
| `Cooldown` | 1.0 | Seconds after arriving before the same player can teleport again. |
| `KeyLabel` | "E" | Key shown in the prompt. |
| `PromptText` | "Teleport" | Text shown in the prompt (change it to translate). |
| `PromptScreenY` | 0.72 | Vertical position of the prompt on screen (0 = top, 1 = bottom). |

## 6. How it works

1. The player presses E near a teleport.
2. The player is frozen, the departure effect and Start Sound play, the character disappears and the screen fades to Fade Color.
3. The player is moved in front of the destination teleport (or right above it if that spot is blocked).
4. The arrival effect plays at the destination, the screen fades back, the player reappears, End Sound plays and the player can move again.

Only the teleporting player sees the screen fade. Everyone sees the effects and hears the sounds.

## 7. Notes and limitations

- **Input:** uses Fortnite's **Interact** action (E on keyboard). The prompt always shows `KeyLabel` ("E"), even on gamepad.
- **Multiplayer:** each player has their own prompt, cooldown and fade. Several players can teleport at the same time.
- The cooldown is shared by all teleports, so one E press never triggers two teleports that are close to each other.
- The ambient effect is spawned again when a player comes within `AmbientDistance` or joins the game, so late joiners also see it.
- **Effects are chosen with Teleport FX, not with a particle system field.** A Niagara system picked in a device's Details panel does not show in game in UEFN 43.x, while one referenced from Verse does.
- The Audio Players you assign are moved by the teleport: do not use them for anything else.
- The device mesh is invisible in game. Only the effect is visible.

## 8. Troubleshooting

Messages appear in the **Output Log**.

| Problem | Solution |
|---|---|
| `teleport_device: no other teleport with ID X` | There is only one teleport with that ID. Place another one with the same Teleport ID. |
| `teleport_device: could not teleport to teleport ID X (destination blocked?)` | Both the spot in front of the destination and the spot above it are blocked. Move the destination teleport to an open area. |
| No prompt appears | Get closer (Interact Distance) or wait for the cooldown to end. |
| No effect visible | Check Teleport FX is not NoFX. The effect only shows within `AmbientDistance`. |
| Ambient sound plays only once | Enable looping in the Audio Player device. |
