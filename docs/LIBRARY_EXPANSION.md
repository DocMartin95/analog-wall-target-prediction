# Library expansion: screening 1.74M CNS-like compounds

A test of whether the reliability-aware pipeline is stable when the input library
grows by ~40× and shifts from natural products to synthetic screening space.

## Motivation

COCONUT 2.0 already covers ~99.9% of known natural-product structural space, so
expanding *natural-product* breadth has diminishing returns. The higher-value move
is a **different** chemical space. Because the framework had just been ported to
CNS targets (AChE, MAO-B), we screened a large CNS-physicochemistry library.

## What we screened

**1,736,189 unique compounds** from ZINC 2D molecular-weight/logP tranches selected
for CNS-like properties (MW 300–350, logP 2–3).

> **Honest scope note.** These are MW/logP-selected 2D tranches. Their commercial
> in-stock/purchasable status was **not verified** — the ZINC purchasability API was
> not reachable from our environment. They are screening hypotheses, not an orderable
> shortlist. The `pool` column is labeled `ZINC_CNS_tranche` accordingly.

## Pipeline (unchanged from the CNS port)

1. **BBB gate** — random-forest classifier (BBBP benchmark, ROC-AUC 0.893), keep predicted-permeant.
2. **Nominate + analog wall** — rank AChE/MAO-B targets, annotate each with `nn_analog` distance.
3. **Dock representative above-wall candidates** — Vina vs AChE 4EY7 / MAO-B 2V5Z, controls + decoys.

## Results

| stage | count |
|---|---|
| ZINC CNS tranche (unique) | 1,736,189 |
| BBB-permeant | 1,695,813 (97.7%) |
| above-wall nominations (analog-supported) | 2,033 |
| below-wall (0.35–0.5) nominations | 29,785 |
| docked shortlist | 8 |

**Behavior is stable at scale.** The 97.7% permeant fraction matches the 40k pilot
and is far above the 75.2% seen on a mixed drug/NP pool — confirming the CNS-tranche
selection worked. The analog wall stratified the library as expected.

**Docking — same honest result as the CNS pilot.** Controls validated (donepezil
−11.46, safinamide −9.96, both far above decoy median), but **no candidate cleared
the decoy 5th percentile**. The strongest MAO-B nominations (ZINC3181507 −9.82,
ZINC62117537 −9.75) only *approached* positive-control strength. The top AChE
nomination (ZINC4038896) is a galantamine-like benzofuran — a recovery signal that
the library surfaces analogs of validated chemotypes.

Several library compounds matched known binders at Tanimoto 1.0 — a recovery sanity
check, not a discovery.

## Interpretation

The value of this experiment is **not** a hit. It is a demonstration that the
reliability-aware protocol behaves identically when the library is 40× larger and
from a different chemical origin: it gates, stratifies, validates its controls, and
declines to over-nominate. The instrument is portable and its calibration holds at
scale.

See `figures/zinc_screen_figure.png` and `data/zinc_dock_results.csv`.
