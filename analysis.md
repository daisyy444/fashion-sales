# Fashion Sales — Analysis (2024–2025)

**Dataset:** `fashion_sales.csv` — 240 rows = 24 months (Jan 2024 – Dec 2025) × 5 categories × 2 channels, 14 columns.

All figures below are computed from the file. Correlations are Pearson *r* across the 240 aggregate records (an "ecological" correlation — see caveats at the end).

## Column reference

| Column | Meaning |
|---|---|
| `month` | Calendar month as `YYYY-MM` |
| `year` | Year (2024 or 2025) — redundant with `month` |
| `month_num` | Month number 1–12 — redundant with `month` |
| `category` | Product category (5 values) |
| `channel` | `Online` or `Store` |
| `avg_temp_F` | Average monthly temperature (°F); same for all rows in a month |
| `list_price` | Regular list price (fixed per category: $35 / $70 / $85 / $110 / $180) |
| `discount_pct` | Discount in whole percent (0, 10, 15, 20, 25, 30, 40) |
| `sale_price` | Actual selling price |
| `ad_spend` | Advertising spend for that category/channel/month |
| `avg_rating` | Average customer rating (3.4–5.0) |
| `units_sold` | Units sold that month |
| `revenue` | Total revenue |
| `return_rate` | Share of units returned (0.01–0.356) |

**Data quality:** no missing values, no duplicate (month, category, channel) keys. One caveat — `sale_price` does **not** equal `list_price × (1 − discount_pct/100)`; 126 of 240 rows deviate by more than 5%, and 43 rows with 0% discount have a sale price that isn't the list price. `revenue = sale_price × units_sold` holds exactly for all rows.

---

## 1. Seasonal patterns

Temperature cycles from Jan 33.8°F to Jul 79.2°F. Two categories are strongly seasonal; three are holiday-driven.

| Category | Peak month | Trough month | Peak/trough ratio | Peak vs trough units |
|---|---|---:|---:|---|
| Dresses | Jul 2025 ($231,398) | Jan 2025 ($58,455) | **4.0×** | 2,160 (Jul) vs 479 (Feb) |
| Outerwear | Dec 2025 ($298,278) | Jun 2024 ($81,290) | **3.7×** | 1,243 (Dec) vs 312 (Jun) |
| Accessories | Nov 2025 ($79,538) | May 2025 ($40,412) | 2.0× | 1,432 (Dec) vs 811 (Feb) |
| Denim | Nov 2024 ($118,810) | Apr 2024 ($61,659) | 1.9× | 1,184 (Dec) vs 639 (Apr) |
| Footwear | Dec 2025 ($166,873) | Feb 2024 ($88,334) | 1.9× | 1,185 (Dec) vs 497 (Apr) |

- Dresses sell in the heat; Outerwear sells in the cold.
- Denim, Footwear and Accessories share a "holiday" shape — biggest months Nov–Dec with a secondary January bump; troughs in spring.
- Nov + Dec are the two biggest revenue months in both years (~21% of all revenue).

---

## 2. 2024 vs 2025

| Metric | 2024 | 2025 | Change |
|---|---:|---:|---:|
| Revenue | $6,406,734 | $6,813,748 | **+6.4%** |
| Units | 97,712 | 104,788 | **+7.2%** |
| Ad spend | $1,098,180 | $1,239,350 | +12.9% |
| ROAS (rev per $1 ad) | 5.83 | 5.50 | **−5.7%** |
| Return rate | 0.172 | 0.174 | +0.2 pts |
| Avg rating | 4.237 | 4.200 | **−0.04** |
| Avg discount | 22.9% | 22.2% | −0.7 pts |

- Growth is real but less efficient: units +7.2% while ad spend +12.9%, so ROAS fell 5.83 → 5.50.
- Online is the growth engine: Online revenue +10.0% ($3.317M → $3.650M) vs Store +2.4% ($3.090M → $3.164M).
- Every category grew. Dresses led (+8.7% revenue, +8.9% units); Accessories +6.7% / +9.0%; Outerwear +5.5%; Footwear +5.1%; Denim +6.1%.
- Best same-month gains: Aug +23.5%, Jun +15.6%, Apr +12.1%, Jan +10.0%. Only declines: Oct −5.1% and Nov −4.7% (tough holiday comps).
- Return rates flat YoY (Online 0.247 → 0.246; Store 0.097 → 0.103); ratings slipped (Online 4.157 → 4.142; Store 4.318 → 4.258).

---

## 3. How temperature affects each category

| Category | r(temp vs units) | Slope | R² | r(temp vs revenue) |
|---|---:|---:|---:|---:|
| Outerwear | **−0.771** | −16.3 units/°F | 0.60 | **−0.858** |
| Dresses | **+0.621** | +18.6 units/°F | 0.39 | **+0.796** |
| Denim | −0.274 | −4.4 | 0.07 | −0.249 |
| Accessories | −0.251 | −3.9 | 0.06 | −0.270 |
| Footwear | −0.215 | −3.2 | 0.05 | −0.059 |

- Temperature is the dominant driver for the two anchors. Outerwear loses ~16 units per +1°F (strongest relationship in the dataset); Dresses gain ~19 units per +1°F.
- The other three categories are nearly temperature-insensitive (R² ≤ 0.07); their swings come from holidays.

---

## 4. Do bigger discounts sell more units?

Yes — clearly, and consistently within every category.

| Scope | r(discount vs units) | Slope (units per +1pp) | R² |
|---|---:|---:|---:|
| All rows | **+0.513** | +13.3 | 0.26 |
| Denim | +0.748 | +13.6 | 0.56 |
| Footwear | +0.694 | +12.9 | 0.48 |
| Outerwear | +0.569 | +13.7 | 0.32 |
| Dresses | +0.550 | +18.8 | 0.30 |
| Accessories | +0.552 | +9.9 | 0.30 |
| Online | +0.587 | — | — |
| Store | +0.454 | — | — |

Mean units by discount level:

| Discount | 0% | 10% | 15% | 20% | 25% | 30% | 40% |
|---|---:|---:|---:|---:|---:|---:|---:|
| Mean units | 586 | 607 | 812 | 784 | 802 | 911 | **1,129** |

- 40% off sells ~1.9× the units of 0% off (1,129 vs 586).
- Strongest in Denim and Footwear (R² ≈ 0.5–0.6); holds in both channels.
- Confounding caution: discounts are heaviest in peak/clearance months (Jan 32.5%, Jul 33.5%, Dec 32.5%; lightest Feb 13.5%, Sep 14.0%), so part of the lift is seasonality. But discount vs temperature is only r = −0.14, and the effect survives within every category.
- Price sensitivity elsewhere: units vs sale price r = −0.52.

---

## 5. Does ad spending pay off?

Only weakly — and unevenly.

| Relationship | r |
|---|---:|
| ad_spend vs revenue | +0.208 |
| ad_spend vs units | +0.252 |
| ad_spend vs discount | −0.002 |

- Simple regression: $1.23 revenue per $1 ad spend, R² = 0.04 (ad spend explains ~4% of revenue variation).
- ROAS by category: Outerwear **8.52**, Dresses 6.57, Footwear 6.18, Denim 4.25, Accessories **2.81**.
- ROAS by channel: Store **6.27** vs Online **5.20**.
- Within-category ad↔revenue correlations: Footwear +0.61, Denim +0.56, Dresses +0.37, Accessories +0.38, Outerwear +0.18.
- Ad spend shows almost no seasonality (monthly averages $8.2k–$10.9k) — it doesn't track demand peaks.

---

## 6. What is related to returns

| Comparison | Value |
|---|---|
| Online return rate | **0.247** |
| Store return rate | **0.100** |
| Footwear (overall) | 0.224 |
| Dresses (overall) | 0.219 |
| Denim (overall) | 0.145 |
| Outerwear (overall) | 0.141 |
| Accessories (overall) | 0.139 |

Category × channel:

| Category | Online | Store |
|---|---:|---:|
| Footwear | **29.9%** | 14.8% |
| Dresses | **28.9%** | 14.8% |
| Denim | 22.3% | 6.7% |
| Accessories | 21.3% | 6.4% |
| Outerwear | 20.9% | 7.2% |

Correlations with `return_rate`:

| Variable | r | Reading |
|---|---:|---|
| avg_rating | **−0.353** | higher returns ↔ lower ratings |
| ad_spend | +0.228 | more spend ↔ slightly more returns |
| revenue | +0.150 | weak |
| discount_pct | −0.102 | essentially no effect |
| units_sold | +0.078 | weak |
| avg_temp_F | −0.004 | none |

- Dominant factor is channel: Online returns are ~2.5× Store, in every category.
- Fit-sensitive items return most (Footwear, Dresses), and only in Online; Store rates cluster at 6–15%.
- Discounting does NOT drive returns (return rate is slightly lower at 40% off than at 0%).
- Returns are flat YoY — structural, not new.

---

## 7. What is related to ratings

Overall mean rating **4.22**, range 3.4–5.0 (tight).

| Group | Avg rating |
|---|---:|
| Store | **4.288** |
| Online | 4.149 |
| Accessories | 4.252 |
| Outerwear | 4.229 |
| Dresses | 4.227 |
| Footwear | 4.202 |
| Denim | 4.183 |

Correlations with `avg_rating`:

| Variable | r | Reading |
|---|---:|---|
| return_rate | **−0.353** | more returns ↔ worse ratings |
| units_sold | +0.132 | weak |
| revenue | +0.112 | weak |
| ad_spend | −0.085 | none |
| discount_pct | −0.013 | none |
| avg_temp_F | +0.004 | none |

- The one real signal is returns (r = −0.35), and it holds within both channels (Online −0.27, Store −0.29).
- Discounting doesn't affect ratings (r = −0.01).
- Category differences are trivial (4.18–4.25).

---

## 8. Correlation matrix (key numeric fields)

| | temp | disc | price | ad | rating | units | rev | return |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| **temp** | 1.00 | −0.14 | 0.06 | 0.04 | 0.00 | −0.08 | −0.12 | 0.00 |
| **disc** | | 1.00 | −0.27 | 0.00 | −0.01 | **0.51** | 0.20 | −0.10 |
| **sale_price** | | | 1.00 | 0.00 | −0.01 | **−0.52** | **0.53** | 0.05 |
| **ad_spend** | | | | 1.00 | −0.08 | 0.25 | 0.21 | 0.23 |
| **rating** | | | | | 1.00 | 0.13 | 0.11 | **−0.35** |
| **units** | | | | | | 1.00 | 0.32 | 0.08 |
| **revenue** | | | | | | | 1.00 | 0.15 |
| **return** | | | | | | | | 1.00 |

Strongest relationships in the dataset, ranked:

1. Temperature ↔ Outerwear revenue: **−0.86**
2. Temperature ↔ Dresses revenue: **+0.80**
3. Temperature ↔ Outerwear units: **−0.77**
4. Discount ↔ Denim units: **+0.75**
5. Discount ↔ Footwear units: **+0.69**
6. Temperature ↔ Dresses units: **+0.62**
7. Sale price ↔ units: **−0.52**
8. Discount ↔ units (all): **+0.51**
9. Rating ↔ returns: **−0.35**

---

## 9. Key findings

1. The business is two seasonal engines plus a holiday block — Outerwear (winter) and Dresses (summer) are weather-driven; Denim/Footwear/Accessories ride Nov–Dec.
2. Growth is healthy but efficiency is slipping — revenue +6.4% while ad spend +12.9% and ROAS fell 5.83 → 5.50, driven by Online.
3. Discounts reliably lift volume (~+13 units per point; 40%-off ≈ 1.9× the units of 0%-off), strongest in Denim and Footwear.
4. Ad spend is a weak lever (r ≈ 0.21, R² = 0.04); Outerwear (ROAS 8.5) is the best place to spend, Accessories (2.8) the worst.
5. Returns are the central problem, structural and online-specific (24.7% Online vs 10.0% Store), worst for fit-sensitive Footwear/Dresses, unchanged YoY, and not caused by discounting.
6. Returns and ratings are linked (r = −0.35), so fixing online fit would likely lift both.

---

## 10. Recommendations

- Fix online fit/sizing for Footwear and Dresses — the single biggest source of returns.
- Shift ad budget toward Outerwear (ROAS 8.5) and away from Accessories (2.8).
- Keep discounting as a volume lever, but time it to the right seasons.
- Investigate Online's falling ad efficiency (ROAS 5.83 → 5.50).
- Manage returns and ratings as one problem — improving fit should lift both.
- Protect holiday Nov–Dec performance, the peak revenue period.

---

## Caveats

- **Data-quality issue:** `sale_price` cannot be reconciled with `list_price × discount_pct/100`, so discount/price conclusions are directional. Revenue itself is internally consistent (`revenue = sale_price × units_sold`).
- **Ecological correlations:** each row is an aggregate, so *r* describes relationships between monthly group averages, not individual customers. Confounding (season, category, price) is present; within-category checks were used where noted.
- **Ad spend ↔ returns (r = +0.23)** likely reflects that Online both spends more and returns more — a channel artifact, not causation.
- Temperature is identical for all rows in a month, so temperature results are effectively a 24-month time series repeated across categories.
