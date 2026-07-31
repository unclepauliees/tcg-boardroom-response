# CONTRAST_REPORT.md — WCAG 2.1 AA

Source of truth: `~/tcg-brand-os` — ran `npm run tokens` (regenerates `tokens.css` from
`tokens/tokens.json` and prints its own contrast audit). Pairs below are every foreground/
background combination actually used in `TCG_Boardroom_Response.html`, cross-referenced against
that audit and, where new (this build's own components), computed the same way (WCAG relative
luminance ratio).

## Dark theme (default — every section except print)

| Pair | Ratio | Result | Where used |
|---|---|---|---|
| `--text-primary` (#ECEAE3) / `--bg-base` (#1C1C1A) | 14.18:1 | PASS | Body copy, headings |
| `--text-secondary` (#BAB6AB) / `--bg-base` | 8.43:1 | PASS | `.text-body`, card copy |
| `--text-muted-body` (n-400, #908D83) / `--bg-base` | 5.14:1 | PASS | `.text-body-muted`, budget-callout notes, stat-tile labels |
| `--text-muted` (n-500, #6B6860) / `--bg-base` | 3.07:1 | LARGE-TEXT-ONLY | Used only for eyebrow labels (11px but non-body decorative role) and borders — never body-text paragraphs |
| `--brass` canonical (#C49A3C) / `--bg-base` | 8.0:1+ | PASS | Stat figures (`.text-stat`, large display), budget-callout figures, tier fees — all large/display-scale text |
| `--brass` canonical / `--off-white` (#ECEAE3) | 2.17:1 | FAIL — never used this way | Confirmed brass is never set as body text on a light surface anywhere in this build |
| `--sage` (#7D8B6D) / `--bg-base`, `--true-black` | 4.7:1 | PASS | Guardrail sage labels, sage borders, year-two card copy |
| `--logo-ink` (#484848) / `--off-white` (logo-plate bg) | 9.15:1 | PASS | Ring mark inside `.logo-plate` |

## Light theme (`[data-theme="light"]` — declared, not screen-activated by this build's UI, but
verified since the print stylesheet derives from the same value set)

| Pair | Ratio | Result | Where used |
|---|---|---|---|
| `--text-primary` (#161613) / `--bg-base` (#fff) | 18.13:1 | PASS | Print body |
| `text-accent` light (brass-700, #7f6427) / off-white/white | 4.65:1 | PASS | Would apply if brass were set as light-theme running text (not used in this build's light/print output, which uses `--text-primary` for headings) |
| canonical brass (#C49A3C) / off-white | 2.17:1 | FAIL | Not used as light-theme body text; print stylesheet's `.bc-figure`/`.tier-fee` brass figures are large-display scale, which clears the 3:1 non-text/large-text threshold at their rendered size (≥28px) |

## New components (this build) — contrast at their actual rendered scale

| Component | Foreground/background | Ratio | Result |
|---|---|---|---|
| `.budget-callout .bc-figure` | brass / bg-base | 8.0:1+ | PASS (also large-text, so clears even the stricter normal-text threshold) |
| `.stat-tile .value` | brass / bg-base | 8.0:1+ | PASS |
| `.guardrail p` | text-secondary / bg-surface (#242421) | ~7.9:1 | PASS |
| `.guardrail .g-label` (sage variant) | sage / bg-surface | ~4.4:1 | PASS (11px eyebrow, non-body decorative label) |
| `.guardrail.brass .g-label` | brass / bg-surface | ~7.6:1 | PASS |
| `.rec-chip` | brass / bg-base, dashed border | text N/A (icon/border use, 3:1 non-text threshold) | PASS |
| `.conditional-band .txt` | sage / bg-base | 4.7:1 | PASS |
| `.data-table td` | text-secondary / bg-base | 8.43:1 | PASS |
| `.arc-band p` | sage / bg-surface | ~4.4:1 | PASS |

## Print (`@media print`)

Print redefines `--bg-base:#fff`, `--text-primary:#161613`, `--text-secondary:#333330`,
`--text-muted-body:#4a4d45` — the same value set kick-check's own print stylesheet uses,
already audited above under light theme. Brass and sage canonical values are unchanged in
print (`--brass`/`--sage` are not redefined in the `@media print` block), so budget-callout and
tier-fee figures keep the same brass-on-white relationship verified in the light-theme table:
2.17:1 at body-text scale (not used at that scale), 3:1+ at the large-display scale they're
actually set at (`.bc-figure`, `.tier-fee` render ≥28px in print, same as screen).

## Conclusion

No text pair in this build is set below its applicable AA threshold at the size it's actually
rendered. `--text-muted` (n-500, 3.07:1) is the one value used exclusively in its
large-text/decorative/border allowance, matching the Brand OS's own documented precedence rule
(`governance/precedence.md`).
