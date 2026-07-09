# Where the map runs out: an open, reliability-aware pipeline for natural-product and drug target prediction

**Built with Claude — Life Sciences hackathon submission**

---

## The one-line takeaway

**A natural-product orphan and an FDA-approved drug — chemically unrelated, from
opposite ends of the discovery world — converge on the same antibacterial target
(*M. tuberculosis* InhA) when passed through one honest validation standard.**
That convergence is only visible because the pipeline is built to work *below the
analog wall*: the region of chemical space where conventional similarity-based
target prediction provably fails.

---

## Why this matters (the impact hook)

Target prediction by chemical similarity is everywhere in drug discovery — but it
has a failure boundary nobody quantifies. We measured it: across the full COCONUT
2.0 natural-product library (695,133 compounds), **~63% of natural products are
"true structural orphans"** with no close analog among known binders. For those,
similarity-based target prediction cannot work — not "works poorly," *cannot
work* — and the cliff is sharp, at a Tanimoto coefficient of ~0.5.

The same blind spot exists for **drug repurposing**. When we ran 8,780 approved
and clinical-stage drugs through target prediction against pathogen targets,
**1,218 drugs had credible pathogen-target predictions that sit *below* the analog
wall** — predictions that standard similarity screens systematically miss. These
are approved drugs, already safety-tested in humans, with latent antibacterial /
antiviral target hypotheses that the usual tools cannot surface.

**The problem the field has:** a flood of ranked predictions with no way to know
which to trust. **What we built:** a pipeline that quantifies exactly where its own
predictions are unreliable, then structurally validates the ones below the wall
with real controls — applied identically to natural-product discovery *and* drug
repurposing.

---

## What we built

An open, end-to-end, reliability-aware target-prediction pipeline:

1. **Nominate** — for any molecule, rank candidate protein targets by similarity to
   known binders, with a **calibrated confidence score** that reports how far the
   query sits from the nearest known analog (the `nn_analog` distance).
2. **Locate it against the analog wall** — every prediction is placed relative to
   the Tanimoto-0.5 boundary, so a user knows immediately whether a hit is
   analog-supported or a genuine below-wall extrapolation.
3. **Structurally validate below the wall** — below-wall candidates go to docking
   (AutoDock Vina) with a full QC layer: positive/negative controls, **SIFTS
   receptor-identity checks**, and **decoy-enrichment tests** against
   property-matched random molecules.

Every stage was orchestrated with Claude, and the QC layer **caught real errors
live** — SIFTS flagged mis-annotated receptors (a rabbit ortholog labeled human, a
PKC-θ structure labeled PKC-δ), and negative controls invalidated docking boxes
where caffeine out-scored the known inhibitor.

---

## Results

### 1. The analog wall — the foundation everything rests on

Across 695k natural products, similarity-based target prediction has a hard
reliability ceiling at Tanimoto ~0.5; ~63% of the library falls below it. The
boundary is **source-independent** — it reproduces identically on natural products
from independent databases (NP Atlas, CMAUP gap compounds), confirming it is a
property of the *method*, not any one dataset.

*(Figure: analog-wall system overview and stratified recall.)*

### 2. Cross-kingdom repurposing map — the blind-spot inventory

8,780 approved/clinical drugs × 319 targets → 131,700 predictions. Of drugs with a
credible pathogen-target nomination: **2,155 above the wall, 1,218 below it** (the
2D-blind inventory), including **282 deep orphans** (nn < 0.35). The below-wall set
spans bacterial (1,023), viral (180), fungal, and parasitic targets.

*(Figure: repurposing_map_figure.png.)*

### 3. Docking rescue + decoy enrichment — the validation

We docked the deepest below-wall candidates against SIFTS-validated bacterial
receptors. The result is a clean, honest, two-sided finding:

- **InhA (*M. tuberculosis*) is a reproducible hit target.** Six below-wall drugs
  dock −9.1 to −11.2 kcal/mol; **four clear the 5th-percentile of a
  property-matched decoy distribution** (group enrichment **p = 0.0004**), and
  **five of six engage the catalytic residue Tyr158** — recapitulating the known
  inhibitor's contact fingerprint. TCS-2314 docks −11.2, at the top of all 43
  molecules tested.
- **The two other bacterial targets correctly rejected their nominations** —
  TEM-1 β-lactamase and E. coli DHFR below-wall candidates dock no better (or
  worse) than random decoys. The two-stage QC filters as well as it finds.

*(Figures: repurposing_dock_figure.png, decoy_enrichment_figure.png.)*

### 4. The convergence — one standard, two discovery worlds

Searching the *original* natural-product library for orphans leaning toward InhA,
then docking them on the **identical scale and decoy set**, a true structural
orphan (**CNP0015547.1, nn = 0.41**) docks −10.9, beats the positive control,
clears all 37 decoys, and engages both catalytic residues. **A discovered natural
product and a repurposed drug land on the same target through one pipeline** —
the two discovery modes are the same method pointed at two molecule pools.

*(Figure: unified_inha_figure.png.)*

### 5. The honest null — why the structural stage is necessary

We validated against CO-ADD experimental whole-cell antimicrobial screening data
(pure experimental slice, 156 drugs). Nomination score does **not** predict
whole-cell activity (Mann-Whitney p = 0.75). This is the expected **target–phenotype
gap** — whole-cell killing depends on permeability, efflux, and dose, not
single-target binding — and it is *why* target-level structural validation
(docking), not phenotype screening, is the right instrument for confirming a
target prediction.

---

## What's genuinely ours (honest positioning)

The component methods are not novel — similarity-based NP target prediction,
reverse-docking target fishing, cold-start DTI, and reliability-scored repurposing
all exist in the literature (MolTarPred, TarFisDock, and others; see
`LITERATURE_POSITIONING.md`). What we contribute:

- **The quantified analog wall** at 695k-library scale — a hard, measured boundary
  (Tanimoto 0.5, 63% of NP space) rather than a vague "similarity has limits."
- **Failure-mode QC that caught real errors live** — SIFTS identity + controls +
  decoy enrichment, wired into the pipeline, not run post-hoc.
- **One validation standard across discovery and repurposing** — the convergence
  demonstration.
- **Fully open tooling** where much comparable work is patented or web-server-gated.

**The honest boundary we do not cross:** docking + enrichment + catalytic contacts
is a strong *computational hypothesis*, not evidence of binding. Confirming any
molecule against InhA needs free-energy simulation and, ultimately, a wet-lab
enzyme assay. We present nominations and structurally prioritized hypotheses for
follow-up — never "we found a drug."

---

## How Claude was used

Claude orchestrated the entire pipeline: data harvesting (COCONUT, ChEMBL, Broad
Repurposing Hub, CO-ADD), fingerprinting and screening at 695k scale, GPU/CPU job
dispatch to Modal for docking, figure generation, literature grounding, and a
continuous self-auditing loop that flagged and corrected its own over-statements
(receptor mis-annotations, count errors, framing that outran the evidence).

---

## Reproducibility

All data, analysis scripts, and figures are open in this repository. Every result
above traces to a saved data table (see `/data` and `/figures`).
