# Orphan Finder | video script (read-aloud)

Spoken, expert voice. Short sentences. Land the bold lines. ~2:45 at a calm pace.
Team: Past the Wall. Presenter: James K. Martin II.

===================================================================

## SLIDE 1 | Title  (0:00-0:15)

I'm James Martin. This is Orphan Finder.

Half of all approved drugs trace back to natural products. But most natural
products have no known target, so they sit on the shelf.

Orphan Finder predicts those targets, and tells you which predictions to believe.

===================================================================

## SLIDE 2 | The analog wall  (0:15-0:50)

Target prediction leans on chemical similarity. Find a molecule like a known drug,
inherit its target. That breaks the moment a molecule has no close relative.

We quantified exactly where it breaks. Across 695,000 natural products in COCONUT,
similarity prediction collapses at a Tanimoto of 0.5.

**Sixty-three percent of natural product space sits past that wall, unreachable.**

So every prediction carries a distance to that wall. Above it, trust the analogy.
Below it, we dock the pose against real controls.

===================================================================

## SLIDE 3 | Built on Claude Science  (0:50-1:20)

The whole engine runs on Claude Science. It pulled the data from COCONUT, ChEMBL, and
the Broad repurposing library, ran ESM-2 protein embeddings, and dispatched docking to
GPUs on Modal.

**One prompt drives a pipeline that fingerprints 695,000 compounds and docks the
survivors against verified receptor structures.**

A scientist points it at a molecule, and it returns ranked, structure-checked targets.

===================================================================

## SLIDE 4 | Convergence on InhA  (1:20-1:55)

Here is what it finds. A natural product with no annotated target, and an approved
drug, share no chemical scaffold. Both land on the same enzyme: InhA, the
tuberculosis target of isoniazid.

Both dock into the substrate channel. Both contact the catalytic tyrosine, Tyr158.
Both beat a matched decoy set. Docking alone can flatter a pose, so we confirmed it a
second way, with an independent structure predictor. It agrees.

**Two unrelated molecules, one validated target, reached by one standard.**

===================================================================

## SLIDE 5 | Resistance-robust hits  (1:55-2:25)

Then the question that decides an antibiotic's future: does the target survive
resistance mutations?

We built a mutant-aware scoring layer and validated it on known resistance: it
reproduces how trimethoprim loses DHFR, and pyrimethamine loses malarial DHFR.

Then we screened orphan natural products against those same mutated pockets.

**On DHFR, seven natural products are predicted to hold their binding where the
front-line drug fails.** Those are resistance-robust leads, ranked and ready to test.

===================================================================

## SLIDE 6 | One engine, three problems  (2:25-2:45)

The same engine generalizes. It screened 1.7 million compounds for Alzheimer's
targets, and flagged a family of microbial carotenoids all converging on the human
receptor PXR, every one confirmed by docking.

Antibiotics, neurodegeneration, the microbiome. One pipeline.

===================================================================

## SLIDE 7 | Close  (2:45-3:00)

Orphan Finder turns the ninety percent of natural products with no known target into
ranked, structure-backed, resistance-aware leads.

It is fully open. Every result reproduces from the repository. Built on Claude Science.

Thank you.
