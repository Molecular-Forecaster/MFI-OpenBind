# MFI docking program - self-docking analysis of the OpenBind benchmark

Docked poses and compound-level analysis from Molecular Forecaster's
self-docking evaluation of the OpenBind enterovirus A71 2A protease benchmark.
The dataset covers 611 compounds with OpenStructure scores and includes two
PoseBusters-valid pose selections per compound: lowest energy and best RMSD.

## Contents

- `analysis/compound_comparison.csv`: compound properties, pose-selection results,
  PCA coordinates and chemical-cluster assignments.
- `analysis/pose_manifest.csv`: one row per exported SDF, linking its filename to
  the compound, original event folder, run, selection rule and analysed scores.
- `poses/<original-folder>/<original-folder>_lowest-energy.sdf`: lowest FITTED-energy
  PoseBusters-valid pose for that compound.
- `poses/<original-folder>/<original-folder>_best-RMSD.sdf`: minimum OpenStructure-RMSD
  PoseBusters-valid pose for that compound.
- `poses/<original-folder>/<pose-name>.openstructure.json`: OpenStructure report.
- `poses/<original-folder>/<pose-name>.posebusters.csv`: PoseBusters report.

Each original-name folder contains its selected docked poses and their corresponding
reports together. Only runs represented by the selected poses are included.
Report filenames match the SDF filename stem, including `best-RMSD` or
`lowest-energy`, so each labelled pose has its own pair of reports.

The original folders have names such as `A71EV2A-x5747a`. Each selection contains
611 files, for 1,222 labelled files total. Where the two rules select the same
event and run, that pose appears under both descriptive filenames. The manifest
identifies these overlaps. The CSV retains all 611 scored compounds, including
poses with RMSD above 2 angstroms.

There are 1,114 distinct event/run poses and 1,222 labelled reports from each
scoring tool. For the 108 poses selected by both rules, identical report contents
appear under both selection names. The manifest
links every SDF to its two reports and records SHA-256 hashes. Reports retain
the input paths and molecule names from the scoring runs. Package-relative
paths in the manifest locate the corresponding SDFs and reports.

Selections pool the available events and runs for each compound. “Best RMSD”
uses the experimental reference and is a retrospective diagnostic. Lowest-energy
selection here filters invalid poses first, as in the compound analysis; it
differs from the unfiltered event-level Top-1 benchmark selection.

## SDF contents

Each SDF contains one docked pose and one data field, `FittedScore`.
This is FITTED's fitted score, not its energy. Lowest-energy selection uses
FITTED energy; the manifest records both values, OpenStructure RMSD, LDDT-PLI
and PoseBusters validity.

SDF molecule titles match their filenames. Structures include the docked atom
coordinates, atom order, bonds, hydrogens, charge and stereochemical records.

## Reproducing the export

Python 3.10 or newer; standard library only. Supply the original selected-compound
analysis, the full evaluated pose-score CSV, extracted poses and evaluation reports:

```sh
python scripts/export_selected_poses.py --analysis compound_comparison.csv --scores pose_scores.csv --pose-root /path/to/extracted_results/poses --evaluation-root /path/to/extracted_results/evaluation --out /path/to/package
```

Use a dedicated output directory. The exporter verifies pose selections against
the analysis, checks OpenStructure scores and PoseBusters validity, validates
SDF contents and records file hashes. It creates a ZIP alongside the output folder.

## Source and methods

OpenBind benchmark: https://github.com/OpenBind-Consortium/EV-A71_2A_benchmark

OpenBind preprint: https://doi.org/10.64898/2026.08.27.747600

Scoring used OpenStructure 2.11.1 and PoseBusters 0.6.5. RMSD success is
RMSD <= 2 angstroms with PoseBusters validity. The analysis includes rotatable-bond
counts, LDDT-PLI, frozen PCA coordinates and chemical-cluster assignments.

Only the selected docked poses and their reports are distributed here. Receptors,
reference ligands and unselected docking runs are not included. Best-RMSD
selection requires the experimental reference and does not represent prospective
pose ranking. Results describe self-docking, not cross-docking or virtual-screening
performance.
