# HD2Lab · Shutterplate (PKPilot0003)

One photo — or four — onto a single **plate**. Drop a photo in and the app reads its EXIF, lays the exposure
spec and a signature on it, and saves it to your **photo album** at the original 1:1 resolution in one of
**13 signature frames** (11 single-cut, 2 four-cut multi). It is a **browser-local photo-frame studio**.

> 2026-11-15 — the 13-frame build verified at `/PKPilot0003/beta/` was promoted to the production build.
> The beta folder is still there, so `/PKPilot0003/beta/` keeps working.

- Live: <https://hyangdan2.github.io/PKPilot0003/>
- Uploads **0 B** — photos are never sent anywhere. Parsing, rendering and encoding all happen on this device.
- No external CDN — even the EXIF parser is bundled locally. Only the handwritten caption font loads
  `Caveat` from Google Fonts, and if that request is blocked the frame's local script face is used instead.

## Landing screen — a Film Studio style home

Opening the app shows a **landing** first (`#homeView`), not the editor. The editor sits behind it, and the
**title in the top left (`Shutterplate` · HD2Lab mark) brings you back to the landing at any time** — the
photo, frame, margins and emblem stay as they were, so `Continue editing` picks up exactly where you left off.
(The `#editor` hash goes straight to the editor — handy as a bookmark.)

- **Hero** — the official dual-skew mark, `Shutterplate`, the HD2Lab badge, a one-line subtitle, and the
  local-EXIF note.
- **Active session** — when a photo is already open, it shows its thumbnail, file name, resolution and current
  frame, with `Continue editing` to return.
- **Open photo / sample photo** two-card row — `Choose photo` (multi-photo workspace on desktop, single
  selection on phones), `Open folder` (File System Access) and drag and drop. The right card is an instant
  demo: `Landscape (sample-01.jpg)` and `Cafe portrait (sample-02.jpg)`.
- **13 SIGNATURE FRAMES** — 13 cards, each **painting the current photo (or a prototype) in that frame**, so
  tapping a card opens the editor with that frame selected (and opens sample-01 if no photo is loaded). The
  first 11 are single-cut; the last two (Contact Sheet, FYRA 04) make one plate from four photos.
- The landing scrolls inside its own box — the document itself stays locked to 100dvh. Centred at up to
  1040px on desktop, a single column on phones.
- The in-app-browser notice (KakaoTalk and friends) is shown at the top of the landing in the same way.

## Editor layout

Every control fits one 100dvh screen. There is no vertical page scroll.

1. **Canvas** — the live plate preview. Wheel/pinch zoom, drag to pan, dark/light plate background.
   No bar overlays the canvas: the viewport runs from just under the header down to the frame library.
2. **Frame library head** — the loaded photo's identity: **file name · resolution · EXIF count**, the file name
   taking the slot the `FRAME LIBRARY` label used to keep (`…` for a long name, so the row never breaks).
   With no photo open the slot falls back to `FRAME LIBRARY`. Opening files is the header's job
   (`Open files` · `Open folder` · `Image` · `Sample` · `Save` · `⋮`), and `⋮ → Clear photo` empties the plate.
3. **FRAME LIBRARY** — a horizontal slider under the stage with live miniatures of the 13 frames, name and
   feature badge; move with the wheel, drag, swipe or `←`/`→`, and a tap applies the frame instantly with an
   accent ring on the selected card. The compact shelf (56px thumbnails on a phone, 74px on a PC) leaves the
   plate the height the old file bar used to take. The strip header on the right holds **preview background
   (dark/light) and zoom (out · fit · in)** — the old canvas toolbar's work, now at the bottom.
4. **Tool bar** — `EXIF edit` · `Frame/margin` · `Emblem` · `Export`. Collapsed by default; tapping one raises
   only that control panel as a sheet (bottom sheet on mobile, closed with Esc or the backdrop).
5. **Light/Dark floating toggle** — the single shared button at the bottom right is the only theme switch. In
   the editor it sits 8px above the top edge of the bottom control panels (frame library · workspace strip ·
   tool bar) and moves aside to the left of the `4-cut` button on four-cut plates. When a panel changes
   height (frame card, workspace, safe area) the offset is measured again and it re-attaches.

### Desktop (≥ 1024px) — a two-column studio

Above 1024px the same control panels become a **fixed inspector on the right (372px)**. The bottom tool bar and
floating sheets disappear and the screen splits in two.

- **Left — main stage**: the live canvas, the one-line file bar and the 13-frame strip. Plate background and
  zoom live in the strip header on the right. Nothing covers the canvas.
- **Right — inspector**: four tabs on top (`EXIF edit` · `Frame/margin` · `Emblem` · `Export`) choose the panel,
  and sliders, text and emblems **redraw the left canvas as you change them**. A **format (JPEG/PNG) · quality ·
  save** dock is always at the inspector's foot, so saving never needs a sheet.
- Tabs are switched by click and by `←`/`→`.

### Top-right `⋮` menu — Workspace · Samples · Navigation

A dark dropdown with the same structure as Film Studio. `aria-haspopup="menu"` and `aria-expanded` are kept in
sync, it closes on outside click and Escape, and Escape returns focus to the `⋮` button (with ↑/↓ · Home/End
navigation).

| Section | Items |
| --- | --- |
| **WORKSPACE** | `Home / landing` · `Restore EXIF values` (disabled with no photo) · `Emblem colour: original colour ↔ All Black` (current state badge, one-click toggle) |
| **SAMPLES** | `Landscape (sample-01)` · `Cafe portrait (sample-02)` |
| **NAVIGATION** | `HD2Lab Web-HQ` (main portal) · `ApplicationHub` (apps catalog) |

On desktop the menu must float above the fixed inspector, so the header's stacking order is raised only while
the menu is open.

### Desktop workspace — several photos, or a folder

Opening several photos at once, or a folder, attaches a **workspace photo strip** under the frame strip. (A
one-photo folder still counts as a folder and shows the strip.)

| Action | Behaviour |
| --- | --- |
| Header `Open files` / landing card | Select several photos at once (`multiple`) |
| Header `Open folder` | Scans a folder handle with `showDirectoryPicker`; the `Subfolders` checkbox includes subfolders |
| Same button (unsupported browser) | Falls back to `<input type="file" webkitdirectory multiple>` |
| Click a strip item / `←` `→` | Switch photo — reads its EXIF again and repaints the canvas |
| `Name ↑` / `Name ↓` | Sort by path (numeric-aware) |
| Strip header | Collapse / expand |
| Export tab → `Save all (N)` | Renders each photo with its own EXIF and saves them in order, with progress |

- Switching photos **keeps the frame, margins, emblem and typeface you chose** (only EXIF values and frame
  numbers come from the new photo).
- Thumbnails are 168px canvas copies, **lazily decoded one at a time only when visible**, so a folder of
  hundreds does not delay the first photo.
- Handles and files live only in browser memory. Uploads are still **0 B**.

## The 13 signature frames

All the theme styles of the origin project `jeonghyeon-net/exif-frame` are carried over under HD2Lab naming.

| # | Frame | Origin theme | Character |
| --- | --- | --- | --- |
| 01 | **Clean Horizon** | `SIMPLE` | Minimal margin · make/model contrast · one-line exposure spec at the bottom |
| 02 | **Classic Gallery** | `ONE_LINE` | White mat · centred emblem · one spec line · serif signature |
| 03 | **Dual Deck** | `TWO_LINE` | Camera and lens on top, aperture/shutter/ISO in two columns below |
| 04 | **Strap Bar** | `STRAP` | A wide camera-neck-strap band · stitch detail · two columns left and right |
| 05 | **Film Sprocket** | `FILM` | 35mm perforations top and bottom · film-stock badge · frame number · four meta lines under the emblem · vertical ISO/SEC/F stack |
| 06 | **Studio Dark** | `LIGHTROOM` | Dark `#101110` mat · orange rail and corner brackets · mono meta |
| 07 | **Minimal Edge** | `CUSTOM_ONE_LINE` | Slim border tight to the photo · one tracked meta line |
| 08 | **Pro Studio** | `CUSTOM_TWO_LINE` | Generous band · free ratio · full spec in a 2×2 grid |
| 09 | **Instant Polaroid** | — | Wide instant-photo card at the bottom · a handwritten caption slot |
| 10 | **Frosted Glass** | — | Frosted glass that shows the photo through the margin · glass band |
| 11 | **Editorial Cover** | — | Masthead over the photo · magazine cover rhythm |
| 12 | **Contact Sheet** | — | 2×2 contact sheet · four 3:2 cells (multi-cut) |
| 13 | **FYRA 04** | — | Nordic four-cut strip · six themes (1×4 / 2×2, multi-cut) |

### Multi-cut and the four slots

The first 11 use one photo; the last two build one plate from **four**. `state.slots` holds up to four photos
and **slot 0 doubles as `state.photo`** — the EXIF sheet, single-cut frames and export all see the same photo
without a second implementation.

| Function | What it does |
| --- | --- |
| `loadSlots(files)` | Put four photos into the four slots at once (`photos 4`) |
| `setSlotFile(i, file)` | Replace one slot (tap the cell) |
| `swapSlots(a, b)` | Swap two slots (`⇄`) |
| `seedSlots()` | Fill empty slots with the photo already open |
| `loadSampleSlots()` | Fill all four from the bundled samples |

The four slots open and close with the **floating `▦ 4` button** on the stage (12px from the bottom right).
Open, the **four-cut slot panel** (`#slotBar`, 66px from the bottom right; a full-width sheet at 640px and
below) appears above the button, and it closes on an outside tap, by tapping the button again, or with
`Escape`. On multi-cut plates the library head keeps naming the photo while the four cells live in the panel.

### FYRA 04 — tap again for themes

The FYRA 04 tile is **selected on the first tap** and opens its **theme window** (`#fyraModal`) on the
**second**. Choosing a theme applies it and closes the window; `Escape` returns focus to the tile. Badges never
overlap — `NORDIC 4-CUT` top-right of the thumbnail, `tap again for themes` (or `6 THEMES` on the landing)
bottom-left.

| id | Theme | Layout |
| --- | --- | --- |
| `classic` | Classic Strip | 1×4 ivory strip + FYRA 04 / date / emblem |
| `nordic` | Nordic Grid | 2×2 off-white grid + editorial margins |
| `dark` | Dark Studio | 1×4 dark mat strip + orange accent |
| `film` | Film Strip | 1×4 perforations + #01~#04 frame numbers |
| `editorial` | Editorial Grid | 2×2 serif masthead + magazine grid |
| `minimal` | Minimal Twin | 2×2 hairline + a single emblem/date line |

### Caption typeface — frame default or custom

Under the `Caption / Photographer` fields, **Frame default** keeps each frame's own typeface. **Custom** reveals
`Sans · Serif · Mono · Script (Caveat)` chips plus `bold` and `italic` checkboxes, and repaints the caption line
only in that face (maker, exposure and date keep the frame's face). Pressing **Frame default** again restores
the original.

The Frame/margin sheet controls border padding, bottom band, rounding, **plate ratio (Auto · 1:1 · 4:5 · 5:4 ·
3:2 · 16:9)**, film stock and typeface; changing the style resets to that frame's defaults.

### Text fitting rules

Every frame uses the same one-line fitting rule: as space narrows it first tightens tracking, then shrinks the
size, down to 45% of natural size, and only then adds an ellipsis. So the exposure spec survives vertical
photos (2:3, 3:4) and long iPhone lens names or equivalent focal lengths (`24mm (24mm)`).

Frames with two columns (Studio Dark · Strap Bar · Film Sprocket · Pro Studio · Minimal Edge) measure the right
column first and give the remaining width to the left. The emblem width (brand marks run from Apple at 0.8:1 up
to Hasselblad at 12.8:1) is subtracted from the available width too, and each frame caps its emblem width, so
wide emblems and long specs never collide.

Film Sprocket's two perforation rows each take the **rail between the edge and their own rule line**, and the
holes sit at the exact centre of that rail. Because both rails have the same height, the top and bottom margins
are a **perfect mirror** (edge-to-hole gap === hole-to-rule gap), and the strip left below the rule (`flow`)
becomes the print block, so the date line touches neither the rule nor the holes. The stock badge sits in the
centre of the band between the top rail and the photo.

The bottom print block puts the **emblem at the left edge with caption, camera, lens and date each on its own
line below it** (all sharing the same left `pad`), while `ISO · SEC · F` sit in a **vertical stack** at the
opposite bottom corner, right-aligned. The right stack is measured first and its width subtracted, so the two
columns never meet even on vertical photos. So that perforations and the film badge do not vanish at 0% border
padding, Film Sprocket and Dual Deck keep a minimum top margin, and the rail height is capped at 56% of that
margin to leave room for the badge.

Strap Bar's bar is a slim band taking only 12% of the photo height — both lines ride at the top and the stitch
closes the bottom, so the margin right under the photo and the margin under the last line are both tight. The
`HD2LAB · STRAP 04` watermark sits above the bottom stitch on the right, clear of the dashed line.

### Band diet (2026-11-14) — three frames only

Three frames got shorter bottom bands with their lines re-seated. Against the photo height the bands went
**Classic Gallery 17→13% · Dual Deck 21→13.5% · Studio Dark 15.5→11.5%**, and border padding went Gallery
7→4.5% · Dual 5.5→3.8% · Studio 5.5→3.8%. **Film Sprocket (6 / 16%) and Pro Studio (8 / 25%) were reverted to
the previous contract** — their perforation-rail symmetry and 2×2 spec grid are already complete in that
placement, and a shorter band would break their print rhythm.
Because a thinner band also shrinks the same-ratio font cap, each line has its **own cap for the new band**.

- **Classic Gallery** — emblem 0.075 · spec 0.40 · rule 0.53 · serif signature 0.685 · date 0.855. The emblem
  height is capped at the raised height of the spec line below it, so even a 12.8:1 wordmark does not touch it.
- **Dual Deck** — divider 0.13 · exposure 0.42 · focal length and date 0.66 · caption 0.86.
- **Studio Dark** — rail 0.11 · emblem/body/spec line 0.44 · lens and date 0.63. The bottom footer rises
  **above** the corner brackets' horizontal arms (which shrink with the band) and starts to the right of their
  vertical arms.

## Structure

```
PKPilot0003/
├─ index.html              shell · inline SVG icon sprite (hand-made)
├─ style.css               Soft Iris / Navy & Lavender instrument shell, 100dvh compact layout
├─ app.js                  landing/editor switching · upload · EXIF · preview · frame strip · desktop inspector/workspace · ⋮ menu · export wiring
├─ js/
│  ├─ exif-core.js         EXIF → 8 normalised fields (pure functions)
│  ├─ emblems.js           11 emblems (maker marks + HD2Lab) → SVG and Canvas rendering
│  ├─ plate.js             the 13-frame layout and paint engine (preview/strip/export share it)
│  └─ vendor/exifr.full.umd.js
├─ assets/hd2-theme.*      shared theme tokens and floating light/dark toggle
├─ assets/samples/         three demo samples with embedded EXIF
├─ beta/                   the previously promoted 13-frame build, still served at /beta/
├─ tools/
│  ├─ generate_brand_assets.py   favicon set · OG image (official dual-skew mark)
│  └─ make_samples.py            sample JPEGs + EXIF
├─ tests/run_tests.py      static + browser (Playwright) checks
├─ verify/                 deployment screenshots
├─ manifest.json
└─ favicon.svg · favicon.ico · icon-192/512.png · apple-touch-icon.png · og-image.png
```

### How rendering works

Preview, strip thumbnails and export call **the same paint routine in the same plate coordinate space**. The
preview only multiplies `ctx.scale()` to fit the screen, while export draws into an original 1:1 canvas. That is
why screen, thumbnail and result never disagree on typeface, tracking or margins.

If the device cannot hold that canvas (for example iOS Safari's canvas area limit), it does not fail silently:
it retries at `4096 → 3072 → 2048px`. Both allocation refusal (`getContext()` null, canvas dimensions not
applied) and encoding failure (`toBlob` null, 0 bytes) count as failures, and only a stage that actually
produced bytes is saved. When it saves a reduced size it says so with
`device memory limit — saved safely at 4096px`.

### Branding

The header mark, the favicon and the `HD2Lab` emblem on plates all use the same official dual-skew mark.

```
M15 20L35 10V43L15 53Z   M29 24L49 14V47L29 57Z
```

The app chrome (header, panels, sheets, menu) follows the shared **Soft Iris** light theme and **Navy &
Lavender** dark theme (`#FAFAFD` / `#8B6CE8` accent, `#0B1020` / `#A78BFA` dark), with the 44px floating toggle
as the only theme switch; the plate stage keeps its own print colours. On plates the HD2Lab mark prints in the
frame accent `#ff824f`, or in the frame ink when **All Black** is selected.

## 11 maker emblems

`Sony · Canon · Nikon · Fujifilm · Samsung · Leica · Hasselblad · Apple · Lumix · OM System` plus the HD2Lab
mark use **the manufacturer's own published logo vectors**. No font impressions: the original path data was
moved over and only normalised to 100 units of height (shapes and proportions unchanged). The HD2Lab mark uses
the project's brand sheet.

The **Emblem colour** switch in the `Emblem` tab chooses between two inks.

- **Original colour** (default) — each manufacturer's official colour. Canon Red `#CC0000`, Leica Red `#E20613`
  round badge, Lumix Red `#E60012`, Fujifilm `#ED1A3D` notch, Samsung Blue `#1428A0`, OM System Blue `#0033A0`.
  On dark frames (Film Sprocket · Studio Dark) only the blues are lifted to `#3B6BFF` / `#4B83FF` so they do not
  sink into the background. Sony, Nikon, Hasselblad and Apple are single-colour wordmarks, so they print in the
  frame ink.
- **All Black (single ink)** — every mark, HD2Lab's orange included, prints in the frame's one ink colour. The
  picker tiles turn monochrome too, and the choice is part of the export cache signature so it applies at once.

Each logo's original URL, licence and trademark notice, plus the colour table, are in
[`EMBLEM-SOURCES.md`](EMBLEM-SOURCES.md).

Because brand widths differ from Apple's 0.8:1 to Hasselblad's 12.8:1, `emFit` in `plate.js` sets a maximum
emblem width per frame and reduces the height when it would overflow, keeping the original proportions.

## Export — save as a photo

`Export` renders one plate into memory first and then exports it three ways.

1. **Save to photo album / share** — on browsers where `navigator.canShare({files})` is true (iOS Safari,
   Android Chrome) the image File goes through the Web Share API. Choosing `Save image` in the iOS share sheet
   puts it in the photo album; closing the sheet simply ends quietly.
2. **Download as a file** — the fallback for browsers without a share sheet, and for desktop (same file name and
   format). The `Save` button in the desktop inspector dock takes this route directly. With two or more photos in
   the workspace, `Save all (N)` uses the same renderer to save each with its own EXIF and file name.
3. **Preview** — shows the result full screen; a long press offers `Save to Photos`, and no vertical scrolling
   appears.

iOS only allows a share sheet inside a tab, so opening the export sheet pre-renders the result and hands it over
on one tap. If the settings changed and the prepared result is stale, the result card opens and one more tap
renders again.

Format is JPEG (quality 70–100, default 95) or lossless PNG, chosen in the Export panel or the desktop dock.

## Tests

```bash
python3 tests/run_tests.py        # -v for verbose output
```

- **Static**: HTML semantics (title, viewport, OG), SVG symbol validity, asset reference integrity, brand asset
  specs, CSS tokens, 100dvh / no-scroll rules, 16px inputs and 44px touch targets, **the 13 frame definitions**,
  **no BETA badge or production link in the production header**, four-cut slot, theme window and caption toggle
  markup, no CDN use (one script-font sheet excepted)
- **Browser**: real EXIF parsing of the bundled samples, formatter unit tests, selecting and individually
  rendering all 13 frames, the **text fitting matrix** (2:3, 3:4, 3:2 × long iPhone lens names, wide emblems,
  missing EXIF × the 11 single-cut frames = 220 plates with 0 ellipses, 0 escapes from the plate, 0
  text/text, text/emblem or text/perforation overlaps, and perforations drawn even at `padPct: 0`),
  **the four-cut slot model** (open four, replace one, swap two, seed four from one, keep slots after re-reading
  EXIF), **the floating four-cut control** (fixed 12px from the stage's bottom right, panel above the button,
  closing by outside tap/Esc/re-tap, focus return), **FYRA 04** (first tap selects,
  second opens the six-theme window, each of the six paints a different 1×4/2×2 plate, window closes on apply),
  **badge separation** (`NORDIC 4-CUT` top-right and `tap again for themes` bottom-left in the strip and the
  landing tile, zero overlap area), **caption typeface** (frame default ↔ custom, four faces, bold/italic reaching
  the plate opts, custom changing the caption line only), **four-cut 1:1 export**,
  **the production header** (product name kept without a BETA badge at 390px, actions pinned right, one line),
  **Film Sprocket symmetry** (horizontal, vertical and square × default/0%/9% margin: top and bottom hole height
  and edge distance within 0.5px, equal rail heights, each hole centred in its own rail, painted holes' top/bottom
  gaps within 0.5px, the date line clear of both the rule and the perforations, room for the stock badge between
  rail and photo), **Strap Bar margins** (bar at most 13% of the photo height in all orientations at default
  padding, tight margins above the first line below the photo and above the stitch, text never touching the
  stitch), **the three-frame band diet + two reverts** (Gallery/Dual/Studio geo constants matching the app
  defaults, real bands at 13/13.5/11.5% of photo height in portrait and landscape, Film/Pro still 6/16 and 8/25),
  **Film Sprocket bottom block** (from painted glyph boxes: all four meta lines under the emblem share the same
  left edge and sit above the bottom perforation rule, and the ISO/SEC/F pairs are right-aligned in **three rows**),
  **Pro Studio 2×2 grid** (four labels in two rows and two columns at `inner/2` spacing, values under their labels,
  footer rule and caption below it), **landing · title · ⋮ menu** (landing first and the editor behind it, all 13
  cards painting their own plate, sample and frame cards opening the editor with that photo and frame, the title
  returning to the landing with an Active session that shows file name, resolution and frame and a `Continue
  editing` that keeps state, the WORKSPACE/SAMPLES/NAVIGATION sections with EXIF enable/disable, the emblem colour
  toggle, Escape close with focus return, outside-click close, and zero horizontal overflow at 390), **emblem
  colour** (canvas pixels confirming Canon/Leica/Lumix red, Samsung/OM System blue and the Fujifilm notch, zero
  brand colours under All Black, every mark in one ink, the picker SVG tiles the same, the panel toggle state
  reflected), strip selection/wheel/keyboard, opening and closing sheets, UI before and after upload,
  **export resolution** (a 5200×3400 photo saved at original 1:1 instead of being cut to 4096, a 4096px fallback
  save with a notice toast when `toBlob`/`getContext` return null) and file name, **the 1440 desktop two-column
  studio** (inspector always visible, tab switching, sliders applying at once, one-click dock save), **workspace**
  (multi-select, folder handle scan, subfolder option, thumbnails, sorting, EXIF refresh and frame settings kept on
  `←`/`→`, collapse, three-photo bulk save), the sheet path regression below 1024px, the 900 tablet sheet, and
  mobile 390/320 (zero horizontal overflow, zero vertical page scroll, one-line header, a compact frame
  library whose head carries the file name, and no desktop-only inspector, dock or workspace leaking into the
  phone layout)

On a machine without Playwright/Chromium the browser layer is SKIPped and the rest decides the result.

## Sample photos

The three files in `assets/samples/` are generated demo frames with EXIF (SONY / FUJIFILM / Canon) embedded. They
are not real photographs; they exist to demonstrate the app.

## Regenerating brand assets

```bash
python3 tools/generate_brand_assets.py
python3 tools/make_samples.py --src-dir <folder of source PNG samples>
```

HD2Lab standard: favicon SVG/ICO/32/180/192/512 and a 1200×630 OG card, with the dual-skew mark.
