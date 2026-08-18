# Pre-registered analysis plan

**Written before any RSV-A EVEscape score has been obtained or inspected.**

The point of fixing this in advance is narrow and specific: given how the escape signal is
distributed (see §1), a weak global correlation is the *expected* result. If the analysis were
chosen after seeing the scores, a weak correlation would be indistinguishable from a discarded
analysis. Committing first is what makes the outcome interpretable either way.

Everything below is fixed. Deviations must be recorded in a "Deviations" section with the date and
reason, not silently applied.

---

## 1. Why the design is shaped this way

Two facts about the benchmark, both measured in `notebooks/01_build_dms_benchmark.ipynb`:

**The escape signal is extremely concentrated.** Nirsevimab binds a specific epitope (~64–73 and
~201–216). Of 3,977 Fab measurements, only 343 (8.6%) lie in that epitope — yet **93 of the 98**
mutations with Fab escape > 1.0 are there, and the **top 50** Fab-escape mutations span only **12
sites, all in `region_0`** (antigenic site Ø).

**EVEscape does not know about nirsevimab.** It combines an EVE evolutionary-fitness term, WCN
structural accessibility, and amino-acid dissimilarity. The EVEscape authors describe it as
identifying antigenic regions *"without antibody information"*.

So roughly 91% of the benchmark consists of mutations where nirsevimab escape is near zero
essentially by construction, and where EVEscape has no reason to be low. A global Spearman ρ is
therefore diluted by design. This is not a defect in either method — it is what happens when an
epitope-agnostic model is scored against an antibody-specific readout. It has two consequences:

* the **primary** statistic is reported with n and n_sites, not with a p-value headline (§4);
* **top-rank enrichment** (§5) is the analysis that actually answers the surveillance question, and
  is given equal weight rather than being relegated to a supplement.

## 2. Input data

| Input | Source | Status |
|---|---|---|
| DMS benchmark | `data/dms_nirsevimab_functional.csv`, 4,276 rows | present |
| RSV-A EVEscape scores | `mutation, wt, i, mut, evescape, evescape_composite, evescape_percentile`, 10,906 rows | **awaited** |

No filter on the DMS side will be changed after the EVEscape table arrives. In particular
`cell entry > -2.5` is fixed and is the source paper's own published criterion.

## 3. Merge

Exactly as implemented and dry-run-verified in `notebooks/02_reference_compatibility.ipynb`:

* key `(site, wildtype, mutant)` ↔ `(i, wt, mut)`, `how="left"`, `validate="one_to_one"`
* drop the 8 reference-disagreement sites (80, 101, 105, 124, 213, 276, 384, 515)
* **never** merge on `sequential_site` (it is ectodomain-relative, `site - 25`)

**Report with every result**, whatever the numbers turn out to be:

```
n_dms_benchmark  n_merged  n_dropped_reference_mismatch
n_dropped_out_of_range  n_dropped_not_scored  n_evescape_unused
```

Expected: `n_merged = 4139`, `n_dropped_reference_mismatch = 137`, others 0.
**Any deviation is investigated before any correlation is computed**, since it would mean the
delivered table differs from the assumed schema.

State explicitly that site 213 — inside the nirsevimab epitope — is among the dropped sites.

## 4. Primary and secondary analyses

**Primary**

```
Spearman( evescape_percentile , Nirsevimab-Fab escape )    on the merged Fab subset
```

**Secondary**

```
Spearman( evescape_percentile , Nirsevimab-IgG escape )    on the merged IgG subset
```

Rationale, all from the source papers:

* **Spearman, not Pearson.** EVEscape is a log-sigmoid composite; DMS escape is a `polyclonal` model
  estimate. Neither is on a linear scale relative to the other and both have heavy tails. Only the
  monotone relationship is meaningful.
* **Percentile vs raw `evescape` is irrelevant to the result.** `evescape → evescape_composite`
  (`exp`) and `→ evescape_percentile` (`rank`) are both strictly monotone, so all three give
  identical Spearman coefficients. The percentile is reported for readability; the equivalence is
  stated so it cannot be mistaken for a choice.
* **Fab primary.** Simonich et al.: *"Fab measurements are more generalizable across genetic
  backgrounds due to the absence of affinity-dependent buffering of bivalent IgG neutralization."*
  EVEscape is a background-agnostic sequence model, so the mutation-intrinsic endpoint is the closer
  match. This is a justified choice, and is stated as a choice.
* **IgG secondary**, because it is the clinically administered form.
* **Never average Fab and IgG.** They measure different quantities; their measured rank correlation
  on this dataset is only ρ = 0.263 (n = 3,945).

**Reporting format — fixed:**

> ρ = X.XXX (n = N mutations across S sites)

with a scatter plot, EVEscape percentile on x, DMS escape on y, points coloured by antigenic region.

**On p-values.** Multiple substitutions occur at the same site (mean 9.4 per site), so observations
are not independent; the effective sample size is closer to the number of independent *sites* — and,
given the concentration described in §1, arguably to the handful of epitope sites carrying signal.
Conventional Spearman p-values therefore overstate independence substantially. **ρ, n, n_sites and
the scatter are reported; an extremely small p-value is not headlined.** Site-block bootstrap,
site-level permutation, and cluster-aware resampling are legitimate refinements and are named in
Limitations as optional future work — they are **not** required for this exploratory benchmark.

## 5. Top-rank enrichment (equal weight with §4)

The surveillance-facing question:

> Are mutations with strong experimentally measured nirsevimab escape enriched among the top-ranked
> EVEscape mutations?

For k ∈ {1%, 5%, 10%} of EVEscape-ranked mutations within the merged set, report:

* the number and fraction of DMS strong-escape mutations captured;
* the median and IQR of DMS Fab escape inside vs outside the top-k;
* fold enrichment relative to the base rate, with the base rate stated alongside.

For descriptive purposes only, "strong escape" is summarised at Fab escape > 1.0 (n = 98 in the full
benchmark) **and** > 0.5 (n = 227), with **both** reported so the conclusion cannot depend on either
cutoff. These are descriptive strata, not a classification label — see §6.

This is more interpretable for genomic surveillance than a single global coefficient: a surveillance
team triaging variants cares whether the model's top slice is enriched for real escape, not whether
rank order is preserved across 4,000 mostly-inert mutations.

## 6. What will NOT be done

* **No invented binary escape threshold.** Simonich et al. define no "escape positive" cutoff; a
  search of the paper confirms none exists. The primary benchmark stays continuous. AUROC / AUPRC
  are therefore **not** primary. If a defensible threshold is later justified from the source, AUPRC
  must be reported against its null = prevalence of positives.
* **No imputation of missing escape values.** `NaN` is a censored measurement, not a zero.
* **No averaging of Fab and IgG.**
* **No post-hoc filter changes** on the DMS side.
* **No claim of "validation of the UAE findings".** The UAE escape-burden signal is RSV-B; its
  recurrent RSV-A substitutions rank low on the single-mutation EVEscape scale (L381F highest at the
  8.7th percentile). This is an RSV-A proof-of-concept benchmark of the *model*, not a test of that
  study's prioritized variants.
* **No modelling beyond the benchmark.** No predictive model is fitted to the DMS data, and EVE
  is not retrained. This project measures agreement between an existing prediction and an
  existing measurement; anything further is a separate piece of work.

## 7. Interpreting discordance

Descriptive framing, using neutral language because no formal binary label is defined:

| Pattern | Reading |
|---|---|
| High EVEscape + high DMS escape | successful prioritization |
| High EVEscape + low DMS escape | **discordant high-prediction / low-experimental-effect** — expected in bulk, since EVEscape scores escape risk against *any* antibody, not nirsevimab specifically |
| Low EVEscape + high DMS escape | **discordant low-prediction / high-experimental-effect** — the scientifically interesting class: experimentally important nirsevimab escape that the model ranks low |

Do **not** call these false positives / false negatives. The middle row in particular is not
necessarily model error: a mutation can be genuinely escape-prone for a different antibody while
being irrelevant to nirsevimab.

## 8. Pre-committed interpretation of the likely outcomes

| Outcome | What may be concluded |
|---|---|
| Weak global ρ (\|ρ\| < 0.2), no top-k enrichment | EVEscape's RSV-A ranking does not track nirsevimab-specific escape. Given §1 this is the *expected* result and is a legitimate, reportable finding — it bounds how far an epitope-agnostic prior can substitute for antibody-specific measurement. |
| Weak global ρ **but** clear top-k enrichment | The global coefficient is diluted by the ~91% of mutations outside the epitope; the model is still useful for triage. This would be the most informative outcome for surveillance. |
| Moderate ρ (0.2–0.4) with enrichment | EVEscape carries real signal for nirsevimab escape despite having no antibody information. A strong positive result. |
| Strong ρ (> 0.4) | Would be surprising given §1 and must be checked for an artefact — most plausibly a shared dependence on structural accessibility or site conservation — before being reported as such. |

None of these outcomes is a failure of the project. The benchmark answers a question that has not
been asked for RSV, and the answer is informative in every direction.

## 9. Deviations

*(none yet — record any change here with date and reason)*
