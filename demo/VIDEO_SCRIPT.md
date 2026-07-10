# Video script, read-aloud version

This is written to be spoken, not read. Short sentences. One breath each.
Slow down on the bold lines. Every slide has a time budget; if you are running
long, cut the sentence marked (optional).

Total target: about 2 minutes 50 seconds. Speak calmly, do not rush.

---

## SLIDE 1 | Title  (0:00 to 0:15)

Hi. I'm James Martin.

Most drug discovery finds new targets by chemical similarity.

You take a molecule, find something like it, and borrow its target.

But that approach fails, quietly, exactly where new medicines have to come from.

---

## SLIDE 2 | The analog wall  (0:15 to 0:45)

We measured where it fails. We call it the analog wall.

We looked at six hundred and ninety-five thousand natural products.

**Sixty-three percent have no close match among known drugs.**

For those, similarity prediction simply cannot work. There is nothing to compare to.

Nobody usually measures this line. We did.

---

## SLIDE 3 | The pipeline and Claude  (0:45 to 1:20)

So we built an open pipeline that respects that wall.

It nominates a target, and it tells you how much to trust the nomination.

For the risky predictions, it checks them with real docking and real controls.

Claude ran the whole thing. And here is the part I like most.

Claude audited its own work, and caught real mistakes as they happened.

**One receptor was labeled human but was actually a rabbit protein. Claude caught it.**

One docking test let caffeine beat the real drug. Claude flagged it and fixed it.

---

## SLIDE 4 | The convergence  (1:20 to 1:55)

Here is what that honesty buys you.

A natural product with no known target. And an approved drug. Two unrelated molecules.

**They land on the same tuberculosis target, through one shared test.**

Both beat a decoy control. An independent structure tool confirms how they bind.

That is a real, checkable result. Not a lucky guess.

---

## SLIDE 5 | Resistance  (1:55 to 2:25)

Then we asked the question that matters for antibiotics.

Will the target still work after the bacteria mutate?

Normal docking is blind to that. So we built a resistance layer.

It passes the mutation test that ordinary docking fails.

**It finds natural products that hold up better against resistance than the current drug.**

Seven of them, on one enzyme family alone.

---

## SLIDE 6 | It generalizes  (2:25 to 2:45)

This is not a one-target trick.

We pointed the same pipeline at Alzheimer's targets, across one point seven million compounds.

And at gut microbe molecules, where a whole family of pigments converges on one human receptor.

Same tool. Three different problems. (optional)

---

## SLIDE 7 | Close  (2:45 to 3:00)

One last thing, and it matters.

Every molecule here is a lead to test in a lab. Never a finished drug.

When the pipeline cannot support a hit, it says so. We report those too.

It is fully open. Everything you just saw reproduces from the repository.

Built with Claude. Thank you.
