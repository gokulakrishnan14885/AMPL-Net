# Gujarat Cotton AMPL-Net + IECO Reproducibility Code

Code for the manuscript **AI-Enabled AMPL-Net and Improved Ecological Cycle Optimization for District-Level Cotton Productivity Prediction and Simulated Seed-Rate Optimization in Gujarat**.

Dataset: [Kaggle Gujarat Crop Data Analysis](https://www.kaggle.com/datasets/darshansavaliya/gujratcropdataanalysis).

## Scientific scope

This code separates (1) data-derived district cotton productivity prediction using historical lagged records from (2) conditional seed-rate optimisation scenarios. The Kaggle data are administrative district/crop/year/season statistics, not field plots. They do not measure seed rate, emerged plants, cultivar-specific dose-response, GPS management zones, planter telemetry or post-treatment yields.

The historical manuscript values (RMSE 4.38, MAE 3.36, MAPE 6.58%, R² 0.972, 22.85% seed saving, 96.41% seed-use proxy and 2724 kg/ha) are not hard-coded or claimed as reproduced. New metrics are calculated only from the supplied file.

## Install and run

```bash
pip install -r requirements.txt
python src/run_pipeline.py --input data/raw/your_dataset.csv --out outputs --repeats 5 --folds 5
```

XLSX is also supported. Download the Kaggle file manually; place it under `data/raw/`. The loader recognises common names such as `Crop_Year`/`fiscal_year`, `District_Name`/`district`, `Crop`, `Season`, `Area`, `Production`, and `Yield`/`crop_yield`.

If a recorded kg/ha yield column exists, it is used as the target. Otherwise the script derives yield only when `--production-unit tonnes` is explicitly provided: 1000 × production / area. Do not apply that conversion to bales or unspecified units.

## Method

- Filter Gujarat and cotton records; exclude aggregate district rows where identifiable.
- Construct lagged productivity, area and production features within each district. Same-season area, production and derived productivity are never used as predictors.
- Evaluate with repeated five-fold district-grouped splits. Imputation/scaling are fitted inside each training fold.
- Compare AMPL-Net, MLP, Random Forest and optional XGBoost on identical predictors/folds.
- AMPL-Net: 64-dimensional embedding, feature attention, ReLU(128)-ReLU(64), sigmoid output on training-normalized target, Adam 0.001, batch size 32, max 150 epochs, dropout 0.20, L2 1e-4, MSE and early stopping.
- Report RMSE, MAE, MAPE (nonzero targets), R², fold predictions and metrics.
- If there are insufficient repeated historical observations or districts, the program stops rather than silently training a leaky model.

## Conditional optimisation

IECO, PSO, GWO and Differential Evolution use the same explicit assumed quadratic seed-response objective, bounds, evaluation budget and paired seeds. The response curve is not fitted from the Kaggle data; its parameters are declared scenario assumptions. Optimized rates and associated yield/saving values are hypothetical outputs, not observed agronomic outcomes.

## Outputs

Cleaned data, feature audit, fold assignments, fold and summary metrics, out-of-fold predictions, optimizer run/trace tables, scenario district rates and run metadata.

Record Kaggle version, download date, checksum, licence, column mapping, units, district mapping, software versions, hardware, seeds and command line. Do not redistribute the dataset unless its licence permits it. Code licence: MIT; dataset licence remains separate.
