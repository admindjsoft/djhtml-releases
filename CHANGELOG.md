# Changelog

## v1.5.7 — 2026-07-17

- **Smooth playback on CasparCG interlaced channels (1080i50/60).** Exported
  templates now deliver a fresh frame to CasparCG for every field instead of
  every other one, eliminating the judder that made animations stutter on
  interlaced channels even though they looked smooth in Chrome and other
  playout servers. The keep-alive mechanism costs nothing while a graphic is
  holding still on screen.
- **Decode-ahead for image-sequence layers.** Upcoming frames of embedded
  image banks are decoded a few frames in advance, off the critical path —
  heavy full-frame intros now play at full speed from the very first frame
  instead of slowing down and stuttering while PNG frames decode. The
  lookahead correctly follows loop restarts and clip cuts, and the small
  frame cache is released as soon as the graphic leaves the screen.
- **Clean playout logs.** Per-frame Dynamic Anchor (X) diagnostics are no
  longer written to the CasparCG log during normal playback. For
  troubleshooting, set `window._kfaDebug = 1` in the template's DevTools
  console (CasparCG remote-debugging port) to turn them back on.

## v1.5.6 — 2026-07-13

- **Smart video memory — decode on demand.** Importing a
  video no longer loads the whole file into RAM up front; frames are decoded as
  you scrub or play, and a green **ready-for-playback** bar on the timeline ruler
  shows the cached range. A memory budget (**Settings → Memory**, adjustable with
  the new Video cache slider) keeps RAM in check by releasing the oldest frames
  once it fills up. Preview resolution is adaptive for smooth scrubbing —
  **Full / Half / Quarter**, with a crisp re-render when you pause — and you can
  pin it to Full whenever you want full quality at all times.
- **Child Text Layer & Pin/Follow now work in nested compositions with dynamic
  text.** A child text attached inside a nested composition and placed into a main
  composition now shows correctly from the first frame — even when you change its
  text at runtime (e.g. via CasparCG) — instead of staying hidden until the
  release keyframe. **Pin Right Edge** and **Follow** keep the words aligned on the
  shared guideline for any typed length, including while a Font Size animation is
  running.
- **Deleting an image or video now clears it from its container.** Removing an
  image or video from the Project panel immediately empties any Image / Video
  Loader that was using it — you no longer have to move the playhead for the
  container to clear. Works for containers inside nested compositions too, and
  Undo restores both the asset and the container assignment.
- **More under-the-hood fixes** across the editor, preview, and export paths.

## v1.5.5 — 2026-07-11

- **Use a layer as a mask (alpha matte).** Any graphic layer can now act as a
  mask for another layer: select it, press **MASK** in the Inspector's
  **Use as Mask** section and pick the target. The mask's real alpha does the
  cutting — soft gradients give feathered edges — and an **Invert** toggle
  flips the matte. The mask stays a normal layer ([MASK] badge, yellow shape
  outline), so you move, scale and keyframe it with the standard gizmos.
  Works identically in the editor preview, HTML export and nested compositions.
- **Two-Part Text — one text row, two fonts, two data fields.** A text layer
  can render as two inline runs on a shared baseline (e.g. first name regular +
  surname bold), each with its own font, size, weight, style, color and its own
  update key. Fit Width compresses the combined row as one text. Enable it with
  the **Two-Part Text** checkbox in the text properties.
- **Child Text Layer — attach & release.** Attach a text layer to a parent text
  (**right-click → Child Of (text)**): while attached it inherits everything —
  font, style, color, Fit Width compression — and always sits exactly one space
  after the parent's text, while keeping its own update key. Right-click a
  Position keyframe and choose **Release Child Here** to detach it mid-animation;
  the handoff is seamless for any typed text length, so two dynamic words can
  enter as one row and then split into stacked lines.
- **Dynamic Anchor (X) — Pin Right Edge & Follow for dynamic text.** Tag a
  Position keyframe (**right-click → Dynamic Anchor (X)**) to pin a text's
  right edge at its authored position, or to make it follow the end of another
  text, regardless of how long the typed content is at runtime. The correction
  blends in along the tagged move with its easing; tagged keyframes show an
  orange outline in the timeline. Perfect for aligning mixed left/right-aligned
  dynamic texts on a shared vertical guideline.
- **Video Loader — runtime video container.** The video counterpart of the
  Image Loader: drag a container with the new toolbar tool and the playout
  system fills it with a video URL at runtime (autoplay, loop, muted). Same fit
  modes as the Image Loader. For CasparCG use WebM (VP9) with alpha — it plays
  natively in the built-in browser, transparency included.
- **Faster HTML preview and export.** WebP encoding uses a faster compression
  profile, and previews render with a lighter quality cap — noticeably shorter
  export and preview times with no visible quality change in broadcast use.
- **Export fixes for nested compositions.** Baked nested layers that sit above
  an image loader / text / shape inside a nested composition now keep the
  correct stacking order in the CasparCG export at every nesting depth; photos
  with EXIF rotation no longer appear rotated in the HTML preview.
- **A lot of under-the-hood improvements** to rendering, performance, and the
  export pipelines, plus bug fixes across the editor, preview, and export paths.

## v1.5.4 — 2026-05-19

- **New Roll layer — vertical scrolling text and image strip.** Add a credits-style
  roll via **Layers → New Layer → New Roll Layer…** The dedicated editor lets you
  type or paste multi-line text and drop inline images (logos, player photos)
  between lines. Inspector controls cover speed (1–10, same scale as Crawl),
  direction (Bottom → Top default, or Top → Bottom), and an optional Loop toggle
  (off by default — the roll ends at the next stop marker). The whole roll wrap
  is a regular layer, so it accepts the standard transform / scale / rotate /
  animation pipeline.
- **Stop marker inside the Roll editor.** Right-click any line in the editor
  and choose **Add Stop Marker** to pause the roll when that line reaches the
  vertical center of the composition. CasparCG `CG NEXT` (or the matching
  playout trigger) resumes the scroll — useful for revealing a headline,
  winner, or sponsor card mid-roll.
- **A lot of under-the-hood improvements** to rendering, performance, and the
  HTML / Lottie export pipelines.
- **A lot of bug fixes** across the editor, preview, and export paths.

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
