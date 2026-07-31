# GAP_LOG.md — Assumptions beyond the Brand OS spec, for ratification

Mirrors the format of `~/tcg-brand-os/governance/gap-log.md`: everything here is additive or a
documented substitution, nothing changes a canonical hex, font, or spacing value.

1. **Logo asset substituted.** `TCG_Logo_No_Background.png` as supplied in Downloads has a
   checkerboard transparency pattern baked into flattened RGB pixels (verified with PIL:
   `mode=RGB`, no alpha channel) — it is a flattened preview export, not an actually-transparent
   asset, and rendering it as supplied would show a literal gray-and-white checkerboard square
   in the deck. Substituted `~/tcg-brand-os/public/brand-assets/tcg-ring-mark.png` — the same
   artwork, same pixel dimensions as `~/Downloads/TCG Logo.png`, true RGBA alpha — which is the
   exact file the Brand OS's own `<RingMark>` component uses as the governed primary identifier.
   Same ink (#484848), correctly transparent. Needs ratification: confirm this is acceptable as
   the canonical "no background" source file going forward, or supply a corrected export.

2. **`filter:brightness(0) invert(1)` + `mix-blend-mode:difference` logo-on-dark technique
   dropped.** Attempted first (matching a common "invert for dark backgrounds" pattern) but
   confirmed via live inspection that Chrome's `filter:brightness(0)` on this PNG collapses the
   image to a solid opaque bounding-box rectangle rather than preserving the ring/wordmark
   shape — a rendering defect independent of the asset, reproduced with filter alone (no blend
   mode needed to trigger it). Replaced with the spec's own explicitly sanctioned fallback: a
   light (`--off-white`) clear-space plate behind the mark, used identically in all three
   placements (header lockup, footer/closing, contact block). No filter or blend-mode applied to
   the logo anywhere in this build — it renders as supplied, unmodified, at every size.

3. **HeroLockup `.heavy` clamp retuned for a single 9-character word.** The Brand OS's own
   `HeroLockup` JSDoc (and this brief) both flag that the component's sizing was tuned to
   kick-check's own hero words (KICK / CHECK, two lines, ≤5 characters each) and overflows on
   longer words. BOARDROOM is one word, 9 characters, rendered on one line (no natural break
   point) — reusing the existing clamp (`4rem, 14vw, 13rem`) overflowed the viewport at both
   1440px and 390px on first render (caught by browser screenshot, not assumed). Retuned to
   `clamp(2.5rem, 12vw, 10rem)` with `white-space:nowrap`; verified clean at both widths. This is
   a local override scoped to this document's hero, not a change to the Brand OS's shared
   component — flagging here since it's the exact overflow risk the brief called out by name and
   the fix should be reviewed before reuse elsewhere.

4. **Hero background is a CSS-only "motion plate," not photography or video.** No approved hero
   asset exists and the brief explicitly prohibits stock photography of handshakes/skylines/
   conference rooms. Built a slow, token-derived gradient/grid field (`.motion-plate`) — brass
   and sage radial washes over a fine repeating grid, animated via `background-position` drift,
   disabled under `prefers-reduced-motion`. Used for the hero, the §07 Boardroom 100 statement
   band, the §07 TextParallax media slot, and the §19 finale-zoom background (in place of a real
   photo). All four slots are logged individually in `ASSETS_NEEDED.md` for real asset
   replacement.

5. **Print fix: `.arc-strip` (§14 Annual Arc) overridden from horizontal-scroll to a 3-column
   grid inside `@media print`.** The live/screen version is an `overflow-x:auto` horizontal
   strip (matching the brief's "horizontal timeline" instruction) — correct on screen, but print
   has no scroll mechanism, so 2 of 6 platform cells were silently cropped off the page edge in
   the first print-to-PDF test. Caught by rendering an actual print PDF and inspecting each page
   image, not assumed. Fixed with a print-only grid override; screen behavior is unchanged.

6. **Nav nineteen-item nav map to section IDs, not a 1:1 count of `<section>` elements.** The
   document has 22 `<section class="slide">` elements (19 numbered sections plus 3 sub-slides:
   the §02 statement band, and §07's concept/PESO sub-slides, which don't get their own nav
   entry or number per the brief's section table). The scroll-spy script maps the nearest
   preceding numbered section ID rather than a raw section index, so the active nav state and
   keyboard Home/End/PageUp/PageDown navigation both work correctly across the sub-slides — this
   required a small deviation from kick-check's original index-based nav-highlight script (which
   assumed nav-items:sections was 1:1).

7. **Appendix "Final content check" line retained verbatim** even though its stated condition
   (case studies confirmed) is now satisfied by this same document's §17 — see `COPY_DIFF.md`.
   Kept literal per the verbatim-copy constraint rather than edited for internal consistency;
   flagging here since a human reviewer may want to soften or remove it now that it's self-
   referentially satisfied.
