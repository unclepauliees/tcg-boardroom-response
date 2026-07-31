# ASSETS_NEEDED.md — Every media slot standing on a placeholder

No approved photography or video exists for this response, and the brief prohibits stock imagery
of handshakes, skylines, or conference rooms. Every media slot below currently renders a
CSS-only `.motion-plate` (a slow, token-derived gradient/grid field — brass and sage washes over
a fine repeating grid) rather than a broken-image state, so the document is presentation-ready
as-is. Replace in priority order (hero first — it's the first thing anyone sees).

| # | Section | Slot | Current state | Recommended real asset |
|---|---|---|---|---|
| 1 | §01 Hero | Full-bleed background | `.motion-plate`, full viewport | A single high-quality still or slow looping muted video — poured-concrete texture, structural grid in motion, or an abstract brand-palette field. 16:9 minimum, works cropped to any viewport ratio. If video: ≤5MB compressed, `muted playsinline loop`, poster frame required. |
| 2 | §07 Boardroom 100 | Statement-band background | `.motion-plate`, full viewport | Same treatment as hero, or a distinct abstract variant — this is the document's single heaviest visual moment and should feel different from the hero, not repeated. |
| 3 | §07 Boardroom 100 | TextParallax sticky media (concept sub-slide) | `.motion-plate` inside the 150vh sticky region | Editorial, globally-authoritative imagery per the slide's own DESIGN DIRECTION — explicitly *not* a membership-roster look. A world-map-adjacent abstract or an editorial texture, not literal portraits (no confirmed honorees exist yet). |
| 4 | §19 Closing | Finale-zoom background | `.motion-plate`, animates via the ported `useFinaleZoom` scale/blur/fade | A closing brand plate — the concrete ring mark motif rendered large and abstract, or the same texture family as the hero for narrative bookending. |

## Not needed / intentionally absent

- **Talent, honoree, or member photography** — none exists (no cohort has been selected), and
  the brief's own placeholder convention (`Illustrative, Not Confirmed:`) is reserved for named
  people, not imagery. No talent images are referenced anywhere in this build.
- **Brand-partner logos** (§15) — explicitly instructed to use categories, not speculative logos.
- **Case-studies thumbnail/cover image** (§17 download block) — the download link works from the
  PDF's own bytes; no cover-image asset is required for the `<a download>` pattern used.

## Format guidance if/when real assets land

Match kick-check's own asset discipline: video sources get a `poster` frame and stay
`muted playsinline loop preload="metadata"`; images get `loading="lazy"` below the fold and
`loading="eager"` only for the hero. Both should degrade to the existing `.motion-plate` via the
same `onerror` fallback pattern kick-check uses (`.media-parallax--missing`), so a broken asset
path never produces a blank box in front of a client.
