# References

All sources are cited by DOI rather than redistributed as PDFs.

## Primary sources

**Simonich CA, et al.** *Complete definition of how antibodies used to prevent RSV are affected by F
mutations.* bioRxiv (2026).
DOI: [10.64898/2026.02.12.705519](https://doi.org/10.64898/2026.02.12.705519) — CC-BY 4.0
Repository: <https://github.com/dms-vep/RSV_Long_F_DMS> · Interactive:
<https://dms-vep.org/RSV_Long_F_DMS/>
Source of the DMS benchmark. Supplementary File 1 ≡ `results/summaries/all_antibodies.csv`.

**Zhou H, Ali S, Senghore M, Amirtharaj F, Murad P, Al Hosani M, Al Ahbabi F, Henschel A,
Al Hosani F, Everett D.** *Genomic surveillance in the UAE reveals the global origins and local
diversification of RSV lineages.* Research Square preprint (2026), posted 14 April 2026.
DOI: [10.21203/rs.3.rs-9344370/v1](https://doi.org/10.21203/rs.3.rs-9344370/v1)
Repository: <https://github.com/hz424/rsv_global>
Source of the RSV-A/RSV-B EVEscape analysis this project aims to benchmark.

## Methods background

**Thadani NN, et al.** *Learning from prepandemic data to forecast viral escape.* Nature 622,
818–825 (2023). DOI: [10.1038/s41586-023-06617-0](https://doi.org/10.1038/s41586-023-06617-0)
The EVEscape framework. Covers SARS-CoV-2, influenza, HIV, Lassa, Nipah — **not RSV**.

**Frazer J, et al.** *Disease variant prediction with deep generative models of evolutionary data.*
Nature 599, 91–95 (2021). DOI: [10.1038/s41586-021-04043-8](https://doi.org/10.1038/s41586-021-04043-8)
EVE, supplying the evolutionary-fitness component of EVEscape.

**Wilkins D, et al.** *Molecular and phenotypic characteristics of RSV infections in infants during
two nirsevimab randomized clinical trials.* Nature Communications 14, 4933 (2023).
DOI: [10.1038/s41467-023-40592-4](https://doi.org/10.1038/s41467-023-40592-4)
Clinical context for nirsevimab resistance-associated substitutions (I64T, K68E, N208S,
I206M:Q209R:S211N). Uses in-silico structural modelling; no EVEscape.

## Data and structures

| Item | Accession | Role |
|---|---|---|
| RSV-A F reference (EVEscape) | [KX858757.1](https://www.ncbi.nlm.nih.gov/nuccore/KX858757.1) | hRSV/A isolate 13-005275; CDS 1..1725 = 574 aa; protein `AOZ56961.1` |
| RSV-B F reference (EVEscape) | [KX858756.1](https://www.ncbi.nlm.nih.gov/nuccore/KX858756.1) | Subtype B counterpart, not used here |
| RSV-A prefusion F + nirsevimab Fab | [5UDC](https://www.rcsb.org/structure/5UDC) | WCN structural term, RSV-A |
| RSV-B prefusion F + nirsevimab Fab | [5UDD](https://www.rcsb.org/structure/5UDD) | WCN structural term, RSV-B |

Note: `rsv_global/.../compute_wcn.py` defaults to `--pdb …5UDD.pdb`, which is the **RSV-B**
structure per the paper. An RSV-A run must pass `--pdb 5UDC` explicitly.

## Antigenic region definitions

**Gilman MSA, et al.** Sci Immunol 1, eaaj1879 (2016). PMC5244814
**Rossey I, et al.** Trends Microbiol 26, 209–219 (2018). PMID 29054341
Source of the six broadly defined antigenic regions in the DMS `region` column.

## Pipeline

**dms-vep-pipeline-3** — <https://github.com/dms-vep/dms-vep-pipeline-3>
Produces `all_antibodies.csv` from the per-assay files. Present as an (empty) git submodule in the
DMS repository clone; not required to reproduce this package.

**polyclonal** — <https://jbloomlab.github.io/polyclonal/>
Fits the neutralization curves and mutation-effect estimates behind the escape columns.
