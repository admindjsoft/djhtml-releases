# Changelog

## v1.6.1 — 2026-09-04

- **Layers with more than one mask are visible again in the exported template.**
  A layer carrying two or more masks, or a single feathered mask, could come out
  of the export invisible while the editor preview showed it correctly. The masks
  themselves were exported intact — they were simply drawn in the wrong place. A
  mask stays where you authored it in the composition, and the export applies it
  through a wrapper that does not move with its layer; the outline-shaped masks
  already accounted for that, but the two kinds that are drawn as an image —
  multiple masks combined together, and a feathered edge — were still positioned
  as though they rode along with the layer. On a lower third sitting near the
  bottom of frame that pushed the mask right off the top of the screen, so the
  layer was masked away entirely. Both now land where they were drawn.

## v1.6.0 — 2026-08-30

- **Play, Next and Stop in the editor preview.** Checking how a template behaves
  at its stop markers meant exporting it and opening the HTML preview window.
  Three transport buttons now sit beside the marker button, above the frame
  counter: **Play** runs from the start of the work area and holds at the first
  stop marker, **Next** jumps straight to the following marker and carries on
  from there, and **Stop** jumps to the last marker and plays the outro. The
  jumps are immediate, so there is no waiting for the animation to reach a pose
  before moving on. Ordinary playback is unchanged — it still runs straight
  through the markers as before.
- **Font weight is now chosen from a list, and can be animated.** The inspector
  used to offer only a Bold switch, so a family's Light, Medium, SemiBold or
  Black cuts were out of reach even though the project file, the editor preview
  and the HTML export all understood them. Text layers now carry a **Weight**
  dropdown that lists only the cuts the chosen family really has, for both fonts
  of a **Two-Part Text** layer. Bold stays as a shortcut and now picks the
  nearest heavier cut the family actually offers. Changing family or italic no
  longer silently resets the chosen cut — it is carried over to the closest cut
  the new family has, which matters for italics because many families ship fewer
  italic cuts than upright ones.
- **Weight is a keyframed property.** Text layers get a **Weight** row in the
  layer panel with the usual keyframe diamond, so a caption can grow from Light
  to Black over time. Between keyframes the weight is interpolated as a number
  and then snapped to a cut the family really has, so the editor preview and the
  exported HTML always land on the same font file — a family with six cuts gives
  a stepped ramp, one with only regular and bold gives a single switch halfway.
  Every cut the animation passes through is embedded in the exported HTML. Hold
  interpolation is available on the row as usual if a hard switch is wanted.
- **The second font of a Two-Part Text layer has its own animated weight.** It
  used to be selectable but frozen, because the second run followed the first by
  a size ratio and a weight cannot be scaled that way. It now has its own
  **Weight 2** row in the layer panel, with its own keyframe diamond, so the two
  runs of one line can move between cuts independently — a name that thickens
  while the surname stays Light, for instance. The row is present on every text
  layer and comes to life once Two-Part Text is switched on, so turning the
  feature on or off never shifts the keyframe markers of the rows below it.
  An attached child text layer is unaffected: it still follows its parent's
  animated weight, as it does for size.
- **Imported fonts now offer their cuts in the Weight list.** Importing an HTML
  template brings in every cut it uses as its own project font — "Changa",
  "Changa Light", "Changa Medium" — and the Weight list saw those as three
  unrelated families, so it showed a single locked cut for each. Cuts of the
  same family are now recognised as belonging together and offered as a list;
  picking one moves the layer to that cut. A family that only came in with one
  cut is untouched, a name that already spells out its own cut is never
  re-labelled, and system fonts behave exactly as before. Because an imported
  font renders the cut stored inside its file, the Weight row for such a layer
  offers the choice but not the keyframe diamond.
- **Roll layers can animate their weight.** The rolling credits baked the font
  into the rolled lines, so the Weight keyframe button was switched off for
  them; the lines now take the weight from the layer itself and the row behaves
  like any other text layer. The rolled content is no longer rebuilt when the
  weight changes, so a roll never jumps back to its start mid-animation.
- **The font size of a Roll layer can no longer be keyframed.** The diamond was
  offered but the animation never reached the exported template: a roll measures
  the height of one pass once, when it is set up, and uses that to know where the
  loop seam is — so a size that grows mid-roll would leave that measurement stale
  and the seam would visibly jump. The size is baked into the rolled lines for that
  reason, and the row now says so instead of quietly doing nothing. Weight is not
  affected and animates normally, because it only changes glyph widths.
- Known limits of this first version: the **Lottie** export carries the static
  cut only.
- **Lottie: weights other than Light, SemiBold and Bold no longer come out
  regular.** The exporter recognised three cuts by name and silently mapped
  everything else — Thin, ExtraLight, Medium, ExtraBold, Black — to regular. It
  now reads the real weight. Two cuts of the same family also used to be written
  under the same font name, so a Two-Part Text layer using both had one of them
  rendered in the other's cut; each cut is now named separately.
- **Roll layers keep their weight.** The roll line builder recognised nine
  weight names and turned everything else — including the common aliases
  UltraBold, DemiBold and Heavy — into regular.
- **Text no longer exports the cut from whatever frame you were parked on.**
  The second font of a Two-Part Text layer, the rolled lines of a Roll layer,
  the single-frame HTML export, the Lottie export and the Bold switch of the
  second font all read the live per-frame weight instead of the authored one,
  so exporting while scrubbed into the middle of an animation could ship the
  wrong cut.
- **Saving no longer records the size or weight from whatever frame you were
  parked on.** Projects saved through the .htmc writer stored the live per-frame
  values instead of the authored ones, so scrubbing across a font-size animation
  and then saving could bake that frame's size into the project.

## v1.5.9.1 — 2026-08-18

- **A layer named "0" can now be controlled from the playout host.** Element
  numbers are read from layer names, and zero was mistaken for "this layer has
  no number at all". Such a layer was left out of its element's group and
  turned into a separate opening group of its own — one with no number, which
  meant no `show`, `showBar` or `level` command could ever address it, and the
  layer stayed on screen when the rest of its element went down. Zero is now
  treated as the valid number it is, so a layer named "0" joins element 0 like
  any other.

## v1.5.9 — 2026-07-31

- **OGraf graphics now play their out-animation.** The OGraf wrapper reported
  a graphic as finished the moment its out-animation started, and renderers
  took that as permission to remove it — so the graphic vanished on Stop
  instead of animating off, whether or not it was sitting at a holding point.
  The renderer is now told only once the outro has really finished, with a
  safety timeout so it can never be left waiting. Disposing a graphic also
  stops every loop it started — timeline, crawl, roll and the idle keep-alive —
  instead of leaving them running inside the renderer.
- **Bold and italic now export as the real font.** Only one font file per
  family was ever embedded, so a layer set to Bold shipped with the regular
  file — and because exported templates never fake a weight, it came out plain
  regular. For families whose Light cut is also registered as the regular one
  (Segoe UI, Calibri), even ordinary text could be exported in Light. Every
  family, weight and italic combination a project uses is now embedded as its
  own font file, matched to the one the editor preview draws with. The second
  font of a **Two-Part Text** layer, which was never collected at all, is
  included too, and names that already denote a cut ("Segoe UI Semibold") no
  longer fall out of the export. Importing an HTML file keeps every cut of a
  family — the extra ones arrive as their own project fonts while the regular
  one keeps the plain family name, so existing layers are unaffected — and each
  imported text layer lands on the cut it was written in, so a bold title comes
  back bold instead of resolving to the family's regular file. Applies to the
  CasparCG HTML export and to OGraf packages alike.
- **Two-part and child text no longer sit high in their layer box.** Every text
  layer's box starts at the top of a fixed reference glyph, so the same font and
  size always give the same box — but the two-run builder measured from the
  typographic line instead. A **Two-Part Text** layer, and a parent while a
  **Child Of (text)** layer was attached to it, therefore drew its words that
  much higher than the identical text drawn as a plain layer, and dropped into
  place at the child's release frame when the plain builder took over. On a
  116 px face that was a visible ten-pixel lift for the whole attached pair.
  Both runs now use the same reference box as every other text layer, which also
  puts the editor preview back on the same origin the exported HTML was already
  compensating for. The same was true sideways: the box began at the pen position
  rather than at the first glyph, so these layers also drew a fraction to the
  right of where the exported template put them. Both axes now start on the ink.
  Note that existing Two-Part Text layers move down by the vertical gap — roughly
  a tenth of the font size — and a little to the left, and may want their
  position nudged; exported templates do not move, since that is where they were
  drawing all along.
- **Drive an element straight to a level from the playout host.** In templates
  where each element climbs through several stop markers — a bar stacking
  segment by segment, a lane rising wave by wave — the `level` and `levels`
  keys of `CG UPDATE` now put an element exactly where you want it instead of
  stepping it there with `CG NEXT`. The level is absolute: 0 is the base pose,
  each higher number is one more completed move, a negative value hides the
  element, and re-sending the value it already has does nothing — so a live
  scoreboard can simply keep resending its current state. A lower level walks
  the same baked animation backwards. Elements move in parallel, each at its
  own pace, so raising three at once takes as long as the slowest one rather
  than all three in a row, and a command arriving mid-move starts its element
  right away. `showBar` / `hideBar` spotlight swaps stay sequential, as before.
- **Play a single segment on demand.** Templates built as a series of separate
  moves — six transitions separated by stop markers, say — could only be driven
  in order, so reaching the third move meant playing the first two. A `seg`
  field sent with `CG UPDATE` now plays exactly one of them, in any order:
  `seg` 1 is the stretch between the first and second stop marker, `seg` 2 the
  one after it, and the opening move stays plain `CG PLAY`. The graphic snaps
  to that segment's starting pose and animates through to its stop marker with
  nothing replayed on the way there. `seg` rides the same `CG UPDATE` path as
  `show` and `level`, so it also works on hosts that accept `CG INVOKE` but
  never actually run it.
- **Per-element control now works when moves run back to back.** The export
  used to work out which layers belong to which element from timing alone, by
  looking for gaps between their animations. Where every move starts exactly
  where the previous one ended there are no gaps, so the layers collapsed into
  one or two useless groups and `showBar`, `hideBar` and the level commands had
  nothing to address. When layer names describe more elements than the timing
  does, the names now win: a trailing number is the element number (`Bar3`,
  `Lane 7`), and the `Talas` wave form keeps an element's successive steps
  apart, with the last two digits reading as the step. Name prefixes may
  contain spaces, underscores and hyphens. Templates whose elements do enter at
  separate times keep exactly the grouping they had before.
- **Text and shape sizes no longer drift to the playhead.** While a layer has
  **Font Size** or shape-size keyframes, the editor rewrites that layer's size
  on every frame in order to draw it — and saving recorded that momentary value
  as the layer's real size. A title authored at 170 could quietly come back at
  120 simply because the playhead happened to sit on a smaller keyframe when
  the project was saved, when you switched composition, or when the layer was
  precomposed; copying a layer baked in the size from the frame you copied on.
  The authored size is now kept apart from the animated per-frame one, so
  saving, precomposing and copy/paste keep the size you actually set. Exports
  still bake the animation itself, exactly as before.
- **Layers keep their proportions by default.** A chain toggle now sits beside
  **Scale** in the layer's transform controls, and the **Constrain
  proportions** checkbox in **Shape / Image Size (px)** is no longer hidden on
  everything except ellipses. Both are on by default: dragging a corner handle
  scales evenly without holding a key, and typing one dimension pulls the other
  along. Hold **Shift** while dragging to scale a layer freely for that one
  drag. Note the change of behaviour — shape and image size handles now keep
  the ratio unless you clear the checkbox, where they used to move one axis
  only; there **Shift** can only add the constraint, so untick the box when you
  want a single axis. The choice is per layer and lasts for the session. Mask
  handles are deliberately left out of both and stay single-axis, with
  **Shift** as the manual override.
- **The mask anchor point sits where the numbers say.** The yellow crosshair
  for a mask is now drawn — and dragged — through exactly the transform the
  mask itself is drawn with. On a scaled shape layer it used to sit up and to
  the right of the mask, so the pivot you saw and the values in **Anchor
  Point** disagreed with where the mask really was. Typing or scrubbing an
  anchor value now gets the same Pan Behind correction as dragging the
  crosshair, instead of making the mask jump. A new mask on a scaled shape
  layer also starts out matching the layer instead of stopping short of its
  edges. Masks in saved projects open exactly where they were.
- **Snapping and the on-canvas frame tell the truth on transformed layers.**
  The layer you drag is now measured by its real on-screen footprint, like
  everything it snaps to — canvas edges and centre, manual guides, the grids
  and other layers — so rotated and skewed layers land on the guide they light
  up instead of coming to rest beside it. The selection frame, handles, anchor
  marker and mask outlines also follow **Zoom**, **Scale X/Y** and **3D Flip**
  transitions: parking the playhead inside one of those used to show the layer
  growing, shrinking or flipping while the overlay stayed frozen at its resting
  size.
- **Copy and paste carries the whole layer.** A layer with more than one mask
  pasted with exactly one, silently dropping the rest — and every mask's name
  and its Add / Subtract mode went missing even when there was only one. A text
  layer attached through **Child Of (text)** pasted still pointing at the
  parent in the original composition, and **Dynamic Anchor (X)** tags, **Size
  Binding**, an unlinked Scale W/H and a shape collapsed to zero were all left
  behind. All of that now travels with the layer, and a child text layer whose
  parent is missing from the composition you are working in draws itself
  instead of staying invisible.
- **More under-the-hood fixes** across the editor, preview, and export paths.

## v1.5.8 — 2026-07-21

- **Heavy 4K exports no longer freeze the computer.** Exporting a large,
  multi-thousand-frame 4K project used to build every frame in RAM before
  encoding — enough to exhaust system memory and lock up Windows. The export
  now renders and encodes in small windows, so peak memory stays low no matter
  how long the composition is. A pre-flight estimate warns you before an
  export that looks too big to finish, and a memory watchdog stops the export
  cleanly (instead of taking the whole machine down) if the system runs
  critically low.
- **Dramatically faster HTML export.** Compositions where many baked layers
  repeat the same few images — nested comps, duplicated players, mostly-static
  overlays — are detected and rendered once instead of thousands of times.
  Templates that used to take tens of minutes now export in a fraction of the
  time, with pixel-identical output.
- **Rock-solid playback speed on every playout host.** Exported templates now
  keep exact composition speed even when the host delivers frames slower than
  the project's FPS (for example a 50 fps template on an interlaced CasparCG
  channel, or a host running its browser at a lower tick rate). Animations that
  looked like they were "not really running at 50 fps" — dragging or stuttering
  — now play at the correct wall-clock speed by skipping intermediate frames
  instead of slowing down, while stop poses and the outro are never skipped.
- **NEXT reacts instantly, even mid-animation.** Pressing `CG NEXT` (vMix
  Continue / OBS continue) while a sequence is still animating now jumps to the
  upcoming stop and keeps playing straight toward the next one — no more
  double-press and no waiting for the pose to settle first. On the last stop it
  triggers the outro, exactly as before.
- **On-screen playback diagnostics.** Add `?diag` to a template's URL, or send
  `CG INVOKE diag`, to show a small HUD with the real refresh rate, painted
  frames per second, timeline speed and current frame — a quick way to confirm
  a playout host is actually feeding the template at full frame rate.
- **Masks stay exactly where you put them.** Masks are now anchored in
  composition space and move only through their own keyframes. Moving,
  scaling, or retyping the layer under a mask — including dynamic text that
  grows or shrinks and right-pinned names — no longer drags the mask off
  position. The editor preview and the CasparCG export match by construction,
  in nested compositions too. Existing projects are converted automatically on
  first open.
- **Import WebP images.** `.webp` files can now be imported into the Project
  panel and used like any other still or sequence, including transparency.
- **Editor fixes.** Font Size edits made on layers with keyframes no longer
  revert when you move the playhead; stray "ghost" keyframe diamonds no longer
  linger while dragging; and the timeline no longer jumps back to frame 0 when
  you click another layer while zoomed in.
- **More under-the-hood fixes** across the editor, preview, and export paths.

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
