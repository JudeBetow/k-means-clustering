# Diversity Reduction Workflow

Select a diverse subset of standardised ReFRAME compounds for docking workflows.

The main workflow is implemented in `k-Means_Clustering_Notebook.ipynb`. It is designed to run after the molecule standardisation notebook has produced a clean `reframe_std.csv` file.

## What the Notebook Does

1. Loads standardised ReFRAME molecules.
2. Calculates physicochemical descriptors:
   - molecular weight
   - LogP
   - hydrogen bond donors
   - hydrogen bond acceptors
   - TPSA
   - heavy atom count
3. Applies Lipinski-style filtering.
4. Computes Bemis-Murcko scaffolds.
5. Optionally limits the number of molecules per scaffold.
6. Generates Morgan fingerprints.
7. Clusters compounds with k-means.
8. Selects representative molecules nearest to cluster centroids.
9. Exports CSV and SDF files for downstream docking.
10. Searches compound names for possible positive-control matches.
11. Generates scaffold and clustering plots.

## Requirements

- Python 3.8+
- Jupyter Notebook or JupyterLab
- pandas
- numpy
- RDKit
- scikit-learn
- tqdm
- matplotlib
- seaborn

Recommended installation with conda:

```bash
conda install -c conda-forge rdkit pandas numpy scikit-learn tqdm matplotlib seaborn jupyterlab
```

## Input Data

The notebook expects a standardised CSV with a `std_smiles` column.

The default input is:

```python
INPUT_CSV = "skc_outputs/reframe_std.csv" # change path 
```

An example standardized file, `reframe_std.csv`, is included in this folder. If you keep the notebook default path, place that file at:

```text
skc_outputs/reframe_std.csv
```

or update `INPUT_CSV` to point to the file directly:

```python
INPUT_CSV = "reframe_std.csv"
```

Expected columns include:

- `std_smiles`
- `name`
- `source_id`

## Usage

Start Jupyter:

```bash
jupyter lab
```

Open:

```text
ReFRAME_Clustering_Notebook.ipynb
```

Update `INPUT_CSV` if needed, then run the notebook from top to bottom.

## Key Parameters

### Scaffold Cap

The notebook limits large scaffold families so they do not dominate the final set:

```python
MAX_PER_SCAFFOLD = 5
```

### Number of Clusters

The number of k-means clusters controls the number of representative compounds:

```python
N_CLUSTERS = 500
```

Suggested values:

| Docking Budget | Clusters |
|---|---:|
| pilot run | 200-300 |
| moderate run | 500 |
| larger run | 1000 |

## Outputs

By default, outputs are written under `skc_outputs/`.

Expected outputs include:

- `ReFRAME_cluster_representatives.csv`: selected representative compounds
- `ReFRAME_scaffold_filtered.csv`: scaffold-capped compound set
- `ReFRAME_lipinski_filtered.csv`: compounds passing Lipinski filtering
- `controls.csv`: possible positive-control name matches
- `ReFRAME_cluster_rep_500.sdf`: representative compounds exported as SDF
- `top_scaffolds.png`: top Bemis-Murcko scaffold frequency plot
- `cluster_size_distribution.png`: k-means cluster size plot

The notebook also creates a t-SNE cluster visualisation. In the current notebook, that image is saved as `tsne_clusters.png` in the working directory.

## Notes

The default workflow is tuned for a modest workstation and a docking campaign of about 500 representative compounds. Increase `N_CLUSTERS` for broader diversity or reduce it for faster pilot docking.
