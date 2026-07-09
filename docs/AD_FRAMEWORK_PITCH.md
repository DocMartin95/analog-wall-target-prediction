# Adapting the HitScreen Target-Prediction Pipeline to Neurodegenerative Disease

**A design-doc pitch — grounded, and brutally honest about crowdedness.**

*Scope: can the existing two-stage (similarity-nomination + docking-validation, analog-wall-aware) pipeline be repointed from antimicrobial/human targets to Alzheimer's and related neurodegeneration, screening the COCONUT natural-product orphans and the ~8,780 approved/clinical repurposing pool?*

---

## TL;DR verdict

**The generic play — "dock a natural-product / repurposing library against BACE1 / AChE / GSK-3β and report top hits" — is one of the most saturated niches in all of computational drug discovery. Do not do that.** A single 2025 review screened the entire approved-drug pharmacopeia against multiple AD targets; dozens of NP-vs-AD virtual screens are published *per year* against exactly the targets our assets would point at.

**What is genuinely open, and what our pipeline is uniquely built to deliver, is the *honesty layer*:** (1) a calibrated **analog-wall / distance-to-nearest-known-binder** score that tells you *which* NP nominations are extrapolations vs interpolations — almost no AD-VS paper reports this; (2) a **blood-brain-barrier gate applied before docking, not after** — most AD-VS ignores BBB entirely or bolts on an ADMET table at the end; and (3) **decoy-enrichment + SIFTS receptor-identity controls** that most single-paper screens skip. The scientific contribution is not "we found new hits for AD" (nobody will believe another docking list). It is **"we built a reliability-aware CNS screening protocol that reports its own confidence and filters for brain exposure up front, and we demonstrate it recovers known chemistry before nominating anything novel."**

**Recommended first targets: acetylcholinesterase (AChE) + monoamine oxidase B (MAO-B)** — both have crystal structures with *approved-drug* co-crystals (donepezil, safinamide) that are ready-made positive controls, both have real natural-product chemical matter, and both are enzymes with well-defined orthosteric pockets where docking is scientifically defensible. **Explicitly avoid docking the disordered targets (tau monomer, Aβ monomer)** — this is where docking is *scientifically inappropriate* and where a naïve pipeline would embarrass itself.

---

## Q1 — Structural targets: what actually has a usable pocket?

I verified structure availability directly against the RCSB PDB search API (experimental structures only; counts as of this session). The critical split is **well-folded enzyme/receptor with a defined pocket** (docking-appropriate) vs **intrinsically disordered monomer** (docking-inappropriate).

### Docking-appropriate (well-defined pocket + inhibitor co-crystals)

| Target | Role in AD | Example verified PDB IDs | # exp. structures | Control ligand in PDB |
|---|---|---|---|---|
| **Acetylcholinesterase (AChE)**, human | Cholinergic; symptomatic Rx | **4EY7** (donepezil), 6O4W, 7E3H | small but definitive | donepezil (approved) |
| **Butyrylcholinesterase (BChE)**, human | Rises as AD progresses; dual-target angle | **7AIT**, 7AIV, 7BO4, 5K5E | 74 | multiple inhibitors |
| **Monoamine oxidase B (MAO-B)**, human | Oxidative stress, astrocytic | **2V5Z**, 2V60, 2V61, 1OJD | 82 | safinamide / rasagiline (approved) |
| **GSK-3β** | Tau hyperphosphorylation | **8DJD**, 6Y9S, 6Y9R, 4PTC | 83 | ATP-competitive inhibitors |
| **BACE1 (β-secretase)** | APP processing → Aβ | **2ZHR**, 4TRZ, 4DJU, 7B1E | 267 | verubecestat-class (see caveat, Q3) |
| **CDK5 (with p25)** | Tau kinase | **1H4L**, 1UNL, 1UNH, 7VDP | ~10 | roscovitine-class |
| **DYRK1A** | Tau/APP kinase | **3PWD**, 6QU2, 6LN1 | 74 | harmine-class (NP!) |
| **MAO / soluble epoxide hydrolase (sEH)** | Neuroinflammation | **4O08**, 3ANS, 3WK4 | 81 | urea-class inhibitors |
| **Sigma-2 / TMEM97** | Aβ-oligomer toxicity modulator (newer) | **7M93**, 7M94, 7M95 | 5 | — |
| **NMDA receptor GluN1/GluN2B** | Excitotoxicity; memantine target | **5UOW**, 5UP2, 3QEK | 116 | ifenprodil-site ligands |
| **NLRP3** | Neuroinflammation (newer) | **6S73**, 6S75, 8IQJ | 375 | MCC950-class (allosteric — harder) |
| **LRRK2** | PD/neurodegeneration kinase | **4YZM**, 8R4C, 9C61 | 126 | kinase inhibitors |
| **γ-secretase / presenilin** | APP processing | **5A63** (cryo-EM), 5FN2, 8KCT | 17 | membrane complex — allosteric only |

### Docking-INAPPROPRIATE (intrinsically disordered — do not treat as a rigid receptor)

- **Tau monomer** and **Aβ42 monomer** are intrinsically disordered proteins (IDPs). <cite index="16-1,16-2">Intrinsically disordered proteins play vital roles in biology, and their dysfunction contributes to many major disease states; they remain challenging targets for rational ligand discovery because they are highly dynamic and fluctuate through a diverse set of conformations, frustrating structure-based approaches.</cite> <cite index="17-1">A major hurdle is the lack of distinguishable binding pockets on the tau monomer according to the lock-and-key mechanism used for well-folded proteins.</cite>
- Cryo-EM **fibril** structures *do* exist (tau paired-helical-filament **9B4I, 7NRQ**; Aβ fibrils) — but these are the aggregated end-state, not a monomer druggable pocket, and docking small molecules into a fibril groove is a different (and much shakier) proposition than docking into an enzyme active site.
- The field's own answer for IDPs is **ensemble docking / enhanced-sampling MD**, not single-structure Vina. <cite index="16-3,16-4,16-5">Protocols to identify ligands of disordered proteins use enhanced-sampling molecular dynamics and conformational analysis optimized for disordered targets, coupled with computational docking and ML-based screens.</cite> That is a *different pipeline* than ours; standard AutoDock Vina against a single rigid structure is the wrong tool here. **Recommendation: exclude tau/Aβ monomers from the docking stage entirely, and say so loudly — it is a credibility signal.**

---

## Q2 — Existing efforts: how crowded is this? (Honest answer: extremely.)

**AD virtual screening — including specifically NP-vs-AD and repurposing-vs-AD — is a mature, saturated field.** Evidence from real, recent publications:

- A 2025 paper explicitly screened **drugs against *multiple* targets of AD** (Ben Zaken et al., *J Alzheimers Dis*, DOI 10.1177/13872877251387167) — the exact multi-target repurposing framing our pool would use.
- A 2026 *Scientific Reports* paper: **"Repurposing FDA-approved drugs as multi-target neuroprotective agents for Alzheimer's disease via computational screening and experimental validation"** — again the exact concept.
- Multi-target NP-like screens against **GSK-3β / NMDA / BACE-1 simultaneously** (PMC10143309); NP-as-BACE1 VS with ADMET+MD (*Sci Rep* 2024, s41598-024-75292-6); an **ML+DL framework identifying novel natural GSK-3β inhibitors** (*J Comput Aided Mol Des* 2025, s10822-025-00637-w); marine-sponge NP anti-AD pharmacophore screens; NP-as-AChE-inhibitor screens (PMC9145144). There is even a **2025 systematic review** of computational + experimental early-stage AD drug discovery (s10822-025-00610-7).
- **The incumbent to beat on repurposing is the Feixiong Cheng lab (Cleveland Clinic).** Their network-medicine / endophenotype approach nominated **sildenafil** and validated it with real-world insurance data. <cite index="33-3,33-4">They developed an endophenotype disease-module methodology for AD drug repurposing and identified sildenafil as a potential risk modifier; in retrospective analyses of insurance claims for 7.23 million individuals, sildenafil use was associated with a 69% reduced risk of AD (HR 0.31, 95% CI 0.25–0.39).</cite> They have since extended it (cross-species protein-interactome / x-QTL target discovery for AD, PMC11710113). **This is a well-funded, publishing-heavily group — we will not out-repurpose them on network topology or real-world data.**

**What is NOT crowded (the openings):**
1. **Reliability-calibrated nomination.** The published screens report docking scores and rank-order; they essentially never report *distance to the nearest known binder* / analog-wall status. "This hit is 0.3 Tanimoto from anything with known activity, so treat it as a genuine extrapolation" is information the field routinely omits.
2. **BBB-gated screening as a first-class filter.** See Q3 — this is the single clearest differentiator.
3. **Honest controls at scale.** Decoy enrichment + receptor-identity (SIFTS) checks + explicit positive/negative controls, reported per target, is rare in one-off screens.

**Conclusion for the user (who hates me-too work):** a "new NP hits for BACE1" paper is dead on arrival. A **methods contribution — a reliability-aware, BBB-first CNS screening protocol that recovers known chemistry before extrapolating, benchmarked with controls and analog-wall calibration** — is defensible and genuinely underserved.

---

## Q3 — What the pipeline must CHANGE

### 3.1 Target-panel assembly (moderate work)
Swap the antimicrobial/bacterial target list for a **curated human CNS panel** (the Q1 docking-appropriate table). This is straightforward — the ESM-2 embedding + benchmark machinery is target-agnostic. Key change: targets are **human, membrane-proximal, and often have paralogs** (BACE1/BACE2, AChE/BChE, MAO-A/MAO-B) — so **selectivity controls matter more than in the antimicrobial setting**, and the SIFTS receptor-identity check earns its keep.

### 3.2 Blood-brain-barrier permeability gate (the differentiator — build this)
CNS drugs must cross the BBB; **most target-prediction VS ignores this entirely.** Applying a BBB filter *before* docking (not as an afterthought ADMET table) is a real, defensible novelty and it also **shrinks the compute bill** (fewer molecules to dock on the limited Modal budget).

- Feasible with off-the-shelf, well-benchmarked ML: BBB classifiers on the MoleculeNet **BBBP** benchmark reach <cite index="7-1">AUC ≈ 0.93 and balanced accuracy ≈ 0.84 on held-out test sets</cite> using tree ensembles + fingerprints. Graph-NN approaches now <cite index="11-11,11-12">outperform the classic CNS-MPO rule, with an attentive GNN achieving 0.23–0.26 higher AUROC than MPO on full test sets.</cite>
- **Design choice:** run a BBB classifier (RDKit descriptors + tree ensemble, or a published GNN) on both COCONUT orphans and the repurposing pool; keep only predicted-permeant molecules for docking. Report the BBB probability alongside the analog-wall score. **Caveat to state honestly:** BBBP labels are noisy and passive-permeability-biased; active efflux (P-gp) is under-modeled. Treat the filter as an enrichment prior, not a hard truth.

### 3.3 Disordered-target handling (the credibility move)
Do **not** dock tau/Aβ monomers. Route them (if at all) to an ensemble-docking / MD side-branch, or drop them. Docking a rigid IDP structure and reporting a Vina score would be the single fastest way for a reviewer to dismiss the whole pipeline. State the exclusion as a deliberate scientific decision.

### 3.4 The target–phenotype gap for CNS (the honesty framing that transfers directly)
Our antimicrobial pipeline already carries a "target engagement ≠ phenotype" caveat. **In AD this is not a caveat — it is the defining lesson of the entire field, and it is our strongest honesty hook.** BACE1 is the poster child: <cite index="34-5,34-6">all five phase-III BACE1 inhibitors reduced CSF Aβ by ~50–85%, yet failed to provide cognitive or functional benefit, and in several cases worsened cognition.</cite> <cite index="35-1">Verubecestat at 12 mg and 40 mg was ineffective at slowing cognitive or functional decline over 78 weeks despite reducing CSF Aβ by 63–81%.</cite> A pipeline that nominates a strong BACE1 binder and stops there is nominating a target that *has been maximally engaged in humans and did not modify disease.* **Our reliability framing should extend from "distance to known binder" to "distance to clinical validation of the target itself"** — i.e., flag targets where potent engagement has already failed the clinic. No AD-VS paper I found does this; it is a natural extension of the analog-wall philosophy from chemistry to biology.

### 3.5 Does the analog-wall honesty add anything the AD-VS field lacks?
**Yes — this is the crux.** The AD-VS literature is a pile of ranked docking lists with little calibration of *how much of a leap* each hit is. Our pipeline's native output — "this nomination is X below the nearest known binder; here is the decoy-enrichment AUC for this receptor; here is the BBB probability; and note this target itself has/has-not survived clinical target-engagement" — is a **reliability report, not a hit list.** That reframing is the paper.

---

## Q4 — Feasibility ranking + first experiment

**Fit criteria: (a) well-defined orthosteric pocket, (b) approved/known inhibitor available as positive control, (c) real disease relevance, (d) existing NP chemical matter to make below-wall nominations meaningful.**

### Tier 1 — dock these (excellent fit)
1. **AChE (4EY7)** — donepezil co-crystal = built-in positive control; huge NP space (galantamine, huperzine A are NPs); symptomatic but clinically real.
2. **MAO-B (2V5Z)** — safinamide/rasagiline co-crystals as controls; many NPs are MAO inhibitors; clean pocket.
3. **BChE (7AIT)** — pairs with AChE for a dual-cholinesterase selectivity story; rises in late AD.
4. **GSK-3β (8DJD)** — bridges to tau biology without docking tau itself; tideglusib (an NP-derived thiadiazolidinone) in trials as control.

### Tier 2 — dock with caveats
5. **DYRK1A (6QU2)** — harmine (an NP) is a known inhibitor; nice NP-relevance, smaller literature.
6. **CDK5 (1UNL)**, **sEH (4O08)**, **sigma-2/TMEM97 (7M93)** — defensible pockets, thinner control sets.
7. **BACE1 (2ZHR)** — *great* pocket and abundant controls, **but include the clinical-failure flag** (§3.4). Useful as a *methods demonstrator* (controls are excellent), risky as a *therapeutic claim*.
8. **NMDA-GluN2B (5UOW)** — memantine is approved but binds the channel pore, not a classic pocket; ifenprodil allosteric site is dockable.

### Tier 3 — do NOT dock
- **Tau monomer, Aβ42 monomer** — disordered, no pocket (§Q1). **γ-secretase, NLRP3** — allosteric/large-complex, wrong for rigid single-structure Vina as a first pass.

### First experiment (concrete, cheap, controls-first)
**Targets:** AChE (**4EY7**) and MAO-B (**2V5Z**) — two Tier-1 enzymes, each with an *approved-drug co-crystal*.

**Step 0 — receptor prep + control validation.** Redock the co-crystallized ligand (donepezil into 4EY7; safinamide into 2V5Z); confirm pose RMSD < 2 Å and that Vina ranks the true ligand above a DUD-E/DEKOIS decoy set (report enrichment AUC). *If controls fail, stop — the target isn't ready.*

**Step 1 — BBB gate.** Run the BBB classifier on (a) COCONUT below-wall orphans and (b) the ~8,780 repurposing pool. Keep predicted-permeant only. Report the pass rate — this number itself is interesting (how much of NP space is even brain-relevant?).

**Step 2 — nominate + score.** For BBB-passing molecules, run the similarity-nomination stage against AChE/MAO-B known binders and record the **analog-wall distance** for each nomination.

**Step 3 — dock the below-wall nominations** into 4EY7 / 2V5Z with the validated Step-0 setup.

**What we'd expect (and how we'd know it worked):** the pipeline should **re-discover known cholinesterase/MAO NP scaffolds** (galantamine/huperzine-like for AChE; harmine/β-carboline-like for MAO) *near* the wall — that is the recovery check. Genuinely novel candidates appear *below* the wall, explicitly flagged as extrapolations, BBB-permeant, and docked with positive/negative controls passing. **Deliverable is a reliability report per molecule — not a ranked hit list — which is exactly what the crowded field does not produce.**

**Compute budget note:** BBB-gating first + restricting docking to below-wall nominations keeps the Modal GPU spend bounded; two targets × validated setup is a contained pilot.

---

## Honest bottom line
- **Crowdedness:** AD/NP/repurposing virtual screening is **saturated**; a hit-list paper is me-too and will be dismissed.
- **The open lane** is a **reliability-aware, BBB-first, controls-validated CNS protocol** that reports confidence (chemical analog wall *and* clinical target-validation status) and recovers known chemistry before extrapolating.
- **Docking is scientifically inappropriate for tau/Aβ monomers** — excluding them is a feature, not a limitation.
- **Start with AChE + MAO-B** because their approved-drug co-crystals make the controls airtight.

*All PDB IDs verified against the RCSB search API this session. Papers cited with DOI/PMID where available in the References inline above.*
