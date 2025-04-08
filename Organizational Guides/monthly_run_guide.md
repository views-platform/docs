# 📊 VIEWS Monthly Run Routine

This document contains step-by-step instructions for executing the VIEWS ML pipeline to produce monthly true future forecasts. It is split into three parts – the first part refers to updating the necesary data; the second part contains instructions for executing the pipeline and producing the forecasts, while the third part details the steps for publishing the forecasts.


- **Part 1**: Data Ingestion  
- **Part 2**: Running Forecasts (CM + PGM)  
- **Part 3**: Publishing the Forecasts

---

## 📚 Table of Contents

### Part 1: Data Ingestion
- [Environment Setup](#environment-setup)
- [1. GED Ingestion](#1-ged-ingestion)
- [2. ACLED Ingestion](#2-acled-ingestion)
- [3. Topics Ingestion](#3-topics-ingestion)
- [4. Other Datasets (TBD)](#4-other-datasets-tbd)
- [5. Wait for `viewser`](#5-wait-for-viewser)
- [6. (Optional) Check Ingested Data](#6-optional-check-ingested-data)

### Part 2: Running Forecasts (CM + PGM)
- [1. Run *cm* Forecasts](#1-run-cm-forecasts)
- [2. Run *pgm* Forecasts](#2-run-pgm-forecasts)
- [3. Create Maps and Figures](#3-create-maps-and-figures)

### Part 3: Publishing the Forecasts
- [1. Update the API Codebooks](#1-update-the-api-codebooks)
- [2. Push the Run to the API](#2-push-the-run-to-the-api)
- [3. Update the API Wiki](#3-update-the-api-wiki)
- [4. Download Forecast CSVs](#4-download-forecast-csvs)
- [5. Check API Datasets](#5-check-api-datasets)
- [6. Wait for Dashboard Update](#6-wait-for-dashboard-update)
- [7. Update Eval Data](#7-update-eval-data)
- [8. Publish and Share](#8-publish-and-share)

---

## 🧪 Part 1: Data Ingestion

### Environment Setup

See: [Setting up the ViEWS environment](https://github.com/prio-data/ViEWS_organization/blob/main/The%20Views%20Environment/Setting%20up.MD)  
Check requirements: [Essential Requirements](https://github.com/prio-data/ViEWS_organization/blob/main/The%20Views%20Environment/EssentialRequirements.MD)

Clone the required repositories:

```bash
git clone https://github.com/UppsalaConflictDataProgram/ingester3_loaders
git clone https://github.com/prio-data/viewsforecasting
```

To update later:

```bash
git pull origin main
```

---

### 1. GED Ingestion

✅ **Feb 2025: Done**

```bash
conda activate viewser
cd ~/ingester3_loaders
jupyter notebook
```

Open `GED_loader.ipynb`, clear output, restart kernel.  
Update the dataset version number in the relevant cell, based on [UCDP downloads](https://ucdp.uu.se/downloads/).  
Run the notebook.

---

### 2. ACLED Ingestion

✅ **Feb 2025: Done**

Open `ACLED_loader.ipynb`, clear output, restart kernel.  
Update this cell:

```python
acled = AcledLoader(512, 513)
```

Then run the notebook.

---

### 3. Topics Ingestion

✅ **Feb 2025: Done**

Open `Topics_data/Topics_data_version2.ipynb`, clear output, restart kernel.

Update this cell:

```python
dataset = 'topics_202410.csv'
```

Then run the notebook and update the `README.md` in `Topics_data`.

---

### 4. Other Datasets (TBD)

✅ **Feb 2025: Done (none needed)**

> This step is still under development. Coordinate with Håvard and the team for update routines.

---

### 5. Wait for `viewser`

✅ **Feb 2025: Done**

Wait ~30–40 minutes for `viewser` to restart. Data will only be available after this.

---

### 6. (Optional) Check Ingested Data

✅ **Feb 2025: Done**

Use the [Ingestion Test Notebook](https://github.com/prio-data/views_outreach/blob/main/monthly_run/ingestion_test.ipynb) to verify that everything loaded correctly.

---

## 🚀 Part 2: Running Forecasts (CM + PGM)

### 1. Run *cm* Forecasts



---

### 2. Run *pgm* Forecasts


---

### 3. Create Maps and Figures


---

## 🌍 Part 3: Publishing the Forecasts

### 1. Update the API Codebooks

✅ **Feb 2025: Done**

Located in: `codebooks/master-codebooks/api/`

Update the forecast and predictor codebooks:
- `date_of_access`
- `dataset`
- Visualization configs (check with Angelica)

Save archive copies with `_YYYYMM` suffix.  
Send links to Jim for pulling to the API.

---

### 2. Push the Run to the API

Ask Jim to push the *cm* and *pgm* forecasts to the API.  
🗓️ Record the date for tracking.

---

### 3. Update the API Wiki

✅ **Feb 2025: Done**

Edit the [Available Datasets Wiki](https://github.com/prio-data/views_api/wiki/Available-datasets)  
Add a new row for the latest run.

---

### 4. Download Forecast CSVs

Run: `download_API_data_for_website.ipynb` in `views_outreach`

Update this line:

```python
dataset = 'fatalities002_2023_08_t01'
```

This will generate `cm` and `pgm` CSVs and save them to your desktop.

---

### 5. Check API Datasets

Ensure:
- Data appears in the dashboard
- Codebooks match the data
- No missing or mismatched variables

---

### 6. Wait for Dashboard Update

🕒 The dashboard syncs at 03:00 daily.  
Announce only the day *after* the update.

---

### 7. Update Eval Data

✅ **Feb 2025: Done**

Marina/Angelica will upload updated eval files to the predcomp server and leaderboard.

---

### 8. Publish and Share

- Upload CSVs and codebook link on [viewsforecasting.org](https://viewsforecasting.org/data/#latest-data)
- Send the monthly newsletter
- Share updates on Twitter/X and LinkedIn

---

Happy forecasting! 🚀