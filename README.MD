# Snaphomz Trial – Yishan Cai

## Setup
- Python version: 3.9.2
- OS / CPU / GPU: Windows 10 / 8 cores / No GPU used
- Create env:
  ```
  python -m venv .venv && source .venv/bin/activate
  pip install -r requirements.txt
  ```
- (Optional) Local model setup:
  - llama.cpp / mistral inference notes here

## How to Run
1. Open `notebooks/trial.ipynb` and run all cells.

## Decisions
- Target & features: Regression Model
    - Target variable: `price_per_sqft`
    - numeric features: `beds`, `baths`
    - binary features: `open_concept`, `updated_kitchen`, `corner_lot`, 
       `fresh_paint`, `vaulted_ceilings`, `solar_panels`, `granite_counters`,
       `near_good_schools', 'quiet_cul_de_sac`, `hoa_includes_pool`,
       `ev_charger', 'two_car_garage', 'large_backyard`, `walk_to_park`,
       `stainless_appliances', 'hardwood_floors`, `new_roof_2022`,
       `tile_flooring', 'primary_suite', 'home_office`, 
    - one-hot encoding features: `city_Frisco`, `city_Mountain View`, `city_Palo Alto`, 
        `city_Plano`, `city_San Jose`, `city_Santa Clara`, `city_Sunnyvale`, `state_TX`, 
        `list_year`, `list_month`, `list_quarter` 
- Train/test split & metrics: 8/2, RMSE, MAE, R2
- Retrieval approach: Implemented a lightweight retrieval-based Q&A system over the `remarks` column. First, a BM25 retriever builds a tokenized corpus of all property remarks. User questions are parsed with regex to extract the target address, and then matched against the database. A predefined keyword-to-feature mapping (e.g., "two-car garage" -> two_car_garage) is used to link natural language queries to structured binary columns in the dataset. The system returns a direct yes/no answer along with supporting evidence from the original remarks text.
- Assumptions & trade-offs: 
    - `sqft` > 0 for all valid rows
    - Used `drop_first=True` in one-hot encoding, assuming the baseline city/state can be implicitly represented
    - Chose Random Forest over more complex models, trading off some potential accuracy for faster training and easier interpretation
    - Assumed user questions only query features explicitly present in the remarks column. The trade-off is simplicity and reproducibility, but with less flexible answers.


## Results (TL;DR)
- Key EDA insights:
    - Geographic location drives the largest price variations, with California cities (Palo Alto, Santa Clara) commanding 2-3x higher prices per square foot than Texas cities (Dallas, Frisco).
    - Properties with more bathrooms command progressively higher prices per square foot, with 3-bathroom homes showing the highest median around $400-500/sqft.
    - The number of bedrooms has minimal impact on price per square foot, with all categories (2-5 beds) showing similar median values around $300-400/sqft.
- Best metric(s): Random Forest Baseline -> RMSE: $164.49/sqft; MAE: $119.86/sqft; R2: 0.506
- Sample Q&A:
    - Q: Does 960 Cedar Ct, Frisco, TX have hardwood floors?
    - A: No, 960 Cedar Ct, Frisco, TX does not have hardwood floors.
    Property features: quiet cul-de-sac, two-car garage, near good schools, vaulted ceilings, updated kitchen, HOA includes pool
    
## Known Limitations
- The dataset only has around 160 rows, which limits model complexity and predictive performance. More advanced models are unlikely to generalize well with such a small sample size.

## Time Spent
- Approx hours: 4
