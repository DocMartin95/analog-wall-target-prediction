# An open, reliability-aware framework for structural target nomination of natural products and repurposed drugs beyond the 2D-similarity wall

## Abstract

Two-dimensional (2D) molecular similarity is the workhorse of ligand-based target prediction, but it fails silently for compounds that have no close analog in reference databases. We show that this failure defines a large, systematic blind spot in drug repurposing. Scoring 8,780 approved and clinical-stage drugs against 319 learnable protein targets, we find that 1,218 drugs carrying a credible pathogen-target nomination sit **below** a Tanimoto ≈ 0.5 analog wall — the region where similarity-based methods are least trustworthy — and 282 are deep structural orphans (nearest-neighbor Tanimoto < 0.35). This below-wall inventory is dominated by antibacterial nominations (1,023 of 1,218) and is invisible to purely 2D pipelines. We assemble a pipeline of established components — RDKit Morgan fingerprints, protein language-model embeddings (ESM-2), homology-based target identification, and AutoDock Vina reverse docking — and, critically, we make it reliability-aware: every nomination is annotated with its distance to the nearest training analog and passed through property-matched decoy enrichment and catalytic-contact checks before it is reported. On the true structural-orphan stratum, a naive per-compound fusion of similarity and a multi-task neural predictor recovers recall@10 of 0.571 versus 0.547 for similarity alone (McNemar p = 0.006), a small but reproducible gain across three scaffold-disjoint splits. Our headline result is a convergence: a below-wall approved-drug set and a structurally orphan natural product independently nominate *Mycobacterium tuberculosis* enoyl-ACP reductase (InhA) and are validated on a single decoy scale. Four of six below-wall drugs clear the decoy 5th percentile (docking −9.1 to −11.2 kcal/mol; group enrichment Mann-Whitney p = 0.0004; five of six engage the catalytic Tyr158), while the natural-product orphan CNP0015547.1 (nearest-neighbor Tanimoto 0.41) docks at −10.92 kcal/mol, beats the positive control, clears all 37 decoys, and contacts the Tyr158–Lys165 catalytic pair. Negative controls behave as required (TEM-1 β-lactamase enrichment p = 0.95; *E. coli* DHFR candidates rank below the decoy median), and an experimental whole-cell null (CO-ADD, n = 156) confirms that nomination score does **not** predict phenotype (Mann-Whitney p = 0.75) — the target–phenotype gap that motivates target-level, rather than phenotype-level, computational triage. Our contribution is **integration, rigor, openness, and a quantified limitation**, not a new algorithm or a claimed binder. Every molecule here is a structurally prioritized nomination for free-energy simulation and wet-lab follow-up.

## 1. Introduction

Ligand-based target prediction underpins much of computational drug repurposing: if a query compound is structurally similar to a molecule with a known target, the target is inferred by transfer. Methods built on this premise — nearest-neighbor similarity searching, similarity-ensemble approaches, and their descendants — are fast, interpretable, and effective when a close analog exists. Reverse (target-fishing) docking such as TarFisDock and its successors offers a complementary, structure-based route, and cold-start deep learning for drug–target interaction (DTI) attempts to generalize to unseen chemistry. These approaches are prior art, and a reader will already know them (e.g., MolTarPred and related similarity target-prediction tools; TarFisDock-style reverse docking; cold-start DTI benchmarks such as those surveyed in PMC10611213; and natural-product target prediction by chemical-similarity transfer in the PLOS Computational Biology literature). We do not claim to improve on any of them algorithmically.

The problem we address is a blind spot that all similarity-transfer methods share and that is rarely quantified: they degrade precisely for compounds with no near analog, yet report a nomination anyway. Natural products are the acute case. Many are well documented pharmacologically but lack a co-crystallized target, and a large fraction occupy chemical space with no close relative in target-annotated databases. A method that is silent about its own applicability domain will therefore hand a confident-looking target to exactly the compounds for which it is least reliable.

We make three moves. First, we **quantify the blind spot** as an inventory: how many repurposable drugs carry a credible pathogen-target nomination while sitting below the similarity wall, and are thus effectively invisible to a 2D pipeline. Second, we build a pipeline whose distinguishing property is **reliability-awareness** — every nomination carries its distance to the nearest training analog and is only reported as a lead candidate after structural filtering (decoy enrichment plus catalytic-contact geometry). Third, we demonstrate a **convergence**: the natural-product discovery track (unknown compound → new target) and the drug-repurposing track (known drug → new pathogen target) are the same phenomenon — the analog wall — seen from two sides, and they meet at a single validated target, InhA.

Framed plainly: the hook is the repurposing blind spot; the headline is the InhA convergence; the foundation is the analog wall and an honest instrument that knows when it is guessing.

## 2. Methods

### 2.1 Chemical libraries and the analog wall

Natural-product structural space was surveyed using COCONUT 2.0 (695,133 natural products). For every compound we computed the nearest-neighbor Tanimoto similarity (`nn_analog`) to the target-annotated benchmark set using RDKit Morgan fingerprints (radius 2, 2,048 bits). We define the **analog wall** at Tanimoto ≈ 0.5: compounds with `nn_analog < 0.5` are "below the wall" (no close annotated analog), and those below 0.35 are deep structural orphans. Approximately 63% of COCONUT are true structural orphans by this criterion. The gap is source-independent: the same below-wall population reproduces on independent natural-product collections (NP Atlas, CMAUP gap compounds), so it is a property of natural-product chemistry rather than of one database's coverage.

### 2.2 Benchmark and learnable targets

The learnable benchmark comprises 15,418 known compound–target pairs spanning 9,259 distinct compounds and 319 protein targets with at least 5 annotated binders each (the minimum for a learnable per-target model). Bioactivity pairs were drawn from a curated Kd/affinity subset. Compounds were represented by 2,048-bit Morgan fingerprints; targets by frozen ESM-2 protein-language-model embeddings.

### 2.3 Target-nomination models

Three predictors were compared on identical inputs:
1. **Similarity transfer** — nearest-neighbor Tanimoto vote over annotated binders (the standard ligand-based baseline).
2. **Multi-task MLP** — a shared-representation neural classifier over the 319 targets, trained on the benchmark pairs.
3. **Per-compound fusion** — a naive rank fusion of (1) and (2) computed independently for each compound; no additional parameters are learned by the fusion step.

We evaluated on the **true structural-orphan stratum** (`nn_analog < 0.5`, n = 1,026 held-out compounds) because this is the region where similarity transfer is expected to fail and where any added value must appear. The primary metric is recall@10 (fraction of compounds whose true target is in the top 10 nominations). Statistical comparison between the fusion and the similarity baseline used McNemar's test on paired per-compound hit/miss outcomes. Robustness was assessed on three scaffold-disjoint train/test splits (Bemis–Murcko scaffolds fully partitioned between train and test, so no test scaffold is seen in training).

We tested ESM-2 embeddings as an auxiliary target feature; over a baseline of 2D fingerprints plus homology-based target transfer, ESM-2 added no measurable recall on the orphan stratum, and it is therefore not part of the reported nomination score (it is retained only for target representation in the multi-task model).

### 2.4 Drug-repurposing screen

8,780 approved and clinical-stage drugs were scored against the 319 learnable targets, producing 131,700 retained drug–target predictions. Each drug was annotated with its `nn_analog`, its wall side (above/below 0.5), its orphan depth (edge 0.45–0.5, mid 0.35–0.45, deep < 0.35), and the organism class of the nominated target (bacterial/viral/fungal/parasitic). A drug was recorded as carrying a *credible pathogen nomination* when its best-scoring pathogen target passed the calibrated score threshold; 3,373 drugs met this bar and constitute the repurposing map (one nomination per drug).

### 2.5 Structural validation: reverse docking and decoy enrichment

Candidates advancing to structural validation were docked with AutoDock Vina 1.2.7. Receptor identity and binding-site definition were fixed using SIFTS/PDBe residue mappings, and docking boxes were centered on the annotated catalytic/orthosteric site. For each target we assembled a property-matched decoy set (37 decoys matched on molecular weight) to estimate a null docking-score distribution. A candidate was called **enriched** when its Vina affinity fell in the top tail of the decoy distribution; we report per-candidate z-scores, the count of decoys scoring stronger, and a group-level Mann-Whitney enrichment p-value comparing candidate affinities against decoys. Each candidate was paired with a positive control (a known binder) and a negative control. Pose plausibility was assessed by pocket centroid-to-box distance and by explicit contact with catalytic residues (e.g., InhA Tyr158, Lys165).

### 2.6 Negative and experimental controls

Two negative-control targets tested filter specificity: TEM-1 β-lactamase and *E. coli* dihydrofolate reductase (DHFR), each challenged with below-wall candidates that should not be enriched. As an experimental null for the target→phenotype step, we used a pure whole-cell antimicrobial slice from CO-ADD (n = 156 drugs; 42 active, 114 inactive) and tested whether the nomination score separates phenotypic actives from inactives by Mann-Whitney U.

### 2.7 Software

RDKit (Morgan fingerprints, scaffold decomposition), `fair-esm` (ESM-2 embeddings), AutoDock Vina 1.2.7 (docking), SIFTS/PDBe (receptor identity), and standard SciPy statistics. All code and intermediate tables are released (Section 7).

## 3. Results

### 3.1 The similarity wall is a real, source-independent boundary

Across COCONUT 2.0 (695,133 compounds), roughly 63% of natural products have no annotated analog within Tanimoto 0.5 (Figure 4). The distribution is not an artifact of one database: the same below-wall fraction reproduces on NP Atlas and CMAUP gap compounds. Because ligand-based transfer is only as good as its nearest annotated neighbor, this majority of natural-product space is, by construction, the domain where a 2D pipeline is least reliable and yet gives no warning.

### 3.2 A reliability-aware fusion recovers a small, reproducible edge on true orphans

On the true structural-orphan stratum (`nn_analog < 0.5`, n = 1,026), similarity transfer achieves recall@10 = 0.547 and the multi-task MLP alone is worse (0.459) — a language-model/neural predictor does not, on its own, beat nearest-neighbor voting for orphans. Their per-compound fusion reaches recall@10 = 0.571, a +0.024 absolute gain over similarity that is statistically reliable (McNemar p = 0.006; 51 compounds rescued by fusion vs. 26 lost). The gain reproduces across three scaffold-disjoint splits (+0.032, +0.024, +0.025), i.e., it is not a single-split accident (Figure 5). We emphasize the modesty of this result: the value of the instrument is not a large accuracy jump but that it flags orphan status explicitly and still extracts a defensible signal where the naive neural model degrades.

### 3.3 The repurposing blind spot: 1,218 credible nominations live below the wall

Of the 3,373 drugs carrying a credible pathogen-target nomination, 2,155 sit above the analog wall and **1,218 sit below it** (Figure 1). This below-wall set — the "2D-blind inventory" — is what a similarity-only pipeline would either miss or mis-rank. It is overwhelmingly antibacterial: 1,023 bacterial, 180 viral, 10 fungal, and 5 parasitic nominations. A deep-orphan core of 282 drugs (`nn_analog < 0.35`) has essentially no annotated structural relative at all. This inventory is the practical payload of quantifying the wall: it is a ranked, structurally flagged list of repurposing hypotheses that conventional 2D triage systematically overlooks.

### 3.4 Structural validation filters correctly on controls

Before interpreting any positive, we confirmed that the docking/enrichment filter rejects what it should (Figure 2). Against TEM-1 β-lactamase, three below-wall candidates showed no enrichment (group Mann-Whitney p = 0.95; all with 24–33 of 37 decoys scoring stronger). Against *E. coli* DHFR, the candidate chuanxiongzine ranked *below* the decoy median (37 of 37 decoys stronger; z = +2.61, i.e., worse than the null). The instrument does not manufacture hits on the wrong target.

### 3.5 InhA convergence: an approved-drug set and a natural-product orphan meet at one target

The central result is that both tracks independently nominate *M. tuberculosis* InhA (enoyl-ACP reductase) and survive structural validation on a single decoy scale (Figure 3).

**Repurposing arm.** Six below-wall drugs dock InhA at −9.1 to −11.2 kcal/mol. Four clear the decoy 5th percentile: TCS-2314 (−11.19 kcal/mol, 0/37 decoys stronger), radiprodil (−10.55, 0/37), PK-THPP (−10.15, 1/37), and AK-7 (−10.09, 1/37). The group is enriched over decoys (Mann-Whitney p = 0.0004). Five of the six engage the catalytic Tyr158 (all except PF-750), and three of the four top candidates (TCS-2314, PK-THPP, AK-7) also contact Lys165, while radiprodil engages Tyr158 only. Two compounds illustrate the filter's discrimination rather than passing it: PF-750 is a geometric outlier — its pose centroid sits 8.13 Å off the box center, it misses Tyr158, and it makes a single pocket contact — while OAC2 is the weakest by score (−9.10 kcal/mol, 10/37 decoys stronger) yet still engages the Tyr158–Lys165 catalytic dyad. We explicitly do **not** describe five of six as top-tail; only four clear the 5th percentile.

**Natural-product arm.** The structural orphan CNP0015547.1 (`nn_analog` = 0.41, a genuine below-wall compound) docks InhA at −10.92 kcal/mol — stronger than the positive-control binder (−10.66) — clears all 37 decoys (0/37 stronger; 100th percentile), and contacts both Tyr158 and Lys165. On the identical decoy scale, then, one natural-product orphan and four repurposed drugs are validated against the same catalytic machinery.

**Weight of evidence.** The two arms do not carry equal weight, and the paper states this plainly: the repurposing arm rests on the full 8,780-drug screen and a six-compound enrichment series, whereas the natural-product→InhA nomination is supported by only 7 InhA binders in the knowledge base. The NP-orphan arm is therefore an illustrative demonstration of the convergence, not a high-confidence lead in its own right.

### 3.6 The target–phenotype gap: nomination score does not predict whole-cell activity

Using a pure experimental whole-cell slice from CO-ADD (n = 156; 42 active, 114 inactive), the nomination score does not separate phenotypic actives from inactives (median score 0.339 vs. 0.341; Mann-Whitney p = 0.75), and the null holds within both wall strata and on a merged ChEMBL+CO-ADD set. This is the expected and honest result: a target-level nomination is not a phenotype prediction — whole-cell activity depends on permeability, efflux, and metabolism that our pipeline does not model. Far from undermining the approach, this null is the justification for the instrument's design: because score does not forecast phenotype, the correct downstream test is target-level structural validation (docking, enrichment, catalytic geometry, then free-energy simulation and biochemical assay), not a claim of antimicrobial efficacy.

## 4. Discussion

The framework integrates prior-art components (similarity transfer, reverse docking, cold-start DTI, decoy enrichment) into a pipeline whose novelty is organizational and epistemic rather than algorithmic: it refuses to report a target without also reporting how far the query sits from anything it was trained on, and it will not promote a nomination to a lead candidate until that candidate survives a decoy-referenced structural filter. Against MolTarPred-style similarity prediction, TarFisDock-style reverse docking, and cold-start DTI models, our results do not claim higher accuracy; they claim honest coverage of the region those methods handle worst.

The InhA convergence is the conceptual payoff. Discovery (an orphan natural product → an unexpected target) and repurposing (an approved drug → a new pathogen target) are usually treated as separate problems with separate tooling. Here they are two views of one boundary — the analog wall — and the same two-stage nominate→structurally-validate stack handles both, meeting at one catalytically credible target. InhA is a validated tuberculosis drug target (the activated form of isoniazid inhibits it), which makes it a reasonable, mechanistically interpretable place for such a convergence to land.

## 5. Limitations

1. **These are nominations, not binders.** Docking plus decoy enrichment plus catalytic-contact geometry constitute a computational hypothesis. Confirmation requires free-energy simulation (e.g., FEP/absolute binding free energy) and then biochemical/wet-lab assay. No compound here is a claimed InhA inhibitor.
2. **The natural-product→InhA arm is thin.** Only 7 InhA binders exist in the knowledge base, so the CNP0015547.1 nomination is illustrative of the convergence concept, not a robust lead. It should not be over-read.
3. **The method gain is small.** The fusion improves orphan recall@10 by ~0.02–0.03; it is reproducible and significant but not transformative. The instrument's value is applicability-domain honesty, not a large accuracy jump.
4. **Target-level only.** As the CO-ADD null shows directly, the pipeline does not and cannot predict whole-cell antimicrobial phenotype. Permeability, efflux, toxicity, and pharmacokinetics are out of scope.
5. **Docking-model dependence.** Vina scores, box definitions, and property-matched decoy construction all carry known systematic error; enrichment statistics mitigate but do not eliminate this.
6. **Benchmark coverage.** 319 learnable targets and a bioactivity-curated benchmark bound what can be nominated; targets and chemotypes outside this set are unreachable by construction.

## 6. Conclusion

We quantified a systematic blind spot in similarity-based drug repurposing — 1,218 credible pathogen nominations, mostly antibacterial, that live below the 2D-similarity wall — and built an openly released, reliability-aware pipeline that flags when it is extrapolating and validates candidates against property-matched decoys before reporting them. Its headline demonstration is a convergence in which a below-wall approved-drug series and a structurally orphan natural product independently nominate, and structurally survive validation against, *M. tuberculosis* InhA. The contribution is integration, rigor, openness, and a quantified limitation, delivered as ranked, structurally prioritized hypotheses for free-energy and wet-lab follow-up — not as a new algorithm and not as a discovered drug.

## 7. Data and Code Availability

All intermediate result tables underlying the figures and statistics — the orphan-stratum method comparison, the full repurposing map, combined repurposing docking, three-target decoy enrichment, InhA pose contacts, the natural-product InhA validation, and the CO-ADD experimental null — are released with the accompanying open repository, together with the analysis code and figure-generation scripts, to permit end-to-end reproduction. Source chemical libraries (COCONUT 2.0, NP Atlas, CMAUP), the bioactivity benchmark, CO-ADD, and PDB/SIFTS receptor definitions are publicly available from their respective providers.

## Figures

**Figure 1. The repurposing blind spot.** Inventory of 3,373 credible pathogen nominations partitioned by the analog wall: 2,155 above vs. 1,218 below (282 deep orphans), with the below-wall set dominated by antibacterial nominations.
![Figure 1](figures/repurposing_map.png)

**Figure 2. Three-target decoy enrichment.** Candidate Vina affinities against property-matched decoy null distributions for InhA (enriched, p = 0.0004) and the negative controls TEM-1 β-lactamase (p = 0.95) and *E. coli* DHFR (candidate below decoy median).
![Figure 2](figures/decoy_enrichment.png)

**Figure 3. The InhA convergence.** A below-wall approved-drug series and the natural-product orphan CNP0015547.1 validated on one shared decoy scale against *M. tuberculosis* InhA.
![Figure 3](figures/unified_inha_convergence.png)

**Figure 4. The analog wall and the 2D system.** Nearest-neighbor Tanimoto distribution across COCONUT 2.0 showing the ≈0.5 wall and the ~63% structural-orphan majority.
![Figure 4](figures/analog_wall_system.png)

**Figure 5. Method capstone.** Fusion vs. similarity vs. multi-task MLP on the true-orphan stratum (recall@10 = 0.571 / 0.547 / 0.459; McNemar p = 0.006; reproducible across three scaffold-disjoint splits).
![Figure 5](figures/method_capstone.png)

**Figure 6. Framework overview.** Pipeline schematic: library → analog-wall annotation → reliability-aware nomination → decoy-referenced structural validation.
![Figure 6]({{artifact:art_3d557362-ecc4-49c8-a58a-f3ef5323d156}})

**Figure 7. Below-wall drugs docked.** Reverse-docking results for the below-wall repurposing candidates advanced to structural validation.
![Figure 7](figures/repurposing_docking.png)
