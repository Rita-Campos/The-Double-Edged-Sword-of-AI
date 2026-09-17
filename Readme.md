# The Double-Edged Sword of AI — AI as Weapon and Shield in Cybersecurity

A data analysis project on how artificial intelligence is, at the same time, arming attackers and shielding defenders in cybersecurity — built end to end with Excel, Python, and Tableau.

**The headline result:** a Random Forest classifier trained on real network traffic reaches Precision 0.999 / Recall 0.999 / F1 0.999 at catching intrusions — against a simple, fixed-threshold baseline that reaches only Precision 0.92 / Recall 0.65 / F1 0.76. AI is not a marginal improvement here; it is the difference between catching an attack and missing a third of them. But the same classifier can be pushed into missing an attack it already caught correctly, 3.4% of the time, with nothing more than a small, realistic nudge to its input — a controlled demonstration of exactly the unpredictability the security industry warns about.

## The two questions this project answers

1. **Is AI going to be crucial in cybersecurity defense?** Answered empirically: an ML classifier vs. a non-AI baseline, head to head, on the same held-out data.
2. **What makes AI possibly unpredictable at a security level?** Answered empirically too: a feature-perturbation test that measures how easily the classifier's correct predictions can be flipped.

## Data

| Dataset | Role | Source |
|---|---|---|
| Network Intrusion Dataset (CIC-IDS-2017) | **Core evidence** for both questions above — 2.83M rows of labeled real network traffic (benign + 14 attack types), across 8 daily capture files | [Kaggle](https://www.kaggle.com/datasets/chethuhn/network-intrusion-dataset) |
| Global Cybersecurity Threats (2015–2024) | Macro-overview narrative opener and the 5-year forecast only — tested three independent ways (ANOVA, OLS trend, k-means clustering) and found to carry **no statistically significant signal**, consistent with a synthetically generated benchmark. Kept for scene-setting, not used as evidence for any AI-specific claim. | [Kaggle](https://www.kaggle.com/datasets/atharvasoundankar/global-cybersecurity-threats-2015-2024) |

Neither raw dataset is committed to this repo — the Network Intrusion Dataset alone is several GB across 8 files, well past what a git repository should hold. See `data/README.md` for exact download + reproduction steps.

## Method, by phase

**Excel** — both datasets get a workbook: a data dictionary, a data-quality report, and (for the intrusion dataset, too large for any spreadsheet at 2.5M rows) a stratified sample capped at 600 rows per class so every one of the 15 classes — including the rarest, with 11 to 36 rows total — stays visible by hand.

**Python** — the intrusion dataset's 8 raw files were consolidated, cleaned (331,200 exact duplicates removed, 4,376 infinite values and 2,867 null rows dropped, a mojibake encoding bug in the attack labels fixed, one duplicated column removed), leaving 2,497,873 clean rows. A modeling subset (114,172 rows — every rare class kept in full, the four largest classes capped at 20,000 each, for tractable training on 2 CPU cores) was split 75/25, stratified by class rather than by capture day, since every attack type here is locked to a single day and a day-based split would leak no attack examples into one side. A single-feature, fixed-threshold rule (chosen by AUC, thresholded by Youden's J on the training split only) stands in for a legacy non-AI detector; a Random Forest (`class_weight="balanced_subsample"`, 200 trees) stands in for the AI classifier. Feature importance substitutes for SHAP (not installable in the build environment); a perturbation test — Gaussian noise scaled to a percentage of each top feature's own spread, clipped at zero, applied only to already-correctly-classified attack rows — substitutes for a temporal drift test that this dataset's structure doesn't support.

**Forecast** — the market-overview dataset is aggregated to one row per year (2015–2024) and projected to 2029 with OLS + a 95% prediction interval, computed by hand (no `statsmodels` in the build environment). The projection is flat with wide bands, which is the statistically honest result of a trend that isn't significant (p = 0.60 for average loss, p = 0.25 for incident count) — not a modeling shortfall.

**Tableau** — four sheets combined into one Story: a world map (macro overview), the AI-vs-baseline comparison, the feature-importance-and-perturbation panel, and the 5-year outlook with its confidence band.

## Key findings

- **AI vs. a non-AI baseline, on real network traffic:** F1 0.999 vs. 0.763 — a 23.6-point gap, driven mostly by recall (0.999 vs. 0.652): the baseline's single fixed rule misses roughly a third of all attacks that the classifier catches.
- **Accuracy alone would have hidden the real story:** per-class F1 ranges from 1.000 (FTP-Patator, SSH-Patator, Heartbleed) down to 0.395 (Web Attack - XSS) and 0.333 (Web Attack - Sql Injection, on only 5 test rows) — the rarest classes are still genuinely hard, exactly why this project reports precision/recall/F1 per class rather than one overall number.
- **A small, realistic nudge measurably degrades detection:** perturbing the 5 most important features by just 5% of their normal spread flips 3.4% of correctly-caught attacks to "no attack detected"; at 80% noise, 8.2% evade detection outright, with more misclassified into the wrong attack type without evading entirely.
- **The macro dataset is a lesson in verification, reported openly:** three independent statistical tests (ANOVA, OLS trend, k-means clustering) all came back null on the Global Cybersecurity Threats dataset — evidence it doesn't reflect a real incident log. Rather than force a finding, this project narrowed that dataset's role to scene-setting and reported the null result as a limitation, not a setback.
- **Market context:** independently, published research puts AI-driven scams up ~456% year over year and AI-based SOC detection cutting response time by roughly half — this project's own empirical result (the F1 gap above) lines up with that external picture rather than contradicting it.

## Limitations

The intrusion-detection model was trained on a class-capped subsample (114,172 of 2,497,873 clean rows), not the full dataset, for tractable training time on 2 CPU cores — a documented trade-off, not a hidden one. Feature importance substitutes for true SHAP values. No temporal drift test was run, because every attack type in this dataset is confined to a single capture day. The three rarest classes (11–36 rows in the full clean dataset) will carry high metric variance regardless of model quality, simply because there is so little data to test on. The forecast rests on roughly ten yearly data points and is reported with a correspondingly wide, honestly-labeled uncertainty band rather than false precision.

## Repository structure

```
repo/
├── README.md                          # this file
├── data/
│   └── README.md                      # where to download each dataset + how to reproduce the clean version
├── notebooks/
│   ├── act1_landscape_full_analysis.ipynb   # macro dataset: EDA, ANOVA, OLS trend, clustering, forecast
│   └── act2_defense.ipynb                   # intrusion dataset: classifier vs. baseline, perturbation test
├── excel/
│   ├── AI_Weapon_and_Shield_DataWorkbook.xlsx      # Global Cybersecurity Threats (2015–2024)
│   └── Network_Intrusion_DataWorkbook.xlsx         # Network Intrusion Dataset (CIC-IDS-2017)
└── dashboard/                          # Tableau Public link + a screenshot or GIF
```

## Dashboard

Interactive Tableau Public dashboard: *link goes here once published — see the project plan's Tableau phase.*

## Author

Rita Campos

## Sources

- [AI Security Statistics 2026 — Practical DevSecOps](https://www.practical-devsecops.com/ai-security-statistics-2026-research-report/)
- [Adversarial AI Attacks — Palo Alto Networks](https://www.paloaltonetworks.com/cyberpedia/what-are-adversarial-attacks-on-AI-Machine-Learning)
- [Data Poisoning — IBM](https://www.ibm.com/think/topics/data-poisoning)
- [The Hidden Risks of Relying on AI in Cybersecurity — VikingCloud](https://www.vikingcloud.com/blog/disadvantages-of-ai-in-cybersecurity)
- [9 AI Cybersecurity Trends to Watch in 2026 — SentinelOne](https://www.sentinelone.com/cybersecurity-101/data-and-ai/ai-cybersecurity-trends/)
