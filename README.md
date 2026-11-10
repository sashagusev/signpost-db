# SIGNPOST

Signed gene effects across 433 human traits. For genes at genome-wide significant loci, SIGNPOST predicts whether more of the gene (higher expression or protein) raises or lowers each trait, how likely that call is to be wrong, and how likely the gene is the one acting at its locus.

This repository is the static results browser. Open `index.html` through a web server (the page loads its data with `fetch`):

```bash
python3 -m http.server 8000
```

then visit http://localhost:8000.

## What is in it

| | |
|---|---|
| Cohorts | Pan-UKB (158 traits), FinnGen R12 (187 endpoints), a batch of 88 Open Targets studies (GWAS Catalog and FinnGen R12) |
| Calls | 31,512 gene–trait pairs at qualifying loci (credible-set lead p < 5×10⁻⁸, minor allele frequency ≥ 1%) with P(wrong) ≤ 30%; traits without such a call are left out |
| Pages | Gene–trait (the call, each source's evidence and its top features, gene-burden test, other cohorts, causal genes at the locus), trait (all genes with a call), gene (all traits, drugs, function), About (accuracy figures) |

## Files

| File | Contents |
|---|---|
| `index.html` | The site (single page, no build step) |
| `core.json` | Traits, genes, calls with compact per-source evidence, drug targets, cross-cohort trait matches |
| `d/00.json` … `d/31.json` | Per-source strengths, feature contributions (SHAP) and locus candidates, split by trait; loaded on gene–trait pages |
| `uniprot.json` | UniProt function summaries, loaded on gene pages |
| `about.json` | Data for the About page figures |
| `signpost_calls.csv` | Download: the site's calls (P(wrong) ≤ 30%), one row per gene and trait: direction, P(wrong), the source that made the call, P(causal), lead variant, each source's strength and P(wrong), gene-burden test |
| `signpost_all_pairs.csv.gz` | Download: the same columns for every scored gene–trait pair at qualifying loci, including calls close to a coin flip (gzipped) |

## Reading a call

- **Direction**: the effect of more of the gene on the trait. Loss-of-function carriers are expected to show the opposite.
- **P(wrong)**: the chance the direction is wrong, from per-source models trained jointly on gene-burden tests in exome data and the genome-wide burden likelihood, recalibrated against genome-wide burden with the gene's locus held out, with an offset for the lead-variant class (coding; regulatory and enhancer-linked by pgBoost; regulatory and unlinked). The call uses the most confident source; a seventh source, the gene's own rare-variant burden below genome-wide significance, enters the same choice, and a genome-wide significant burden test sets the call directly (the page names the test and its p). Values below 5% are shown as <5%. The coding-variant model uses AlphaMissense pathogenicity.
- **P(causal)**: the chance the gene is the one acting at its locus, shared among nearby protein-coding genes. Learned from loci where a coding credible-set variant names the gene, using distance, credible-set position relative to the gene, ENCODE-rE2G enhancer–gene links, pgBoost, PoPS, constraint and expression; burden-significant genes, which it never saw, are recovered as the top candidate at 63% of their loci (nearest gene: 38%).

Agreement with approved drugs is highest for the most confident calls (about 80% for the top 25 targets) and falls toward 60% over all targets; burden-known signs reach 89% on the same test. See the About page.

## Data sources

GWAS summary statistics from Pan-UKB, FinnGen R12 and the GWAS Catalog (through Open Targets). Gene-burden labels from Genebass, BRaVa, FinnGen and Open Targets; sub-threshold burden evidence and the genome-wide burden likelihood use BRaVa's cross-biobank meta-analysis where it covers the trait, combined with FinnGen's own burden for FinnGen endpoints. Drug mechanisms and clinical stages from Open Targets (ChEMBL). Protein function summaries from [UniProt](https://www.uniprot.org) (CC BY 4.0). Missense pathogenicity from [AlphaMissense](https://doi.org/10.1126/science.adg7492) (CC BY-NC-SA 4.0). Enhancer–gene links from ENCODE-rE2G through Open Targets. Mouse knockout phenotypes from IMPC were used for validation only. All of Us data are used only for aggregate accuracy figures.

Research use only; not for clinical decisions.
