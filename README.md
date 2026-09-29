# NYC Taxi Rides: Fares, Duration, Payment and Demand Patterns

**A question-driven analysis of 6,433 New York City taxi rides (March 2019)**

Every taxi ride leaves a small trail of data: when it started and ended, how far it went, what it cost, how it was paid for, and where it began and finished. This project turns that trail into answers about how fares are built, what drives ride duration and cost, how payment method changes what the data shows, and when and where demand concentrates.

## Business Questions

1. How does the total fare differ by payment method, and is that difference real or a recording artefact?
2. How well do ride duration and distance predict the fare?
3. Do the fare–duration relationship and pricing differ by payment method or taxi colour?
4. How do passengers tip, and does tipping differ between yellow and green taxis?
5. When is demand highest, and how do speeds and fares change through the day?
6. Which boroughs, zones, and routes generate the longest and most expensive rides?
7. How concentrated is revenue: which rides account for a disproportionate share of total fares?

## Key Insights

1. **Payment-method comparisons of `total` are distorted by recording.** Cards appear to cost about $5 more, but cash tips simply aren't recorded, and card rides also tend to be longer. Per mile, cash rides are actually slightly *more* expensive.
2. **Distance predicts fare better than duration.** Metered fare is well approximated by `$2.42 + $2.08/mile + $0.29/minute` (R² = 0.95); duration adds little once distance is known.
3. **The fare–duration relationship is stable** across payment methods and taxi colours (roughly $0.80–$1.00 of fare per extra minute).
4. **Green taxis record far lower tips than yellow taxis** on card rides (median 11.8% vs. 26.3%; 46% of green card rides show no tip vs. 5% for yellow).
5. **Demand peaks at 6 pm; congestion roughly halves speeds** during the day (~9 mph vs. ~17 mph at 5 am), yet median fares stay flat across the day.
6. **Long trips drive the money:** rides that cross a borough boundary are 12.7% of rides but cost about 3x as much as local trips; airport trips are 6% of rides but 17% of revenue.
7. **The top 10% of rides generate about 28% of revenue**, and over half of those are airport trips.
8. **Data quality:** 1.3% of rides were physically implausible and removed; `passengers` has no relationship with fare.

## Conclusion

The dataset tells a consistent story: **taxi fares are about distance and route type, not payment method, passenger count, or time of day.** Most rides are short Manhattan trips at $13–$14, while a small minority of long airport and inter-borough trips contribute a disproportionate share of revenue. The most important analytical lesson is how recording conventions (untracked cash tips, fixed fees folded into `total`) can create differences that aren't real, and how decomposing `total` into its components resolves them.

### Limitations

- The data covers a single month (March 2019) and 6,433 rides — a sample, not the full population — with yellow taxis and Manhattan heavily over-represented.
- Cash tips are not recorded, so no conclusion about overall tipping behavior can be drawn; tip analysis is restricted to card rides.
- Differences between green and yellow taxis may reflect zones served or trip mix rather than rider behavior alone. All findings are associations, not causal claims.
- Speed is derived from straight-line distance and elapsed time, and the flagged/removed rides are defined by simple thresholds.

### Possible Next Steps

- Compare weekday and hour effects after controlling for distance.
- Model the tip decision separately from the tip amount.
- Map zone-level demand and revenue.

## Data

- **Source:** seaborn's built-in `taxis` dataset (6,433 rides, 14 columns; pickups from Feb 28 to Mar 31, 2019).
- **Loading:** the notebook loads the data directly via `seaborn.load_dataset("taxis")` — no external file or download is required.

## Data Quality Issues Found and Corrected

| Issue | Action |
|---|---|
| 72 rides lasting under a minute, 51 with zero distance, 11 with speeds above 80 mph | Removed (84 implausible rides total, 1.3% of data, some overlapping) |
| Missing payment method (44 rides) and pickup/dropoff zone/borough (26–45 rides) | Kept; dropped only from the specific analyses that need those columns |

## Methods

- Feature engineering: `duration_min`, `speed_mph`, `hour`, `weekday`, fixed `surcharges`, `fare_per_mile`, `tip_pct` (credit-card rides only), `has_toll`, `airport_trip`, and `same_borough` flags.
- Decomposition of `total` into fare, tip, tolls, and fixed surcharges to separate real price differences from recording artifacts.
- OLS regression (via `statsmodels`) of fare on duration and distance, compared by R².
- Spearman correlation, Mann–Whitney tests for group comparisons (e.g. tipping by taxi color).
- Revenue concentration analysis (cumulative share of revenue by ranked ride value).

## Tools

- Python
- Pandas
- NumPy
- SciPy (`scipy.stats`)
- statsmodels
- Matplotlib
- Seaborn

## Project Structure

```text
nyc-taxi-rides-analysis/
├── notebooks/
│   └── Taxis_Data_Analysis.ipynb
└── README.md
```

No `datasets/` folder is needed — the notebook loads its data directly from seaborn's built-in datasets.

## How to Run

```bash
git clone <your-repository-url>
cd nyc-taxi-rides-analysis
pip install pandas numpy scipy statsmodels matplotlib seaborn jupyter
jupyter notebook
```

Open `notebooks/Taxis_Data_Analysis.ipynb` and run the cells sequentially.
