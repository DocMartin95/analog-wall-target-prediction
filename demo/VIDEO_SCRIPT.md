# Orphan Finder | video script (read-aloud)

Team: Past the Wall  ·  Presenter: James K. Martin II
Comprehensive version, no fixed cap. One continuous story. Land the bold lines.

===================================================================

## SLIDE 1 | Title

I'm James Martin, and this is Orphan Finder.

Nature is still our best source of medicine. About half of all approved drugs trace
back to a natural product, to something first made by a plant, a microbe, or a fungus.

But most of these molecules are stuck. We can see they do something, but we don't know
which protein in the body they act on. And without the target, you can't develop the
drug.

**We call those orphan molecules. Orphan Finder gives them a target.**

===================================================================

## SLIDE 2 | Why they stay orphans

Why don't we already know their targets? It comes down to how prediction normally works.

The standard approach is guilt by association: if a new molecule looks like a drug we
understand, we assume it hits the same protein. Chemists score that resemblance from
zero to one. One means near-identical twins; zero means nothing in common.

That works, but only up to a point. The moment a molecule looks like nothing we've seen,
there's nothing to compare it to, and the method quietly falls apart. And orphans, by
definition, are the molecules that look like nothing we've seen.

So we measured where it breaks, and found a hard cliff: below a similarity of one half,
prediction stops working.

**Across 695,000 natural products, sixty-three percent live past that cliff. We call it
the analog wall.**

Our central idea: Orphan Finder always knows which side of that wall it's on. With a
close relative, it uses the shortcut. Past the wall, it stops guessing from resemblance
and switches to physics, folding the molecule's 3D shape into the protein's pocket to
see if it truly fits.

===================================================================

## SLIDE 3 | The engine

That whole system runs on Claude Science.

From one instruction, it pulls together the world's natural-product libraries, reads in
each protein's 3D structure, sends the hard past-the-wall cases to physics-based docking
on GPUs, and ranks everything by how strong and how trustworthy the prediction is.

**You point it at a molecule, and it hands back the proteins it most likely targets,
with an honest confidence for each.** A months-long lab hunt becomes a single run.

===================================================================

## SLIDE 4 | Proof it works

So when Orphan Finder goes past the wall, does it land on real targets? Here's the
result that convinced me.

Take two molecules. One orphan natural product, and one compound from a drug-repurposing
library, made for a different purpose. On that similarity score they sit at zero point
two eight, far below the wall. They are chemical strangers, sharing no common skeleton.

And yet both fold into the same pocket of the same bacterial enzyme, InhA, the enzyme
the frontline tuberculosis drug isoniazid was built to shut down. Both grip the same
catalytic residue. Both beat a whole field of decoy controls. And a separate
structure-prediction method puts them in the same place.

**Two chemical strangers, pointed at the same target, found by one rule. That's the tool
working.**

===================================================================

## SLIDE 5 | The part that matters for antibiotics

For an antibiotic, naming the target isn't enough. Bacteria fight back: they mutate the
target protein, reshaping the pocket just enough that the drug stops sticking. That's
antibiotic resistance, and it's retiring our medicines faster than we invent new ones.

So we taught Orphan Finder a harder question, not just does it bind, but does it still
bind after the bacteria mutate? We checked it against resistance we already understand,
and it correctly reproduced how today's drugs get defeated. Then we turned it on the
orphans.

**Against an enzyme called DHFR, seven orphan natural products are predicted to keep
their grip exactly where the frontline drug loses it, holding on through the resistance
mutations.**

Resistance-proof leads, from molecules that had no known target at all.

===================================================================

## SLIDE 6 | One engine, many diseases

And this isn't a one-trick tool for tuberculosis. It's a general instrument, and we
proved it on two more problems.

First, the microbiome. We aimed the same engine at molecules made by our gut bacteria
and asked what they do to us. It flagged a whole family of microbial pigments that all
converge on one human protein, PXR, the master switch controlling how our bodies clear
drugs. Every one confirmed by docking, a concrete, testable link between our gut microbes
and our own drug metabolism.

Second, the brain. We ran the same pipeline across 1.7 million brain-penetrant molecules
aimed at the two enzymes that Alzheimer's drugs target, AChE and MAO-B. The real
Alzheimer's drugs, our controls, scored right where they should, proving the engine
transfers cleanly to a whole new disease area at massive scale.

**Antibiotics, the microbiome, the brain. One engine, three frontiers of human health.**

===================================================================

## SLIDE 7 | Close

About ninety percent of nature's molecules have no known target. That's not a dead end.
It's an enormous, unopened library of possible medicines.

Orphan Finder opens it, taking molecules nobody had a target for and handing back ranked,
structure-backed, resistance-aware leads a lab can test. It's fully open source, and
every result reproduces from the repository.

Built on Claude Science. Thank you.
