# Future Directions — Natural-Product → Target-Prediction Pipeline

**Strategy scout report.** Ranked next-project directions for the reliability-aware NP
target-prediction stack (the "analog wall" + two-stage similarity/docking pipeline + calibrated
trust layer). Every idea below is grounded in real tools, repos, and papers with identifiers,
and assessed on **novelty · feasibility (on ~$21 Modal + Claude credits, several days) · effort ·
connection to what we already built.**

Bottom line up front: our defensible identity is *reliability-aware characterization of where
computational NP→target methods break*, not a new generative or docking method. The rankings
reward directions that deepen that identity and are honest about which ideas are crowded.

> **Provenance note.** All DOIs, PMIDs, PMC IDs, bioRxiv/arXiv IDs, and the repos `NVlabs/f-RAG`
> and `datamol-io/safe-gpt` cited below were retrieved via web search during this session and
> appear verbatim in those search results. Two items are flagged inline as domain knowledge rather
> than retrieved records: the additional pocket-diffusion tool names (Pocket2Mol/TargetDiff/DiffSBDD/
> DecompDiff/MolCRAFT) and REINVENT4's GitHub repo path/license — verify those against the primary
> sources before relying on them.

---

## Ranking (most to least recommended)

| # | Direction | Novelty | Feasibility ($21) | Effort | Connection |
|---|-----------|---------|-------------------|--------|------------|
| 1 | Co-fold **reliability wall** for NPs (3D analog to the 2D wall) | **High** | Medium (subset) | Medium | Direct extension |
| 2 | Co-fold **orthogonal rescue** of our top hits (Boltz-2/Chai-1) | Low–Med | **High** | **Low** | Direct extension |
| 3 | Generative design seeded on below-wall binders (user's idea) | Low as posed / Med if reframed | Medium | Med–High | Extension (risk: me-too) |
| 4 | Reliability layer ported to a new question (target–phenotype gap) | Medium | High | Medium | Orthogonal reuse |

---

## Idea 1 — The co-folding "reliability wall" for natural products *(top pick, novel)*

**Claim.** We quantified a *2D-similarity* wall: below Tanimoto ~0.5, similarity-based target
prediction fails and ~63% of COCONUT NPs are orphans. The obvious, un-taken next question is
whether there is an analogous **3D structure-prediction reliability wall** — i.e. does co-folding
(Boltz-2 / Chai-1 / AF3), the tool the field now reaches for to "validate" a docking hypothesis,
*systematically degrade on natural-product chemical space* and as a function of distance from the
PDB training distribution? Deliver a stratified reliability curve + a per-prediction NP-aware
trust score.

**Novelty — high.** Co-folding reliability is being probed, but only on drug-like/PDB-adjacent
ligands, and *nobody has characterized it against NP structural features at library scale threaded
through a wall metric.* Concrete evidence the gap is real and open:
- Carbohydrate/glycoside ligands: **85.8% of 900 carbohydrate ligands had errors in AlphaFold3**
  (chirality, bond conversions, ring distortions); Boltz-1x fixes chirality but introduces 22.1%
  improper configurations — *Physical Implausibility of Carbohydrate Ligands...* (J. Chem. Inf.
  Model., DOI 10.1021/acs.jcim.5c03075).
- Stereochemistry violations in AF3/Boltz-1 protein–ligand complexes are "more prevalent than
  currently appreciated" (bioRxiv 2025.03.25.645362).
- Co-folding "relies heavily on memorization of training complexes rather than truly learning...
  molecular interactions," limiting generalization to novel ligands (bioRxiv 2025.12.04.692352,
  *AlphaFold3 for Structure-guided Ligand Discovery*; and the Apheris analysis: accuracy drops on
  targets/chemotypes underrepresented in training).
NPs are exactly the OOD case: macrocycles, dense stereocenters, sugars, unusual ring systems. No
one has drawn the reliability-vs-NP-features curve. This is a *characterization* contribution —
the same kind of contribution as the analog wall itself, which is our proven strength.

**Feasibility — medium, budget-bound.** Boltz-2 is open (MIT; Passaro et al., bioRxiv
2025.06.14.659707, PMID 40667369) and runs on Modal (bundled Boltz image already exists). Inference
is ~minutes/complex on A100. To stay under ~$21: stratified **subset** of ~100–150 NP→target pairs
(not the full 695k), binned by RDKit descriptors we already compute (macrocycle flag, #stereocenters,
sugar/glycoside SMARTS, Bemis–Murcko novelty, and analog-wall Tanimoto bin). Score each prediction
with PoseBusters physical-validity + confidence (pLDDT/ipTM) and, where a crystal exists, RMSD.
Batch aggressively; cap the campaign with a hard spend guard.

**Connection — direct.** Extends the wall from 2D → 3D; reuses the 695k library, the RDKit feature
layer, and the calibrated-trust philosophy verbatim. Output is a new "don't-trust-here" region for
the *validation* stage of our own pipeline — it makes the honesty layer stronger, not just wider.

**First experiment (concrete).**
1. Sample ~120 NP→target pairs from existing artifacts, stratified: 3 analog-wall bins × {macrocycle,
   glycoside, high-stereocenter, "drug-like" control} — include the InhA orphan CNP0015547.1 pair as
   an anchor.
2. On Modal, run Boltz-2 co-fold for each; capture confidence, predicted affinity, PoseBusters pass,
   and (where PDB holo exists) ligand-RMSD.
3. Plot physical-validity / confidence vs. NP-feature bin and vs. analog-wall bin. Test the hypothesis:
   *validity and confidence fall off with NP-character and with wall distance.* Ship the curve + a
   per-prediction NP-reliability flag.

---

## Idea 2 — Orthogonal co-fold rescue of our validated hits *(do this first; cheapest, highest certainty)*

**Claim.** Our InhA convergence result (TCS-2314 repurposing hit + CNP0015547.1 NP orphan, both
docking −11 kcal/mol, beating 37 decoys p=0.0004, engaging Tyr158) currently rests on
Vina + enrichment + contacts. Add a **third, method-orthogonal** signal by co-folding each top hit
with its target and reading out Boltz-2's pose agreement + affinity head. Agreement across an
independent architecture materially hardens the hypothesis; disagreement is itself informative and
on-brand for the "two-failure-mode" framing.

**Novelty — low-to-medium (as a *method*).** Co-folding-as-docking-rescore is now established:
- Boltz-2 "excels at discriminating true from false positives, overcoming by a large margin all
  scoring functions tested so far on raw docking poses" (J. Chem. Inf. Model., DOI
  10.1021/acs.jcim.5c02630).
- Prospective 557-ligand Mac1 study: Boltz-2 affinity showed the *strongest* correlation with
  measured potency and best rescored docking hit-lists (AmpC, D4, σ2) vs AF3/Chai-1 (PMC12776374).
So this is not a new method — but applied to *our specific below-wall hits* as confirmatory
cross-validation it is a legitimate, expected strengthening step, not a repeat of someone's paper.

**Feasibility — high.** A handful of complexes (top ~10–20 hits from both pools) → a few dollars,
a few hours, using the existing Modal Boltz image. Well inside budget. Chai-1 can be run as a second
independent co-folder for consensus.

**Connection — direct extension.** Slots straight into the current validation stage as an added
orthogonal column next to Vina/enrichment/SIFTS. No new conceptual scaffolding.

**First experiment.** Co-fold the InhA pair (both ligands) + top hits with Boltz-2 (and Chai-1 for
consensus); compare predicted pose to the Vina pose (ligand-RMSD, Tyr158 contact retained?) and
report the Boltz-2 affinity. Flag hits where the two methods *agree* as strengthened and where they
*disagree* as the scientifically interesting cases (promiscuity vs annotation-bias, per our framing).

---

## Idea 3 — Generative / de novo design seeded on below-wall binders *(the user's idea — evaluate honestly)*

**Verdict: crowded as posed; salvageable only with a specific reframing tied to the wall.**

**Novelty — low as literally posed.** "Use generative AI to design InhA-focused analogs of our
hits" hits two saturated areas at once:
- Generative small-molecule design is a mature, commoditized stack (all IDs below appear in the
  retrieved search results): **REINVENT4** (J. Cheminform. 2024, DOI 10.1186/s13321-024-00812-5;
  de novo, scaffold decoration, linker design, scaffold hopping, RL/TL/curriculum);
  **SAFE / SAFE-GPT** (Digital Discovery 2024, DOI 10.1039/D4DD00019F; arXiv:2310.10773; HF model
  datamol-io/safe-gpt, fragment-constrained design); **f-RAG** (NeurIPS 2024, arXiv:2411.12078;
  github NVlabs/f-RAG; fragment retrieval-augmentation on SAFE-GPT); and pocket-conditioned 3D
  generators — retrieved example: *Generation of 3D molecules in pockets via a language model*
  (Nature Mach. Intell. 2023, nature.com/articles/s42256-023-00775-6). Other widely-cited members
  of this structure-based family (Pocket2Mol, TargetDiff, DiffSBDD, DecompDiff, MolCRAFT on the
  CrossDocked2020 benchmark) are named here from domain knowledge, not from a retrieved record.
- InhA is one of the most heavily prosecuted antibacterial targets in medchem; generative InhA
  design specifically would be a me-too demo.
A generic "design pretty molecules for InhA" run adds nothing the field hasn't done and cuts against
the user's stated aversion to repeating existing work.

**The one reframing that would make it novel:** use generation as a probe of the **wall itself** —
generate *synthesizable* analogs that deliberately walk a true structural orphan back across
Tanimoto ~0.5, and ask whether the *prediction pipeline's own confidence recovers* as designs cross
the wall. That turns generative design from "make a molecule" into "map the reachability boundary of
our own method" — which is on-brand and unclaimed. Be aware the readout risks circularity (docking
your own designs), so it must be paired with the co-fold reliability check from Idea 1 and synthetic-
accessibility filtering.

**Feasibility — medium.** REINVENT4's RNN prior + Vina scoring is largely CPU-friendly and cheap;
f-RAG needs training a small fragment-injection module; pocket-diffusion needs GPU but generation is
cheap. All installable. The cost is *effort* (a whole new stack) and *validation debt* (payoff needs
MD/free-energy then wet-lab, which we've honestly flagged is far off).

**Connection — extension, but with drift risk.** Uses our validated binders as seeds, but easily
becomes an orthogonal side-quest that dilutes the reliability-characterization identity. Recommend
only *after* Ideas 1–2, and only in the wall-crossing framing.

**First experiment (if pursued).** Take CNP0015547.1 as seed; run REINVENT4 scaffold-decoration /
Mol2Mol with a multi-objective score = (Vina to InhA) + (SA score) + (Tanimoto trajectory toward the
nearest known InhA binder). Track whether pipeline confidence + co-fold validity rise monotonically
as generated analogs approach the wall. Report the reachability map, not a "hit."

---

## Idea 4 — Port the reliability/calibration layer to the target–phenotype gap *(orthogonal, reuses assets)*

**Claim.** Our honest NULL from CO-ADD (nomination score does not predict whole-cell antimicrobial
phenotype) is itself a finding. Reframe it as the headline question: *build a calibrated model of the
target→phenotype gap for antibacterials* — which target-level predictions do/do not survive to
whole-cell activity, and can permeability/efflux features (Gram−/Gram+ split) recalibrate the trust
score. This reuses the calibration machinery and the wall concept on a genuinely different axis.

**Novelty — medium.** The gap is widely acknowledged but rarely *quantified as a calibration
problem* with an explicit "phenotype-reachability" score layered on target predictions.

**Feasibility — high, mostly CPU.** Uses existing CO-ADD/screen data + RDKit permeability descriptors;
little/no GPU. Lower risk, complements the two enrichment/docking tracks already planned.

**Connection — orthogonal reuse.** Same reliability philosophy, new endpoint. Good "second paper"
seed rather than an immediate compute campaign.

---

## Recommended sequence

1. **Today / cheap:** Idea 2 (co-fold rescue of top hits) — a few dollars, hardens the InhA claim
   with an independent method before any bigger spend.
2. **Novel headline:** Idea 1 (co-fold reliability wall) — the genuinely new contribution, a direct
   3D extension of our signature result; run a budget-capped stratified subset on Modal.
3. **Only if reframed:** Idea 3 (generative wall-crossing), and Idea 4 as a low-compute parallel /
   next-paper seed.

**What to avoid:** a generic generative-design demo, or "yet another reverse-docking target-fishing"
tool — both are crowded (MolTarPred, TarFisDock, SwissTargetPrediction, SEA) and add no novelty. Our
edge is characterizing *where methods fail on NPs*, with calibrated honesty — every top pick above
deepens that, rather than competing on method horsepower we don't have the budget to win.

*All docking/co-folding outputs remain computational hypotheses, not validated binders — the same
boundary we've held throughout. These directions strengthen or map the hypothesis space; none
substitute for MD/free-energy then wet-lab assay.*
