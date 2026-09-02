# Perfume Representation Bridge

Code and aggregate results supporting **From fragrance families
to continuous odor representations: multimodal learning and bounded
composition reconstruction for real-world perfume mixtures**.

## 1. Research purpose

This repository implements a three-stage workflow:

- **Stage I:** fragrance-family classification from sample-level GC-MS features.
- **Stage II:** continuous odor-profile prediction from GC-MS and text-derived representations.
- **Stage III:** molecular-composition retrieval or reconstruction and evaluation.

The software is intended for non-commercial academic research and method
inspection, not commercial model training, automated formulation, or commercial
data products.

## 2. Repository structure

```text
perfume-representation-bridge/
|-- README.md
|-- LICENSE
|-- DATA_NOTICE.md
|-- CITATION.cff
|-- environment.yml
|-- requirements.txt
|-- .zenodo.json
|-- docs/
|-- configs/
|-- code/
|   |-- README.md
|   |-- gcms_process/
|   |-- stage1/
|   |-- stage2/
|   |-- stage3/
|   |-- evaluation/
|   |-- figures/
|   |-- release/
|   `-- vendor/
|-- data/
|-- results/
|-- supplementary_data/
`-- reproduce.sh
```

The public release contains code, permitted GC-MS and molecular features,
public descriptor mappings, aggregate statistical outputs, and publication
figures. It does **not** contain restricted NoseTime records or reversible
sample-level derivatives.

To keep the GitHub software repository manageable, large multilevel and
molecularly enhanced feature directories are excluded from Git and supplied in
the companion Zenodo dataset record. Small GC-MS inputs needed to inspect the
core preprocessing remain under `data/`.

Manuscript and journal-template source files are maintained separately from
this software repository. The article title and preferred citation remain in
`CITATION.cff`.

The large `zenodo_archive/` working directory is intentionally excluded from
Git. Only its upload-ready dataset ZIP and checksum are deposited separately on
Zenodo. See `docs/PUBLIC_RELEASE.md`.

## 3. Installation

Python 3.11 is recommended.

```bash
conda env create -f environment.yml
conda activate perfume-representation-bridge
```

Alternatively:

```bash
python -m venv .venv
# Linux/macOS: source .venv/bin/activate
# Windows PowerShell: .venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

PyTorch and PyTorch Geometric must match the local CUDA driver. CPU builds can
also be used. Text encoding uses `BAAI/bge-large-en-v1.5`.

## 4. Running Stage I-III

Full retraining requires a separately authorized local copy of the restricted
NoseTime inputs. Place or link the two ignored mapping files at the documented
local paths and keep review text outside this repository:

```bash
export PERFUME_COMMENT_DIR=/authorized/local/nosetime/reviews_en
ln -s /authorized/local/nosetime/perfume_fragrance_codes.xlsx data/mappings/perfume_fragrance_codes.xlsx
ln -s /authorized/local/nosetime/odor_features_matrix_en.xlsx data/mappings/odor_features_matrix20260322_en.xlsx
bash reproduce.sh
```

Windows PowerShell:

```powershell
$env:PERFUME_COMMENT_DIR = "D:\authorized\nosetime\reviews_en"
# Copy or create local links to the two ignored files under data\mappings\,
# then run the pipeline from Git Bash or WSL.
```

Stage commands, after authorized inputs have been configured:

```bash
# Stage I
python code/stage1/stage1_oof.py
python code/stage1/stage1_multilevel.py --mode fragrance --force

# Stage II
python code/stage2/stage2_outer_fold.py --force --gnn-epochs 40 --deepsets-epochs 40
python code/stage2/stage2_204d_metrics.py
python code/evaluation/evaluate_predictions_vs_odor_matrix.py --test-only

# Stage III
python code/stage3/stage3_nested_cv.py --methods method3_retrieval,e2e,p3_e2e,p3_cvae --retrain-textodor --fold-bge-embeddings --force
python code/stage3/stage3_nested_cv_eval.py
python code/stage3/stage3_paired_tests.py
python code/stage3/stage3_p3_ablation.py --force
```

These commands document authorized reproduction. They do not imply that
third-party inputs are publicly downloadable from this repository.

## 5. Reproducing tables and figures

Aggregate manuscript tables are stored under `supplementary_data/tables/` and
publication figures under `results/figures/`.

```bash
python code/evaluation/build_reported_numbers.py
python code/figures/generate_main_figures_fig04_to_fig09.py
python code/figures/generate_main_figures_fig_s01_to_s06.py
```

Some generators require restricted local inputs or sample-level intermediate
outputs and therefore run only in the authorized environment. Public aggregate
tables and figures support inspection without redistributing restricted records.

## 6. NoseTime data restrictions

Perfume-level fragrance-family labels, odor descriptors, odor descriptions,
and review-derived text used in this study were obtained from **香水时代
NoseTime (https://www.nosetime.com)** under written permission for
non-commercial academic research by the authorized research team.

These third-party materials are subject to access, redistribution, privacy,
purpose, and retention restrictions. They are not distributed publicly and may
not be incorporated into a public dataset or text corpus. The public release
excludes:

- perfume names and NoseTime page links;
- perfume-level NoseTime fragrance-family and odor-label matrices;
- user names, user identifiers, profiles, and reviewer metadata;
- original comments and translated comments;
- comment-to-user relationships and reversible comment derivatives;
- review embeddings and restricted sample-level ground truth or predictions.

Access is controlled by NoseTime, not by the repository authors. Requests must
be directed to NoseTime and remain subject to its independent approval. See
`DATA_NOTICE.md` and `data/README.md`.

Pyrfume resources are reused public datasets governed by the licences and
attribution requirements supplied by their original sources.

## 7. Public-release audit

Before creating a GitHub tag or Zenodo software release, run:

```bash
python code/release/audit_public_release.py
python code/release/build_release_manifest.py
```

The first command checks required metadata, restricted-input boundaries,
absolute local paths and GitHub's per-file size limit. The second writes
`PUBLIC_RELEASE_MANIFEST.csv` and `PUBLIC_RELEASE_SUMMARY.json`, including
SHA-256 checksums for the intended public software release. Restricted or
generated paths may remain in an authorized working tree, but they must remain
ignored and absent from the public Git history.

GitHub/Zenodo software-release instructions and the separate dataset-record
procedure are documented in `docs/PUBLIC_RELEASE.md`.

## 8. License and citation

The MIT License applies only to original repository software code. It does not
apply to NoseTime materials, Pyrfume datasets, pretrained models, or other
third-party content. See `LICENSE` and `DATA_NOTICE.md`.

If you use this software, cite `CITATION.cff` and the associated manuscript:

> Yufei Fan and Weijia Wen. *From fragrance families to continuous odor
> representations: multimodal learning and bounded composition reconstruction
> for real-world perfume mixtures* (2026).

Update the DOI, journal citation, version, and archived repository identifier
after publication or Zenodo release.

## 9. Availability of data and materials

Restricted NoseTime inputs cannot be deposited publicly or redistributed by
the authors. Publicly shareable code, permitted processed GC-MS resources,
aggregate statistical outputs, and publication figures are provided here.
Access to NoseTime data must be requested directly from the provider.

## 10. Code availability

Analysis, modelling, evaluation, and figure-generation code is available in
this repository. Full retraining requires separately authorized third-party
inputs that are not bundled with the public code release.
