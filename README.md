# IssueSpec — A Framework for Structured Review-to-Issue Translation

Anonymous code release for CIKM 2026 submission.

IssueSpec is a five-stage pipeline that converts noisy app-store reviews into
typed, developer-routable issue specifications via knowledge-graph clustering,
LLM-based template-filled IR generation, and CMDP-grounded RLHF response alignment.

## Repository layout

```
.
├── paper/                  LaTeX source + figures for the submitted paper
│   ├── IssueSpec/          Final paper (main.tex + figs + acmart class)
│   ├── build_v2/           Figure-generation Python scripts + PNGs
│   └── experiments/        Pre-submission experiment scripts + curation rubric
├── scripts/                Pipeline scripts (Stage 1-5, ablations, scorers)
│   ├── speccov.py          Standalone SpecCov faithfulness scorer
│   ├── compute_3rater_krippendorff.py
│   ├── audit_hierarchical_cluster_purity_llm.py
│   ├── run_rlhf_head_to_head.py
│   └── ... (~80 scripts)
├── src/                    Core library modules (taxonomies, schema, utils)
├── api/                    Optional API wrappers
├── configs/                YAML configuration files
├── notebooks/              Exploratory Jupyter notebooks
├── annotator_materials/    Templates for human-in-the-loop annotation
├── human_work/             Lead-author rating spreadsheets (anonymized)
├── tests/                  Unit tests
├── figures/                Final figures used in the paper
├── verify_paper_results.py Reproduce every numerical claim in the paper
├── verify_paper_results.ipynb  Notebook version
├── ANNOTATION_PROTOCOL.md  Detailed annotation rubric
├── IMPLEMENTATION_GUIDE.md How to run the full pipeline end-to-end
├── Dockerfile              Pinned-environment reproducibility
└── pyproject.toml          Python dependencies
```

## Quick start

```bash
# 1. Install dependencies
pip install -e .

# 2. Download released data + model bundle (see RELEASE.md)
#    Then place under data/processed/ and models/

# 3. Verify all paper numbers
python3 verify_paper_results.py

# Or run individual segments:
python3 verify_paper_results.py 5   # Stage 3 SpecCov scorer only
python3 verify_paper_results.py 9   # Stage 5 RLHF policies only
```

## Reproducing each paper claim

The script `verify_paper_results.py` re-runs the computation behind each headline
result and prints the value alongside the paper claim. Ten segments cover:

| # | Segment | Verifies |
|---|---|---|
| 1 | Corpus + provenance | 215,583 working corpus, 5,230 anchor, 79.49/18.08/2.43% |
| 2 | Stage 1 κ progression | Table 8: V2 0.163, cleanlab 0.333, V5 0.592 |
| 3 | Stage 2 cluster quality | Table 11: DB, CH, 5.4× and 1.9× ratios |
| 4 | A1b ablation | §5.5 count-matched flat-605 vs KG-605 |
| 5 | SpecCov scorer | §4.4 4.16/3.33/5.00/4.00 |
| 6 | Stage 4 human eval | Table 10: 2.31/2.98/2.26/4.62; +2.36 Δ |
| 7 | A5 no-RAG ablation | §5.5 ΔBLEU/ROUGE/BERTScore |
| 8 | Agentic vs vanilla | §5.2 0.58→0.70, 0%→60% citations |
| 9 | Stage 5 RLHF | §5.3 5 policies + +52% BLEU-1 gain |
| 10 | Inter-rater α | Table 4: α=0.451 (99 reviews) |

## Released artifacts (downloaded separately)

To respect repo size limits, the following are released as a separate bundle:

- **V1-V5 classifier checkpoints** + cleanlab correction procedure
- **5,230-review verified anchor** and 490-review classification gold
- **400-row blinded evaluation** and count-controlled A1b output
- **CMDP-RLHF testbed** (KTO, DPO, constrained-proxy, and Lagrangian Constrained PPO trainers)
- **100-cluster benchmark** with 99-review three-rater α=0.451 subsample
- Pinned-environment Docker image (single-command build on any CUDA 12.x host)

See `RELEASE.md` for download instructions (link redacted for anonymous review).

## SpecCov standalone scorer

The `scripts/speccov.py` module is the released SpecCov extractive-coverage
faithfulness scorer. Usage:

```bash
# Pure SpecCov (default)
python3 scripts/speccov.py \
    --specs path/to/specs.json \
    --clusters path/to/clusters.json \
    --out path/to/scores.json

# Paper-reproduction mode (applies per-condition floor from §4.4)
python3 scripts/speccov.py \
    --specs path/to/raw_summary_specs.json \
    --clusters path/to/clusters.json \
    --condition raw_summary \
    --out path/to/scores.json
```

## License

MIT License — see [LICENSE](LICENSE).

## Anonymous review

Author identities and institutional affiliations are withheld in compliance with
the CIKM 2026 double-blind review policy. Personal paths and identifiers have
been replaced with `<HOME>`, `<PROJECT_ROOT>`, and `ANONYMOUS` placeholders
throughout the codebase.
