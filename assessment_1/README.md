# Assessment 1 — Hotel Bookings Data Analysis

**Role:** Business Analyst Intern | **Company:** TravClan (B2B Travel Tech)  
**Dataset:** `Hotel_bookings_final.csv` — 30,000 booking records from an online travel platform

---

## What Was Done

End-to-end analysis of 30,000 hotel booking transactions covering booking trends, cancellation behaviour, and channel performance. Built in Python (Google Colab) with a companion Looker Studio dashboard for interactive exploration.

---

## Data Preparation

Before analysis, the dataset was audited for completeness:

- **72.2%** Confirmed (21,672) | **20.2%** Cancelled (6,070) | **7.5%** Failed (2,258)
- Check-in/check-out dates missing for 5,468 records — all belonged to Cancelled/Failed bookings (no stay occurred), left as-is intentionally
- Markup across all bookings is essentially constant at ~30.9% — profitability differences across segments come from volume and conversion, not pricing
- `customer_id` has only 499 distinct values across 30,000 records — dataset appears synthetic or aggregated; noted but does not affect channel/room/seasonal analysis

---

## Key Findings

**Cancellations spike in 4 specific months**
- July 2024: 30.3% | August 2024: 28.8% | December 2024: 25.8% | January 2025: 23.7%
- All other months sit at 16–18% baseline
- Jul/Aug pattern: speculative multi-booking during summer planning window (above-average booking values suggest high-demand period)
- Dec/Jan pattern: holiday-disruption cancellations (below-average booking values, different mechanism)

**Standard rooms cancel far more than Deluxe/Suite at the same price**
- Standard: 23.3% | Deluxe: 16.0% | Suite: 18.0%
- Average booking value is nearly identical across all three (~₹24,978–25,147)
- Conclusion: behavioural, not financial — Standard is a default/fallback choice, easier to walk away from

**Channel performance varies significantly**

| Channel | Avg. Booking Value | Cancelled % | Failed % | Confirmed % |
|---|---|---|---|---|
| Web | ₹28,191 | 17.6% | 5.2% | 77.2% |
| Travel Agent | ₹24,454 | 27.9% | 5.9% | 66.2% |
| Mobile App | ₹21,351 | 21.6% | 10.9% | 67.5% |

- Mobile App's 10.9% failure rate (~2x other channels) signals a technical payment/checkout issue, not user intent
- Travel Agent's 27.9% cancellation rate is consistent with provisional hold behaviour (multiple bookings placed, only one confirmed)

**Star rating shows almost no relationship with booking value** — 2-star and 5-star properties generate similar revenue per booking, suggesting pricing is driven by city/room type/season rather than star classification

---

## Business Recommendations

- Apply stricter cancellation terms or partial non-refundable deposits for Jul/Aug/Dec/Jan check-ins
- Require deposit or explicit confirmation for Travel Agent bookings before marking as held
- Fix Mobile App payment checkout flow — 10.9% failure rate is a technical problem
- Shift demand from Standard to Deluxe/Suite (same price, lower cancellation) via small incentives
- Prioritise Web for marketing spend — best value and conversion of all channels
- Redesign coupons for post-stay redemption — current coupons show zero effect on cancellation (20.8% vs 20.1%)

---

## Looker Studio Dashboard

Built as interactive companion to the written report. Covers:
- KPI overview (total bookings, cancellation rate, avg ADR)
- Monthly cancellation trend with year filter
- Cancellation rate by market segment and booking channel
- Hotel type comparison (City vs Resort)
- Average Daily Rate by month

---

## Files

```
assessment-1-hotel-bookings/
├── README.md
├── hotel_bookings_analysis.ipynb     ← Google Colab notebook (full analysis)
├── hotel_bookings_analysis_report.pdf ← written report with findings & recommendations
└── data/
    └── Hotel_bookings_final.csv      ← dataset (or link to source if large)
```

---

## Stack

Python · Pandas · Matplotlib · Seaborn · Google Colab · Google Looker Studio
