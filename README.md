# Supply Chain Delay & Carrier Performance Analysis

## 📌 Project Overview & Objective
Global supply chain operations frequently encounter unexpected bottlenecks that delay deliveries and increase overhead costs. The goal of this analysis is to profile recent logistics pipeline data using Python to isolate underperforming carriers, identify geographic delay patterns, and deliver data-driven operational recommendations.

## 🛠️ Tech Stack Used
* **Python** (Pandas for data profiling, filtering, and aggregation)
* **Matplotlib & Seaborn** (For professional, statistical data visualization)
* **Jupyter Notebooks** (For interactive development and documentation)

## 🧹 Data Profiling & Advanced Quality Assurance
Real-world datasets are inherently flawed. During the initial data profiling phase of the logistics pipeline, I conducted rigorous data validation and uncovered three critical anomalies that threatened downstream analytical integrity. Below is the documentation of how these issues were programmatically diagnosed and resolved:

### 🔍 Hurdle 1: The Mixed-Type Feature Trap
* **The Issue:** The critical metric column `delay_hours_avg` was imported as an `object` (text) data type rather than a float, blocking all downstream statistical calculations (`.mean()`, `.median()`). 
* **The Investigation:** Running a distribution analysis revealed that human logging errors had introduced hardcoded string indicators like `'On-Time'` and `'Delayed'` directly into an otherwise numerical schema.
* **The Resolution:** I isolated the string variants and applied Pandas' `pd.to_numeric()` function with `errors='coerce'`. This seamlessly separated the categorical delivery statuses from the numeric hours, mapping the strings to an independent feature column without corrupting or dropping core operational metrics.

### 🔍 Hurdle 2: The Left-Join Dimension Explosion
* **The Issue:** Merging the core master database with the secondary tracking logs via a `LEFT JOIN` resulted in an unexpected wall of missing values (`NaN`) across the entire secondary dataset.
* **The Investigation:** Rather than assuming a broken join key, I audited the temporal boundaries of both tables using `.min()` and `.max()`. The audit revealed a massive scope mismatch: the core database spanned two full calendar years, while the secondary operational tracking logs were strictly constrained to a 25-day operational window in June 2024.
* **The Resolution:** I engineered a datetime mask to truncate the analysis window precisely to the overlapping June horizon. This eradicated the artificial null values and saved the system from processing millions of unmatchable out-of-scope rows.

### 🔍 Hurdle 3: The "Ghost Data" Uniformity Alarm
* **The Issue:** While calculating baseline performance benchmarks, the average `customs_clearance_time_days` across four highly competitive shipping vendors returned identical values down to the sixth decimal place.
* **The Investigation:** Recognizing that real-world operational variance makes identical performance statistically impossible, I deployed `.value_counts()` and structural grouping to profile the distributions. I discovered that every carrier shared the exact same distribution of clearance times, confirming the column was filled with synthetic/mock data.
* **The Resolution:** I formally flagged the feature as non-viable for operational insights. By stripping this "noise" out of the final scope, I successfully shifted the project focus to regional operational delays where true statistical variance existed.

## 📊 Key Insights & Visualizations

<img width="1180" height="558" alt="Screenshot 2026-07-07 at 6 40 19 PM" src="https://github.com/user-attachments/assets/c18633a1-65a5-4c1f-892f-ee4fe710159d" />


The polished analysis uncovered clear, actionable operational insights regarding shipping efficiency:
* **Vendor Bottlenecks:** `OceanExpress` was isolated as a severe operational outlier, exhibiting nearly double the average delay hours of competing carriers.
* **Geographic Constraints:** Cross-tabulating carriers by region proved that this delay issue is entirely regional—confined strictly to the **Asia-Pacific** sector where `OceanExpress` operates.
* **Temporal Patterns:** Plotting daily averages across June revealed a highly rhythmic, wave-like zigzag pattern fluctuating between **3.8 and 5.0 hours**. This confirms an ongoing, systemic operational loop (such as weekend port congestion or fixed cargo backlogs) rather than an isolated, freak incident.

  <img width="1160" height="522" alt="Screenshot 2026-07-07 at 6 43 41 PM" src="https://github.com/user-attachments/assets/3316a4bf-fdec-440d-b7c4-99900d3dd22c" />


## 🚀 Strategic Business Recommendations
Based on the findings, the following actions are recommended for the logistics management team:
1. **Targeted Operational Audit:** Initiate a process audit on `OceanExpress` operations specifically within Asia-Pacific ports to identify the root cause of the cyclical delay pattern.
2. **Vendor Diversification:** Introduce a secondary logistics vendor into the Asia-Pacific corridor to mitigate dependency on `OceanExpress`, introduce cost/performance competition, and safeguard supply chain timelines.
