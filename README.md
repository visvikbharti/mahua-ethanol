# Mahua Flower → Ethanol — Aspen Plus Model Review

Review of a friend's Aspen Plus fermentation simulation (mahua / *Madhuca longifolia* flowers → ethanol with *Saccharomyces cerevisiae*), based on three screenshots of the FERMENT reaction setup and the control-panel errors.

## Deliverables (in `Shared-Docs/`, Word format)

| Document | Contents |
|---|---|
| `1 - Read Me First - Summary.docx` | Plain-English summary of everything found, with the screenshots embedded |
| `2 - How to Fix the Error and Warnings.docx` | Step-by-step fix for the RBatch error, the two value corrections, plain-word explanation of all three warnings, pre-run checklist |
| `3 - Every Value Checked with References.docx` | Value-by-value audit with verdicts, mahua-specific feed/validation numbers, and a 30-item verified reference list |

## Key findings (TL;DR)

1. **Source of the values identified.** The kinetic parameters (μmax 0.373 h⁻¹, Ks 4.1, Xm 76.9, KI,p 107.7, Yp:x 9.041, Ys:x 19.78 = 1/0.05055, exponents 1 and 1.5, β 0.1) match the **34 °C column of Rivera et al. 2006, *Process Biochemistry* 41(7):1682–1687** (DOI 10.1016/j.procbio.2006.02.009) — S. cerevisiae on sugarcane molasses. Verified against a full-text copy. These values are fine to keep; the reactor should run at 34 °C and the paper should be cited.
2. **Hard error:** the Fermentation reaction class is not supported by RBatch. Per the Aspen V14 release notes and AspenTech's eLearning catalog, the supported blocks are **BatchOp** (batch) and **RCSTR** (continuous). Fix: replace block B4 with BatchOp.
3. **Two wrong values:**
   - `Yp:s = 0.5144` exceeds the stoichiometric ceiling 0.5114 g/g (2×46.068/180.156) — physically impossible, likely a typo of 0.5114. Recommended: **0.50** (= the paper's mp/mx = 0.1/0.2, which makes Aspen's substrate balance match the source paper exactly: β/Yp:s = 0.1/0.50 = 0.2 = mx).
   - `Ms = 0.0005` matches nothing (paper uses mx = 0.2; measured yeast anaerobic maintenance ≈ 0.09 g/g/h, Boender 2009). Recommended: **0** — maintenance is carried by the β term, as in the source model.
4. **The three "gap species negative coefficient" warnings** are CHON atom-balance artifacts: (a) growth reaction — biomass slightly more reduced than glucose, small negative H₂O, harmless; (b) maintenance reaction — any Ms > 0 without O₂ forces negative H₂O (fixed by Ms = 0); (c) non-growth reaction — Yp:s above 0.5114 forces negative H₂ (fixed by Yp:s ≤ 0.511).
5. **Mahua-specific caution:** use measured flower sugar content ~37 g/100 g dry (Behera 2010; Swain 2007: 28–36 g/100 g), **not** the old 68–72 % figures — those overpredict ethanol ~1.8×. Validation targets: Yp/s 0.42–0.48 g/g, ~20–22 g/L ethanol from a 1:5 flower:water slurry in 96 h. No published Monod/Luedeking–Piret fit exists for mahua (verified negative result; only Halder 2016, Hinshelwood model, parameters paywalled), so borrowing Rivera 2006 is the defensible choice.

## Provenance

Research run 2026-08-21 with a 20-agent workflow (4 research agents + 16 adversarial citation checks). Citations in the documents were verified against publisher pages / Crossref / PubMed / full texts where accessible; the few items that could not be fully verified (Aspen help pages behind login, one Ghose & Tyagi number) are flagged honestly in document 3, section E.
