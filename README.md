<p align="center">
  <img src="assets/logo.png" width="420" alt="Camyla">
</p>

<p align="center">
  [ English | <a href="README_zh.md">中文</a> ]
</p>

## Large-scale, fully autonomous medical image segmentation research — dataset in, paper out.

---

## 📄 Paper

### Camyla: Scaling Autonomous Research in Medical Image Segmentation

Yifan Gao¹², Haoyue Li¹, Feng Yuan¹, Xin Gao¹\*, Weiran Huang²³\*, Xiaosong Wang²⁴\*

<sup>¹ USTC · ² Shanghai Innovation Institute · ³ SJTU · ⁴ Shanghai AI Lab · \* Corresponding authors</sup>

📑 **[Read the paper (PDF)](assets/camyla.pdf)** · **[alphaXiv](https://www.alphaxiv.org/abs/2604.10696)** · **[arXiv](https://arxiv.org/abs/2604.10696)**

<p align="center">
  <img src="assets/hero.png" width="760" alt="Camyla hero">
</p>

**Headline results** (28 days, **zero human intervention**):

- **40** complete manuscripts written end-to-end
- **Cost-efficient**: only **$20–30 per paper** in LLM API spend
- **Beats the strongest per-dataset baseline** (chosen from 14 established architectures including nnU-Net) on **24 of 31** datasets under identical training budgets
- **CamylaBench**: contamination-free benchmark of 31 datasets, built exclusively from 2025 publications
- **Stronger long-horizon orchestration on the experiment stage.** During experiment execution, driven by cost-efficient backends (**GLM-4.7** + **MiniMax-M2.5**), Camyla outperforms **AI Scientist**, **autoresearch Claude Code (Opus 4.6)**, and **autoresearch Codex (GPT-5.4-xhigh)** on execution success, completion rate, and fidelity to the original proposal

**Manuscript quality — double-blind evaluation.** Camyla-generated manuscripts were mixed with real 2025 publications and judged without reviewers knowing which was AI-written. Four independent panels — **5 senior reviewers**, **10 junior reviewers**, **5 different AI models**, and the **Stanford Agentic Reviewer** — all place Camyla's output between the T1 and T2 tier of contemporary medical-imaging journals. The T1 anchor is IEEE TMI / Medical Image Analysis; the T2 band is JCR Q1 medical-imaging journals:

| Tier | Journals | Papers | Representative venues |
|------|:-------:|:------:|-----------------------|
| **T1** (top-tier) | 2 | 10 | IEEE Transactions on Medical Imaging; Medical Image Analysis |
| **T2** (JCR Q1) | 7 | 35 | IEEE Journal of Biomedical and Health Informatics; Artificial Intelligence in Medicine; et al. |
| **T3** | 9 | 45 | International Journal of Computer Assisted Radiology and Surgery; Biomedical Physics & Engineering Express; et al. |
| **Total** | 18 | 90 | |

---

## 📚 Sample generated papers

A 10-paper subset of the manuscripts Camyla produced end-to-end — no hand-editing,
LaTeX compiled as-is by the pipeline. Each PDF lives under
[`assets/paper_pdf/`](assets/paper_pdf/).

| # | Title | Modality / task |
|:-:|-------|-----------------|
| 1 | [CDFL: Cross-Directional Feature Lattice for Brain Tumor Segmentation](assets/paper_pdf/01.pdf) | MRI · brain tumor |
| 2 | [SFA-Net: Scale-Frequency Adaptive Fusion for Multiple Sclerosis Lesion Segmentation](assets/paper_pdf/02.pdf) | MRI · MS lesions |
| 3 | [HCF-Net: Hierarchical Context Gating for Neonatal Brain Lesion Segmentation](assets/paper_pdf/03.pdf) | MRI · neonatal HIE |
| 4 | [CSM-Net: Cross-Scale Mutual Refinement for Bronchoalveolar Lavage Fluid Cell Segmentation](assets/paper_pdf/04.pdf) | Microscopy · BALF cells |
| 5 | [SAC-Net: Symmetry-Aware Cascaded Attention for Panoramic Tooth Segmentation](assets/paper_pdf/05.pdf) | Dental X-ray · tooth |
| 6 | [SRD-Net: Specular-Residual Decoupled Encoding for Surgical Scene Segmentation](assets/paper_pdf/06.pdf) | Laparoscopy · surgical scene |
| 7 | [ScaleGate-Net: Adaptive Scale-Aware Feature Integration for Liver Lesion Segmentation](assets/paper_pdf/07.pdf) | CT · liver lesion |
| 8 | [BHD-Net: Boundary-Hierarchical Decomposition for Fetal Brain Tissue Segmentation](assets/paper_pdf/08.pdf) | MRI · fetal brain |
| 9 | [VBR-Net: Vessel-Guided Boundary Residual Networks for Dermatological Vessel Segmentation](assets/paper_pdf/09.pdf) | OCTA · dermatological vessel |
| 10 | [HRFuse-Net: Hierarchical Resolution-Retentive Feature Encoding for Brain Metastasis Segmentation](assets/paper_pdf/10.pdf) | MRI · brain metastasis |

---

## 📰 News

- **2026-04-13** — Initial public release of the code, the paper, and **CamylaBench** (31 pre-formatted datasets, [📦 Google Drive](https://drive.google.com/drive/folders/11BrGWWZw8yr2o2AkVV9o5e7de-T9nngz?usp=sharing)). We are still cleaning the full **CamylaTrace-232K** trajectory dataset (per-run logs + intermediate artifacts); release will follow shortly.

---

Camyla is an automated research pipeline that takes a medical image segmentation task,
searches the literature for relevant ideas, proposes research hypotheses, runs
end-to-end deep-learning experiments (Baseline → Creative Research → Ablation), and
writes up the results as a publication-ready paper.

It combines:

- **Quality-Weighted Branch Exploration (QWBE)** over experiment configurations
- **OpenHands**-driven code generation and iterative debugging
- Multi-source literature search (ArXiv, OpenAlex, PubMed, Semantic Scholar)
- A paper agent that drafts, compiles, and cites LaTeX papers (Elsevier format supported)
- A flexible LLM routing layer (`llm_endpoints` + `llm_roles`) so you can mix providers
  (OpenRouter, GLM, MiniMax, …) without touching code

> **Status.** Research prototype, preparing for open-source release. APIs may change.

---

## Quick start

### 1. Install

```bash
git clone https://github.com/yifangao112/Camyla.git camyla
cd camyla
python -m venv .venv && source .venv/bin/activate   # or: conda create -n camyla python=3.10
pip install -r requirements.txt

# Sister packages (segmentation framework + raw-data conversion agent)
pip install git+https://github.com/yifangao112/CamylaNet.git
pip install git+https://github.com/yifangao112/nnPrep.git
```

> A Python 3.10+ environment is expected. GPU is required for actually running
> the segmentation experiments (baseline uses CamylaNet / nnU-Net v2).

### 2. Configure

Set CamylaNet's data-path environment variables (same convention as CamylaNet's
own README — the baseline pipeline reads and writes under these paths):

```bash
export camylanet_raw="/path/to/camylanet_raw"
export camylanet_preprocessed="/path/to/camylanet_preprocessed"
export camylanet_results="/path/to/camylanet_results"   # baseline artifacts land here
```

Then copy the example Camyla config and edit it:

```bash
cp config_example.yaml config.yaml
```

At minimum, set:

- At least one entry under `llm_endpoints` with a valid `api_key` (or export the
  matching `api_key_env`, e.g. `OPENROUTER_API_KEY`)
- `default_endpoint` — the endpoint name most roles will use

See [Configuration](#configuration) below for the full layout, role-routing
semantics, and N-way model competition. The shipped
[config_example.yaml](config_example.yaml) already has a complete multi-provider
setup you can copy from.

### 3. Prepare the dataset

Camyla operates on a dataset that already lives in nnU-Net v2 layout under
`$camylanet_raw/Dataset{ID}_{Abbr}/`. You have three options:

- **Use CamylaBench (recommended).** The 31 datasets used in the paper are
  pre-formatted and ready to drop in — download from
  [📦 Google Drive](https://drive.google.com/drive/folders/11BrGWWZw8yr2o2AkVV9o5e7de-T9nngz?usp=sharing)
  and extract each `Dataset{ID}_{Abbr}/` folder under `$camylanet_raw/`.
  The ready-made idea descriptions in [`ideas/`](ideas/) match these IDs.
- **Bring your own, already in nnU-Net v2 format.** Just point `camylanet_raw` at it.
- **Bring your own raw data in some arbitrary layout.** Use
  [nnPrep](https://github.com/yifangao112/nnPrep) (an LLM agent that converts
  arbitrary medical segmentation datasets into the nnU-Net v2 format) — or
  write your own conversion script.

### 4. Run

Pick an idea from [`ideas/`](ideas/) (31 ready-to-use dataset descriptions) and launch:

```bash
python launch_camyla.py \
    --config config.yaml \
    --load_ideas ideas/900.json \
    --idea_idx 0
```

On first run for a given dataset, Camyla automatically runs the baseline pipeline
(trainer screening + full training for everything that passes) under
`$camylanet_results`. Subsequent runs reuse the artifacts and skip this step.

The pipeline writes everything to `experiments/<date>_<idea>_attempt_<id>/`:

```
experiments/2026-04-12_liver_segmentation_attempt_0/
├── idea.json / idea.md           # task spec
├── config.yaml                   # resolved config used for this run
├── logs/0-run/
│   ├── experiment_report.md      # human-readable summary
│   └── experiment_results/       # metrics, checkpoints, plots
├── research_proposals/           # auto-generated proposals
└── paper/                        # LaTeX + compiled PDF (if writeup enabled)
```

Common flags:

| Flag | Purpose |
|------|---------|
| `--resume_from_checkpoint PATH` | Resume from a previous `checkpoint.pkl` |
| `--skip_writeup` / `--skip_review` | Run experiments only, skip paper generation |
| `--debug-baseline` | Fake the baseline metrics so Stage 2 runs immediately (dev only) |
| `--verbose` | DEBUG-level logging |

---

## How it works

<p align="center">
  <img src="assets/camyla-overview.png" width="820" alt="Camyla system overview">
</p>

- **Phase 1-3 (idea generation).** Searches 1-4 literature sources, extracts open
  research challenges, and generates multiple proposals scored by an assessment LLM.
- **Stage 1-3 (experiment).** QWBE expands a tree of code variants. Stage 2 can run
  **N-way model competition** by listing multiple `experiment.code.candidates`.
- **Paper Agent.** Takes the final experiment results and produces a cited paper.

---

## Configuration

Every LLM call Camyla makes resolves through a two-layer system:

1. **`llm_endpoints`** — your named LLM connections (one per provider/backend).
2. **`llm_roles`** — every internal component ("role") picks an endpoint and
   optionally overrides its model, temperature, or `max_tokens`.

Anything you don't configure at the role level falls back to `default_endpoint`.

### 1. `llm_endpoints` — connections

Each entry is an OpenAI-compatible endpoint. The shape is fixed:

```yaml
llm_endpoints:
  my_openrouter:
    api_key: ""                              # inline key (leave empty → use env)
    api_key_env: OPENROUTER_API_KEY          # env var name to read when api_key is empty
    base_url: "https://openrouter.ai/api/v1"
    model: "deepseek/deepseek-v3.2"          # default model for this endpoint
    temperature: 0.5                         # default temperature
```

**Key resolution order:** non-empty `api_key` > environment variable named by
`api_key_env` > empty (error at first call).

**Naming is free-form.** `my_openrouter`, `cheap_backend`, `gpt4` — all valid.
Roles reference endpoints by name, so changing models across the whole pipeline
is just swapping one string. Common backends we've tested:

| Backend          | `base_url`                                     | Notes |
|------------------|-----------------------------------------------|-------|
| OpenRouter       | `https://openrouter.ai/api/v1`                | one key, 300+ models |
| DashScope (Qwen) | `https://coding.dashscope.aliyuncs.com/v1`    | cheap Qwen/GLM routing |
| MiniMax          | `https://api.minimaxi.com/v1`                 | M-series models |
| OpenAI           | `https://api.openai.com/v1`                   | native |
| Local vLLM / Ollama | `http://localhost:8000/v1`                  | any OpenAI-compatible server |

### 2. `default_endpoint`

The global fallback. Every role without an explicit `endpoint` uses this one.

```yaml
default_endpoint: my_openrouter
```

### 3. `llm_roles` — per-role overrides

A role is one logical LLM use inside the pipeline (feedback, paper writer, idea
generator, etc.). You only specify the fields you want to override:

```yaml
llm_roles:
  # Tree-search roles
  feedback:           { temperature: 0.9, max_tokens: 8192 }
  log_summary:        { temperature: 1.0 }

  # Idea-generation roles — swap to a cheaper model for these
  literature_backbone:  { model: google/gemini-3-flash-preview }
  challenge_extraction: { temperature: 0.3 }

  # Paper agent sub-agents: `_default` applies to the whole group unless
  # an individual sub-agent overrides it.
  paper_agent:
    _default:             { temperature: 0.6 }
    BibtexAgent:          { model: z-ai/glm-4.7, temperature: 0.3 }
    IdeaGenerationAgent:  { model: google/gemini-3-flash-preview, temperature: 0.8 }

  # Paper writing roles can be routed to a different endpoint entirely.
  paper_writing:
    latex_editor:    { endpoint: my_dashscope, temperature: 0.7 }
    image_generator: { endpoint: my_openrouter,
                       model: google/gemini-3.1-flash-image-preview,
                       aspect_ratio: "16:9", image_size: "2K" }
```

Override precedence for any given role: role fields > its endpoint's defaults.
You can point a role at a completely different endpoint with `endpoint: <name>`.

### 4. N-way model competition (Stage 2)

Under `experiment.code.candidates`, list the **endpoint names** that should compete
as code authors for Stage 2. Camyla will run one branch per candidate and keep
the strongest.

```yaml
experiment:
  code:
    candidates: [my_dashscope, my_minimax]
    max_tokens: 16384
```

Set it to a single-element list to disable competition.

### 5. Non-LLM API keys

Literature search keys live under `api_keys` (same inline/env fallback pattern):

```yaml
api_keys:
  s2:   { value: "", env: S2_API_KEY }    # Semantic Scholar
  ncbi: { value: "", env: NCBI_API_KEY }  # PubMed
```

Camyla works without these, but rate limits will be tighter.

### 6. Tuning the run

The rest of the config controls **what** Camyla does, not **which LLMs** it uses:

- `idea_generation.*` — how many papers to search, how to score proposals,
  how many generator personalities to ensemble
- `experiment.stages.*` — per-stage iteration budgets (Stage 1 = baseline
  replication, Stage 2 = creative research, Stage 3 = ablation)
- `experiment.openhands.*` — OpenHands coder settings (python path, iteration
  cap, condenser)
- `experiment.search.*` — QWBE hyperparameters (UCB constant, debug probability,
  draft count)

All of these have sensible defaults in [config_example.yaml](config_example.yaml);
you usually only touch `stages.*_max_iters` when you want a faster / cheaper run.

---

## Repository layout

```
camyla/
├── LICENSE
├── README.md                        # this file
├── config_example.yaml              # documented config template
├── requirements.txt
├── launch_camyla.py                 # main entry point
├── ideas/                           # 31 ready-made idea JSONs
└── camyla/                          # core package
    ├── model_config.py              # LLM config loader (get_endpoint/get_role)
    ├── baseline/                    # screening + full training before QWBE
    ├── infrastructure/literature/   # arxiv / openalex / pubmed / multi-source
    ├── paper_agent/                 # LaTeX writer + plotters + bibtex agent
    ├── tools/                       # OpenAlex / Semantic Scholar tools
    ├── treesearch/                  # QWBE core, parallel agents, OpenHands coder
    └── utils/
```

---

## Related repositories

- [CamylaNet](https://github.com/yifangao112/CamylaNet) — segmentation framework
  built on nnU-Net v2, shipping a curated set of CNN / Transformer / state-space
  backbones. Camyla's baseline stage runs CamylaNet trainers.
- [nnPrep](https://github.com/yifangao112/nnPrep) — LLM agent that converts
  arbitrary medical segmentation datasets into the nnU-Net v2 format consumed
  by CamylaNet (and therefore by Camyla).

---

## Limitations & notes

- The baseline stage currently uses CamylaNet trainers. Swapping in another
  baseline framework requires a corresponding skill under `skills/frameworks/`.
- OpenHands runs code in your local Python env — make sure you point
  `experiment.openhands.python_path` at an env with the needed packages.
- Long runs: a full idea (proposals → 3 stages → paper) typically takes
  several hours to a day on a single A100-class GPU.

---

## Citation

If you find this project useful in academic work, please cite:

```bibtex
@misc{gao2026camyla,
  title         = {Camyla: Scaling Autonomous Research in Medical Image Segmentation},
  author        = {Gao, Yifan and Li, Haoyue and Yuan, Feng and Gao, Xin and Huang, Weiran and Wang, Xiaosong},
  year          = {2026},
  eprint        = {2604.10696},
  archivePrefix = {arXiv},
  primaryClass  = {cs.AI}
}
```

---

## Acknowledgements

Camyla builds on ideas and code from two upstream projects:

- [**AI Scientist** (Sakana AI)](https://github.com/SakanaAI/AI-Scientist) — pioneered the autonomous research-agent paradigm that inspired Camyla's overall pipeline design.
- [**nnU-Net**](https://github.com/MIC-DKFZ/nnUNet) — the self-configuring segmentation framework that Camyla's baseline stage (via [CamylaNet](https://github.com/yifangao112/CamylaNet)) is built on.

---

## License

Released under the **Apache License, Version 2.0** — see [LICENSE](LICENSE).
