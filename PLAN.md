# PLAN.md — TCG × Boardroom RFP Response

## Token map

Source: `~/tcg-brand-os/tokens/tokens.json`, generated `src/styles/tokens.css` (ran `npm run tokens`
to produce it — brass/sage ramps are generated, not hand-computed). Emitted as one `:root` block +
`[data-theme="light"]` override, lifted verbatim from the generated file (values match kick-check.html's
own hand-authored token block exactly — confirmed byte-for-byte on the core/neutral/semanticDark sets).

Contrast pairs actually used in this build, per the repo's own audit (`npm run tokens` output):
- text-primary/bg-base (dark) 14.18:1 PASS
- text-secondary/bg-base (dark) 8.43:1 PASS
- text-muted (n-500) large-only 3.07:1 — used only for borders/large text/eyebrow-adjacent, never body
- text-muted-body (n-400) 5.14:1 PASS — used for all muted body copy
- brass canonical on off-white 2.17:1 FAIL — never set as running text on light bg; large/icon/border only
- brass-700 (text-accent, light theme) on off-white 4.65:1 PASS
- sage/concrete-black 4.7:1 PASS
- logo-ink/true-white 9.15:1 PASS

Full detail in CONTRAST_REPORT.md.

## Logo substitution (see GAP_LOG.md #1)

`TCG_Logo_No_Background.png` as supplied in Downloads has a checkerboard transparency pattern
**baked into RGB pixels** (PIL confirms mode=RGB, no alpha channel) — it is not actually transparent,
it's a flattened preview export. Substituted the governed asset the Brand OS itself uses as the ring
mark (`~/tcg-brand-os/public/brand-assets/tcg-ring-mark.png`, RGBA, true alpha, same artwork/same
pixel dimensions as `~/Downloads/TCG Logo.png`). Same mark, same ink (#484848), correctly transparent.

## Section architecture

19 numbered sections + appendix, per spec table. Blueprint slide order reordered — §04 (System) before
§05 (Sequencing, NEW), then §06 Always-On (Slide 02), §07 Boardroom 100 (Slides 03+04, hero section),
§08 Seat at the Table (Slides 09+10), §09 10 Minutes (Slides 07+08), §10 Awards (Slides 05+06,
conditional), §11 Incubation Week (Slide 11), §12 Classroom (Slides 12+13), §13 Flywheel (Slide 14),
§14 Annual Arc (Slide 16), §15 PESO (Slides 17+18), §16 Measurement (Slide 19), §17 Proof (Slide 20 +
NEW), §18 Team & Fee (Slide 21 + NEW fee block), §19 Closing (Slide 23), Appendix (renumbered mapping).

## Component inventory

Reused verbatim from kick-check.html pattern language (byte-identical CSS class names/mechanics):
EyebrowPill, RuleDot, HeroLockup, StatBlock→extended to StatTile, Card (bordered/glass-dark),
Panel, TierFlow, Divider, Button, TextParallax (tpx-*), MediaParallax, FinaleZoom, Reveal,
section-nav + progress-rail + tcg-rail chrome, availability/entitlement table pattern → reused for
Measurement scorecard, Team grid, Appendix.

New components (per spec, built on existing token/type primitives, no new hex):
- **Budget Callout** — brass hairline rule, eyebrow label, `.text-stat`-scale figure, muted sentence.
- **Stat Tile** — three-across grid, same `.text-stat` geometry as Budget Callout for numeric-voice
  consistency, label beneath in eyebrow/muted.
- **Case Studies Download** — data-URI `<a download>`, brass hairline rule, suppressed in print CSS
  (`display:none` in `@media print`, replaced by a `::after` content string — see build).
- **Conditional Band** — full-width sage-ruled band, identical markup/copy in §10/§11/§12.
- **Guardrail Callout** — sage variant (credibility conditions) and brass variant (§17 "why it matters",
  §18 "why the retainer steps") of the same bordered-panel component.
- **Rubin Callout** — bordered plate variant of Panel, used once in §03.
- **PESO Quadrant** — 4-col grid (collapses to 1-col mobile), reused identically in §07/§08/§09/§10/§15.
- **Tier Block** — 3 stacked panels, Tier 01 full-brass emphasis, Tiers 02–03 carry
  `[RECOMMENDED — FOR CONFIRMATION]` chip (placeholder styling = `.illustrative-flag` pattern).

Tables rendered as designed graphics (not literal `<table>`) per DESIGN DIRECTION: §04 System
(vertical hierarchy list, not table), §13 Flywheel (circular/stage cards), §14 Annual Arc (horizontal
timeline strip), §15 Brand-Partner Ecosystem (category cards). Rendered as genuine `<table>` with
kick-check's `.availability-table`/`.entitlement-table` styling where the spec says a table is clearest:
§16 Measurement scorecard, §18 team grid, Appendix.

## Self-critique (one pass)

- Risk: 19-item nav is a lot for kick-check's proven 9-item pattern. Mitigation: group nav into acts
  via a data attribute for visual grouping without adding new interaction complexity; mobile keeps the
  existing dot-only collapse (works at any item count).
- Risk: §07 must visibly outweigh §10. Mitigation: §07 gets a TextParallax hero moment (150vh) plus a
  standalone full-viewport statement band; §10 opens directly on the Conditional Band with no parallax
  media band, straight into standard card content — structurally lighter by section-primitive count.
- Risk: base64 case-studies PDF (~4MB encoded) ahead of first paint. Mitigation: placed as a data-URI
  only on the `<a>` href late in DOM order (§17), not preloaded/fetched — browsers don't fetch data-URI
  href targets until clicked, so it doesn't block paint despite living in the HTML bytes.
- Confirmed both Blueprint PDF filenames are byte-identical text (diffed, 0 lines) — no ambiguity on
  which is canonical.
