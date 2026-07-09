# The Analog Wall: reliability-aware target prediction for natural products and drug repurposing

An open, end-to-end pipeline that predicts protein targets for small molecules —
and, crucially, **quantifies where it stops being trustworthy**. Built and
orchestrated with Claude for the *Built with Claude — Life Sciences* hackathon.

---

## TL;DR

Chemical-similarity target prediction is everywhere in drug discovery, but it has a
failure boundary nobody measures. We measured it: across **695,133 natural products**
(COCONUT 2.0), **~63% are "true structural orphans"** with no close analog among known
binders, and similarity-based prediction provably cannot reach them. The cliff is
sharp — at a Tanimoto coefficient of ~0.5. We call it **the analog wall**.

We then built a pipeline that respects the wall — nominating targets *with a
calibrated confidence score*, and **structurally validating below-wall predictions
by docking with real controls** — and pointed the *same* pipeline at two problems:
**natural-product discovery** and **drug repurposing**.

**Headline result:** a natural-product orphan and an FDA-approved drug, chemically
unrelated, **converge on the same antibacterial target (*M. tuberculosis* InhA)**
through one identical validation standard.

![Convergence on InhA](figures/unified_inha_convergence.png)

---

## The three-layer story

### Impact — the repurposing blind spot
We ran **8,780 approved/clinical drugs × 319 targets → 131,700 predictions**.
**1,218 drugs have credible pathogen-target predictions *below* the analog wall** —
latent antibacterial/antiviral hypotheses that standard similarity screens
systematically miss (1,023 bacterial, 180 viral, plus fungal/parasitic).

![Repurposing map](figures/repurposing_map.png)

### Takeaway — one standard, two discovery worlds
Docking the deepest below-wall drugs against SIFTS-verified bacterial receptors,
**InhA is a reproducible hit target**: 6 candidates dock −9.1 to −11.2 kcal/mol,
4 clear the 5th-percentile of a property-matched **decoy distribution
(enrichment p = 0.0004)**, and 5 of 6 engage the catalytic **Tyr158**. The two
other targets (TEM-1 β-lactamase, *E. coli* DHFR) correctly reject their
nominations — the QC filters as well as it finds.

![Decoy enrichment](figures/decoy_enrichment.png)

A true natural-product orphan from the original library (CNP0015547.1, nn = 0.41)
docks −10.9 on the same scale, beating the positive control — converging with the
repurposed drugs.

### Foundation — the wall and the honest instrument
Everything rests on the quantified analog wall and a reliability-aware two-stage
pipeline. The wall is **source-independent** (reproduces on NP Atlas / CMAUP gap
compounds) — a property of the method, not any dataset.

![Analog-wall system](figures/analog_wall_system.png)
![Method validation](figures/method_capstone.png)

---

## What is (and isn't) novel

Component methods are **not** new — similarity NP target prediction, reverse-docking
target fishing, cold-start DTI, and reliability-scored repurposing all exist
(MolTarPred, TarFisDock, and others; see [`docs/LITERATURE_POSITIONING.md`](docs/LITERATURE_POSITIONING.md)).
Our contribution is **integration + honesty + openness**:

- the **quantified analog wall** at 695k scale (a measured boundary, not a vague caveat);
- **failure-mode QC that caught real errors live** (SIFTS identity + controls + decoy enrichment);
- **one validation standard** across discovery and repurposing;
- **fully open tooling** where comparable work is patented or web-server-gated.

**The boundary we do not cross:** docking + enrichment + catalytic contacts is a
*computational hypothesis*, not proof of binding. Every molecule here is a
structurally prioritized **nomination for follow-up** — never a claimed drug. A
CO-ADD whole-cell validation gave an honest **null** (nomination score does not
predict phenotype — the target–phenotype gap), which is exactly *why* target-level
structural validation is the right instrument.

---

## Repository layout

```
figures/    all publication figures (PNG)
data/       result tables (CSV) + the InhA–TCS-2314 docked complex (PDB)
docs/       full submission writeup + literature positioning
```

### Key data files
| file | what it is |
|---|---|
| `data/repurposing_map.csv` | 3,373 drugs, reliability-tagged pathogen nominations |
| `data/repurposing_docking_combined.csv` | below-wall drugs docked vs bacterial targets + controls |
| `data/decoy_enrichment_results.csv` | enrichment stats vs property-matched decoys, all 3 targets |
| `data/inha_pose_contacts.csv` | catalytic-residue contacts per candidate |
| `data/np_orphan_inha_validation.csv` | NP orphans docked vs InhA on the shared scale |
| `data/coadd_validation_result.csv` | CO-ADD experimental null result |
| `data/method_validation_orphan_recall.csv` | fusion-vs-similarity orphan recall (method validation) |
| `data/inha_TCS2314_complex.pdb` | InhA + top docked pose (open in any viewer) |

---

## Reproducibility & honesty

Every number in the writeup traces to a table in `data/`. Data sources: COCONUT 2.0,
ChEMBL, Broad Drug Repurposing Hub, CO-ADD-derived screening data, RCSB PDB. Docking:
AutoDock Vina. Receptor identity: SIFTS/PDBe. This work was produced during a
hackathon; results are computational hypotheses intended to be reproduced, scrutinized,
and — for the InhA candidates — followed up with free-energy simulation and wet-lab
enzyme assays.

*Built with Claude.*
