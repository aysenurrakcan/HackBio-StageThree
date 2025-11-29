# SARS-CoV-2 scRNA-seq Analysis (GSE166766)

This repository contains a single-cell RNA-seq analysis of the dataset **GSE166766**, which profiles human bronchial epithelial cells infected with SARS-CoV-2 at different time points (mock, 1 dpi, 2 dpi, 3 dpi).

The goal is to reproduce neighbourhood clustering, cell-type identification, and pseudotime analysis similar to the figures in the reference PLOS Biology paper.

## Reference

Paper: *SARS-CoV-2 infection remodels airway epithelial cell states*  
Journal: PLOS Biology  
Link: https://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.3001143

Dataset: **GSE166766**  
GEO link: https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE166766

## Analysis Overview

The notebook performs the following steps:

1. **Data loading**
   - Download and read MTX/TSV files for each GSM sample.
   - Load each sample using `scanpy.read_10x_mtx`.
   - Annotate samples with condition labels (mock, 1 dpi, 2 dpi, 3 dpi).

2. **Quality control**
   - Filter genes expressed in fewer than 3 cells.
   - Identify mitochondrial genes.
   - Compute QC metrics per cell (total counts, number of genes, mitochondrial percentage).

3. **Normalization and feature selection**
   - Normalize counts per cell using `sc.pp.normalize_total`.
   - Apply log-transformation using `sc.pp.log1p`.
   - Identify highly variable genes.
   - Perform PCA on highly variable genes.

4. **Neighbourhood graph and clustering**
   - Construct k-nearest neighbor graph with `sc.pp.neighbors`.
   - Compute UMAP embedding with `sc.tl.umap`.
   - Run Leiden clustering with `sc.tl.leiden`.

5. **Cell-type annotation**
   - Use canonical airway epithelial marker genes to assign clusters to:
     - Basal cells
     - Club cells
     - BC/club intermediate cells
     - Ciliated cells
     - Goblet cells
   - Visualize marker expression and annotated cell types on UMAP.

6. **Infection-related analysis**
   - Detect viral genes by name.
   - Compute viral RNA counts per cell.
   - Define an "infected" indicator based on viral counts.
   - Visualize infection status on UMAP.

7. **Pseudotime analysis (3 dpi)**
   - Subset cells from the 3 dpi condition.
   - Recompute PCA, neighbors, and UMAP.
   - Compute diffusion maps and diffusion pseudotime (DPT).
   - Use Basal and BC/club cells as root-like populations.
   - Visualize pseudotime over UMAP.

8. **ACE2 and ENO2 analysis**
   - Locate ACE2 (and ENO2 if present) in the gene list.
   - Plot their expression across clusters and cell types.
   - Evaluate their usefulness as infection or susceptibility markers.

## Main Findings

- Major airway epithelial cell types (Basal, Club, BC/club, Ciliated, Goblet) were successfully identified using marker genes and clustering.
- Infection at 3 dpi was enriched in Club and BC/club populations, consistent with airway epithelium biology.
- ACE2 expression was very low and sparse; it did not correlate well with infected cells in this dataset.
- ENO2 behaved more as an infection-response or stress-related marker than ACE2.
- Pseudotime analysis suggested a trajectory from Basal to BC/club to Club cells at 3 dpi, reflecting epithelial differentiation and regeneration under viral stress.

## Requirements

The analysis primarily uses the following Python packages:

- scanpy
- anndata
- numpy
- pandas
- matplotlib
- leidenalg

In Google Colab, the core dependencies can be installed with:

```python
!pip install scanpy anndata matplotlib pandas numpy leidenalg
