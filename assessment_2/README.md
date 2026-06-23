# Assessment 2 — Business Analyst Intern Technical Assessment

**Role:** Business Analyst Intern | **Company:** TravClan (B2B Travel Tech)  
**Dataset:** `hotel_bookings.csv` — 12,000 booking records  
**Sections:** Data Quality · Cancellation Analysis · SQL · Weather API Mini-Project

---

## Structure

```
assessment-2-ba-intern/
├── README.md
├── answers.docx                        ← Sections 1–3 answers with full results
├── sql_section3.docx                   ← SQL schema DDL + query text
├── project/
│   ├── weather_cancellation_analyzer.ipynb
│   └── section4_project.docx          ← Section 4 README + build summary
└── data/
    └── hotel_bookings.csv
```

---

## Section 1 — Data Quality Checks

Dataset: 12,000 rows. Key issues identified and resolved before any analysis:

- **Loyalty tier parsing trap** — `customer_loyalty_tier` contains the literal string `'None'` as a valid tier value. Default pandas reader silently converts it to NaN (affects 5,571 rows, 46.4%). Fixed with `keep_default_na=False, na_values=['']`.
- **Invalid stays** — 120 bookings (1.0%) have `checkout_date ≤ checkin_date`. Excluded from revenue and rate calculations.
- **Zero-room bookings** — excluded from realized revenue (always carry `total_amount = 0`, erroneous entries).
- **Review scale mismatch** — Corporate reviews are on a 1–10 scale; Individual and Group use 1–5. Corporate ratings divided by 2 before any cross-segment comparison.
- **50 reviews on cancelled bookings** — logically impossible, flagged.
- **163 bookings pre-date customer signup** — temporal integrity issue, noted as a limitation.

**Realized revenue for Luxury properties (after all filters): ₹90,694,052.93**  
Unfiltered figure: ₹117,431,484.47 — a 29.5% overstatement, confirming the filtering logic.

**Review means by segment (normalized):** Corporate 3.62 | Group 3.77 | Individual 3.77 — nearly identical across segments; the gap only appears if Corporate ratings are not normalized.

---

## Section 2 — Cancellation Analysis

Platform-wide cancellation rate: **19.15%** (on 11,821 clean bookings after excluding invalid stays and zero-room rows).

**Cancellation rate by channel × lead-time bucket:**

| Channel | 0–7 days | 8–30 days | 31–60 days | 60+ days |
|---|---|---|---|---|
| Direct Website | 12.5% | 13.5% | 18.3% | 20.3% |
| Travel Agent | 16.0% | 13.9% | 21.5% | 18.1% |
| Corporate Portal | 18.5% | 19.3% | 24.3% | 20.4% |
| OTA | 20.3% | 19.5% | **27.3%** | 24.9% |

- **Booking channel is the dominant driver** — OTA highest in every bucket, Direct Website lowest in every bucket
- **OTA × 31–60 days** is the single worst slice: 943 bookings, 257 cancellations, 27.3% rate
- Lead time is a secondary amplifying factor — cancellation peaks in the 31–60 day window across most channels

**Recommendation:** A 15–20% non-refundable deposit applied only to OTA bookings with 31–60 day lead time. Expected platform-wide impact: **0.4–0.6 percentage points** reduction. Reaching the board's 5pp target requires stacking additional interventions across other high-rate slices (e.g. Corporate Portal × 31–60 at 24.3%).

---

## Section 3 — SQL Schema Design

Full DDL and query text in `sql_section3.docx`. Brief summary:

**4 normalized tables:** `customers`, `properties`, `bookings`, `reviews`

Key design decisions:
- `adr` and `total_amount` use `DECIMAL`, not `FLOAT` — money must never use floating point
- `CHECK (checkout_date > checkin_date)` enforces the invalid-stay rule at schema level
- `properties.UNIQUE(property_name, property_city, property_id)` — property names repeat across cities with different IDs; `property_id` is the true uniqueness anchor
- `reviews` is a separate table with `UNIQUE(booking_id)` — optional 1:1 relationship; application logic prevents reviews on Cancelled bookings
- `customer_loyalty_tier` explicitly allows the literal string `'None'` as a default rather than NULL

**Query results:**
- A-Q1 (highest realized revenue per city): 10 rows. Notable: 'The Grand Plaza' tops both Bangalore and Kochi as two distinct properties sharing a name — confirms grouping by `property_id` not `property_name` was essential.
- A-Q2 (customers with avg booking gap < 30 days): **445 customers**

Dialect: SQLite via `sqlite3` + pandas in Google Colab. `julianday()` used for date subtraction (no native date arithmetic in SQLite).

---

## Section 4 — Weather API Mini-Project

**What it does:** Fetches real 2024 daily weather for Goa and Manali from the Open-Meteo Historical Weather API, merges onto each booking's check-in date, and tests whether heavy rain or extreme temperature correlates with cancellation/no-show rates.

**API:** Open-Meteo Historical Weather API (`archive-api.open-meteo.com/v1/era5`)  
**Calls made:** 2 total — one per city, full-year date range (not per-booking or per-date)  
**Scope:** 1,604 bookings (891 Goa, 713 Manali) | **Merge:** 0 unmatched rows

**Weather bucketing:**
- Heavy Rain: precipitation ≥ 10mm (near dataset's 75th percentile)
- Extreme Temp: top/bottom 10th percentile computed **per city separately** — Goa and Manali have non-overlapping temperature ranges (4.7°C–39.4°C combined), a shared cutoff would misclassify normal Manali cold days as extreme

**Key finding:**

| City | Weather | Bookings | Cancel/No-Show Rate |
|---|---|---|---|
| Goa | Heavy Rain | 253 | **39.5%** |
| Goa | Normal | 551 | 25.8% |
| Manali | Heavy Rain | 186 | 24.2% |
| Manali | Normal | 446 | 23.3% |

Heavy rain on check-in day is associated with a **+13.7pp cancellation spike in Goa** but has no meaningful effect in Manali (+0.9pp, noise-level). Goa's appeal (beach, water sports, outdoor dining) is directly disrupted by rain; Manali's (mountains, snow, scenery) is not. This would have been invisible without merging weather data — the raw dataset has no way to surface it.

**Implication:** A Goa-specific rain-contingent rebooking policy would be more targeted than a platform-wide weather policy.

---

## Stack

Python · Pandas · SQLite (sqlite3) · requests · Open-Meteo API · Google Colab
