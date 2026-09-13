# salaryman.co — responsive rebuild

A static, dependency-free copy of https://salaryman.co: full-screen looping
video of an office block at night, one italic **salaryman** link to Instagram,
and the copyright line underneath.

```
salaryman/
├── index.html          the page (+ a few lines of JS for video source / autoplay)
├── assets/
│   ├── style.css       all layout — the positioning maths lives here
│   ├── bg-1080.mp4     1728x1080 h264, 35 s, 12.9 MB  (pulled from the live site's stream)
│   ├── bg-360.mp4      576x360, 6.4 MB — served only on data-saver / 2g-3g connections
│   ├── poster.jpg      first frame; shows while the video loads / if autoplay is blocked
│   └── favicon.svg
└── README.md
```

## What was fixed

**1. The link drifted around the footage.** Squarespace placed it on a fluid
grid that scales with the *browser window*, while the video is cropped with
`object-fit: cover` — two different coordinate systems, so the link only lined
up with the black pocket at one window size, and on phones Squarespace swaps
to a completely different mobile grid.

Now the `<video>` box always keeps the footage's 1728:1080 shape and is
positioned so that **one fixed point of the frame** (`--anchor-x: 0.8336`,
`--anchor-y: 0.585` — centred on the dark column above the pocket, midway
between the windows above and below)
lands on one point of the screen. The link is placed on that same screen
point, so it is pinned to the same pixels of the footage on every device.
Its font size is a fraction of the displayed video width, so it stays in
proportion to the pocket too. Pure CSS, no JavaScript involved.

On very narrow screens the anchor is pulled inward (`--cta-half`) so the
link can never be clipped at the edge.

**2. Mobile was zoomed in ~5×.** Cover-cropping a 16:10 video into a 9:19.5
phone screen leaves ~20 % of the frame. On phones (portrait, ≤ 500 px wide)
the video is now sized to `--portrait-video-height` (70 %) of the screen
height and letterboxed on black — the footage is black at its edges so the
bars are invisible. Result: ~2.4× instead of ~4.9×, four rows of windows
visible instead of one and a bit. Every other window — desktop, tablet, tall
or ultra-wide — is filled edge to edge, exactly like the original.

## Knobs (top of `assets/style.css`)

| Variable | Default | Effect |
|---|---|---|
| `--anchor-x`, `--anchor-y` | `0.8336`, `0.585` | Where in the frame the link sits (fractions of width/height): the centre line of the dark column above the pocket, midway between the windows above and below. |
| `--cta-scale` | `0.01656` | Link text size relative to displayed video width. |
| `--portrait-video-height` | `70` | Phone (portrait ≤ 500 px) zoom. `100` = fill the screen like every other size. |
| `--cta-half` | `110px` | Minimum distance of the link's centre from the screen edge. |

## Replacing the video

Drop your master export in as `assets/bg-1080.mp4` (h264, mp4, any size —
if the aspect ratio is not 1.6, update `--video-ar` and re-check the anchor).
Regenerate `poster.jpg` from its first frame, and optionally a small
`bg-360.mp4`. Keep the file *muted-friendly*: autoplay only works muted.

## Hosting

It is plain HTML/CSS, so anything that serves static files works: Netlify,
Vercel, Cloudflare Pages, GitHub Pages, or an S3 bucket. Upload the contents
of this folder and point the `salaryman.co` DNS at it. Squarespace cannot
host raw HTML pages, so this replaces the Squarespace site rather than
living inside it. Serve the mp4s with `Cache-Control: max-age=31536000`
if the host lets you — they are the only heavy assets.

## Preview locally

From the parent folder: `python -m http.server 8080` then open
http://localhost:8080/salaryman/ (or just double-click `index.html`).
