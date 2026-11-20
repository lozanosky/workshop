# Compass HUD for UEFN

**Version 1.0** · Built and tested with UEFN 43.10

A horizontal compass for your UEFN island, written 100% in Verse.
Place a POI device anywhere on your map and it appears on every player's compass, on the map (Map Indicator) and in the world (Beacon) with its distance.

## Contents

- [What's included](#whats-included)
- [1. Try the demo](#1-try-the-demo)
- [2. Use it in your own island](#2-use-it-in-your-own-island)
- [3. Setup](#3-setup)
- [4. Compass settings](#4-compass-settings)
- [5. Customization](#5-customization)
- [6. Verse API](#6-verse-api)
- [7. Moving POIs](#7-moving-pois)
- [8. Notes and limitations](#8-notes-and-limitations)
- [9. Troubleshooting](#9-troubleshooting)

## What's included

| File | Description |
|---|---|
| `compass_device.verse` | Verse source (`compass_device`, `compass_poi_device`, `compass_style`, `compass_poi`) |
| `Icons/` | 5 example icons, 512x512: `mark_a` green, `mark_b` yellow, `mark_c` red, `mark_d` blue, `mark_e` purple |
| `CompassHUD.umap` | Ready-to-play demo level |

## 1. Try the demo

Open the project in UEFN, click **Launch Session** and play the demo level.

## 2. Use it in your own island

Copy `compass_device.verse` **and** the `Icons` folder into your project's `Content` folder. Keep them together, at the same level:

```
Content/
├── compass_device.verse
└── Icons/
```

The code uses `Icons.mark_a` as the default icon. If you move or rename the `Icons` folder, or separate it from `compass_device.verse`, Verse will not compile.

Then click **Verse > Build Verse Code**.

## 3. Setup

### Step 1 - Add the compass

- Drag `compass_device` from the Content Browser into your level (anywhere).
- Use only **one** `compass_device` per level.

### Step 2 - Create a style (what a POI looks like)

- Select `compass_device`. In the Details panel, find **Styles** and click **+**.
- **Icon:** the image shown on the compass bar.
- **Show Distance:** shows "120 m" under the icon.
- The first style is style 0, the second is style 1, and so on.

### Step 3 - (Optional) Show it on the map

- Place a **Map Indicator** device anywhere and give it the icon and color of your style.
- Add it to your style's **Indicators** list.
- Add one Map Indicator per POI of that style that can be visible at the same time (3 chests at once = 3 Map Indicators).
- The compass moves them to the POIs and enables / disables them automatically.

### Step 4 - (Optional) Show it in the world with distance

- Place a **Beacon** device anywhere and set:

  | Option | Value |
  |---|---|
  | Beacon To Show | Badge |
  | Display Distance Text | On |
  | Beacon Color | Direct Color (pick your color) |
  | Badge Uses Beacon Color | On |

- Add it to your style's **Beacons** list. Same rule: one per POI visible at the same time.

### Step 5 - Place your POIs

Drag `compass_poi_device` to the spot you want to mark and set its options:

| Option | Description |
|---|---|
| Style Index | The style to use (0, 1, 2...). |
| Show On Map | Also use a Map Indicator and Beacon from the style. |
| Icon Size | Icon size on the compass bar. |
| Stick To Edge | When the POI is outside the bar, keep it on the edge you need to turn towards. |
| Visible To Teams | Team numbers (1, 2, 3...) that see it on the compass. Empty = everyone. |

### Step 6 - (Optional) Show / hide a POI during the game

- Uncheck **Visible On Start** so it starts hidden.
- Add Trigger devices to **Show Triggers** / **Hide Triggers**.

## 4. Compass settings

In the `compass_device` Details panel: **Letter Color**, **Show Ticks**, **Tick Color**, **Distance Color**, **Styles**.

## 5. Customization

These settings are not in the Details panel. To change them, open `compass_device.verse`, edit the value in the **Fixed settings** section of `compass_device` and click **Verse > Build Verse Code**.

| Setting | Default | Description |
|---|---|---|
| `BarTop` | 30.0 | Distance from the top of the screen, in pixels. |
| `BarWidth` | 600.0 | Width of the compass bar, in pixels. |
| `BarHeight` | 40.0 | Height of the letters row, in pixels. |
| `MaxIconSize` | 68.0 | Height reserved for POI icons, in pixels. |
| `FieldOfView` | 180.0 | Degrees of the world covered by the bar. Smaller = POIs move faster across the bar. |
| `NorthOffset` | 0.0 | Degrees. By default north is the +X axis of the level. If your map's north is elsewhere, set the angle here (for example 90.0). |
| `Letters` | N, NE, E, SE, S, SW, W, NW | The 8 direction labels, starting at north and going clockwise. Change them to translate the compass (for example "O" instead of "W" in Spanish). |
| `TickWidth` / `TickHeight` | 2.0 / 10.0 | Size of the ticks between letters, in pixels. |
| `DistRowHeight` | 30.0 | Height of the distance row, in pixels. |
| `DistanceUpdateInterval` | 0.25 | Seconds between distance text updates. |
| `MarkerWidth` | 40.0 | Width of the box of each letter / tick, in pixels. |
| `DistWidth` | 80.0 | Width of the box of each distance text, in pixels. |

### Test mode (for development only)

| Setting | Default | Description |
|---|---|---|
| `TestEnabled` | false | Set to true to spawn test POIs automatically. |
| `TestInterval` | 10.0 | Seconds between test POIs. |
| `TestRadius` | 5000.0 | Test POIs appear up to this distance (cm) around the `compass_device`. |
| `TestMaxPois` | 1 | Maximum test POIs at the same time; the oldest one is removed when a new one appears. |

Each test POI uses the next style in the Styles list. Remember to set `TestEnabled` back to `false` before publishing your island.

## 6. Verse API

`compass_device` and `compass_poi_device` are public classes. The simplest setup is to put your Verse file in the same folder as `compass_device.verse`.

### compass_device

| Function | Description |
|---|---|
| `AddPoiAt(Position:vector3, StyleIndex:int):?compass_poi` | Creates a POI at a position with a style. Returns the POI, or false if the style does not exist. |
| `AddPoi(Poi:compass_poi):void` | Adds a POI you built yourself (custom icon size, teams...). |
| `RemovePoi(Poi:compass_poi):void` | Removes a POI from the compass, the map and the world. |
| `MovePoi(Poi:compass_poi, NewPosition:vector3):void` | Moves a POI: compass icon, distance, Map Indicator and Beacon. See [Moving POIs](#7-moving-pois). |
| `GetStyle(StyleIndex:int):?compass_style` | Returns the style at that index, or false if it does not exist. |
| `ShowForPlayer(Player:player):void` / `HideForPlayer(Player:player):void` | Shows or hides the whole compass for one player. A player's compass is created when they spawn, so call these after the player has spawned. |

### compass_poi_device

| Function | Description |
|---|---|
| `ShowPoi():void` / `HidePoi():void` | Shows or hides this POI. |
| `RefreshPosition():void` | Moves this POI to the device's current position. See [Moving POIs](#7-moving-pois). |

### Example - mark a helicopter crash and remove it later

```verse
using { /Fortnite.com/Devices }
using { /Verse.org/Simulation }
using { /UnrealEngine.com/Temporary/SpatialMath }

my_event_device := class(creative_device):

    # Select your compass_device here in the Details panel
    @editable
    Compass : compass_device = compass_device{}

    var CrashPoi : ?compass_poi = false

    # Call this when the event happens
    OnHelicopterCrash(CrashPosition:vector3):void=
        set CrashPoi = Compass.AddPoiAt(CrashPosition, 0)

    # Call this when the event ends
    OnLootCollected():void=
        if (Poi := CrashPoi?):
            Compass.RemovePoi(Poi)
            set CrashPoi = false
```

## 7. Moving POIs

A POI does not follow its device automatically. Its position is read when it appears, and after that it only changes when you tell it to.

**Moving a `compass_poi_device` in the editor:** no problem. The position is read when the game starts, so place it wherever you want before playing.

**Moving a `compass_poi_device` during the game** (for example with `TeleportTo`): call `RefreshPosition()` on the device after moving it. The compass icon, the distance, the Map Indicator and the Beacon move to the new position:

```verse
if (MyPoiDevice.TeleportTo[NewPosition, IdentityRotation()]):
    MyPoiDevice.RefreshPosition()
```

If the POI is hidden, `RefreshPosition()` does nothing: `ShowPoi()` always uses the device's current position.

**POIs created from Verse** (`AddPoiAt` / `AddPoi`): use `MovePoi` on the compass:

```verse
if (Poi := MyPoi?):
    Compass.MovePoi(Poi, NewPosition)
```

**Following something that moves** (a vehicle, a prop, a player...): call `MovePoi` or `RefreshPosition` in a loop, for example every 0.25 seconds:

```verse
loop:
    Sleep(0.25)
    Compass.MovePoi(Poi, Target.GetTransform().Translation)
```

The compass icon moves smoothly. Map Indicators and Beacons are teleported on each call, so very short intervals are not recommended.

> **Do not change `Poi.Position` directly:** the compass icon would move, but the Map Indicator and the Beacon would not. Always use `MovePoi`.

## 8. Notes and limitations

- Map Indicators and Beacons are Fortnite devices: their icon and color are set on the devices themselves. Match them to your style's icon.
- Add as many Map Indicators / Beacons per style as POIs of that style you want visible at the same time.
- Visible To Teams applies to the compass. Map Indicators and Beacons are visible to everyone unless you restrict them in their own device settings.
- Each player's team is read once, when their compass is created (the first time they spawn). If a player changes team during the game, Visible To Teams keeps using their original team.
- POIs do not follow moving objects automatically. Use `MovePoi` or `RefreshPosition` (see [Moving POIs](#7-moving-pois)).
- Map Indicators and Beacons added to a style are controlled by the compass: they are disabled when the game starts and enabled only while a POI uses them. Do not use them for anything else, and do not enable them yourself.
- A POI created by a `compass_poi_device` must be hidden with that device (`HidePoi` or Hide Triggers), not with `compass_device.RemovePoi`. Otherwise the device still thinks the POI is visible and `ShowPoi()` will not show it again.
- Icon Size values bigger than `MaxIconSize` (68 by default) may be cut off or overlap the distance row. Increase `MaxIconSize` (see [Customization](#5-customization)) if you need bigger icons.
- Distances are always shown in meters ("120 m").

## 9. Troubleshooting

Warning messages appear in the **Output Log**.

| Problem | Solution |
|---|---|
| `WARNING: style X does not exist in the compass_device Styles list` | The POI's Style Index is higher than the number of styles. Remember the first style is 0. |
| `WARNING: no free Map Indicators left for style X` | More POIs of that style are visible than Map Indicators in its list. Add more to the style. The POI still appears on the compass. |
| `WARNING: no free Beacons left for style X` | More POIs of that style are visible than Beacons in its list. Add more to the style. The POI still appears on the compass. |
| Map markers appear without color | Set the icon and color on each Map Indicator device. |
| The compass letters do not match the map's north | Change `NorthOffset` (see [Customization](#5-customization)). |
