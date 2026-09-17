# Data — download & reproduction

Raw data isn't committed to this repo (see the root README for why). Here's how to get and rebuild it.

## Global Cybersecurity Threats (2015–2024)

1. Download from [Kaggle](https://www.kaggle.com/datasets/atharvasoundankar/global-cybersecurity-threats-2015-2024) (login required, free).
2. Place the CSV in this folder as `Global_Cybersecurity_Threats_20152024.csv`.
3. Run `notebooks/act1_landscape_full_analysis.ipynb` top to bottom — it reads this file directly and reproduces every number and chart in the notebook and in `excel/AI_Weapon_and_Shield_DataWorkbook.xlsx`.

3,000 rows, 0 missing values, 0 duplicates as downloaded — no cleaning needed for this one.

## Network Intrusion Dataset (CIC-IDS-2017)

1. Download from [Kaggle](https://www.kaggle.com/datasets/chethuhn/network-intrusion-dataset) (login required, free) — 8 CSV files, one per capture day/session:
   `Monday-WorkingHours.pcap_ISCX.csv`, `Tuesday-WorkingHours.pcap_ISCX.csv`, `Wednesday-workingHours.pcap_ISCX.csv`, `Thursday-WorkingHours-Morning-WebAttacks.pcap_ISCX.csv`, `Thursday-WorkingHours-Afternoon-Infilteration.pcap_ISCX.csv`, `Friday-WorkingHours-Morning.pcap_ISCX.csv`, `Friday-WorkingHours-Afternoon-PortScan.pcap_ISCX.csv`, `Friday-WorkingHours-Afternoon-DDos.pcap_ISCX.csv`.
2. Place all 8 files in this folder, unmodified.
3. Run `notebooks/act2_defense.ipynb` top to bottom. It expects a cleaned, consolidated file (`nid_clean_full.pkl.gz`) produced by the consolidation step documented in `excel/Network_Intrusion_DataWorkbook.xlsx` → "Read Me" tab: combine all 8 files, drop the duplicate `Fwd Header Length` column, fix the mojibake in the `Label` column, replace infinite values with nulls, then drop rows with any null, a negative `Flow Duration`, or an exact duplicate.

Raw: 2,830,743 rows. Clean: 2,497,873 rows (88.2% kept). See the workbook's "Data Quality Report" tab for the full per-file breakdown of what was dropped and why.
