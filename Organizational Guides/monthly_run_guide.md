# 📊 VIEWS Monthly Run Routine

### Last Updated: April 2025 by Marina

This document contains step-by-step instructions for executing the VIEWS ML pipeline to produce monthly true future forecasts. It is split into three parts – the first part refers to **updating the necesary data**; the second part contains **instructions for executing the pipeline** and producing the forecasts, while the third part details the steps for **publishing the forecasts**. Each step can be done individually for its own purpose – for example, following instructions in Part 2 can be followed for simply interacting with the pipeline. 


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
- [1. Run *cm* Ensemble](#1-run-cm-forecasts)
- [2. Run *pgm* Ensemble](#2-run-pgm-forecasts)
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

To get started with the ViEWS ingestion workflow, make sure your environment is correctly set up.

📘 **Guides:**

- [Setting up the VIEWS environment](https://github.com/prio-data/ViEWS_organization/blob/main/The%20Views%20Environment/Setting%20up.MD)
- [Essential Requirements](https://github.com/prio-data/ViEWS_organization/blob/main/The%20Views%20Environment/EssentialRequirements.MD)


📁 **Clone the required repositories:**

```bash
git clone https://github.com/UppsalaConflictDataProgram/ingester3_loaders
git clone https://github.com/prio-data/viewsforecasting
```

📥 **To fetch updates later:**

```bash
cd ~/ingester3_loaders && git pull origin main
cd ~/viewsforecasting && git pull origin main
```

> 📌 These paths assume you're storing the repositories in your home directory (e.g., `~/Users/USERNAME` on macOS or `~/home/USERNAME` on Linux). If you're storing them elsewhere, adjust paths accordingly.

---

### 1. GED Ingestion

✅ **Feb 2025: Done**

Steps:

```bash
conda activate viewser
cd ~/ingester3_loaders
jupyter notebook
```

> 💡 You can use `jupyter lab` instead of `jupyter notebook` if you prefer.

1. Open `GED_loader.ipynb`.
2. Clear all outputs and restart the kernel (top menu: `Kernel → Restart & Clear Output`).
3. Locate the cell that specifies the GED version (flagged in the notebook).
4. Check the latest version on [UCDP Downloads](https://ucdp.uu.se/downloads/), and update the version number accordingly.
5. Run the notebook.

If the notebook runs successfully, proceed to the next dataset.

---

### 2. ACLED Ingestion

✅ **Feb 2025: Done**

Steps:

1. Open `ACLED_loader.ipynb` (in `ingester3_loaders`).
2. Clear all outputs and restart the kernel.
3. Find the cell that looks like this:

```python
# Specify the first and last month of loading.
acled = AcledLoader(512, 513)
```

> ⌚ This example loads data from the start of month 512 to the start of month 513. Adjust as needed depending on what’s already loaded.

4. Run the notebook.

If the notebook completes successfully, continue to the next step.

---

### 3. Topics Ingestion

✅ **Feb 2025: Done**

> 📂 Topics data is currently provided via Dropbox in:  
> `ViEWS Dropbox/ViEWS/Data/Topics_data`

Before ingesting:

- Make sure there is no new codebook or dataset structure.
- Coordinate with the Barcelona team providing the data if changes were communicated.

Steps:

1. Open `Topics_data/Topics_data_version2.ipynb`.
2. Clear all outputs and restart the kernel.
3. Locate the cell like this:

```python
# UPDATE MONTHLY
dropbox = home+'/ViEWS Dropbox/'
folder = dropbox+'ViEWS/Data/Topics_data/'

dataset = 'topics_202410.csv'  # Change to the latest dataset
```

4. Change the filename to match the month of the latest data.
5. Run the notebook.
6. Once completed, update the `README.md` inside the `Topics_data` folder to reflect the latest ingestion.

---

### 4. Other Datasets (TBD)

✅ **Feb 2025: Done (none needed)**

> This ingestion step is still under development. We need a proper update routine and schedule for the remaining predictors.  
> 📌 Coordinate with the team if any additional data needs ingestion this cycle.

---

### 5. Wait for `viewser`

✅ **Feb 2025: Done**

> ⏳ After ingestion, it takes ~30–40 minutes for `viewser` to restart and make the ingested data available.

You **must wait** before using ingested data in any downstream notebooks or analysis.

---

### 6. (Optional) Check Ingested Data

✅ **Feb 2025: Done**

> 🔍 To confirm successful ingestion, use the test notebook here:  
> [Ingestion Test Notebook](https://github.com/prio-data/views_outreach/blob/main/monthly_run/ingestion_test.ipynb)

---


## 🚀 Part 2: Running Ensembles 

> 📌  If you are looking for a quick technical guide on your first execution of the pipeline, see our [Technical Guide](../Onboarding%20Resources/internal_technical_guide.md).

Once the latest data is ingested, running the CM and PGM ensambles is done through the terminal. In the [views-models](https://github.com/views-platform/views-models) you can find all of the implemented models and ensembles along with target variables and additional information. Here you can choose which ensemble you wish to run. 

To run any of the VIEWS models or ensembles, you need to make sure you have cloned all of the relevant repos locally. Specifically, cloning [views-models](https://github.com/views-platform/views-models) and [views-pipeline-core](https://github.com/views-platform/views-pipeline-core) is a necesary first step. 



### 1. Run *cm* Ensembles

Currently, the cm ensemble producing monthly forecasts is [pink_ponyclub](https://github.com/views-platform/views-models/tree/main/ensembles/pink_ponyclub), although the steps to run any model or ensemble are the same. 

 1. Once you have cloned the repo, through the terminal navigate to the `ensembles` directory within the `models` directory by running:

 ```
  cd views-models/ensembles/pink_ponyclub
```
If you only wish to run a single model and not an ensemble, you can navigate to all the model directories by typing

 ```
 cd 'model_name'
```

2. Once you are in your desired model/ensemble directory, all you need to do is run the .sh file. This you do by running the following command in the temrinal: 

 ```
./run.sh -r forecasting -t -f  
```

The  `-r` `-t` and `-f` are flags which determine the  specifics of the run type (whether you wish to do calibration/validation/forecasting). You can find full documentation and more information here: [VIEWS Pipeline CLI Documentation](https://github.com/views-platform/views-pipeline-core/tree/main/views_pipeline_core/cli#command-line-arguments).

---

### 2. Run *pgm* Ensembles

Currently, the pgm ensemble producing monthly forecasts is [skinny_love](https://github.com/views-platform/views-models/tree/main/ensembles/skinny_love). Running the pgm ensemble is done the same way as the cm ensembles. In the terminal run:


 ```
  cd views-models/ensembles/skinny_love
```

```
./run.sh -r forecasting -t -f  
```
---

### 3. (Optional) Create Maps and Figures


---

## 🌍 Part 3: Publishing the Forecasts



---

## Monthly Run Checklist

## ✅ Monthly Run Checklist

### Part 1: Data Ingestion
- [ ] GED Ingestion (`GED_loader.ipynb` updated and executed)
- [ ] ACLED Ingestion (`ACLED_loader.ipynb` updated and executed)
- [ ] Topics Ingestion (`Topics_data_version2.ipynb` updated and executed)
- [ ] Other Datasets (Confirmed: none needed this round)
- [ ] Waited for viewser to restart (~30–40 min)
- [ ] Verified ingested data with Ingestion Test Notebook

### Part 2: Running Forecasts
- [ ] Cloned or updated `views-models` and `views-pipeline-core`
- [ ] Ran cm ensemble (`pink_ponyclub`) using `./run.sh -r forecasting -t -f`
- [ ] Ran pgm ensemble (`skinny_love`) using `./run.sh -r forecasting -t -f`
- [ ] (Optional) Generated updated maps and figures

### Part 3: Publishing and API

##  Happy forecasting! 🚀

