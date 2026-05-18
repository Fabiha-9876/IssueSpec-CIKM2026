# Released Data + Model Artifacts

The code in this repository is small (~8 MB). The following artifacts are too
large for the code repo and are released as a separate bundle:

## Bundle contents (~30 GB total)

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

**Anonymous review link:** `<REDACTED>` (will be filled in for camera-ready)

After download:
```bash
tar -xzf issuespec_release_bundle.tar.gz
mv data/ models/ <PROJECT_ROOT>/
```

## Re-verifying paper claims (no GPU needed)

Once `data/processed/` is in place, run:
```bash
python3 verify_paper_results.py
```

This re-runs the computation behind every numerical claim in the paper using
only the saved data files (no model inference required).

## Re-training (GPU required)

To retrain V5 from scratch (approximately 6 hours on a single GPU-equipped
workstation):
```bash
docker build -t issuespec .
docker run --gpus all -v $(pwd):/work issuespec \
    bash scripts/full_pipeline.sh
```

See `IMPLEMENTATION_GUIDE.md` for stage-by-stage retraining instructions.
