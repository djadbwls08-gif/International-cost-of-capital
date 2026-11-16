# 🌍 International Cost of Capital App

A Streamlit app that compares a project's **cost of equity, cost of debt and WACC in USD and in the currency of any selected country**.

## Features
- **Live data** with source and date shown, plus a **🔄 Refresh data** button:
  | Input | Source |
  |---|---|
  | U.S. industry unlevered betas | Aswath Damodaran, [`betas.xls`](https://pages.stern.nyu.edu/~adamodar/pc/datasets/betas.xls), column *Unlevered beta* |
  | USD BBB (Baa2) spread, large non-financial firms | Aswath Damodaran, [`ratings.html`](https://pages.stern.nyu.edu/adamodar/New_Home_Page/datafile/ratings.html) |
  | Corporate tax rates (combined statutory CIT) | OECD Corporate Tax Statistics, retrieved from the OECD Data Explorer API (`DSD_TAX_CIT@DF_CIT`) |
  | 10-year government bond yields | [Trading Economics – Bonds](https://tradingeconomics.com/bonds), scraped without an API key |
- If a source can't be reached, the app uses a clearly labelled **static fallback** and flags it ⚠️ in the *Data sources* panel.
- **Inputs:** industry, target country, equity risk premium, target debt share, and a toggle between the **U.S. and selected-country tax rate**.
- **Manual overrides** for the unlevered beta, both tax rates, the USD BBB spread, both 10Y yields and the foreign-currency BBB spread. When the foreign-currency spread override is off, the app shows the auto-calculated spread.
- **Side-by-side results**, an assumptions and formulas tab, an **all-industry comparison**, built-in **verification checks**, and **Excel/CSV downloads**.
- **Defaults:** Software (System & Application), United States, U.S. tax rate, ERP 6.5%, debt share 30%.

## Methodology
| Step | Formula |
|---|---|
| Relevered beta (Hamada) | βL = βU × [1 + (1 − t) × D/E], with D/E = d / (1 − d) |
| USD cost of equity | ke$ = rf,US + βL × ERP |
| USD cost of debt | kd$ = rf,US + BBB spread$ |
| Foreign-currency cost (international Fisher) | kFC = (1 + k$) × (1 + rf,FC) / (1 + rf,US) − 1, applied to equity and debt |
| FC cost of debt with spread override | kd,FC = rf,FC + spread_FC (manual) |
| WACC | (1 − d) × ke + d × kd × (1 − t) |

The same tax rate, debt share and ERP apply in both currencies.

## Verification (built into the app's *Verification checks* tab)
1. Equal government bond yields give equal USD and FC results (FC spread override off).
2. Zero debt makes WACC equal the cost of equity.
3. Overriding the foreign-currency spread leaves the USD results unchanged.

## Run locally
```bash
pip install -r requirements.txt
streamlit run app.py
```

## Deploy on Streamlit Community Cloud
1. Push `app.py`, `requirements.txt` and `README.md` to a public GitHub repository.
2. Go to [share.streamlit.io](https://share.streamlit.io), click **Create app**, pick the repository, branch `main` and file `app.py`, then click **Deploy**.

## Notes and limitations
- Trading Economics may change its page layout or block automated requests. If that happens, the app falls back and the yields can be entered manually.
- Country names are harmonised between OECD and Trading Economics (e.g. *Korea* becomes *South Korea*, *Türkiye* becomes *Turkey*). The country list shows the countries that have both a yield and a tax rate.
- Check the formulas against the course notes before relying on the results.
