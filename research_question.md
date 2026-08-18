# Research question

## The question

> **How well do RSV-A F-protein EVEscape scores identify DMS-measured nirsevimab escape, among
> substitutions that retain F-mediated cell-entry function?**

Scope: RSV-A · nirsevimab · Fab escape (primary) and IgG escape (secondary) · conditioned on
F-mediated cell entry.

## Why it is answerable now, and was not before

Two independent results, neither of which cites the other:

**Simonich et al.** performed pseudovirus deep mutational scanning of the RSV-A Long F ectodomain,
measuring how nearly all amino-acid mutations affect F-mediated cell entry and neutralization by the
IgG and Fab forms of nirsevimab, clesrovimab, and several other antibodies. They integrate these
measurements into live Nextstrain builds for surveillance. **They never compare their data to any
evolutionary or computational escape model** — a full-text and repository-wide search for
"EVEscape" / "EVE" returns nothing.

**Zhou / Henschel et al.** apply subtype-specific EVEscape models to RSV-A and RSV-B F protein for
genomic surveillance in the UAE, converting single-substitution scores to within-subtype percentiles
over all 10,906 non-reference substitutions per subtype. They state the gap themselves:

> "the escape analyses were computational and not complemented here by functional neutralization or
> virological assays. As such, the prioritized variants identified in this study are best considered
> as candidates for further investigation rather than definitive escape or virulence markers."

The DMS supplies exactly the missing measurement layer, for the same protein, at the same
mutation-level resolution.

## What is and is not new

Stated precisely, because the boundaries matter:

| | |
|---|---|
| **Already done by Simonich et al.** | Comprehensive DMS; per-sequence summed and maximum escape scores; integration into Nextstrain; experimental neutralization validation of natural high-escape strains |
| **Already done by Zhou / Henschel et al.** | Subtype-specific EVE + EVEscape scoring of all single substitutions; within-subtype percentiles; variant-level escape burden vs matched public sequences |
| **Already done by the EVEscape authors** | Benchmarking EVEscape against DMS — for SARS-CoV-2, influenza, HIV, Lassa and Nipah. **RSV does not appear in that paper at all** |
| **What this project adds** | The direct mutation-level comparison of an RSV-A EVEscape ranking against experimentally measured nirsevimab escape — with reference compatibility between the two studies' backgrounds established rather than assumed |

Deliberately **not** claimed:

* not "nobody has compared computational RSV escape predictions with experiments" — the nirsevimab
  clinical-trial literature pairs observed resistance substitutions with in-silico structural
  modelling;
* not "using DMS for surveillance is novel" — Simonich et al. already ship it in Nextstrain;
* not "experimental validation of the UAE RSV findings" — see Scope below;
* not a new modelling method. This is a benchmark, not a model.

## Scope limitation: RSV-A, and why it is a proof of concept

The comprehensive DMS exists only for RSV-A Long, so the direct benchmark is RSV-A EVEscape vs
RSV-A Long DMS. This is a **proof-of-concept benchmark**, not a validation of the UAE study's
conclusions, for a concrete reason: the UAE escape-burden signal is in **RSV-B**. Its recurrent
RSV-A substitutions rank low on the single-mutation EVEscape scale — L381F, the highest among them,
sits at the **8.7th percentile**; K419E at 3.7%. So there is no set of highly-prioritized RSV-A
variants from that study to validate.

What can be tested is the RSV-A EVEscape *ranking itself*, as a model, against experiment. Extending
convincingly to RSV-B would require the RSV-B EVEscape table **and** a comprehensive RSV-B DMS. The
latter does not exist; Simonich et al. measured only selected individual point mutants in the B1
background.

## Terminology used, and avoided

| Avoided | Used instead | Why |
|---|---|---|
| ground truth | experimental reference; DMS-measured escape | The DMS is a measurement with its own assay conditions and precision filters |
| viral fitness, viable virus, fit mutation | F-mediated cell-entry function; retains cell-entry function above the predefined threshold | The assay measures F-mediated entry of a single-round pseudovirus. The source paper: *"cell entry is only one of several F properties that contribute to transmissibility"* |
| functional validation (of EVEscape) | benchmarking; computational prioritization vs experimental measurement | Nothing here validates EVEscape as a method; it is compared on one protein against one antibody |
| resistant | reduced neutralization; DMS-measured escape | Clinical resistance is a different claim from an in-vitro neutralization shift |

## Status

The experimental side is complete and reproducible. The comparison is blocked on obtaining the
production RSV-A per-mutation EVEscape table — see `README.md` for the exact request and
`ANALYSIS_PLAN.md` for the pre-registered analysis.
