# Changelog

## v1.5.3 — 2026-05-10

- **Multi-stop markers — pause and continue through several scripted poses.**
  Set any number of stop markers along a composition's timeline. Play stops at
  the first marker; CasparCG `CG NEXT` (vMix Continue / OBS continue / OGraf
  step) advances pause-to-pause; the final `STOP` triggers the outro from
  whatever pose is on screen at that moment. Drag markers on the ruler to
  retime, right-click to remove. Existing single-stop projects keep working
  unchanged.
- **Next button in HTML Preview.** Test the multi-stop flow without leaving
  the app — the new ⏭ Next button in the HTML Preview window calls
  `window.next()`, the same function CasparCG/vMix/OBS call when an operator
  hits NEXT.
- **Jump to start / end buttons in the playback toolbar.** ⏮ jumps the
  playhead to frame 0, ⏭ jumps it to the last frame.
- **Tabular Nums now matches between WPF preview and HTML output.** Tabular
  text (timers, scores, "168:12"-style strings) was rendering ~30 % wider in
  HTML than in the editor because WPF's `FormattedText` API silently ignores
  the OpenType `tnum` feature. The WPF preview now measures and renders digits
  with the true tabular advance, so what you see in the editor matches what
  CasparCG / OBS / vMix plays back. Toggling Tabular Nums in the inspector
  also resizes the selection bounds and inline editor immediately.
- **RotateSpin OUT transitions with negative rotation no longer collapse into
  a crossfade.** Negative or overflow angles were being clamped to `[0, 360]`
  partway through the transform pipeline, so the rotation reduced to zero and
  the engine fell back to a fade. Negative spins now distribute correctly
  across full turns and play the configured rotation.
- **Special-character text fill (block/dash characters used as foul indicators,
  battery bars, etc.) renders the configured gradient in HTML.** The gradient
  was landing far enough off the actual glyph that the character read as
  transparent and the dark layer underneath showed through. Position is also
  no longer shifted a few pixels down on layers with very large `line-height`
  relative to the font size.
- **Gradient text fill survives a copy into a nested composition.** Pasting a
  layer with a gradient into a nested comp used to drop the gradient settings
  on the inner clip, so it rendered as a solid colour instead. The gradient
  parameters now travel with the layer.

## v1.5.2 — 2026-05-03

- **New trial model — no time limit, no feature limits.** The free trial used to
  run out after 7 days and capped you at 30 frames, 2 compositions, and 30
  characters of crawl text. All of those limits are gone — the trial gives you
  the full app, indefinitely.
- **Watermark on exports.** The trade-off: files exported in trial mode show a
  small "DJ HTML Creator — Trial" badge in the bottom-right corner. Applies to
  HTML (CasparCG / OBS / vMix / SPX / OGraf), Lottie JSON, MP4 / MOV video, and
  PNG / JPEG / TGA image sequences. In-app preview and `.htmc` project files
  stay clean. Activate a license to remove the watermark.
- **Existing trial users — your trial is back.** If your 7-day trial had already
  expired, install v1.5.2 over your existing version and the app reopens in
  unlimited trial mode automatically. Your projects and settings are kept.
- **Lapsed subscriptions no longer block the app.** If your monthly/annual
  subscription expires, you keep using the app — exports just get the trial
  watermark until you renew. Previously the app refused to start.

## v1.5.1 — 2026-05-02

- **Import HTML** — open an existing HTML template back as a fully editable project
  (**Import → Import HTML…**). Compositions, layers, text, image loaders, image
  sequences, fonts, masks, transitions, effects and animation are all reconstructed.
- **Round-trip your own exports** — HTML files exported from DJ HTML Creator
  re-import losslessly, with embedded image sequences and fonts extracted to disk
  alongside the new project file.
- **Cross-tool import** — HTML templates produced by other broadcast template tools
  are auto-detected and converted into native DJ HTML Creator projects, so you can
  edit them just like a project you created from scratch.

## v1.5.0 — 2026-05-01

- **Exports up to 8× smaller** — HTML templates now use WebP instead of PNG by default.
  Same visual quality, dramatically smaller files (e.g. 78 MB → ~12 MB on a 1080p video clip).
- **Exports 6–8× faster** — frame encoding runs in parallel, with up to 8 ffmpeg
  workers instead of one at a time.
- **New Export Settings dialog** (Settings menu) — pick the format (WebP / PNG) and
  quality (Lossless, Q95, Q90, Q85, Q80, Q75). Default is WebP Q90 — visually
  identical to PNG.
- **Works for everything you import** — PNG, JPG, BMP, TGA, PSD, plus all video
  formats (MP4, MOV/ProRes, AVI, MKV, WebM). All converted to WebP automatically
  when WebP is selected.
- **Works in every HTML export** — CasparCG, SPX, OBS, vMix, OGraf, and single-frame
  HTML.
- Cache menu renamed to Settings.
- Lottie export still uses PNG for mobile player compatibility.
