# SIGNPOST

Signed gene effects across 450 human traits. For genes at genome-wide significant loci, SIGNPOST predicts whether more of the gene (higher expression or protein) raises or lowers each trait, how likely that call is to be wrong, and how likely the gene is the one acting at its locus.

This repository is the static results browser. Open `index.html` through a web server (the page loads its data with `fetch`):

```bash
python3 -m http.server 8000
```

then visit http://localhost:8000.

## What is in it

| | |
|---|---|
| Cohorts | Pan-UKB (158 traits), FinnGen R12 (207 endpoints), a batch of 92 Open Targets studies (GWAS Catalog and FinnGen R12) |
| Calls | 48,040 gene–trait pairs at qualifying loci (credible-set lead p < 5×10⁻⁸, minor allele frequency ≥ 1%) with P(wrong) ≤ 40% or a gene-burden label |
| Pages | Gene–trait (the call, each source's evidence and its top features, gene-burden test, other cohorts, causal genes at the locus), trait (all genes with a call), gene (all traits, drugs, function), About (accuracy figures) |

## Files

| File | Contents |
|---|---|
| `index.html` | The site (single page, no build step) |
| `core.json` | Traits, genes, calls with compact per-source evidence, drug targets, cross-cohort trait matches |
| `d/00.json` … `d/31.json` | Per-source strengths, feature contributions (SHAP) and locus candidates, split by trait; loaded on gene–trait pages |
| `uniprot.json` | UniProt function summaries, loaded on gene pages |
| `about.json` | Data for the About page figures |

## Reading a call

- **Direction**: the effect of more of the gene on the trait. Loss-of-function carriers are expected to show the opposite.
- **P(wrong)**: the chance the direction is wrong, from per-source models trained against gene-burden tests in exome data and recalibrated against genome-wide burden. The call uses the most confident source.
- **P(causal)**: the chance the gene is the one acting at its locus, shared among nearby protein-coding genes, from pgBoost links and distance.

Drug-direction support is strongest for genes like those the model was trained on; for genes new to the model, agreement with approved drugs is near chance beyond the most confident calls. See the About page.

## Data sources

GWAS summary statistics from Pan-UKB, FinnGen R12 and the GWAS Catalog (through Open Targets). Gene-burden labels from Genebass, BRaVa, FinnGen and Open Targets. Drug mechanisms and clinical stages from Open Targets (ChEMBL). Protein function summaries from [UniProt](https://www.uniprot.org) (CC BY 4.0). All of Us data are used only for aggregate accuracy figures.

Research use only; not for clinical decisions.
