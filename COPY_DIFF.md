# COPY_DIFF.md — Verbatim Verification

## Method

1. Extracted the Blueprint PDF to plain text with `pdftotext -layout` (exact bytes, no
   smart-quote/dash normalization from a rendering tool) — the same extraction used to author
   the HTML in the first place, so the diff is against the same source, not a re-derivation.
2. Extracted all visible rendered text from `TCG_Boardroom_Response.html` by stripping `<head>`,
   `<script>`, `<style>`, and the two base64 data-URIs, then stripping remaining tags.
3. Normalized both sides (smart quotes → straight, em/en dash unified, whitespace collapsed,
   bullet glyphs stripped, lowercased) and checked every substantive Blueprint line for
   containment in the rendered text.
4. Cross-checked every dollar figure and every §17 stat figure independently by regex extraction
   against both sources (see below) — this catches transcription errors a substring-diff on
   wrapped PDF lines can miss.

## Result: one real defect found and fixed

The Appendix's closing line was originally rendered as a paraphrase:

> ~~Final content check: this response is substantively complete once the approved mix of
> optional production investments is confirmed.~~

against Blueprint verbatim:

> FINAL CONTENT CHECK: The deck is substantively complete once final case-study examples and the
> approved mix of optional production investments are confirmed.

Fixed to render the Blueprint sentence verbatim (§19 Appendix, closing line). No other wording
alterations were found.

## Expected non-matches (by design, not defects)

- **All `SLIDE NN` markers, `DESIGN DIRECTION:` lines (and their PDF-wrapped continuation
  lines), and `THE CONCRETE GROUP | BOARDROOM RFP DECK BLUEPRINT | N` footers** — instructed to
  never render, and confirmed absent (`grep -c "DESIGN DIRECTION"` on the HTML = 0).
- **Table column headers from Slides 01, 04, 06, 08, 10, 14, 16, 18** (e.g. "Platform / Role in
  the System / Primary Outcome", "PESO / Activation", "Stage / Platform / What It Produces") —
  per DESIGN DIRECTION these render as designed graphics (vertical hierarchy, PESO quadrant,
  flywheel grid, arc strip, category cards), not literal tables, so the column-header *strings*
  don't appear even though every cell's *content* does, verbatim, inside the corresponding
  component.
- **Slide 22's original flat-retainer copy** ("Monthly retainer: $25,000", "Recommended
  Structure", the 80–90 hour/$25,175 lead paragraph) — explicitly superseded by the NEW COPY
  tiered fee structure per the brief. `What the Retainer Covers`, `Separate, Pre-Approved Costs`,
  and `Competitive Positioning` were retained verbatim as instructed.
- **The Appendix's RFP-requirement → Slide-number mapping** — explicitly instructed to be
  updated to the new section numbers (§02, §03, §14, §06/§08–§10, §16, §18, §17); the old
  "Slide 16" / "Slides 2, 7–10" etc. values are intentionally not reproduced.
- **Team roster and rate lines (Slide 21)** and **PESO/Brand-Ecosystem table rows** — content
  reformatted from inline em-dash-separated text into table cells (`<td>` per column), per the
  DESIGN DIRECTION instruction to present them as a grid/table. Every word, name, rate, and
  hour figure is present verbatim inside its cell; only the em-dash-separated inline punctuation
  changed to column separation.

## Minor structural compressions (disclosed, not fixed)

Two subheading labels were folded into the body copy they introduce, rather than kept as
separate standalone lines, because they read as connective tissue once the surrounding sentence
became continuous prose:

- Slide 05 (§10 Awards): the "Role in the System" label was dropped; its content sentence ("The
  live, sponsorable and photographable stage where...") is preserved verbatim, appended to the
  concept paragraph.
- Slide 09 (§08 Seat at the Table): "Boardroom's long-form audio front door: a weekly habit..."
  was split at the colon into an H2 ("Boardroom's long-form audio front door.") and a body
  sentence ("A weekly habit that builds depth, trust and familiarity with its leaders."), adding
  a period and dropping the colon. All words preserved; only the colon's punctuation changed.

## Numeric verification (independent regex cross-check)

Every dollar figure in the Blueprint (`$0`, `$40,000–$100,000`, `$750,000–$1.5 million`,
`$125,000–$250,000`, `$150,000–$300,000`, `$250,000–$500,000`, `$175,000–$350,000`,
`$75,000–$150,000`, `$25,175`, `$300,000`, and the five team rate-card figures `$400/$350/
$300/$300/$325/$185`) appears unaltered in the rendered HTML. `$35,000` and `$47,500` are new
figures from NEW COPY (Tier 02/03) and are not claimed to originate from the Blueprint.

Every §17 stat figure (`1.8B+`, `$125M+`, `400+`, `230+`, `3.8B+`, `#1`) matches NEW COPY
character-for-character — no additional statistics, client names, or campaign details were
pulled from `_EXT__TCG_Case_Studies.pdf` (that file was never opened; only its page count and
file size were read via `pdfinfo`/`ls`, per the constraint against fabricating figures).

## Section headline audit (QA #5 — sentence case, ending in a period)

All 19 section H2 headlines were authored to end in a period and use sentence case. None were
Blueprint slide titles copied as-is with a trailing period added to an otherwise unpunctuated
title — each headline is either NEW COPY (already period-terminated) or a Blueprint sentence
fragment already ending mid-sentence in the source, extended to a full sentence using only words
from that slide's own copy.
