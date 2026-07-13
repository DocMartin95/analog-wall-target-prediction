# Presentation kit: 3-minute video + short description

Everything you need to record the hackathon submission. The video and the written
blurb together carry the Demo (30%) and Claude Use (25%) buckets, which is over
half the score, so this is where to spend the effort.

---

## Short description (paste into the submission form)

> Chemical-similarity target prediction is everywhere in drug discovery, but it
> fails silently for compounds with no close analog, and it says nothing about
> whether a drug target survives resistance mutations. We built an open,
> reliability-aware pipeline that measures its own failure boundary, the "analog
> wall": across 695,133 natural products, ~63% have no usable analog and
> similarity methods provably cannot reach them. The pipeline nominates targets
> with a calibrated confidence distance, structurally validates below-wall
> predictions by docking with real controls, and extends to resistance with a
> curated mutant-aware target database and a control-validated ΔΔG layer. Headline:
> a natural-product orphan and a drug-repurposing-library compound, chemically unrelated,
> converge on the same tuberculosis target through one standard, and the resistance layer
> then nominates natural products predicted more resistance-robust than the
> front-line drug. Every stage was orchestrated with Claude, including a
> self-auditing loop that caught and corrected real errors live. Fully open; all
> results are reproducible hypotheses for wet-lab follow-up, never claimed drugs.

(~150 words. Trim the last two sentences if a hard 100-word limit applies.)

---

## 3-minute video script (~450 words spoken, timed)

Record as screen capture with voiceover (Loom or OBS). Do not film a talking head;
your figures, the live dashboard, and a real Claude session are far more persuasive.
Times are cumulative.

### 0:00 to 0:25 : The problem (Impact)
> "Most of drug discovery predicts targets by chemical similarity: find a molecule
> like known binders, borrow their target. But that has a failure boundary nobody
> measures. We measured it. Across 695,000 natural products, 63% have no close
> analog among known binders, so similarity prediction provably cannot reach them.
> We call it the analog wall."

*On screen:* `figures/analog_wall_system.png`, then the dashboard KPI row.

### 0:25 to 1:00 : What we built + Claude (Claude Use)
> "We built an open pipeline that respects the wall: it nominates targets with a
> confidence distance, then structurally validates the risky below-wall predictions
> by docking with real controls. The whole thing was orchestrated with Claude, and
> the part I want to show you is the self-audit. Claude checked its own work and
> caught real errors live: a receptor mislabeled as human that was actually a rabbit
> ortholog, and a docking box where caffeine out-scored the known drug. It fixed
> them before they reached a result."

*On screen:* scroll a Claude session showing a SIFTS identity catch or a control
failure being flagged. This is the single highest-value Claude-Use moment; make it
concrete.

### 1:00 to 1:55 : The result (Depth)
> "Here is what the honesty buys you. A natural-product orphan with no known target,
> and a compound from a drug-repurposing library, chemically unrelated, converge on the
> same tuberculosis target, InhA, through one identical validation standard. Both beat a decoy
> distribution; an independent structure predictor confirms the poses. Then we asked
> the question that matters for antibiotics: will the target still be druggable after
> resistance mutations? Rigid docking is blind to that, so we built a mutant-aware
> database and a ΔΔG layer that passes the resistance test docking fails. It nominates
> natural products predicted more resistance-robust than the front-line drug: seven
> of them on the DHFR family alone."

*On screen:* `figures/unified_inha_convergence.png`, then the dashboard Resistance
tab (live), highlighting the DHFR and InhA rows.

### 1:55 to 2:35 : It generalizes (Depth / Impact)
> "This is not a one-target trick. We ported the same pipeline, unchanged, to
> Alzheimer's targets across 1.7 million CNS compounds, and to microbiome
> metabolites, where a whole family of microbial carotenoids converges on the human
> receptor PXR and every one of them confirms by docking against the known agonist.
> Same instrument, three different problems."

*On screen:* dashboard Carotenoid-to-PXR tab (live bars), then `figures/microbiome_downstream.png`.

### 2:35 to 3:00 : The honest close
> "Every molecule here is a nomination for wet-lab follow-up, never a claimed drug.
> The pipeline refuses when it cannot support a lead, and we report those refusals.
> That is the whole point: a target-prediction tool that tells you where to trust it.
> It is fully open, and everything you just saw reproduces from the repository. Built
> with Claude."

*On screen:* the repo, the dashboard, the two papers.

---

## Recording checklist
- [ ] Open `demo/` dashboard (`index.html`) in a browser, full screen, dark room, high contrast.
- [ ] Have one Claude session scrolled to a visible self-audit / error-catch moment.
- [ ] Figures open in tabs in the order the script calls them.
- [ ] Record at 1080p; keep to 3:00 hard (judges may stop at the limit).
- [ ] End on the repo URL so judges can click through.

## Demo assets in this repo
- `demo/index.html` : interactive dashboard (nominate a molecule, resistance screen, PXR convergence). Opens offline, no server.
- `demo/analog_wall_demo.ipynb` : runnable notebook that reproduces the headline numbers from the open tables.
- `demo/data/` : the exact CSVs behind both.
