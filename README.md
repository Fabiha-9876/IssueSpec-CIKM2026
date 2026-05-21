# IssueSpec — Reproduction Data Bundle

Data artifacts for the CIKM 2026 paper *IssueSpec: A Framework for Structured
Review-to-Issue Translation*. Pair this bundle with the code repository to
reproduce every numerical claim in the paper.

## Contents

```
data/processed/
├── verified_annotations.json                 5,230-review verified anchor (lead-author labels)
├── rrgen_v5_training.json                     215,883-record V5 training set (with provenance)
├── issue_specs/
│   ├── sample_100_clusters.json              100-cluster Stage-3 benchmark
│   ├── specs_with_taxonomy.json              LLM-with-taxonomy IssueSpecs
│   ├── specs_free_form.json                  free-form baseline specs
│   ├── specs_raw_summary.json                raw-concatenation lower bound
│   └── specs_human_written.json              lead-author reference specs
├── responses/                                 Stage-4 RAG responses (4 conditions)
├── expert_evaluation/strict_holdout_kappa.json   490-gold κ progression + 307 held-out
├── experiments/
│   ├── exp2_human_eval.json                   Stage-4 human eval (400 ratings, 4 conditions)
│   └── ablation_a5_results.json              A5 no-RAG ablation
├── ablations/
│   ├── agentic_vs_vanilla_rag.json           agentic-RAG feasibility study (n=10)
│   └── a1b_repbased.json                      count-controlled cluster ablation
├── rlhf/head_to_head/metrics.json            Stage-5 5-policy head-to-head
├── clusters_umap/
│   ├── quality_metrics_flat_vs_hierarchical.json   cluster quality (Table 11)
│   └── quality_metrics_summary.txt
└── inter_annotator/agreement_summary.json    99-review 3-rater Krippendorff α
```

## How to use

1. Clone the code repository: `git clone https://github.com/<ANON>/ReviewAgent`
2. Extract this bundle into the repo root so paths resolve as `data/processed/...`
3. Verify every paper number:
   ```bash
   python3 verify_paper_results.py
   ```

No GPU or API keys are needed for verification — every headline number is
recomputed from these saved files.

## Models

Classifier checkpoints (V1–V5 RoBERTa) are released separately on Hugging Face;
see the code repository's `SETUP_GUIDE.md` §4.4.

## License

CC BY 4.0. If you use this data, please cite the CIKM 2026 paper.
