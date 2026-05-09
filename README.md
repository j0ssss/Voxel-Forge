# ⬡ Voxel Forge
### Image → Roblox Lua Voxel Image Generator

---

## What Is It?

Voxel Forge is a Windows desktop app that converts any image into a Roblox Lua script. Run that script inside Roblox Studio and it builds your image in the 3D world, pixel by pixel, out of small coloured `Part` blocks — voxel art you can walk around, explore, and build on top of.

You upload an image, configure how it looks, hit Generate, and get a `.lua` file in your Downloads folder ready to paste into Roblox Studio. No coding required.

---

## Download

Grab the latest `VoxelForge.exe` from the [Releases](../../releases) page. No Python installation needed — just download and double-click.

> Windows may show a SmartScreen warning on first launch. Click **More info → Run anyway**. This is normal for unsigned executables.

---

## Features

| Feature | Details |
|---|---|
| Image formats | PNG, JPG, JPEG, BMP, GIF, TIFF |
| Resolution modes | FULL (1px = 1 block), MEDIUM (2×2px), LOW (4×4px) |
| Encoding modes | Optimized palette+hex, Legacy RLE, Unoptimized |
| Block size | Adjustable (default 0.05 studs) |
| Block depth | Adjustable Z-thickness to prevent blocks disappearing at angles |
| Placement | Set exact X/Y/Z coordinates in the Roblox world |
| Rotation | 3D gizmo with draggable X/Y/Z arc rings, configurable snap |
| Export name | Custom name with auto-increment if file already exists |
| Colour accuracy | Pillow median-cut quantiser for best palette selection |
| Transparency | Optional — skips pixels with alpha below threshold |
| Output | Single `.lua` file saved to your Downloads folder |

---

## How To Use The App

**1. Upload your image**
Click `▲ UPLOAD IMAGE` and pick any image file. The app shows its dimensions and file size.

**2. Set an export name**
Type a name in the Export Name box. This becomes the `.lua` filename in Downloads and the folder name inside Roblox. If the file already exists, a number is appended automatically — `MyImage`, `MyImage1`, `MyImage2`, and so on.

**3. Pick a resolution mode**
- `FULL` — every pixel becomes one block. Most detail, largest output.
- `MEDIUM` — every 2×2 pixels becomes one block. Good balance.
- `LOW` — every 4×4 pixels becomes one block. Fastest, smallest output.

**4. Pick an encoding mode**
- `Optimized (palette+hex)` — recommended. Reduces the image to up to 255 colours and encodes it as a compact hex string. Around 100× fewer lines than per-pixel for photos.
- `Legacy RLE` — groups identical consecutive pixels into runs. Stable fallback, works well for flat/logo-style images.
- `Unoptimized` — one line of Lua per pixel. Only for tiny images. Shows a crash warning before generating.

**5. Set block size and depth**
Leave both at defaults for standard pixel art:
- Block size `0.05` studs — standard 1px to 1 stud mapping.
- Block depth `0.2` studs — gives blocks enough thickness to stay visible from any camera angle. Going below `0.05` causes blocks to vanish at oblique angles and is clamped automatically.

**6. Set placement coordinates** *(optional)*
Enter X, Y, Z for where the top-left corner of your image appears in the Roblox world. Leave blank to auto-place near the Baseplate. Optionally set a Lower-Right corner to stretch the image to fill a specific region.

**7. Set rotation** *(optional)*
Drag the coloured rings in the 3D gizmo to rotate the image before export:
- Red ring — X axis (tilt up/down)
- Green ring — Y axis (spin left/right)
- Blue ring — Z axis (roll)

Type a number in the snap field to control degree increments (default 30°, set to 0 for freeform). Hit `↺ Reset` to return to 0°. The full image rotates as a unit around its own centre.

**8. Generate**
Click `⬡ GENERATE LUA SCRIPT`. A progress bar shows status. When done, a popup confirms the file path, block count, line count, and file size.

---

## How To Use In Roblox Studio

### Getting your image into Roblox

**Step 1 — Open a Place**
Open Roblox Studio. Start with a Baseplate template or open an existing place.

**Step 2 — Insert a Script**
In the Explorer panel:
- Find **ServerScriptService**
- Right-click → **Insert Object** → **Script**

**Step 3 — Paste the Lua**
- Open your generated `.lua` file from `Downloads` in Notepad
- Select All (`Ctrl+A`) → Copy (`Ctrl+C`)
- Click inside the Roblox Studio script editor
- Delete the default `print("Hello world!")` line
- Paste (`Ctrl+V`)

**Step 4 — Run it**

For a temporary preview:
- Click **▶ Play** — the blocks build in the world while playtesting
- Click **Stop** when done

For permanent blocks that stay in your place:
- Move the Script from `ServerScriptService` directly into **Workspace**
- Click **▶ Run** (not Play) — this runs server-side without a character
- The blocks appear and stay in the Workspace
- Save or Publish your place (`Ctrl+S` or `File → Publish`)

**Step 5 — Done**
The blocks are now a `Folder` in your Workspace named whatever you set as the export name. You can move, group, or anchor them like any other Roblox objects.

---

## Recommended Max Image Sizes

| Mode | Recommended Max | Approximate Block Count |
|---|---|---|
| FULL | 1024 × 1024 px | ~1,000,000 blocks |
| MEDIUM | 512 × 512 px | ~262,000 blocks |
| LOW | 256 × 256 px | ~65,000 blocks |

Larger images generate more blocks and take longer to build in-game. For portraits and logos, FULL at 256×256–512×512 gives excellent results. For large scenic backdrops use MEDIUM or LOW.

---

## Encoding Modes In Detail

### Optimized — Palette + Hex
Uses Pillow's median-cut algorithm to pick the best 255 colours for your specific image, then stores every pixel as a 2-character hex index. A tiny Lua decoder loop reads the string and places each block. This is the recommended mode for almost all images.

A 1920×1080 photo produces around 1,100 lines of Lua with this mode vs around 244,000 with RLE.

Lossless for pixel art and logos that already use 255 or fewer colours. For photographs, the colour rounding is the same imperceptible kind a JPEG already applies.

### Legacy RLE
Stores consecutive identical pixels as `{R, G, B, skip, count}` run entries. Works well for images with large solid-colour areas. Less effective for photographs where nearly every pixel is unique. Confirmed stable in Roblox Studio.

### Unoptimized
One line of Lua per pixel. Produces the largest possible files. A 1920×1080 image = over 2 million lines. Only use this for very small images (under ~100×100). A warning dialog is shown before generating.

---

## Why Blocks Disappear At Angles

At 0.05 × 0.05 × 0.05 studs (a perfect cube), each block has a razor-thin edge. When the camera looks from any non-perpendicular angle, Roblox's renderer culls that near-zero-area surface and the block vanishes.

The app defaults block depth to `0.2` studs, giving each block real volume so it stays visible from any angle — like a painting on a thick canvas rather than a sheet of paper. You can increase this further for a chunky 3D look or keep it at 0.2 for a flat appearance from the front.

---

## Building From Source

Requirements:
```
pip install pillow
```

Run:
```
python main.py
```

To build a standalone `.exe`:
```
pip install pyinstaller
python -m PyInstaller --onefile --windowed --name "VoxelForge" main.py
```

Output will be in `dist/VoxelForge.exe`.

---

## File Structure

```
VoxelForge/
├── main.py       — Full application source
├── README.md     — This file
└── LICENSE       — All Rights Reserved
```

---

## Troubleshooting

**Nothing appears in Roblox after running the script**
Make sure the Script is in `ServerScriptService` and you pressed Play. Check the Output panel for errors. If using Optimized encoding and nothing shows, switch to Legacy RLE — it is the confirmed-stable encoder.

**Colours look inaccurate**
Use the Optimized encoder — it uses median-cut quantisation for the most faithful colour palette.

**Blocks vanish when walking around**
Increase the Block Depth in the app (Section 5). Default is 0.2 studs. Values below 0.05 are clamped. Try 0.3–0.5 if you're still seeing disappearing blocks.

**Roblox Studio freezes or crashes**
Your image is too large or your encoding mode is too heavy. Switch to a lower resolution mode, reduce the image dimensions before uploading, or make sure you're using the Optimized encoder.

**Windows blocks the .exe**
Click **More info → Run anyway** in the SmartScreen popup. This appears on all unsigned executables and is not a virus warning specific to this app.

---

## License

Copyright © 2026. All Rights Reserved.

This software and source code may not be copied, modified, distributed, or used to create derivative works without the express written permission of the author. See the `LICENSE` file for full terms.
