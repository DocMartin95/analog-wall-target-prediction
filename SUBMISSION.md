# Where the map runs out: an open, reliability-aware pipeline for target prediction, from natural products to drug repurposing to antimicrobial resistance

**Built with Claude, Life Sciences hackathon submission**

---

## The one-line takeaway

**A natural-product orphan and an FDA-approved drug, chemically unrelated, from
opposite ends of the discovery world, converge on the same antibacterial target
(*M. tuberculosis* InhA) when passed through one honest validation standard.**
That convergence is only visible because the pipeline is built to work *below the
analog wall*: the region of chemical space where conventional similarity-based
target prediction provably fails.

---

## Why this matters (the impact hook)

Target prediction by chemical similarity is everywhere in drug discovery, but it
has a failure boundary nobody quantifies. We measured it: across the full COCONUT
2.0 natural-product library (695,133 compounds), **~63% of natural products are
"true structural orphans"** with no close analog among known binders. For those,
similarity-based target prediction cannot work, not "works poorly," *cannot
work*, and the cliff is sharp, at a Tanimoto coefficient of ~0.5.

The same blind spot exists for **drug repurposing**. When we ran 8,780 approved
and clinical-stage drugs through target prediction against pathogen targets,
**1,218 drugs had credible pathogen-target predictions that sit *below* the analog
wall**, predictions that standard similarity screens systematically miss. These
are approved drugs, already safety-tested in humans, with latent antibacterial /
antiviral target hypotheses that the usual tools cannot surface.

**The problem the field has:** a flood of ranked predictions with no way to know
which to trust. **What we built:** a pipeline that quantifies exactly where its own
predictions are unreliable, then structurally validates the ones below the wall
with real controls, applied identically to natural-product discovery *and* drug
repurposing.

---

## What we built

An open, end-to-end, reliability-aware target-prediction pipeline:

1. **Nominate**, for any molecule, rank candidate protein targets by similarity to
   known binders, with a **calibrated confidence score** that reports how far the
   query sits from the nearest known analog (the `nn_analog` distance).
2. **Locate it against the analog wall**, every prediction is placed relative to
   the Tanimoto-0.5 boundary, so a user knows immediately whether a hit is
   analog-supported or a genuine below-wall extrapolation.
3. **Structurally validate below the wall**, below-wall candidates go to docking
   (AutoDock Vina) with a full QC layer: positive/negative controls, **SIFTS
   receptor-identity checks**, and **decoy-enrichment tests** against
   property-matched random molecules.

Every stage was orchestrated with Claude, and the QC layer **caught real errors
live**, SIFTS flagged mis-annotated receptors (a rabbit ortholog labeled human, a
PKC-θ structure labeled PKC-δ), and negative controls invalidated docking boxes
where caffeine out-scored the known inhibitor.

---

## Results

### 1. The analog wall, the foundation everything rests on

Across 695k natural products, similarity-based target prediction has a hard
reliability ceiling at Tanimoto ~0.5; ~63% of the library falls below it. The
boundary is **source-independent**, it reproduces identically on natural products
from independent databases (NP Atlas, CMAUP gap compounds), confirming it is a
property of the *method*, not any one dataset.

*(Figure: analog-wall system overview and stratified recall.)*

### 2. Cross-kingdom repurposing map, the blind-spot inventory

8,780 approved/clinical drugs × 319 targets → 131,700 predictions. Of drugs with a
credible pathogen-target nomination: **2,155 above the wall, 1,218 below it** (the
2D-blind inventory), including **282 deep orphans** (nn < 0.35). The below-wall set
spans bacterial (1,023), viral (180), fungal, and parasitic targets.

*(Figure: repurposing_map.png.)*

### 3. Docking rescue + decoy enrichment, the validation

We docked the deepest below-wall candidates against SIFTS-validated bacterial
receptors. The result is a clean, honest, two-sided finding:

- **InhA (*M. tuberculosis*) is a reproducible hit target.** Six below-wall drugs
  dock −9.1 to −11.2 kcal/mol; **four clear the 5th-percentile of a
  property-matched decoy distribution** (group enrichment **p = 0.0004**), and
  **five of six engage the catalytic residue Tyr158**, recapitulating the known
  inhibitor's contact fingerprint. TCS-2314 docks −11.2, at the top of all 43
  molecules tested.
- **The two other bacterial targets correctly rejected their nominations:**
  TEM-1 β-lactamase and E. coli DHFR below-wall candidates dock no better (or
  worse) than random decoys. The two-stage QC filters as well as it finds.

*(Figures: repurposing_docking.png, decoy_enrichment.png.)*

### 4. The discovery arm, orphans validate against their own nominated targets

This is what the project was built to do: take a natural product with no known
target and nominate one, then check it structurally. We screened **258,995 COCONUT
natural products** against 287 protein targets and bridged those targets to
**1,134 target–disease hypotheses** (tuberculosis, bacterial/viral infection,
malaria). Three checks show it works, not just ranks:

- **Proof of concept on *E. coli* DHFR:** a true structural orphan (CNP0255558.1)
  docks −7.50 kcal/mol, **beating the trimethoprim positive control (−7.38)**, a
  compound no similarity search could reach, outscoring an approved antifolate.
- **Agnostic-target campaign:** docking orphans against *their own* nominated
  bacterial targets, an orphan beats the positive control on *E. coli* DHFR
  (−9.05 vs −8.32) and always clears the negative control.
- **Blind-input check:** SCH-79797, a known multi-target compound, run through the
  pipeline with no target specified, docks strongest against **InhA (−10.33)** among
  a five-target panel, the pipeline surfaces a mechanistically plausible target on
  its own.

*(Figure: discovery_arm_figure.png.)*

### 5. The convergence, one standard, two discovery worlds

Searching the *original* natural-product library for orphans leaning toward InhA,
then docking them on the **identical scale and decoy set**, a true structural
orphan (**CNP0015547.1, nn = 0.41**) docks −10.9, beats the positive control,
clears all 37 decoys, and engages both catalytic residues. **A discovered natural
product and a repurposed drug land on the same target through one pipeline.**
The two discovery modes are the same method pointed at two molecule pools.

*(Figure: unified_inha_convergence.png.)*

**We stress-tested both halves of this claim.** Enlarging the InhA reference set
from 7 to **273 ChEMBL binders** and re-docking, the headline orphan reconfirms
(−11.28, clears all 37 decoys) and a **second independent orphan (CNP0541259.0,
−10.11)** also clears the decoy 5th percentile: the natural-product arm is now two
validated orphans, not one. We then hunted for a *second* convergence target
(HIV-1 reverse transcriptase) and report an **honest null**: the docking positive
controls fail there too, so the polymerase site is one rigid docking cannot resolve,
and the pipeline correctly refuses to promote the lead. A method that only ever
confirmed itself would be the warning sign; this one rejects what it cannot support.

*(Figure: weekend_validation_figure.png.)*

### 6. The resistance extension, will the target still be druggable after mutation?

A target nomination is only useful for antimicrobials if the target stays
druggable once resistance arises. Rigid docking cannot answer this: known drugs
score essentially unchanged (ΔΔG ≈ 0) against their own clinical resistance
mutations. So we built the piece no existing resource provides: a **curated
mutant-aware target database** (10 priority pathogen targets, 76 resistance
mutations, all from X-ray structures) and a **control-validated ΔΔG layer**
(mCSM-lig class) that must pass a hard positive-control gate before we trust any
natural-product score. It does: it recovers the affinity loss of trimethoprim
against *S. aureus* DHFR-F98Y and pyrimethamine against *P. falciparum*
DHFR-S108N, leave-target-out (88 to 92% correct destabilization direction).

We then screened **163 below-wall natural-product orphans over 626
ligand–mutation pairs** across the nine targets that yield scorable complexes. A
subset of natural products is predicted **more resistance-robust than the
front-line drug**, led on the validated DHFR family by **seven orphans below the
trimethoprim-class control's worst-case liability**. This is the resistance
question answered with the same reliability-aware discipline as the rest of the
pipeline, and it turns "here is a candidate target" into "here is a candidate that
may still work after the target mutates."

*(Figure: mutant_selective_screen.png.)*

### 7. Orthogonal confirmation, the InhA poses aren't a docking artifact

Docking scores can reflect the box or the scoring function. So we re-ran the InhA
hits through **Boltz-2 co-folding**, an independent, *ab initio* method that
predicts the whole protein–ligand complex without our docking box. All six ligands
returned **high interface confidence (ligand-ipTM 0.83–0.97)**, matching the known
binder (0.90). A second, structurally independent method placing the same molecules
in the same pocket corroborates the result. (Honest note: Boltz-2's *affinity*
module did not discriminate our compounds, so we use co-folding for pose support
only, not as a binding-strength claim.)

*(Figure: cofold_rescue.png.)*

### 8. It generalizes, same protocol, a whole new disease area

To show the framework isn't a one-target trick, we ported it **unchanged** to
Alzheimer's disease targets (acetylcholinesterase, MAO-B), adding one upstream
step, a **blood–brain-barrier gate** (a classifier at AUC 0.89) so only
brain-penetrant molecules are nominated. Applied to **1.74 million** CNS-like
compounds: 97.7% passed the BBB gate, the analog wall stratified them
(2,033 high-confidence above-wall nominations), and docking **controls validated**
(donepezil, safinamide both at the 86th percentile of decoys). The honest part:
**no candidate cleared the strict decoy threshold**, the permissive CNS pockets
don't discriminate like InhA's, and the pipeline *says so* instead of promoting a
weak hit. That's the whole point: a reliability-aware instrument that transfers to
a new disease area and knows when to stay quiet.

*(Figure: alzheimers_pilot.png, zinc_screen_figure.png.)*

### 9. The honest null, why the structural stage is necessary

We validated against CO-ADD experimental whole-cell antimicrobial screening data
(pure experimental slice, 156 drugs). Nomination score does **not** predict
whole-cell activity (Mann-Whitney p = 0.75). This is the expected **target–phenotype
gap**, whole-cell killing depends on permeability, efflux, and dose, not
single-target binding, and it is *why* target-level structural validation
(docking), not phenotype screening, is the right instrument for confirming a
target prediction.

---

## A companion application: microbiome metabolites to host targets

The same reliability-aware framework, pointed in the forward direction at a
different problem, is written up as a standalone short paper
(`PROJECT_B_PAPER.md`). Applied to 1,537 microbial-origin metabolites, it nominates
host protein targets, recovers 3 of 3 testable known microbiome-to-host axes above
the wall (niacin to HCAR2, secondary bile acids to FXR and TGR5), and after a
structural drug-origin filter that separates *Streptomyces* pharmaceuticals
recovering known pharmacology, surfaces a flagship novel hypothesis: a cluster of
**9 microbial carotenoids that all nominate the xenobiotic-sensing receptor PXR**,
which every one confirms by docking within 1 kcal/mol of the canonical agonist
rifampicin. It is the same instrument, generalizing to a second domain.

---

## What's genuinely ours (honest positioning)

The component methods are not novel, similarity-based NP target prediction,
reverse-docking target fishing, cold-start DTI, and reliability-scored repurposing
all exist in the literature (MolTarPred, TarFisDock, and others; see
`LITERATURE_POSITIONING.md`). What we contribute:

- **The quantified analog wall** at 695k-library scale, a hard, measured boundary
  (Tanimoto 0.5, 63% of NP space) rather than a vague "similarity has limits."
- **Failure-mode QC that caught real errors live**, SIFTS identity + controls +
  decoy enrichment, wired into the pipeline, not run post-hoc.
- **A working natural-product discovery arm** at 258,995-compound scale, where true
  orphans validate structurally against their own nominated targets (one beats an
  approved antifolate on *E. coli* DHFR).
- **One validation standard across discovery and repurposing**, the convergence
  demonstration.
- **A resistance-aware extension no existing resource provides**, a curated
  mutant-aware pathogen-target database plus a control-validated ΔΔG layer that
  answers whether a nominated target stays druggable after mutation, and nominates
  natural products predicted more resistance-robust than the front-line drug.
- **Orthogonal structural confirmation**, the InhA poses survive an independent
  *ab initio* co-folding method, not just docking.
- **It generalizes**, the same reliability-aware protocol ports to a new disease
  area (Alzheimer's / CNS targets) and a 1.74M-compound library, transferring
  cleanly and honestly declining to over-nominate.
- **Fully open tooling** where much comparable work is patented or web-server-gated.

**The honest boundary we do not cross:** docking + enrichment + catalytic contacts
is a strong *computational hypothesis*, not evidence of binding. Confirming any
molecule against InhA needs free-energy simulation and, ultimately, a wet-lab
enzyme assay. We present nominations and structurally prioritized hypotheses for
follow-up, never "we found a drug."

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
