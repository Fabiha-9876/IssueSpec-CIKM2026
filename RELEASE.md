# Released Data + Model Artifacts

The code in this repository is small (~15 MB). Data and models are released
separately, in two tiers:

- **Verification bundle (10 MB, Zenodo):** everything `verify_paper_results.py`
  needs to reproduce every number in the paper. No GPU, no API keys. See
  *Download → step 1* below.
- **Full re-training set (~30 GB):** raw RRGen data + all V1–V5 model
  checkpoints, for re-running the pipeline from scratch (optional).

## Full re-training set contents (~30 GB total)

### Models (~21 GB)
- `models/stage1_classifier/`     V1 RoBERTa classifier (baseline)
- `models/stage1_classifier_v2/`  V2 LLM-labeled trained
- `models/stage1_classifier_v3/`  V3 with anchor correction
- `models/stage1_classifier_v4/`  V4 anchor + targeted augmentation
- `models/stage1_classifier_v5/`  V5 production classifier (κ = 0.592)
- `models/anchor_roberta/`        Verified-anchor RoBERTa head

### Data (~8.8 GB)
- `data/processed/rrgen_v5_training.json`  215,883-record V5 training set
- `data/processed/verified_annotations.json`  5,230-review verified anchor
- `data/processed/expert_evaluation/`  490-review classification gold standard
- `data/processed/issue_specs/`  100-cluster benchmark + spec outputs
- `data/processed/responses/`  RAG response outputs (rrgen, prompt, no_spec, full)
- `data/processed/experiments/exp2_human_eval.json`  Stage 4 human eval (400 ratings)
- `data/processed/experiments/ablation_a5_results.json`  A5 no-RAG ablation
- `data/processed/ablations/agentic_vs_vanilla_rag.json`  Agentic RAG comparison
- `data/processed/ablations/a1b_*.json`  Count-controlled cluster ablations
- `data/processed/rlhf/head_to_head/`  Stage 5 RLHF 5-policy results
- `data/processed/inter_annotator/`  99-review three-rater Krippendorff α=0.451
- `data/processed/clusters_umap/`  Cluster quality metrics (Table 11)

### Docker image
Pinned-environment Docker image (single-command build on any CUDA 12.x host).

## Download

### 1. Data bundle (verification — 10 MB, no GPU/API needed)

All processed artifacts needed to reproduce every numerical claim are released
on Zenodo:

- **Zenodo DOI:** [10.5281/zenodo.20320410](https://doi.org/10.5281/zenodo.20320410)
  (`issuespec-data-bundle.tar.gz`, ~10 MB)

```bash
# from the repository root
tar -xzf issuespec-data-bundle.tar.gz   # creates ./data/processed/
python3 verify_paper_results.py          # verifies every paper number
```

### 2. Raw RRGen dataset (full pipeline re-run)

The raw 310,031 review-response pairs come from the public RRGen corpus
(Gao et al., *Automating App Review Response Generation*, ASE 2019). Download
the dataset from the original authors and place the CSV at `data/raw/`.

### 3. Model checkpoints (Stage-1 inference)

The V5 production classifier (κ = 0.592) is the only checkpoint needed to
reproduce the Stage-1 results. It is hosted on the Hugging Face Hub; the
anonymized handle is recorded in the Zenodo deposit's metadata to preserve
double-blind review. Verification (step 1) does **not** require any model —
all Stage-1 numbers are recomputed from the saved evaluation files in the
data bundle.

## Re-verifying paper claims (no GPU needed)

Once `data/processed/` is in place (step 1 above), run:
```bash
python3 verify_paper_results.py
```

This re-runs the computation behind every numerical claim in the paper using
only the saved data files — no model inference, no GPU, no API keys.

## Re-training (GPU required)

To retrain V5 from scratch (approximately 6 hours on a single GPU-equipped
workstation):
```bash
docker build -t issuespec .
docker run --gpus all -v $(pwd):/work issuespec \
    bash scripts/full_pipeline.sh
```

See `IMPLEMENTATION_GUIDE.md` for stage-by-stage retraining instructions.
