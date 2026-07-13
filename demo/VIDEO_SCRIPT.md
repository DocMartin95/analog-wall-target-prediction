# Orphan Finder | video script (read-aloud)

Team: Past the Wall  ·  Presenter: James K. Martin II
One continuous story, natural pace. Land the bold lines. About 3 minutes at ~150 wpm.

===================================================================

## SLIDE 1 | Title  (0:00-0:18)

I'm James Martin, and this is Orphan Finder.

Nature is still our best source of medicine. About half of all approved drugs come
from natural products, from plants, microbes, and fungi.

But for most of these molecules, we don't know what they hit in the body. We can see
they're active. We just don't know where they act.

**We call those orphan molecules. Orphan Finder gives them a target.**

===================================================================

## SLIDE 2 | Why they stay orphans  (0:18-1:00)

Why don't we already know? Because of how target prediction normally works.

The usual approach is guilt by association. If a new molecule looks like a known drug,
we assume it hits the same protein. Chemists score that resemblance from zero to one:
one means chemical twins, zero means nothing in common.

That works right up until a molecule looks like nothing we've seen. And orphans, by
definition, look like nothing we've seen.

We measured where it breaks. Below one half, prediction falls apart.

**Across 695,000 natural products, sixty-three percent live past that line. We call it
the analog wall.**

Orphan Finder always knows which side of the wall it's on. With a look-alike, it uses
it. Without one, it stops guessing and turns to physics, folding the molecule into the
protein to see if it truly fits.

===================================================================

## SLIDE 3 | The engine  (1:00-1:22)

That system runs on Claude Science.

From one instruction, it gathers the world's natural-product libraries, reads each
protein's structure, sends the hard cases to physics-based docking on GPUs, and ranks
what it finds.

**You point it at a molecule. It hands back the likely targets, and how much to trust
each one.**

===================================================================

## SLIDE 4 | Proof it works  (1:22-1:58)

So does the physics route really find targets past the wall? Here's the test that
convinced me.

Two molecules: one orphan natural product, one approved drug. On that similarity score
they sit at zero point two eight. They're chemical strangers, no shared skeleton.

Yet both fold into the same pocket of the same enzyme, InhA, the protein the
tuberculosis drug isoniazid attacks. Both grip its catalytic residue. Both beat a field
of decoys, and a separate structure method agrees.

**Two strangers, the same target, found by one rule. That's the tool working.**

===================================================================

## SLIDE 5 | The part that matters for antibiotics  (1:58-2:35)

For an antibiotic, naming the target isn't enough. Bacteria fight back. They mutate the
target until the drug stops sticking. That's resistance, and it's killing our medicines
faster than we replace them.

So we taught Orphan Finder a harder question: not just does it bind, but does it still
bind after the bacteria mutate? We checked it against resistance we already understand,
and it correctly predicted how today's drugs fail.

**Then we turned it on the orphans. Against the enzyme DHFR, seven natural products are
predicted to keep working right where the frontline drug is defeated.**

Resistance-proof leads, from molecules nobody had a target for.

===================================================================

## SLIDE 6 | Not a one-target trick  (2:35-2:52)

And this isn't just tuberculosis. We pointed the same engine at gut-microbe molecules
and asked what they do to us. It flagged a whole family of microbial pigments all
hitting one human receptor, PXR, the switch that controls how our bodies clear drugs.
Every one confirmed by docking.

**Antibiotics, the microbiome, the brain. One engine.**

===================================================================

## SLIDE 7 | Close  (2:52-3:05)

Ninety percent of nature's molecules have no known target. That's not a dead end, it's
an unopened library.

Orphan Finder opens it, and hands you ranked, structure-backed, resistance-aware leads
to test. Fully open, fully reproducible.

Built on Claude Science. Thank you.
