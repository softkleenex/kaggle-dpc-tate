# Translating Ancient Akkadian to English (Kaggle: Deep Past Initiative)

> Public portfolio snapshot. This repository intentionally excludes competition
> datasets, generated model weights, bulk notebooks, submission files, and local
> execution logs. The full working archive is kept locally; this public copy
> preserves the project narrative, lessons learned, and lightweight MLOps notes.

## Links

- Blog retrospective: <https://softkleenex.github.io/coding_training/kaggle/kaggle-dpc-tate>
- Kaggle profile/project context: <https://www.kaggle.com/softkleenex>

## 📌 Project Overview
This repository contains the post-mortem analysis and MLOps automation pipeline developed for the Kaggle **Deep Past Initiative: Machine Translation** competition. The objective was to translate ancient Akkadian transliterations into English.

**Status:** Competition Ended (March 23, 2026)  
**Public Leaderboard Score:** 33.8 (Top-tier baseline breakthrough)

---

## 🏆 Results & Achievements
- **Score Breakthrough:** Achieved a Public Leaderboard score of **33.8** (CHRF++/BLEU based), placing **approx. 966th out of 2,673 teams (Top 36%)**. 
- **Distance to 1st Place:** The 1st place team scored ~42.9, meaning our custom baseline achieved a highly competitive result (only ~9.1 points behind the winner) using solely character-level modeling and MBR, without relying on massive external LLM data generation.
- **100% Automated MLOps Pipeline:** Developed a completely autonomous Kaggle submission loop using a CLI-based AI agent. The pipeline dynamically bundled code, submitted kernels, polled for scores via API, and iteratively proposed hyperparameter tweaks without human intervention.
- **Vectorized MBR (Minimum Bayes Risk):** Successfully implemented and optimized a PyTorch Tensor-based Vectorized MBR decoding algorithm, drastically reducing inference bottlenecks during ensemble translation.

---

## 🔍 Critical Analysis: What Worked & What Didn't

### ✅ The Good: Core Strategies
1. **Character-Level Translation (ByT5):** Akkadian transliterations mix morphology and special characters in ways standard subword tokenizers (like WordPiece or BPE) struggle to capture. Shifting to **ByT5** (a byte-level model) was a critical decision that allowed the model to learn raw character mappings robustly.
2. **MBR Decoding & Diverse Beam Search:** Standard greedy or beam search lacks diversity. By employing `num_beam_groups` and a `diversity_penalty`, the model generated a wide array of translation candidates. The MBR algorithm then evaluated these candidates against each other to select the statistically safest translation.
3. **External Knowledge Injection (Golden Lookups & Lexicons):**
   - **Golden Lookup:** Cached 1,559 exact matches from the training set, ensuring 100% accuracy on known transliterations.
   - **Hierarchical Lexicon Bonus:** Integrated external linguistic data (`OA_Lexicon_eBL.csv`) to boost candidates containing correct Proper Nouns (PN), Geographical Names (GN), and Divine Names (DN), effectively bridging the gap between statistical ML and rule-based linguistic heuristics.

### 🚧 The Bad: Technical Hardships & Engineering Challenges
1. **The Kaggle Hardware Trap (CUDA Incompatibility):**
   - *Problem:* Kaggle's older P100 GPUs (sm_60) consistently threw `CUDA error: no kernel image is available` when paired with modern PyTorch features (e.g., `bfloat16`, `bitsandbytes`).
   - *Solution:* Engineered a dynamic hardware detection system that automatically implemented a **CPU Fallback** if incompatible GPU architectures were detected, prioritizing pipeline stability over speed.
2. **Dynamic Mount Paths & "Ghost" Errors:**
   - *Problem:* Kaggle dynamically changes the `/kaggle/input/...` mount paths during inference. Hardcoded paths led to catastrophic `HFValidationError` exceptions because the `transformers` library mistook missing local directories for Hugging Face Hub IDs and attempted network requests in an offline kernel.
   - *Solution:* Implemented `os.walk()` for robust directory discovery and strictly enforced `local_files_only=True`.
3. **API Rate Limiting & "Ghosting" in Automation:**
   - *Problem:* The automated submission script overwhelmed the Kaggle API, resulting in `429 Too Many Requests`. Worse, the CLI would sometimes fetch scores from previous runs ("Ghosting"), polluting the optimization loop.
   - *Solution:* Integrated **Exponential Backoff** and injected unique timestamps (`SID-`) into kernel descriptions, enforcing strict `grep` matching to ensure the agent only reacted to the correct score.

### 📉 The Missed Opportunities (Limitations)
While the MBR and ByT5 approach plateaued at 33.8, top competitors reached ~42.8. A critical retrospective reveals that the focus was heavily skewed towards *inference optimization* rather than *data quality*. 
If the competition were ongoing, the next architectural pivot would be:
- **LLM-Based Parallel Corpus Extraction:** Using Llama/Mistral to precisely extract clean transliteration-translation pairs from noisy academic PDFs.
- **Langdetect Filtering:** Programmatically stripping non-English documents before training to eliminate noise in the dataset.

---

## 🛠 Architecture & Repository Structure

### Autonomous Automation Pipeline
The crown jewel of this repository is the MLOps automation script.
- `run_competition.sh`: The main bash loop orchestrating the automated AI CLI agent.
- `scripts/bundle_mbr.py`: Dynamically compiles modular Python source code into a single, offline Kaggle-ready Jupyter Notebook.

### Directory Layout
```text
.
├── data/               # Datasets, lexicons, and local models
├── mlops-pipeline/     # Automated MLOps & Training Pipeline (integrated)
├── notebooks/          # Auto-generated Kaggle submission notebooks
├── scripts/            # CI/CD, bundling, and automated monitoring scripts
├── src/                # Core Python source code (ByT5 models, Vectorized MBR, Utils)
└── README.md           # This document
```

## 📜 Conclusion
This project transitioned from a standard NLP translation task into an exercise in **extreme automation and robust engineering**. The challenges faced with ephemeral cloud environments, hardware incompatibilities, and API limitations provided invaluable experience in building resilient, production-like MLOps pipelines.

<!-- BLOG-URL:START -->

## Blog

- Blog note: [Translating Ancient Akkadian to English (Kaggle: Deep Past Initiative)](https://softkleenex.github.io/coding_training/kaggle/kaggle-dpc-tate)

<!-- BLOG-URL:END -->
