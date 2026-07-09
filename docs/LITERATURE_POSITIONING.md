# Where this pipeline sits in the literature

*An honest positioning of the orphan-NP → target pipeline against current
published work, so the write-up builds on the field rather than re-claiming it.*

## Summary verdict

No individual component of this pipeline is methodologically novel. The
transfer-learning NP target model, the cold-start / orphan degradation, and
reverse-docking target fishing with controls each have direct, sometimes
decades-old prior art. The defensible contribution is **integration + honest
self-checking QC + open tooling at library scale** — an engineering and impact
contribution, not a new algorithm.

## The three subfields we touch

### 1. NP target prediction by transfer-learning MLP — established
"Target Prediction Model for Natural Products Using Transfer Learning"
(*Int. J. Mol. Sci.* 2021, MDPI 22/9/4632, PMC8124298) builds exactly this: a
model pre-trained on a large bioactivity set and transferred to natural
products. Our multi-task MLP is in the same family.

*(Retrieved via web search; identifier from the persisted search result. The
specific reported performance figures are from the paper body — confirm against
the source before quoting any number in the write-up.)*

### 2. The "analog wall" is the cold-start problem — a large active subfield
What we call the analog wall the DTI field calls **cold-start / scaffold
generalization**, and it is one of the most heavily worked problems in the area.
2024–2026 methods that explicitly benchmark unseen-drug / unseen-target /
unseen-pair splits include:
- **CDI-DTI** (arXiv 2510.19520) — defines unDrug / unTarget / unPair tasks.
- **CS-DTA** (*Front. Chem.* 2026) — strict drug/target cold-start, LLM encoders.
- **Co-Diffusion** (arXiv 2603.11125) — a *two-stage* framework for zero-shot
  generalization to unseen scaffolds and protein families (conceptually close to
  our two-stage design).
- **DrugLAMP** (*Bioinformatics* 2024, btae693), **ColdstartMHDTI** (*Front.
  Chem.* 2026), **ColdDTA** (2023).

There is even a dissertation titled *Cold-Start Vulnerability in Drug–Target
Affinity Prediction* (OhioLINK ETD). The phenomenon we quantified is established;
our addition is a concrete boundary (Tanimoto ≈ 0.5) and the ~63%-of-NPs-below-it
statistic at 695k-library scale.

*(arXiv/journal identifiers in this section are from persisted web-search result
titles this session; treat titles/IDs as retrieved and confirm details at the
source before formal citation.)*

### 3. Reverse-docking target fishing — ~20 years old, and so is our caveat
- Reverse-docking target fishing is a mature area (TarFisDock, ChemMapper,
  PharmMapper, idTarget, INVDOCK, CRDS); review of reverse screening methods:
  PMC5954125.
- Our SCH-79797 docking-promiscuity caveat is a *named, published* problem:
  "The scoring bias in reverse docking and the score normalization strategy to
  improve success rate of target fishing" (PMC5308821) — reverse docking scores
  struggle to separate true from non-target proteins.
- "Molecular Docking Suggests the Targets of Anti-Mycobacterial Natural Products"
  (PMC7831053) is close in spirit to our bacterial demo: reverse docking of
  anti-mycobacterial NPs against druggable *M. tuberculosis* targets (including
  InhA) with controls.
- Review of the NP-target-discovery space: "Progress in Natural Products Target
  Discovery Technology" (*MedComm* 2026, doi 10.1002/mco2.70777).

*(All identifiers above are from persisted web-search results — titles + URLs
retrieved this session. Author/year attributions are omitted where they could
not be confirmed from the search metadata; verify against each source before
citing formally in the write-up.)*

## What is defensibly ours

1. **Integration + honesty as a standing system.** Most cited work is
   single-method-beats-baseline on a benchmark. We built an end-to-end, open,
   orphan-first pipeline with two independent guardrails that *caught real errors
   live* — calibration that refuses confidence below the analog wall, and
   controls + SIFTS identity that flagged 2 wrong receptors and 2 bad boxes in the
   demo. The field names the reverse-docking scoring-bias problem; wiring an
   automatic identity + control layer into the pipeline is a practical answer.
2. **Quantified framing at scale** — the Tanimoto-0.5 boundary and the fraction
   of NP space below it, as a motivating number for the NP community.
3. **Open tooling** on a 695k-compound library, where much comparable work is
   closed (Schrödinger, Discovery Studio) or web-server-gated.

## Consequence for the write-up
- Lead with the integration and the two-failure-mode QC (SCH-79797 + bacterial
  docking as evidence).
- Cite the cold-start and reverse-docking literature openly as the foundation.
- Present the fusion result (+0.024, p=0.006) as a small confirmatory footnote,
  not a headline.
- Frame the analog wall as *quantifying a known limitation for the NP field*, not
  discovering it.
