# WageGuard AI — Phase 1 Submission
**Parametric Income Protection for India's Q-Commerce Delivery Partners**

**Demo Video:** https://youtu.be/6VnSHi8V7rE?si=GQTIgD_ShGTu7vmJ

## Inspiration
Meet Arjun. He logs into his delivery app at 6:00 AM in Koramangala, Bengaluru. As a Q-commerce rider, his livelihood depends on executing 10-minute deliveries within a hyper-local 2km radius. By 8:30 AM, he’s on track for a great day. Then, an unseasonal cloudburst hits.

Within an hour, localized flooding forces the platform to suspend operations in his specific zone. Arjun earns nothing for the rest of the day. Because he operates in the gig economy, he has no paid time off and no safety net. His weekly projected income of ₹4,500 drops instantly. 

India's platform-based delivery partners lose up to 30% of their monthly income to uncontrollable external disruptions: flash floods, extreme heatwaves, and sudden civic curfews. Traditional insurance is useless here—it covers hospital bills and vehicle repairs, not lost hours. 

WageGuard AI was built to answer one question: what if a worker's income was protected the moment a severe disruption was detected, with zero paperwork and an instant payout?

## What It Does
[cite_start]WageGuard AI is an AI-enabled parametric insurance platform built specifically to safeguard gig workers against income loss caused by external disruptions such as extreme weather or environmental conditions[cite: 14]. [cite_start]We strictly exclude coverage for health, life, accidents, or vehicle repairs[cite: 17]; this is purely an **income safety net**. 

[cite_start]Because gig workers operate on weekly payout cycles, our financial model is structured on a Weekly pricing basis to match the typical payout cycle of a gig worker[cite: 18]. 



For Arjun, the flow looks like this:
1. He registers on the WageGuard mobile web app, linking his operating zone (Koramangala).
2. [cite_start]The AI Risk Engine evaluates hyper-local weather forecasts and calculates a dynamic weekly premium [cite: 35] for the upcoming week.
3. He subscribes to the weekly policy.
4. [cite_start]When continuous rainfall in Koramangala crosses 50mm/hr, the real-time trigger monitoring [cite: 46] [cite_start]system detects it and executes automatic claim initiation for identified disruptions[cite: 47]. 
5. Arjun’s app pings a success notification: "₹500 Parametric Payout Approved." [cite_start]The system handles instant payout processing for lost income[cite: 49]. 

**Disruption Triggers Supported:**
| Trigger | Threshold | Payout Rate |
| :--- | :--- | :--- |
| **Heavy Rainfall** | > 50 mm / hr | 50% of daily average |
| **Extreme Heat** | > 45°C | 35% of daily average |
| **Civic Curfew** | Zone Locked | 100% of daily average |

## How We Built It

### Backend — Python (FastAPI) + SQL (PostgreSQL)
We architected a high-performance backend using Python to handle real-time API polling and complex data processing. The relational data is managed via a robust SQL database designed from scratch to ensure strict ACID compliance for financial transactions. 

The data model is structured around core tables:
* `riders`: Stores identity, primary delivery zone, and historical earnings.
* `weekly_policies`: Tracks active weekly subscriptions, dynamic premiums, and coverage limits.
* `parametric_events`: An immutable log of triggered external disruptions.
* `payout_claims`: Records automated payouts, timestamps, and fraud validation scores.

### Risk Engine — Predictive ML Modeling
Traditional static premiums fail in the gig economy. Our risk engine utilizes machine learning (leveraging Python libraries like Scikit-Learn) to calculate the dynamic premium. 



The model analyzes historical weather patterns and traffic/civic disruption data for the rider's specific micro-zone. It predicts the probability of a disruption occurring in the upcoming 7 days. 
* **Clear Week Forecast:** Premium sits at the base rate (e.g., ₹20/week).
* **High-Risk Forecast (Monsoon):** The ML model scales the premium up dynamically (e.g., ₹35/week) to balance the liquidity pool.

### Frontend — Mobile-First Web App (PWA)
We built a responsive, mobile-first web interface using HTML/CSS/JS with Tailwind CSS. Q-commerce riders cannot afford heavy app downloads; our PWA provides a seamless, zero-friction UI for checking active policies and viewing instant payout histories.

## Challenges We Ran Into
**1. Handling Asynchronous API Triggers vs. Synchronous SQL Transactions**
Integrating live weather APIs (OpenWeatherMap) to trigger financial payouts created a race condition. When a storm threshold was breached, the system attempted to generate hundreds of automated claims simultaneously. This initially caused SQL transaction deadlocks. We resolved this by implementing a queue-based processing logic in our Python backend, batching the automated SQL `INSERT` commands to ensure ACID compliance without locking the database.

**2. Enforcing Strict Weekly Boundaries**
[cite_start]Because the usecase mandates a strict weekly pricing model[cite: 69], we had to build complex time-zone aware logic in Python. Ensuring that policies instantly expire at 11:59 PM on Sunday and immediately roll over to the newly calculated AI premium for Monday morning required precise datetime handling and scheduled cron-job simulations.

## Accomplishments That We're Proud Of
* **Live AI Integration:** We didn't just hardcode numbers. We successfully architected a data analytics pipeline that dynamically calculates risk and adjusts pricing on the fly.
* **Zero-Touch Parametric Claims:** We shipped a fully automated claim engine. When a disruption event hits, the system queries active policies, runs fraud checks, and commits payouts in a single transaction. Workers do not file anything.
* **Rapid Execution:** Tapping into our experience building end-to-end projects rapidly under extreme pressure, we successfully planned, architected, and deployed a functional frontend simulation and core backend schema before the deadline.

## What We Learned
We learned that parametric insurance is incredibly vulnerable to location spoofing. If payouts are triggered automatically by zone, a bad actor can fake their GPS coordinates to appear inside a flooded area while physically sitting at home. This realization forced us to heavily lean into advanced data analytics to design a robust fraud defense mechanism. Furthermore, we gained deep insights into structuring scalable relational databases for time-sensitive financial tech.

## Adversarial Defense & Anti-Spoofing Strategy
Protecting the platform's liquidity from exploitation is critical. [cite_start]By adapting architecture concepts from offline AI-driven fraud defense platforms, we designed a multi-layered intelligent fraud detection [cite: 39] pipeline. 

Every automated claim passes through this engine before the SQL transaction commits:
* [cite_start]**Layer 1: GPS Mismatch Validation:** Executes location and activity validation [cite: 42] by checking the Haversine distance between the rider's live ping and the claimed disruption zone. 
* [cite_start]**Layer 2: Velocity & Trajectory Checks:** A system for anomaly detection in claims [cite: 40] reviews the rider's recent movement. If a rider claims to be halted by a flood, but their GPS shows them moving at 40km/h across town, the claim is instantly flagged.
* [cite_start]**Layer 3: Duplicate Prevention:** Hashes the `rider_id`, `event_id`, and `calendar_day` to guarantee duplicate claim prevention [cite: 43] so a rider cannot receive double payouts for the same storm.

Claims generating a high fraud score are automatically rejected, keeping the system solvent. 

## What's Next for WageGuard AI
* **Mock UPI Integration:** Transitioning our simulated payouts into a live sandbox environment (like Razorpay or Stripe) to demonstrate real-time wallet crediting.
* **Expanded Predictive Analytics:** Ingesting deeper civic datasets to accurately predict and price the risk of social disruptions (strikes/curfews).
* **Automated Cron Jobs:** Implementing APScheduler to fully automate the polling of our external APIs every 15 minutes, making the backend entirely autonomous.

## Built With
* **Backend:** Python, FastAPI
* **Database:** SQL (PostgreSQL)
* **AI/ML:** Scikit-Learn, Pandas
* **Frontend:** HTML, CSS, JavaScript, Tailwind CSS
* **APIs:** OpenWeatherMap fix these guide me so that i can drecitly cpoy paste and add everything works ok and i can view in prveiw
