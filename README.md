# ✈️ Airline Passenger Operations & Disruption Analytics (DuckDB SQL)

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![DuckDB](https://img.shields.io/badge/DuckDB-0.9%2B-duckyellow?logo=duckdb)
![Pandas](https://img.shields.io/badge/Pandas-2.0%2B-darkblue)
![Seaborn](https://img.shields.io/badge/Seaborn-0.12%2B-teal)
![License](https://img.shields.io/badge/License-MIT-yellow)

An end-to-end operational SQL case study simulating real-time airline passenger disruptions, connection risks, and frontline service desk surge analytics using **DuckDB**, **Python**, and **ANSI SQL Window Functions**.

---

## 📌 Executive Summary

Flight operations generate massive streams of transactional movement data, but standard public datasets lack customer-centric relational structures. This project overlays a synthetic IATA-compliant passenger schema (PNRs, loyalty tiers, checked bags, special assistance) onto historical flight operational feeds (`nycflights13`).

Using DuckDB's columnar ANSI SQL engine, this repository executes advanced operational analytics to:
1. **Predict Missed Connections:** Flag tight transit windows (<45 min) before aircraft touchdown using `LEAD()` window functions.
2. **Optimize Service Desk Labor:** Group hourly delay cascades with `DATE_TRUNC()` to calculate dynamic agent labor requirements.
3. **Automate Priority Rebooking:** Construct a weighted `DENSE_RANK()` standby engine prioritizing high-value VIP loyalists (`Million Miler` / `Morning Calm`).

---

## 🛠️ Tech Stack & Tools

| Category | Technology | Usage / Purpose |
| :--- | :--- | :--- |
| **SQL Engine** | DuckDB | In-memory columnar SQL database engine |
| **Language** | Python 3.13 | Scripting, data orchestration, and visualization |
| **Data Manipulation** | Pandas | Dataframe handling and result extraction |
| **Visualization** | Matplotlib & Seaborn | Multi-panel operational executive dashboards |
| **Data Source** | Rdatasets (`nycflights13`) | Raw transactional flight operations data |

---

## 📂 Repository Structure

```
airline-disruption-analytics-sql/
│
├── airline_disruption_analytics.ipynb   # Main end-to-end Jupyter Notebook (Steps 1–7)
├── README.md                            # Comprehensive project documentation
├── requirements.txt                     # Dependencies (duckdb, pandas, seaborn, etc.)
└── LICENSE                              # MIT License
```

---

## 🏗️ Data Architecture & Pipeline

```
+------------------------+      +------------------------+
|   stg_flights (CSV)    |      |     dim_passengers     |
|  Operational Datasets  |      |   Synthetic 6-Char PNR |
+-----------+------------+      +-----------+------------+
            |                               |
            +---------------+---------------+
                            |
                            ▼
           +--------------------------------+
           |   fact_booking_itineraries     |
           |    1-Leg & 2-Leg Routes        |
           +----------------+---------------+
                            |
                            ▼
           +--------------------------------+
           |  Analytical Engines (Step 5)   |
           |  • LEAD() Connection Risks     |
           |  • DATE_TRUNC() Desk Surges    |
           |  • DENSE_RANK() VIP Priority   |
           +--------------------------------+
```

---

## 🚀 Key SQL Analytics Modules

### 1. Connection Risk Engine (`LEAD()`)
Calculates actual available transit time between inbound arrival and outbound departure legs, assigning real-time risk categories to connecting passengers.
```sql
LEAD(TRY_CAST(f.flight_date || ' ' || SUBSTR(f.sched_dep_time_raw, 1, 2) || ':' || SUBSTR(f.sched_dep_time_raw, 3, 2) AS TIMESTAMP)) 
    OVER (PARTITION BY b.pnr_code ORDER BY b.leg_sequence) AS leg2_sched_dep_time
```

### 2. Service Desk Surge & Staffing Load (`DATE_TRUNC()`)
Groups delay arrivals into 1-hour operational windows and calculates estimated customer service counter hours needed (assuming 6 minutes per rebooking).
```sql
DATE_TRUNC('hour', TRY_CAST(f.flight_date || ' ' || SUBSTR(f.actual_arr_time_raw, 1, 2) || ':' || SUBSTR(f.actual_arr_time_raw, 3, 2) AS TIMESTAMP)) AS arrival_hour,
ROUND((impacted_passengers * 6.0) / 60.0, 1) AS estimated_desk_agent_hours_required
```

### 3. Automated Standby Priority Engine (`DENSE_RANK()`)
Ranks disrupted passengers dynamically by loyalty tier weightings and special handling requirements.
```sql
DENSE_RANK() OVER (
    PARTITION BY impacted_flight, flight_date 
    ORDER BY priority_score DESC, pnr_code ASC
) AS standby_rebooking_rank
```

---

## 📈 Business Impact & Metrics Summary

| Query Module | Primary SQL Function | Business Metric | Strategic Outcome |
| :--- | :--- | :--- | :--- |
| **5.1 Connection Risk** | `LEAD()` | Connection Success Rate / DOT Penalties | Enables proactive rebooking before inbound aircraft landing. |
| **5.2 Surge Desk Load** | `DATE_TRUNC()` | Agent Labor Cost / Passenger Wait Time | Reallocates terminal staff dynamically to peak disruption hubs. |
| **5.3 VIP Priority Queue** | `DENSE_RANK()` | VIP Churn Rate / Customer Lifetime Value | Protects top 20% high-value revenue drivers during irregular ops. |

---

## 💻 How to Run

1. Clone this repository:
   ```bash
   git clone [https://github.com/your-username/airline-disruption-analytics-sql.git](https://github.com/your-username/airline-disruption-analytics-sql.git)
   cd airline-disruption-analytics-sql
   ```
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Open and run the Jupyter Notebook:
   ```bash
   jupyter notebook airline_disruption_analytics.ipynb
   ```

---

## 📬 Contact & Connect

Created as part of an advanced SQL & Data Analytics portfolio project.

* **LinkedIn:** [Mohd Azim Bin Mohd Ali](https://www.linkedin.com/in/mohd-azim-mohd-ali/)
* **GitHub:** [azimgilo](https://github.com/azimgilo)
* **Email:** mohdazimali@gmail.com

---

## 📄 License
Distributed under the MIT License. See `LICENSE` for details.