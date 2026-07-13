# Orphan Finder | video script (read-aloud)

Team: Past the Wall  ·  Presenter: James K. Martin II
Comprehensive version, no fixed cap. One continuous story. Land the bold lines.

===================================================================

## SLIDE 1 | Title

I'm James Martin, and this is Orphan Finder.

About half of all approved drugs come from natural products, from plants, microbes,
and fungi. But most of these molecules are stuck: we can see they do something, yet we
don't know which protein they act on, and without the target you can't build a drug.

**We call those orphan molecules. Orphan Finder gives them a target.**

===================================================================

## SLIDE 2 | Why they stay orphans

Why don't we already know? It comes down to how prediction normally works: guilt by
association. If a new molecule looks like a drug we understand, we assume it hits the
same protein. Chemists score that resemblance from zero to one, one means near-identical
twins, zero means nothing in common.

But that shortcut fails for the molecules that matter most. Orphans, by definition, look
like nothing we've seen, so there's nothing to compare them to. We measured exactly where
it breaks: below a similarity of one half, prediction collapses.

**Across 695,000 natural products, sixty-three percent live past that cliff. We call it
the analog wall.**

So here's our idea: Orphan Finder always knows which side of the wall it's on. With a
close relative, it uses the shortcut. Past the wall, it switches to physics, folding the
molecule's 3D shape into the protein's pocket to see if it truly fits.

===================================================================

## SLIDE 3 | The engine

That whole system runs on Claude Science.

From one instruction, it pulls together the world's natural-product libraries, reads in
each protein's 3D structure, sends the hard past-the-wall cases to physics-based docking
on GPUs, and ranks everything by how strong and how trustworthy the prediction is.

**You point it at a molecule, and it returns the proteins it most likely targets, with
an honest confidence for each.** A months-long lab hunt becomes a single run.

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

For an antibiotic, naming the target isn't enough. Bacteria fight back, mutating the
target until the drug no longer sticks. That's antibiotic resistance, and it's retiring
our medicines faster than we invent new ones.

So we taught Orphan Finder a harder question: not just does it bind, but does it still
bind after the bacteria mutate? We checked it against resistance we already understand,
and it correctly reproduced how today's drugs get defeated. Then we turned it on the
orphans.

**Against an enzyme called DHFR, seven orphan natural products are predicted to keep
their grip exactly where the frontline drug loses it, holding on through the resistance
mutations.**

Resistance-proof leads, from molecules that had no known target at all.

===================================================================

## SLIDE 6 | A second application: the microbiome

And this isn't a one-trick tool for tuberculosis. To show it generalizes, we pointed the
same engine at the microbiome, the molecules our gut bacteria release into us, most of
which have no known human target.

First a sanity check: the engine re-discovered signaling we already know is real, niacin
and bile acids hitting their known human receptors. Then the new finding. It flagged a
family of microbial carotenoids, the pigments these bacteria make, all converging on one
human protein: PXR, the master switch that controls how our bodies clear drugs. All nine
dock as tightly as rifampicin, the known drug that activates it.

**A concrete, testable hypothesis: our gut microbes' pigments may be tuning how we
metabolize medicine. Same engine, a different frontier.**

===================================================================

## SLIDE 7 | Close

About ninety percent of nature's molecules have no known target. That's not a dead end.
It's an enormous, unopened library of possible medicines.

Orphan Finder opens it, taking molecules nobody had a target for and handing back ranked,
structure-backed, resistance-aware leads a lab can test. It's fully open source, and
every result reproduces from the repository.

Built on Claude Science. Thank you.
