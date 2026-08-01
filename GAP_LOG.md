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

8. **Hero (§01) rebuilt on request as a real GSAP ScrollTrigger layered-parallax, Lenis
   smooth-scroll omitted.** User supplied a reference component (Osmo's parallax-layers pattern:
   GSAP timeline + ScrollTrigger scrub over N `[data-parallax-layer]` elements, plus Lenis for
   inertial smooth scrolling) and two real assets — `hero-video.mp4` (background) and
   `hero-portrait.jpg`, Rich Kleiman's photo (foreground). Implemented the GSAP/ScrollTrigger
   layer-tween structure as supplied, using kick-check's own near-identical hero-parallax script
   (already the house pattern — same CDN, same `data-parallax-layer="bg"/"fg"` convention) as the
   integration template rather than writing a third variant. Two layers instead of four (video +
   portrait vs. the reference's four static images), with yPercent 15/55.
   **Lenis was deliberately left out.** Lenis takes over the document's native scroll physics
   globally — it isn't a per-section opt-in — and this document already has several independent
   scroll-position-reading mechanisms (nav scroll-spy, TextParallax, MediaParallax, FinaleZoom,
   the reveal IntersectionObserver) tuned against native `window.scrollY`/`getBoundingClientRect`
   and against this build's `scroll-snap-type: y proximity`. Adding Lenis for one section's effect
   would touch scroll behavior on all nineteen. Flagging for ratification: if inertial smooth
   scroll across the whole document is wanted, that's a bigger, deliberate change to propose
   separately — not a silent side effect of the hero request.

9. **Hero video not muted-loop-checked for content appropriateness.** The supplied `video.mp4` is
   a 5-second night street/arena clip (visible "76 Place" signage). It plays behind a scrim at
   reduced legibility-safe opacity and reads fine as ambient motion, but nobody has confirmed it's
   the intended footage for a Boardroom-facing document (vs. a placeholder/test clip) — flagging
   rather than assuming.

10. **§02 reuses the finale-zoom mechanic (background-size zoom/blur/fade on scroll) with a real
    photo** (Kevin Durant and Rich Kleiman, supplied) as its pinned background, on request. The
    finale-zoom script was previously single-instance (§19 only, `document.querySelector`) —
    generalized to `querySelectorAll` so both §02 and §19 run independently off their own local
    scroll progress, same pattern already used for `.tpx-region` (TextParallax) supporting
    multiple sections. The shared component's default `background-size:250%` (tuned for the
    §19 motion-plate, which has no "faces to lose") over-cropped this photo at the effect's most
    zoomed-in point, cutting off Kevin Durant at the frame edge — corrected with an inline
    per-instance override (`background-size:135% auto;background-position:42% 22%`) so both
    subjects stay in frame across the whole scroll-driven zoom range, verified by scrolling
    through it rather than assumed from a single static screenshot.

11. **§03–§06 sticky media headers (`StickyCard`) — Framer Motion + Lenis reference ported to
    the doc's existing vanilla getBoundingClientRect/rAF pattern, no new dependency added.**
    Requested component used `useScroll`/`useTransform` from Framer Motion plus `ReactLenis` for
    a continuous 5-card stack sharing one `scrollYProgress`. Adapted to four independent
    instances (one per section, since each section here carries real Blueprint/NEW-COPY body
    text, not a bare image gallery) — each computes its own local scroll progress and scales its
    card from 1.0 down to 0.88 as that section scrolls past, same math family as every other
    scroll effect in this build. Framer Motion and Lenis were not added as CDN dependencies for
    the same reason Lenis was dropped from the hero (see #8): they'd be new, single-purpose
    dependencies duplicating what the doc's existing scroll-math pattern already does, and Lenis
    specifically still carries the global-scroll-takeover risk against this doc's other five
    scroll-position-reading mechanisms.
    **Assets, trimmed/compressed before embedding** (originals not touched): §03 — the supplied
    27-minute LeBron James interview (41MB) trimmed to its first 6 seconds, muted, re-encoded to
    426×240 h264 (153KB), since embedding the full interview would have added ~54MB base64 to a
    document already built to stay under a 15MB self-contained ceiling; §04 — Game Plan Summit
    cast photo, used as supplied (97KB, no compression needed); §05 — Boardroom x Visa Talks
    photo, resized from 6653×4435/9.4MB down to 2000px-wide/670KB; §06 — supplied `.avif` file
    embedded as-is (24KB). Combined addition ≈950KB; final document is 14.9MB, just under the
    15MB threshold — flagging that this is close enough to the ceiling that any further embedded
    media should go through the relative-path fallback described in the original build spec
    instead of base64.
    **Print handling added:** like the Annual Arc horizontal strip (#5 above), `position:sticky`
    inside a tall spacer has nothing to pin against on a printed page — added a print-only
    override collapsing each `.sticky-card-region` to a normal static block (video shows its
    poster frame; images render normally), verified by rendering an actual print PDF and
    inspecting the page images, not assumed.

12. **§03–§06 converted from the small rounded "sticky card" (#11 above) to full-bleed pinned
    media on request**, matching kick-check's own §06–§08 pattern exactly — reused the existing
    `.tpx-*` TextParallax component (already built for §07's concept sub-slide) instead of
    inventing a second full-bleed mechanic, and deleted the now-unused StickyCard CSS/JS
    entirely rather than leaving dead code. Each section's own eyebrow + H2 (unchanged, already-
    approved copy) moved into the pinned overlay (`tpx-copy`) instead of sitting above the body
    text — same words, new position.
    **Bug caught during this pass, not before:** `.tpx-media` had a class name in the markup but
    no CSS rule defining it — §07's own tpx instance never exposed this because it uses
    `.motion-plate` (which has its own absolute-positioning rule) rather than a real
    `<video>`/`<img>`. First render showed the LeBron video constrained to a small box instead of
    full-bleed; added the missing `.tpx-sticky .tpx-media{position:absolute;inset:0;width:100%;
    height:100%;object-fit:cover}` rule and re-verified all four sections by scrolling through
    each one, not just the one that surfaced the bug.
    **§02's statement band moved, not duplicated:** the standalone full-viewport `#s02-statement`
    section ("The recognized get covered. The recognizer gets cited.") was deleted outright, and
    the same sentence now renders as a supporting line inside §03's `tpx-copy` overlay (new
    `.tpx-copy .tpx-statement` style, light-weight display type, sits below the section's real
    H2). The words are unchanged and still render exactly once — moved, not copied — verified via
    print PDF (was two separate pages before: a bare statement page + §03; now one page carries
    both).

13. **Header nav/logo overlap fixed; header logo swapped to a derived white transparent mark.**
    The fixed top nav (`.section-nav`, 19 items, `justify-content:center`, spans the full
    viewport width) was overlapping the top-bar's logo/eyebrow lockup on the left at desktop
    widths once real content pushed the layout -- visible in a live screenshot, not assumed.
    Changed `.section-nav` to `justify-content:flex-end` with right-margin padding matching the
    top-bar's own horizontal padding (`var(--sp-6)`), so the nav now sits flush to the right
    margin, mirroring the logo lockup's position on the left, with no overlap at any of the 19
    items. Mobile's collapsed dot-nav (`max-width:1023px` override) is untouched.
    **Logo:** the supplied `Concrete Logo White.png` turned out to be a white mark on a white
    background with only a faint drop-shadow for definition (RGB, no alpha -- confirmed by
    sampling pixel values: background approx (254,254,255), mark edges approx (210,210,210)) --
    reading it directly onto a dark header would show a solid near-white box, not a clean mark.
    Derived a proper white transparent version instead: recolored the governed
    `tcg-ring-mark.png` (the same true-alpha source already used elsewhere in this build) to
    solid white, RGB(255,255,255) with the original alpha channel preserved, saved as
    `TCG_Logo_White.png` -- verified by compositing it onto a dark swatch before using it, not
    assumed correct from the PNG alone. Used in the header lockup only, which no longer needs
    the light clear-space plate from GAP_LOG #2 (a white mark reads directly on the dark
    header). The closing-section lockup (§19) is unchanged -- still the dark-ink mark on its
    light plate, which was already correct.

14. **Section 7 media added: full-bleed statement-band photo, and a new ScaleHero component
    for the concept sub-slide.** §07's opening statement band ("Boardroom's owned annual
    recognition...") previously used the abstract `.motion-plate` per GAP_LOG #4 -- replaced
    with a supplied group photo (Kevin Durant, Rich Kleiman and others) as a real full-bleed
    background with a left-to-right dark scrim for text legibility.
    §07's concept sub-slide ("An annual, globally minded list.") previously used a `.tpx-region`
    sticky-fade-in treatment -- replaced on request with a distinct effect (a small centered
    card that scales up to full size as the 200vh region scrolls past), ported from a supplied
    VideoScrollHero reference. Adapted to a static image instead of video, since the supplied
    asset was a photo (Rich Kleiman on stage) -- same scroll-progress math as the reference
    (progress = scrolled / (containerHeight - windowHeight), scale = startScale +
    progress*(1-startScale), startScale 0.25), ported to this doc's existing
    getBoundingClientRect/rAF pattern instead of a React effect hook, generalized via
    querySelectorAll like every other mechanic here.
    **Print bug caught and fixed during this pass:** the ScaleHero's print collapse initially
    left a large blank gap and clipped the "THE CONCEPT" eyebrow at the left edge, because the
    print override reset the media's transform but not its `position`/`width`/`height`
    (still reserving its full 60vh screen box) and the copy block had no left inset matching
    the rest of the document's content margin. Fixed by resetting `.scale-hero-media` to static
    positioning with auto width/height and giving `.scale-hero-region` the same left padding
    other sections use -- verified by rendering an actual print PDF before and after, not
    assumed. The statement band's photo is dropped in print (same convention as every other
    full-bleed media section) since print re-themes body text dark, which would be illegible
    against the photo's own dark scrim.
    Images compressed before embedding: statement-band photo from 8.1MB/4768x3179 down to
    1600px-wide/283KB; ScaleHero photo used as supplied (74KB, no compression needed).

15. **ScaleHero (§07 concept) now reaches true full-bleed at full expansion, on request.** It
    previously scaled up to a fixed max-size card (min(80vw,900px) x 60vh, rounded corners,
    shadow) rather than filling the viewport. Changed the media box's base (unscaled) size to
    100vw x 100vh -- since the box starts at transform:scale(0.25) and grows to scale(1), the end
    state is now exactly the full viewport, edge to edge, no gutters. Also interpolated
    border-radius from 20px down to 0px alongside the scale (was a fixed 20px throughout,
    which would have left visible rounded corners even at "full size"). Removed the now-unused
    `.tpx-wrap` wrapper around this sub-slide (it only ever provided a 12px inset for real
    `.tpx-region` instances, which this no longer is).
    **Print regression caught and fixed in the same pass:** the print collapse rule reset the
    media box's `width`/`height`/`transform` but not its `overflow:hidden` (inherited from the
    screen rule) -- with the box's computed width now different post-refactor, the left edge of
    "THE CONCEPT" eyebrow clipped its leading "T" in the print PDF. Added `overflow:visible` to
    the print override for `.scale-hero-media`; re-rendered a print PDF at 150dpi and cropped the
    exact region to confirm the character wasn't clipped before calling it fixed, not eyeballed
    at a low-res thumbnail (which was where the regression was first missed).

16. **§08 (A Seat at the Table) reuses the finale-zoom mechanic (background-size zoom/blur/fade
    on scroll) with a supplied photo**, matching the "this effect" reference exactly -- it's the
    same background-size-zoom pattern already generalized for §02 and §19 (GAP_LOG #10), so this
    was a third instance rather than new code: same `.finale-tall`/`.finale-sticky`/`.finale-bg`
    markup, same already-generalized JS. Image (a podcast-table interview setup) used as
    supplied, 115KB, no compression needed. Print drops the photo and shows plain headline text,
    consistent with the other two finale-zoom instances -- verified via an actual print PDF
    render, not assumed.

17. **§08's finale-zoom background over-cropped to a single face at the effect's most zoomed-in
    point**, same class of issue as §02 (GAP_LOG #10) -- the shared component's default
    background-size:250% is tuned for the abstract §19 motion-plate, not a wide three-person
    table shot. Added the same per-instance inline override pattern
    (background-size:110% auto;background-position:center 42%) so all three people and the
    table stay in frame across the zoom range, verified by scrolling through it in the browser.

18. **§09–§12 all reuse the finale-zoom mechanic (background-size zoom/blur/fade on scroll)**,
    each with a supplied photo, on request. Fourth through seventh instances of the same
    already-generalized effect (GAP_LOG #10, #16) — no new mechanic needed for §09/§10.
    §11/§12 needed a real variant, though: the brief required the entire image stay visible,
    never cropped or stretched, which the crop-zoom mechanic's `background-size` inherently
    violates (it's built to crop in). Added a `.finale-bg--contain` modifier
    (`background-size:contain`, letterboxed on `--true-black`) plus a `data-no-zoom` attribute
    the shared FinaleZoom script checks before applying its `transform:scale()` step — §11/§12
    still get the blur/fade half of the effect as they scroll past, just not the crop-zoom half,
    since combining "zoom in" with "never crop" is a contradiction. Verified in-browser that both
    letterbox correctly (dark bars, no stretch) rather than assumed from the CSS alone.
    §09/§10 needed specific framing (a huddle scene keeping heads in view; an awards-stage shot
    with the lighting rig cropped out) — tuned via inline `background-position`/`background-size`
    overrides on each instance, checked against the reference crop screenshots supplied, then
    verified by scrolling through each in the browser rather than trusting the values on read.
    Images compressed before embedding (originals not touched): boardroom-1.webp 1040x1400/112KB
    -> 900px-wide/94KB; Awards Stage PNG 1122x1402/1.9MB -> 800px-wide JPEG/65KB; Freunde
    workshop JPG 930x620/147KB -> 930px/82KB (no resize, quality only); Summit Stage PNG
    1537x1023/1.7MB -> 1050px-wide JPEG/71KB. Needed two compression passes to claw the
    document back under the 15MB ceiling (first pass landed at 15.57MB, second at 15.13MB) --
    final file is 15.03MB, effectively at the ceiling rather than comfortably under it. Flagging
    for ratification: any further media added to this document should go through the relative-
    path fallback described in the original build spec rather than base64, since the embed
    budget is now spent.

19. **§09/§10 framing corrected; §11/§12 switched from letterboxed "contain" back to full-bleed
    "cover", per follow-up feedback.** The crop references supplied this round showed nearly the
    entire source photo for both §09 and §10 -- considerably wider framing than the tight
    head-and-shoulders crop from the previous pass (GAP_LOG #18). Reduced `background-size` from
    115%/125% down to 78-104% and retuned `background-position` on both instances, verified by
    scrolling to each section's resting state and comparing against the supplied reference crops
    directly, not assumed from the CSS values alone.
    §11/§12 reversed the "do not stretch, entire image visible" constraint from GAP_LOG #18 --
    removed the `.finale-bg--contain` class and `data-no-zoom` attribute from both markup
    instances (the CSS class and the JS's `hasAttribute` check are left in place, unused, in case
    a future section needs the letterboxed treatment again). Both now use the standard crop-zoom
    finale-bg treatment, same as every other instance, confirmed full-bleed with no letterbox
    bars in the browser.

20. **§14–§18 reuse the TextParallaxContent mechanic (`.tpx-*`)**, already built for §07's concept
    sub-slide and matching the requested reference exactly (Framer Motion's useScroll/useTransform
    sticky-scale + fade-in overlay copy → this doc's existing getBoundingClientRect/rAF port).
    Each section's own eyebrow + H2 (unchanged, already-approved copy) moved into the pinned
    overlay, same pattern used for §03–§06 (GAP_LOG #11). Media: §14 a calendar/timeline motion
    graphic (trimmed to 5s, 138KB); §15 an abstract finance/data graphic GIF converted to a
    compressed muted MP4 loop (23KB, down from a 1.5MB GIF); §16 a data-network motion graphic
    GIF, same GIF-to-MP4 treatment (253KB, down from 4.8MB); §17 a photo (53KB); §18 a
    brand-writing video (trimmed to 5s, 161KB). GIF-to-MP4 conversion, not straight compression,
    because a muted looping video is dramatically smaller than an animated GIF at the same visual
    quality and this doc already treats all motion the same way (autoplay muted loop video).
    **Budget note:** this pass pushed the document to 15.98MB, meaningfully past the 15MB
    self-imposed ceiling flagged in GAP_LOG #18/#19. Did not compress further -- the assets are
    already fairly aggressively reduced (a few hundred KB combined for five sections), and
    further reduction would start visibly hurting quality for content that's now load-bearing
    (5 of 19 sections). If more media needs to be added after this, it should go through the
    relative-path fallback described in the original build spec rather than base64 -- the
    self-contained single-file budget is now spent.

21. **§19 rebuilt with the ScaleHero effect (a second instance of GAP_LOG #15's mechanic, media
    swapped for the supplied Boardroom x Visa photo), darker media filter added to §3-6/15/17/18,
    §8/§11/§12 framing corrected again, and the closing logo simplified.**
    §19's `.motion-plate` finale-zoom background replaced with the same ScaleHero component built
    for §07 (already generalized via `querySelectorAll`, so this was a markup-only change, no new
    JS). No overlay text was put inside the scaling box this time -- §19 already carries
    substantial real closing copy (headline, outcome statement, CTA, contact block, appendix)
    that shouldn't live inside a shrinking image; it now follows in normal flow after the media
    releases.
    **Darker filter:** added a `.tpx-sticky--dark` modifier (`filter:brightness(.55)` on the
    media only, not the overlay text) to the seven specified instances (§3, §4, §5, §6, §15,
    §17, §18) -- §7, §14, §16 were not requested and left untouched.
    **§8/§11/§12 reframed again:** the crop references this round showed nearly the full source
    photo for all three (same "show everything, not a tight crop" direction as GAP_LOG #19) --
    reduced background-size further on §8 (110%->92%) and added explicit smaller sizes to §11/§12
    (95%, previously defaulting to the shared 250%), each re-verified by scrolling to the
    resting state and comparing against the supplied reference crop directly.
    **Two real bugs caught and fixed during this pass:**
    (1) The closing logo, after being simplified from a wrapped light-plate treatment to a bare
    `<img>`, rendered as a stretched horizontal oval instead of a circle -- `.contact-block` is a
    flex column, and flexbox's default `align-items:stretch` overrides a replaced element's
    `width:auto` aspect-ratio-preserving behavior. Fixed by adding `align-self:flex-start;
    flex-shrink:0` to the shared `.ring-mark` rule. Caught by inspecting the live rendered
    dimensions via JS, not by eyeballing.
    (2) The same logo was then invisible in the print PDF -- print re-themes the page to a white
    background, and the mark is the white-on-transparent version built for the dark screen theme
    in GAP_LOG #13. Added a print-only `filter:invert(1)` on `.ring-mark` so it renders as dark
    ink on the printed page; verified by rendering an actual print PDF page image before and
    after, not assumed from the CSS change alone. (An earlier draft of this same fix briefly
    left a stray `opacity(0)` in the filter list, which would have made the logo invisible on
    screen too -- caught and corrected before it reached the live page.)
