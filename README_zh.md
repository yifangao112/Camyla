<p align="center">
  <img src="assets/logo.png" width="420" alt="Camyla">
</p>

<p align="center">
  [ <a href="README.md">English</a> | 中文 ]
</p>

## 面向医学图像分割的大规模、全自动科研系统 —— 输入数据集，输出论文。

---

## 📄 论文

### Camyla: Scaling Autonomous Research in Medical Image Segmentation

Yifan Gao¹², Haoyue Li¹, Feng Yuan¹, Xin Gao¹\*, Weiran Huang²³\*, Xiaosong Wang²⁴\*

<sup>¹ 中国科学技术大学 · ² 上海创智学院 · ³ 上海交通大学 · ⁴ 上海人工智能实验室 · \* 通讯作者</sup>

📑 **[阅读论文 (PDF)](assets/camyla.pdf)** · **[alphaXiv](https://www.alphaxiv.org/abs/2604.10696)** · **[arXiv](https://arxiv.org/abs/2604.10696)**

<p align="center">
  <img src="assets/hero.png" width="760" alt="Camyla hero">
</p>

**核心实验结果**（28 天，**全程零人工干预**）：

- 端到端产出 **40** 篇完整论文手稿
- **成本极低**：每篇论文在 LLM API 上的花费仅 **$20–30**
- 在 **31 个数据集中的 24 个** 上，在相同训练预算下**超越了 14 种主流架构（含 nnU-Net）中最强的逐数据集基线**
- **CamylaBench**：基于 2025 年新发表数据构建的无污染基准，共 31 个数据集
- **实验阶段更强的 long-horizon 编排**：在实验执行阶段，使用低成本后端（**GLM-4.7** + **MiniMax-M2.5**）的 Camyla 在执行成功率、任务完成率以及对初始 proposal 的一致性（不偏移）上，均超过 **AI Scientist**、基于 **Claude Code (Opus 4.6)** 的 autoresearch、以及基于 **Codex (GPT-5.4-xhigh)** 的 autoresearch

**手稿质量评估（双盲对比）**：将 Camyla 生成的手稿与 2025 年真实发表的论文混合后进行**双盲**评审——审稿人不知道哪一篇是 AI 生成的。四组独立评审（**5 位资深审稿人**、**10 位初级审稿人**、**5 个不同型号的 AI 模型**、以及 **Stanford Agentic Reviewer**）一致认为 Camyla 生成手稿的质量落在医学影像期刊的 **T1–T2 之间**。其中 T1（顶级期刊）以 IEEE TMI 和 Medical Image Analysis 为锚，T2（JCR Q1 期刊）为中科院 / JCR 一区的医学影像期刊：

| 档位 | 期刊数 | 论文数 | 代表期刊 |
|------|:-----:|:-----:|---------|
| **T1**（顶级期刊） | 2 | 10 | IEEE Transactions on Medical Imaging; Medical Image Analysis |
| **T2**（JCR Q1） | 7 | 35 | IEEE Journal of Biomedical and Health Informatics; Artificial Intelligence in Medicine 等 |
| **T3** | 9 | 45 | International Journal of Computer Assisted Radiology and Surgery; Biomedical Physics & Engineering Express 等 |
| **合计** | 18 | 90 | |

---

## 📰 最新动态

- **2026-04-13** —— 首次公开发布代码、论文，以及 **CamylaBench**（31 个已预处理数据集，[📦 Google Drive](https://drive.google.com/drive/folders/11BrGWWZw8yr2o2AkVV9o5e7de-T9nngz?usp=sharing)）。完整的 **CamylaTrace-232K** 轨迹数据集（包含逐次运行日志与中间产物）仍在整理中，随后即将发布。

---

Camyla 是一条自动化科研流水线：输入一个医学图像分割任务，它会自动检索文献、提出研究假设、执行端到端深度学习实验（Baseline → Creative Research → Ablation），并把结果写成一篇可投稿的论文。

它由以下几块组成：

- 基于 **Quality-Weighted Branch Exploration (QWBE)** 的实验配置搜索
- 由 **OpenHands** 驱动的代码生成与迭代调试
- 多源文献搜索（ArXiv / OpenAlex / PubMed / Semantic Scholar）
- 一个 Paper Agent，负责起草、引用、编译 LaTeX 论文（支持 Elsevier 模板）
- 灵活的 LLM 路由层（`llm_endpoints` + `llm_roles`），无需改代码就能混用多家厂商（OpenRouter、GLM、MiniMax 等）

> **状态**：研究原型，正在准备开源发布，API 可能变动。

---

## 快速开始

### 1. 安装

```bash
git clone https://github.com/yifangao112/Camyla.git camyla
cd camyla
python -m venv .venv && source .venv/bin/activate   # 或使用 conda：conda create -n camyla python=3.10
pip install -r requirements.txt

# 姐妹项目（分割框架 + 原始数据转换 agent）
pip install git+https://github.com/yifangao112/CamylaNet.git
pip install git+https://github.com/yifangao112/nnPrep.git
```

> 需要 Python 3.10+。真正跑分割实验需要 GPU（baseline 用到 CamylaNet / nnU-Net v2）。

### 2. 配置

设置 CamylaNet 的数据路径环境变量（与 CamylaNet 自己的 README 保持同一约定 ——
baseline 流水线会在这几条路径下读写）：

```bash
export camylanet_raw="/path/to/camylanet_raw"
export camylanet_preprocessed="/path/to/camylanet_preprocessed"
export camylanet_results="/path/to/camylanet_results"   # baseline 产物写入这里
```

然后复制示例配置并编辑：

```bash
cp config_example.yaml config.yaml
```

最少需要设置：

- `llm_endpoints` 下至少一条带有有效 `api_key` 的条目（或导出对应的 `api_key_env`，例如 `OPENROUTER_API_KEY`）
- `default_endpoint` —— 大多数 role 默认走的端点名称

完整的字段说明、角色路由语义和 N 路模型竞赛见下文 [配置详解](#配置详解)。
仓库里的 [config_example.yaml](config_example.yaml) 已经包含了一份多厂商参考配置，直接抄即可。

### 3. 准备数据集

Camyla 要求数据集已经是 nnU-Net v2 的布局，位于 `$camylanet_raw/Dataset{ID}_{Abbr}/`。有三种获得方式：

- **使用 CamylaBench（推荐）**：论文里用到的 31 个数据集已经预处理好，开箱即用 ——
  从 [📦 Google Drive](https://drive.google.com/drive/folders/11BrGWWZw8yr2o2AkVV9o5e7de-T9nngz?usp=sharing) 下载，
  把每个 `Dataset{ID}_{Abbr}/` 文件夹解压到 `$camylanet_raw/` 下即可。
  [`ideas/`](ideas/) 里现成的 idea 描述与这些 ID 一一对应。
- **自带 nnU-Net v2 格式数据集**：直接把 `camylanet_raw` 指过去。
- **自带任意格式的原始数据**：用 [nnPrep](https://github.com/yifangao112/nnPrep)
  （一个把任意医学分割数据集转成 nnU-Net v2 格式的 LLM agent）—— 或者自己写个转换脚本。

### 4. 运行

从 [`ideas/`](ideas/) 中挑一个 idea（已包含 31 份现成的数据集描述）启动：

```bash
python launch_camyla.py \
    --config config.yaml \
    --load_ideas ideas/900.json \
    --idea_idx 0
```

对任意数据集的首次运行，Camyla 会自动跑 baseline 流水线（trainer 筛选 + 对通过筛选的 trainer 做完整训练），
产物落在 `$camylanet_results` 下。后续运行会复用这些产物，跳过这一步。

整个流水线的输出都写到 `experiments/<date>_<idea>_attempt_<id>/`：

```
experiments/2026-04-12_liver_segmentation_attempt_0/
├── idea.json / idea.md           # 任务描述
├── config.yaml                   # 本次运行实际生效的配置
├── logs/0-run/
│   ├── experiment_report.md      # 人类可读的总结
│   └── experiment_results/       # 指标、checkpoint、图
├── research_proposals/           # 自动生成的 proposal
└── paper/                        # LaTeX + 编译好的 PDF（若启用写作）
```

常用参数：

| 参数 | 用途 |
|------|---------|
| `--resume_from_checkpoint PATH` | 从上次的 `checkpoint.pkl` 恢复 |
| `--skip_writeup` / `--skip_review` | 只跑实验，不做论文生成 |
| `--debug-baseline` | 伪造 baseline 指标，让 Stage 2 立刻起跑（仅限开发调试） |
| `--verbose` | 打开 DEBUG 级别日志 |

---

## 工作原理

<p align="center">
  <img src="assets/camyla-overview.png" width="820" alt="Camyla system overview">
</p>

- **Phase 1-3（idea 生成）**：检索 1-4 个文献源，抽取开放研究 challenge，生成多份 proposal 并由评估 LLM 打分。
- **Stage 1-3（实验）**：QWBE 在代码变体树上展开搜索。在 Stage 2 下通过列出多个 `experiment.code.candidates` 可启用 **N 路模型竞赛**。
- **Paper Agent**：接收最终实验结果，生成带引用的论文。

---

## 配置详解

Camyla 的每一次 LLM 调用都走一套两层系统：

1. **`llm_endpoints`** —— 你自己命名的 LLM 连接（每个 provider / backend 一条）。
2. **`llm_roles`** —— 每个内部组件（"role"）选一个 endpoint，并可覆盖其 model、temperature 或 `max_tokens`。

你不显式配置的 role 一律回落到 `default_endpoint`。

### 1. `llm_endpoints` —— 连接定义

每一条都是 OpenAI 兼容端点，结构固定：

```yaml
llm_endpoints:
  my_openrouter:
    api_key: ""                              # 内联 key（留空则读环境变量）
    api_key_env: OPENROUTER_API_KEY          # 当 api_key 为空时读取的环境变量名
    base_url: "https://openrouter.ai/api/v1"
    model: "deepseek/deepseek-v3.2"          # 该端点默认模型
    temperature: 0.5                         # 默认 temperature
```

**Key 解析优先级**：非空 `api_key` > `api_key_env` 所指向的环境变量 > 为空（首次调用报错）。

**名称自由命名**：`my_openrouter`、`cheap_backend`、`gpt4` 都合法。
Role 按名称引用 endpoint，所以全流水线切换模型只需改一个字符串。我们测试过的常见后端：

| 后端              | `base_url`                                     | 备注 |
|------------------|-----------------------------------------------|-------|
| OpenRouter       | `https://openrouter.ai/api/v1`                | 一把 key 覆盖 300+ 模型 |
| DashScope（Qwen）| `https://coding.dashscope.aliyuncs.com/v1`    | 便宜的 Qwen / GLM 路由 |
| MiniMax          | `https://api.minimaxi.com/v1`                 | M 系列模型 |
| OpenAI           | `https://api.openai.com/v1`                   | 原生 |
| 本地 vLLM / Ollama | `http://localhost:8000/v1`                  | 任何 OpenAI 兼容服务器 |

### 2. `default_endpoint`

全局兜底端点。没有显式指定 `endpoint` 的 role 都走这里。

```yaml
default_endpoint: my_openrouter
```

### 3. `llm_roles` —— 按 role 覆盖

一个 role 就是流水线里的一个 LLM 用途（feedback、paper writer、idea generator 等）。
只需要填写你想覆盖的字段：

```yaml
llm_roles:
  # Tree-search 相关 role
  feedback:           { temperature: 0.9, max_tokens: 8192 }
  log_summary:        { temperature: 1.0 }

  # Idea 生成 role —— 这里换成更便宜的模型
  literature_backbone:  { model: google/gemini-3-flash-preview }
  challenge_extraction: { temperature: 0.3 }

  # Paper agent 子 agent：`_default` 覆盖整组，除非某个子 agent 自己覆盖
  paper_agent:
    _default:             { temperature: 0.6 }
    BibtexAgent:          { model: z-ai/glm-4.7, temperature: 0.3 }
    IdeaGenerationAgent:  { model: google/gemini-3-flash-preview, temperature: 0.8 }

  # 论文写作 role 可以整个切到另一个 endpoint
  paper_writing:
    latex_editor:    { endpoint: my_dashscope, temperature: 0.7 }
    image_generator: { endpoint: my_openrouter,
                       model: google/gemini-3.1-flash-image-preview,
                       aspect_ratio: "16:9", image_size: "2K" }
```

单个 role 的覆盖优先级：role 字段 > 所属 endpoint 的默认值。
通过 `endpoint: <name>` 可以把 role 指向完全不同的 endpoint。

### 4. N 路模型竞赛 (Stage 2)

在 `experiment.code.candidates` 下列出要参赛的 **endpoint 名称**，Camyla
会给每个 candidate 跑一条分支，保留最强的。

```yaml
experiment:
  code:
    candidates: [my_dashscope, my_minimax]
    max_tokens: 16384
```

如果只给一个 endpoint，就相当于禁用竞赛。

### 5. 非 LLM 的 API key

文献搜索相关的 key 放在 `api_keys` 下（同样支持内联 / 环境变量回落）：

```yaml
api_keys:
  s2:   { value: "", env: S2_API_KEY }    # Semantic Scholar
  ncbi: { value: "", env: NCBI_API_KEY }  # PubMed
```

没有这些 key Camyla 也能跑，只是速率限制更紧。

### 6. 运行参数调优

配置里剩下的部分只管 **Camyla 做什么**，不管用 **哪个 LLM**：

- `idea_generation.*` —— 检索多少篇文献、proposal 如何打分、集成几个 generator 人格
- `experiment.stages.*` —— 每个 stage 的迭代预算（Stage 1 = baseline 复现，Stage 2 = creative research，Stage 3 = ablation）
- `experiment.openhands.*` —— OpenHands coder 设置（Python 路径、迭代上限、condenser）
- `experiment.search.*` —— QWBE 超参数（UCB 常数、debug 概率、draft 数）

这些都在 [config_example.yaml](config_example.yaml) 里有合理默认值；
你只在想跑得更快 / 更省钱时才需要调 `stages.*_max_iters`。

---

## 仓库结构

```
camyla/
├── LICENSE
├── README.md                        # 英文说明
├── README_zh.md                     # 本文件
├── config_example.yaml              # 带注释的配置模板
├── requirements.txt
├── launch_camyla.py                 # 主入口
├── ideas/                           # 31 份现成 idea JSON
└── camyla/                          # 核心包
    ├── model_config.py              # LLM 配置加载器（get_endpoint / get_role）
    ├── baseline/                    # QWBE 之前的 trainer 筛选 + 完整训练
    ├── infrastructure/literature/   # arxiv / openalex / pubmed / multi-source
    ├── paper_agent/                 # LaTeX 写作 + 绘图 + bibtex agent
    ├── tools/                       # OpenAlex / Semantic Scholar 工具
    ├── treesearch/                  # QWBE 核心、parallel agents、OpenHands coder
    └── utils/
```

---

## 相关仓库

- [CamylaNet](https://github.com/yifangao112/CamylaNet) —— 基于 nnU-Net v2 的分割框架，
  提供一组精选的 CNN / Transformer / state-space backbone。Camyla 的 baseline 阶段运行 CamylaNet 的 trainer。
- [nnPrep](https://github.com/yifangao112/nnPrep) —— 一个把任意医学分割数据集转成
  nnU-Net v2 格式的 LLM agent，供 CamylaNet（进而供 Camyla）使用。

---

## 📚 论文样例

以下是 Camyla 端到端生成的 10 篇手稿样例（没有任何人工编辑，LaTeX 由 pipeline 直接编译）。PDF 文件都在 [`assets/paper_pdf/`](assets/paper_pdf/) 目录下。

| # | 标题 | 模态 / 任务 |
|:-:|------|------------|
| 1 | [Cross-Directional Feature Lattice for Brain Tumor Segmentation](assets/paper_pdf/01.pdf) | MRI · 脑肿瘤 |
| 2 | [Scale-Frequency Adaptive Fusion for Multiple Sclerosis Lesion Segmentation](assets/paper_pdf/02.pdf) | MRI · 多发性硬化病灶 |
| 3 | [Hierarchical Context Gating for Neonatal Brain Lesion Segmentation](assets/paper_pdf/03.pdf) | MRI · 新生儿 HIE |
| 4 | [Cross-Scale Mutual Refinement for Bronchoalveolar Lavage Fluid Cell Segmentation](assets/paper_pdf/04.pdf) | 显微镜 · BALF 细胞 |
| 5 | [Symmetry-Aware Cascaded Attention for Panoramic Tooth Segmentation](assets/paper_pdf/05.pdf) | 口腔 X 光 · 牙齿 |
| 6 | [Specular-Residual Decoupled Encoding for Surgical Scene Segmentation](assets/paper_pdf/06.pdf) | 腹腔镜 · 术野 |
| 7 | [Adaptive Scale-Aware Feature Integration for Liver Lesion Segmentation](assets/paper_pdf/07.pdf) | CT · 肝脏病灶 |
| 8 | [Boundary-Hierarchical Decomposition for Fetal Brain Tissue Segmentation](assets/paper_pdf/08.pdf) | MRI · 胎儿脑 |
| 9 | [Vessel-Guided Boundary Residual Networks for Dermatological Vessel Segmentation](assets/paper_pdf/09.pdf) | OCTA · 皮肤血管 |
| 10 | [Hierarchical Resolution-Retentive Feature Encoding for Brain Metastasis Segmentation](assets/paper_pdf/10.pdf) | MRI · 脑转移瘤 |

---

## 局限 & 注意事项

- baseline 阶段目前用的是 CamylaNet 的 trainer。如果要换 baseline 框架，需要在
  `skills/frameworks/` 下补一套对应的 skill。
- OpenHands 在你本地 Python 环境里执行代码 —— 记得把 `experiment.openhands.python_path`
  指向装好依赖的 env。
- 长时运行：一条完整 idea（proposal → 3 个 stage → 论文）在单张 A100 级 GPU 上通常要几小时到一整天。

---

## 引用

若本项目对你的学术工作有帮助，请引用：

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

## 致谢

Camyla 借鉴了两个上游项目的思路和部分代码：

- [**AI Scientist** (Sakana AI)](https://github.com/SakanaAI/AI-Scientist) —— 开创了自动科研 agent 的范式，启发了 Camyla 的整体流水线设计。
- [**nnU-Net**](https://github.com/MIC-DKFZ/nnUNet) —— Camyla 的 baseline 阶段（通过 [CamylaNet](https://github.com/yifangao112/CamylaNet)）构建于其上的自配置分割框架。

---

## 许可

以 **Apache License, Version 2.0** 发布 —— 见 [LICENSE](LICENSE)。
