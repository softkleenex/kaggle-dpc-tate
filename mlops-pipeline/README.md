# Kaggle Akkadian Translation MLOps 🏺

![Kaggle](https://img.shields.io/badge/Kaggle-Deep%20Past%20Initiative-blue?logo=kaggle)
![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python)

This repository contains the 100% automated **MLOps pipeline** developed for the Kaggle **Deep Past Initiative: Machine Translation** competition, where the goal was to translate ancient Akkadian transliterations into English.

This project focuses on the automation, model training loop, and evaluation pipeline that powered our primary solution (housed in the `kaggle-dpc-tate` repository).

---

## 🏆 Results & Achievements
- **Score Breakthrough:** Achieved a Public Leaderboard score of **33.8** (CHRF++/BLEU based).
- **Final Rank:** **approx. 966th out of 2,673 teams (Top 36%)**. 
- **Distance to 1st Place:** The 1st place team scored ~42.9. Our custom baseline achieved a highly competitive result (only ~9.1 points behind the winner) using solely character-level modeling and MBR, without relying on massive external LLM data generation.

---

## 🚀 Key Features of the Pipeline
- **100% Automated Kaggle Submission Loop:** Developed a completely autonomous Kaggle submission loop using a CLI-based AI agent. The pipeline dynamically bundled code, submitted kernels, polled for scores via API, and iteratively proposed hyperparameter tweaks without human intervention.
- **Vectorized MBR (Minimum Bayes Risk):** Successfully implemented and optimized a PyTorch Tensor-based Vectorized MBR decoding algorithm, drastically reducing inference bottlenecks during ensemble translation.

---

## 📁 Repository Context
- This repository handles the **Automated MLOps & Training Pipeline**.
- The main competition codebase, post-mortem analysis, and detailed learnings can be found in the [kaggle-dpc-tate](../kaggle-dpc-tate) repository.