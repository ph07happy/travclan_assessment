# TravClan — Business Analyst Intern Assessments

**Applicant:** Happy Kumar  
**Role:** Business Analyst Intern  
**Company:** TravClan (B2B Travel Tech Platform)

---

## Repository Structure

```
travclan_assessment/
├── assessment-1-hotel-bookings/     ← Booking trends & cancellation analysis (30,000 records)
└── assessment-2-ba-intern/          ← Technical assessment: Data Quality, SQL, Cancellation, Weather API
```

---

## Assessment 1 — Hotel Bookings Analysis

Analysis of 30,000 hotel booking transactions covering channel performance, cancellation behaviour, room type trends, and seasonal patterns. Delivered as a written report with a Looker Studio dashboard.

**Key finding:** Cancellations spike to 25–30% in 4 specific months (Jul, Aug, Dec, Jan) while sitting at 16–18% the rest of the year. Standard rooms cancel at 23.3% vs 16% for Deluxe — at identical price points. Mobile App has a 10.9% failure rate vs 5–6% on other channels, pointing to a technical checkout issue.

**Stack:** Python · Pandas · Matplotlib · Google Colab · Google Looker Studio

→ [See assessment_1/README.md](./assessment_1/README.md)

---

## Assessment 2 — BA Intern Technical Assessment

Four-section structured assessment covering data quality auditing, cancellation root cause analysis, SQL schema design, and a live weather API mini-project.

**Key finding:** OTA bookings with 31–60 day lead time are the single highest-risk cancellation slice (27.3%). Weather API integration revealed heavy rain on check-in day causes a +13.7pp cancellation spike in Goa specifically — with no meaningful effect in Manali — a pattern invisible without merging external data.

**Stack:** Python · Pandas · SQLite · requests · Open-Meteo API · Google Colab

→ [See assessment_2/README.md](./assessment_2/README.md)
