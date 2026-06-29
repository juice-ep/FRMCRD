# FRMCRD

**A browser-based shot data card generator for intentional photographers.**

Drop a photo. Extract its EXIF. Resolve the location in local script. Export a clean metadata card — timestamp, place, aspect ratio, focal length, ISO, shutter, EV — ready to share.

No server. No account. No upload. Everything runs locally in the browser.

---

## The Build Story

This started as a question: *what would a share card look like if it told the truth about a photo?*

Most phone cameras hide what they did to an image — tone-mapping, multi-frame stacking, AI sharpening — behind a single JPEG. Apps like [VWFNDR™ + MBL](https://www.vwfndr.camera) push back on this. MBL shoots unprocessed Bayer RAW and embeds Content Credentials at capture, so the image carries verifiable proof of how it was made. It's a camera app built around trust, authorship, and the direct relationship between photographer and image.

FRMCRD is a homage to that ethos, extended into the sharing layer.

When you shoot with a camera that respects the image — capturing real ISO, real shutter speed, real focal length — that metadata deserves to travel with the photo when you share it. Not buried in an invisible EXIF block, but on the face of the card, the way film photographers wrote exposure notes on the back of a print.

FRMCRD reads what the sensor recorded and surfaces it. Nothing more.

---

## Features

**EXIF extraction** — ISO, shutter speed, focal length (35mm equivalent), EV compensation, capture timestamp, and aspect ratio are read directly from the image file in the browser. No file leaves your device for this step.

**Aspect ratio detection** — calculated from pixel dimensions and snapped to the nearest standard camera ratio (1:1, 4:3, 3:2, 16:9, etc.).

**Reverse geocoding in local script** — if your image carries GPS coordinates, FRMCRD can resolve them to a place name via [Nominatim](https://nominatim.openstreetmap.org) (OpenStreetMap). The result is returned in the location's native script — 東京都, 서울특별시, القاهرة — not a transliteration. Only coordinates are transmitted; no image data leaves the device. Requires explicit user confirmation before any network request is made.

**EXIF Artist signing** — if your camera app writes an `Artist` tag (or Windows `XPAuthor`), FRMCRD auto-populates the photographer field from it and marks it as signed from EXIF.

**Device GPS fallback** — a secondary option to pull current location from the browser's geolocation API, for images without embedded GPS. Requires HTTPS or localhost (see note below).

**Card export** — renders the card to a 2× resolution JPEG via html2canvas for clean output at any display size.

---

## Card Layout

```
┌─────────────────────────────────────────────────┐
│ 2026.06.14 · 09:32              東京都 · JP      │  ← timestamp · location
├─────────────────────────────────────────────────┤
│                                                 │
│                   photo                         │
│                                                 │
├─────────────────────────────────────────────────┤
│ 3:4 · 26mm · ISO 50 · 1/2000 · +0.3EV          │  ← shot data
└─────────────────────────────────────────────────┘
```

All card text is white on black, bold, monospace. Designed to survive compression on social platforms.

---

## Usage

FRMCRD is a single HTML file with no build step and no dependencies to install.

**Option 1 — open directly**

```
open frmcrd.html
```

Works for everything except device GPS (see below).

**Option 2 — serve locally** (enables device GPS)

```bash
python3 -m http.server 8080
```

Then open `http://localhost:8080/frmcrd.html`.

**Option 3 — host anywhere static**

Drop `frmcrd.html` onto GitHub Pages, Netlify, Vercel, or any static host. Device GPS works from any HTTPS origin.

---

## Privacy

| Action | Data transmitted | Destination |
|---|---|---|
| Load image | None | — |
| EXIF extraction | None | — |
| IMAGE GPS resolve | GPS coordinates only | Nominatim / OpenStreetMap |
| DEVICE GPS resolve | GPS coordinates only | Nominatim / OpenStreetMap |
| Save card | None | — |

The Nominatim request is gated behind an explicit confirmation dialog. No request is made silently.

---

## GPS / Geolocation Note

Browsers block `navigator.geolocation` on `file://` URLs — this is a browser security policy, not something FRMCRD can bypass. The **IMAGE GPS** button reads coordinates directly from EXIF and is unaffected. The **DEVICE GPS** button requires a served URL:

```bash
# Python
python3 -m http.server 8080

# Node
npx serve .
```

---

## Stack

| Piece | What it does |
|---|---|
| [exif-js](https://github.com/exif-js/exif-js) | EXIF extraction from JPEG/TIFF in the browser |
| [html2canvas](https://html2canvas.hertzen.com) | Renders the card DOM to a downloadable canvas |
| [Nominatim](https://nominatim.openstreetmap.org) | Reverse geocoding via OpenStreetMap |
| Vanilla HTML/CSS/JS | Everything else |

Both JS libraries are loaded from cdnjs. After first load they are browser-cached; the tool works offline after that (except for geocoding, which requires a network request by definition).

---

## Inspiration

[VWFNDR™ + MBL](https://www.vwfndr.camera/mbl) — a free Android camera built around unprocessed Bayer RAW capture and C2PA Content Credentials, made in Tokyo. FRMCRD is an independent homage, unaffiliated with VWFNDR™.

> *Real photography must remain in people's hands.*

---

## License

MIT
