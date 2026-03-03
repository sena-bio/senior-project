# Gene Gating Prediction in IMR90 Cells
Integration of DamID, ChIP-seq, and Hi-C to model potential oncogenes that undergo gene gating.

## Project Overview

This project investigates the regulatory and structural architecture underlying nuclear pore complex (NPC) association in IMR90 human fibroblasts:

- RNA-seq expression filtering
- DamID (Nup153)
- Histone ChIP-seq
- Hi-C contact domains and loops
- TF ChIP-seq

Using a data-driven statistical framework (PCA + logistic regression), we construct a quantitative NPC probability score for highly expressed genes.

### Biological Question:
Do highly expressed genes associate with the nuclear pore randomly, or is NPC proximity predicted by coordinated regulatory architecture?

We test whether: 
Promoter DamID signal,
Enhancer association within TADs,
Promoter–enhancer loops, and
Transcription factor support jointly predict nuclear pore association.

## Data Sources

### DamID (Nup153)
GEO: GSE87831
Cell line: IMR90
Genome: hg19 (lifted to hg38)
Source:
https://genesdev.cshlp.org/content/suppl/2016/11/02/gad.287417.116.DC1/Supplemental_Material.pdf

### ChIP-seq Datasets (ENCODE)
Accession	Assay	Genome
ENCSR000EFI	CTCF ChIP-seq	hg38
ENCSR002YRE	H3K27ac ChIP-seq	hg38
ENCSR087PFU	H3K4me3 ChIP-seq	hg38
ENCSR437ORF	H3K36me3 ChIP-seq	hg38

ENCODE Portal:
https://www.encodeproject.org/

### Hi-C (ENCODE)
ENCSR345VTI
Contact domains (TADs) — .bedpe
Hi-C loops — .bedpe
Genome: hg38

Used for:
TAD-based enhancer assignment
Promoter–enhancer loop detection

## Analytical Framework

### Expression Filtering
RNA-seq replicates averaged
zFPKM filtering (zFPKM > -3)
Top 10% TPM retained
Gene IDs mapped to Entrez

### Structural Feature Extraction
For each highly expressed gene:
Promoter DamID overlap (binary + quantitative)
Gene body DamID overlap
Enhancer DamID within TAD (enhancer validation by H3K27ac)
Promoter–enhancer Hi-C loops
TF support (CTCF, POLR2, RAD21 counts)
Histone validation (H3K4me3, H3K36me3)

### Architecture Discovery (PCA)
Principal Component Analysis is applied to:
Promoter DamID signal
Enhancer DamID signal
Loop contact score
TF support

--> **PC1** defines the Architecture Axis
--> **PC2** defines the Promoter Axis

### Statistical Modeling

- Fisher’s Exact Test (enrichment)
- Wilcoxon rank-sum test (distribution shift)
- Logistic regression (NPC probability modeling)
- ROC curve and AUC validation
- Benjamini–Hochberg correction for multiple testing

### NPC Probability Score

Final NPC score:
NPC_probability = logistic(PC1 + PC2)

--> Genes are ranked by predicted probability of nuclear pore association.

### Functional Enrichment

Top NPC-associated genes are analyzed using:
- GO (Biological Process)
- KEGG pathways
- Reactome pathways

Implemented with:
clusterProfiler,
ReactomePA

## Repository Structure
npc-architecture-imr90/
│
├── README.md
├── LICENSE
├── .gitignore
│
├── scripts/
│   ├── 01_processing.R
│   ├── 02_structural_features.R
│   ├── 03_pca_modeling.R
│   ├── 04_enrichment.R
│
├── data/
│   └── (not included; see Data Access section)
│
├── results/
│   ├── figures/
│   ├── tables/
│
└── sessionInfo.txt

## Data Access & Reproducibility

Raw datasets are not included due to size constraints.

To reproduce:

Download datasets from GEO and ENCODE using accession IDs above.

Place files in appropriate subdirectories:

dam_id/

chip/

hi_c/

rna_seq/

Run scripts sequentially:

01_processing.R
02_structural_features.R
03_pca_modeling.R
04_enrichment.R

## Statistical Rationale

- PCA reduces correlated regulatory features into orthogonal architecture axes.

- Logistic regression models NPC association probability.

- BH correction controls false discovery rate.

- ROC/AUC ensures predictive validity.


## Software & Environment

R version: ≥ 4.2

Required packages:

GenomicRanges

rtracklayer

TxDb.Hsapiens.UCSC.hg38.knownGene

org.Hs.eg.db

clusterProfiler

ReactomePA

pROC

zFPKM

Full environment recorded in sessionInfo.txt.

## Outputs

- Ranked NPC-associated gene list

- PCA loadings and scores

- ROC curve (model performance)

- GO / KEGG enrichment tables

- Figures

## Thesis Context

This repository accompanies:

[Your Thesis Title Here]

The analysis demonstrates that nuclear pore association in IMR90 cells is predicted by coordinated promoter-proximal and structural regulatory architecture.
