# Dataset Overview

This directory contains a single annotated single-cell dataset in AnnData format:

- `mixscape_hvg_filter.h5ad`

The file appears to be a processed single-cell perturbation dataset prepared for downstream analysis with Mixscape-style annotations. It contains:

- `43,550` observations (cells)
- `2,000` variables (genes/features)
- precomputed PCA and UMAP embeddings
- neighbor graph and distance graph
- additional expression layers and Mixscape-related metadata

The file size is approximately `1.14 GB`.

## Environment Setup

To explore the data and run the notebook, install the Python packages listed in `requirements.txt`.

```bash
python -m pip install -r requirements.txt
```

The notebook uses:

- `anndata`
- `h5py`
- `jupyter`
- `matplotlib`
- `numpy`
- `pandas`
- `scipy`
- `seaborn`

If you prefer a minimal install for just loading and inspecting the `.h5ad` file, the most important packages are:

```bash
python -m pip install anndata h5py numpy pandas scipy matplotlib
```

## File Format

`mixscape_hvg_filter.h5ad` is an [AnnData](https://anndata.readthedocs.io/) HDF5-backed file, commonly used in the Python single-cell ecosystem (`anndata`, `scanpy`).

Top-level components in the file:

- `X`: main data matrix with shape `(43550, 2000)`
- `obs`: cell-level metadata
- `var`: gene-level metadata
- `layers`: alternate expression matrices
- `obsm`: low-dimensional embeddings
- `obsp`: pairwise sparse matrices
- `uns`: unstructured analysis metadata
- `varm`: feature loadings

## What The Data Contains

### Main Matrix

- `X` has shape `(43550, 2000)`
- The `2,000` features correspond to genes stored in `var`
- Based on the metadata, these appear to be highly variable genes selected for analysis

### Cell Metadata (`obs`)

Cell identifiers are stored in `obs['Cell_barcodes']` and used as the observation index.

Available cell-level fields:

- `gene`: categorical perturbation/target assignment
- `n_genes`: per-cell gene count metric
- `n_genes_by_counts`: number of genes detected by counts
- `total_counts`: total counts per cell
- `total_counts_mt`: mitochondrial counts per cell
- `pct_counts_mt`: percent mitochondrial counts
- `leiden`: Leiden cluster assignment
- `mixscape_class_p_ko`: numeric Mixscape score/probability-like field
- `mixscape_class`: categorical Mixscape class
- `mixscape_class_global`: coarse global Mixscape class
- `pertclass`: categorical perturbation class

Notable metadata characteristics:

- `gene` has `88` categories
- `leiden` has `13` clusters
- `mixscape_class_global` has `3` categories
- `pertclass` has `3` categories
- `mixscape_class` has `89` categories

Example perturbation labels visible in the file include:

- `AARS`
- `AMIGO3`
- `ARHGAP22`
- `ASCC3`
- `ATF4`

### Gene Metadata (`var`)

Gene symbols are stored in `var['Gene_symbol']` and used as the variable index.

Available gene-level fields:

- `ENSEMBL`: Ensembl gene identifier
- `ENTREZID`: Entrez gene identifier
- `n_cells`: number of cells in which the gene is detected
- `mt`: mitochondrial gene flag
- `n_cells_by_counts`: cells with nonzero counts
- `mean_counts`: mean counts per gene
- `pct_dropout_by_counts`: dropout rate by counts
- `total_counts`: total counts per gene
- `highly_variable`: highly variable gene flag
- `means`: mean expression statistic
- `dispersions`: dispersion statistic
- `dispersions_norm`: normalized dispersion statistic

All `2,000` genes in this file are marked as `highly_variable = True`, which suggests the dataset was filtered to highly variable genes before saving.

Example gene symbols at the start of the matrix:

- `MIR1302-10`
- `SAMD11`
- `HES4`
- `TNFRSF18`
- `TNFRSF4`

### Additional Layers (`layers`)

The file contains two alternate matrices with the same shape as `X`:

- `X_pert`: `(43550, 2000)`
- `normal_counts`: `(43550, 2000)`

These likely represent alternate normalized or perturbation-specific expression values. Their exact generation is not described inside the file itself.

### Embeddings (`obsm`)

Precomputed low-dimensional representations are available:

- `X_pca`: PCA embedding with shape `(43550, 50)`
- `X_umap`: UMAP embedding with shape `(43550, 2)`

### Pairwise Graphs (`obsp`)

Sparse cell-cell graphs are included:

- `connectivities`: CSR sparse matrix with shape `(43550, 43550)`
- `distances`: CSR sparse matrix with shape `(43550, 43550)`

These are consistent with a standard nearest-neighbor graph used for clustering and UMAP.

### Feature Loadings (`varm`)

- `PCs`: per-gene PCA loadings

### Analysis Metadata (`uns`)

Stored analysis metadata includes:

- `hvg`
- `leiden`
- `log1p`
- `mixscape`
- `neighbors`
- `pca`
- `umap`

Recovered analysis settings include:

- HVG selection flavor: `seurat`
- Leiden resolution: `1`
- neighbor search: `15` neighbors, `euclidean` metric, `umap` method
- PCA performed with `use_highly_variable = True`

The `uns['mixscape']` group also contains many perturbation-specific entries keyed by gene name.

## Interpretation

This looks like a processed single-cell RNA-seq perturbation dataset that has already gone through:

- quality-control metric calculation
- highly variable gene filtering
- dimensionality reduction
- nearest-neighbor graph construction
- Leiden clustering
- Mixscape-based perturbation labeling

Because the file does not include a standalone project description, sample origin, organism, cell type, experimental protocol, or preprocessing script, those details cannot be recovered confidently from the `.h5ad` file alone.

## Loading The Data

Example in Python:

```python
import anndata as ad

adata = ad.read_h5ad("mixscape_hvg_filter.h5ad")

print(adata)
print(adata.obs.columns)
print(adata.var.columns)
print(adata.obsm.keys())
print(adata.layers.keys())
```

For memory-efficient inspection:

```python
import anndata as ad

adata = ad.read_h5ad("mixscape_hvg_filter.h5ad", backed="r")
print(adata.shape)
```

## Caveats

- This README describes the structure and contents inferable from the file itself.
- Biological interpretation should be validated against the original study or preprocessing notebook if available.
- The meanings of `X_pert`, `normal_counts`, and the exact Mixscape class labels should be confirmed from the upstream analysis code.
