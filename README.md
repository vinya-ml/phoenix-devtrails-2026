# 🔥 Phoenix — Parametric Weather Insurance for Bengaluru's Gig Delivery Workers

> **DEVTrails 2026 | Guidewire University Hackathon**
> Team: Phoenix | Phase 1 Submission | March 20, 2026

---

## Table of Contents

1. [Problem Statement](#1-problem-statement)
2. [Persona-Based Scenarios & Workflow](#2-persona-based-scenarios--workflow)
3. [Weekly Premium Model & Parametric Triggers](#3-weekly-premium-model--parametric-triggers)
4. [Platform Choice: Web App](#4-platform-choice-web-app)
5. [AI/ML Integration](#5-aiml-integration)
6. [Adversarial Defense & Anti-Spoofing Strategy](#6-adversarial-defense--anti-spoofing-strategy)
7. [Tech Stack & Development Plan](#7-tech-stack--development-plan)

---

## 1. Problem Statement

Bengaluru's gig delivery economy employs over 200,000 active delivery workers across platforms like Swiggy, Zomato, and Dunzo. These workers are informal, uninsured, and economically exposed to severe weather events — rain-logged roads, flash floods, and cyclonic winds that are increasingly common in Bengaluru's monsoon season (June–October).

When weather turns dangerous, delivery partners face a brutal dilemma: risk safety for income, or stay home and earn nothing. No traditional insurer covers them. Phoenix solves this with **parametric weather insurance** — automatic, trigger-based payouts that activate the moment verified weather conditions cross a defined threshold, with zero claim paperwork.

---

## 2. Persona-Based Scenarios & Workflow

### Persona 1: Ravi, 28 — Full-time Zomato Delivery Partner, Koramangala

**Scenario:** It's a Tuesday evening. The IMD issues a Red Alert for Bengaluru. Ravi can't safely ride. He opens Phoenix on his browser, sees his policy is active, and receives a ₹350 payout automatically within 2 hours — no form, no call center.

**Workflow:**
1. Ravi registers on Phoenix web app, completes KYC (Aadhaar-linked), and links his UPI ID.
2. He subscribes to the ₹49/week "Basic Monsoon Cover" plan every Monday.
3. Phoenix continuously monitors IMD weather feeds and cross-references hyperlocal rain gauge data for his registered delivery zone (Koramangala, BTM, Indiranagar).
4. When a Red Alert triggers (rainfall ≥ 64.5mm/24hr), Phoenix auto-validates and initiates UPI payout.
5. Ravi gets an SMS + web notification: "₹350 credited. Stay safe."

---

### Persona 2: Meena, 34 — Part-time Dunzo Partner, Whitefield

**Scenario:** Meena only works weekends. She opts for the ₹19/week "Weekend Cover" plan. A storm hits Saturday morning. Phoenix detects the trigger in her zone, but Meena hasn't logged an active shift that day.

**Workflow:**
1. Phoenix checks shift-activity signals (anonymized platform ping data or manual shift declaration).
2. Since Meena declared a Saturday shift during signup, her payout eligibility is confirmed.
3. She receives ₹200 — a lower tier payout reflecting weekend-only coverage.
4. The system logs this as a valid, single-zone, non-anomalous claim.

---

### Persona 3: Arjun, 22 — New User, Attempting First Claim

**Scenario:** Arjun signed up 3 days ago, immediately during a forecasted rain event, with no prior activity history.

**Workflow:**
1. Phoenix flags Arjun's account as **"New Account — Elevated Scrutiny"** (< 7 days old, no historical delivery activity).
2. His claim is not auto-paid. It enters a **manual micro-review queue** (reviewed within 4 hours).
3. A lightweight verification request is sent: "Please confirm your current zone via a selfie with location tag."
4. If verified, payout is processed. If no response in 2 hours, claim is deferred to next review cycle.
5. After 30 days of clean history, Arjun's account graduates to standard auto-payout tier.

---

### Core Application Workflow

```
[User Registration & KYC]
        ↓
[Weekly Plan Selection + UPI Linkage]
        ↓
[Zone Declaration (Delivery Neighborhood)]
        ↓
[Real-Time Weather Monitoring — IMD + Hyperlocal APIs]
        ↓
    [Threshold Crossed?]
       /         \
     YES          NO
      ↓            ↓
[AI Fraud Score   [No Action —
  Check]          Monitoring Continues]
      ↓
  [Score < 0.3?]
   /        \
 YES         NO
  ↓           ↓
[Auto-Payout] [Flag → Human Review Queue]
      ↓
[UPI Transfer + SMS Notification]
      ↓
[Claim Logged → Model Retraining Feed]
```

---

## 3. Weekly Premium Model & Parametric Triggers

### Premium Tiers

| Plan | Weekly Cost | Coverage | Payout per Trigger |
|------|------------|----------|-------------------|
| Basic Monsoon | ₹49 | Mon–Sun, all zones | ₹350 |
| Weekend Cover | ₹19 | Sat–Sun only | ₹200 |
| Pro Shield | ₹89 | Mon–Sun + heatwave | ₹600 |

Premiums are collected every Monday via UPI auto-debit (UPI Mandate). No premium = no coverage for that week. This keeps the liquidity pool predictable and prevents adverse selection spikes around forecasted storms.

### Parametric Triggers (Bengaluru-Specific)

All triggers are sourced from **IMD Bengaluru** + **Open-Meteo API** + **BBMP flood sensors**:

| Trigger Type | Threshold | IMD Classification |
|---|---|---|
| Heavy Rain | ≥ 64.5mm / 24hr | Red Alert |
| Very Heavy Rain | ≥ 115.6mm / 24hr | Extreme Red |
| Thunderstorm + Wind | Gust ≥ 60 kmph | Orange Alert |
| Flash Flood Signal | BBMP flood zone active | Zone-specific |
| Heatwave (Pro only) | ≥ 43°C for 2 consecutive days | IMD Heatwave Alert |

Triggers are **zone-specific** — divided into 8 delivery zones matching Bengaluru's major delivery clusters (Koramangala, Whitefield, Jayanagar, Hebbal, HSR, Indiranagar, Marathahalli, Electronic City). A Red Alert in Whitefield does not trigger payouts for users registered in Hebbal.

### Why Weekly?

- Gig workers have irregular, week-to-week income — monthly premiums create dropout risk.
- Weekly billing aligns with their earnings cycle (most platforms pay weekly).
- Allows Phoenix to dynamically adjust pool sizing based on monsoon season intensity.

---

## 4. Platform Choice: Web App

**Chosen Platform: Responsive Web Application**

**Rationale:**
- Delivery workers in Bengaluru predominantly use Android mid-range phones. A PWA (Progressive Web App)-compatible website requires no Play Store approval lag and works on low-end browsers.
- Easier KYC integration via browser-based Aadhaar OTP flows.
- Lower barrier for first-time users — a WhatsApp link can open the registration page directly.
- Enables faster iteration during the hackathon without native SDK constraints.

The web app will be **mobile-first responsive**, designed for 360px–414px screen widths as primary viewport.

---

## 5. AI/ML Integration

Phoenix integrates AI/ML across three core functions:

### 5.1 Dynamic Premium Calculation

**Model:** Gradient Boosted Regressor (XGBoost)

**Inputs:**
- User's declared delivery zone
- Historical monsoon intensity for that zone (5-year IMD data)
- User's account age and claim history
- Time of year (monsoon season multiplier)
- Current IMD seasonal forecast severity

**Output:** Personalized weekly premium recommendation, displayed at signup. Users can choose a fixed tier or the AI-recommended custom rate.

---

### 5.2 Automated Fraud Detection

**Model:** Isolation Forest + Rule-Based Layer (ensemble)

**Triggered on every claim event. Inputs include:**
- Account age
- Number of simultaneous claims from same zone
- Claim frequency relative to weather event frequency
- Device fingerprint consistency
- Network metadata (IP geolocation vs. declared zone)
- Historical payout-to-premium ratio

**Output:** Fraud risk score (0.0 – 1.0). Score < 0.3 = auto-pay. Score 0.3–0.7 = soft flag + lightweight verification. Score > 0.7 = hold + manual review.

---

### 5.3 Weather Trigger Validation

**Model:** Rule-based threshold engine + anomaly detection

- Pulls real-time weather data every 15 minutes from IMD + Open-Meteo.
- Cross-validates: if IMD says Red Alert but hyperlocal sensors show normal, human override is required before mass payout.
- Trigger confidence scoring: payouts only auto-fire when ≥ 2 independent data sources confirm the threshold breach.

---

## 6. Adversarial Defense & Anti-Spoofing Strategy

> This section was added in response to the Phase 1 Market Shift brief (March 20, 2026), addressing the GPS-spoofing syndicate threat vector.

### 6.1 The Differentiation — Genuine Stranded Worker vs. Bad Actor

Phoenix's core architectural insight is this: **a genuinely stranded worker produces an organic, messy data signature. A GPS spoofer produces a suspiciously clean one.**

A real delivery partner caught in a Bengaluru Red Alert will have:
- A delivery history that shows movement patterns ending abruptly near their zone
- Network connectivity drops and reconnections (bad weather disrupts cell towers)
- Their device's battery and sensor logs reflecting environmental stress
- No active delivery pings in the 30–60 minutes before the trigger (they stopped working)

A spoofer at home will have:
- A static GPS coordinate that is unnaturally stable (real GPS in rain drifts ± 5–15 meters)
- Normal, uninterrupted network connectivity (home WiFi)
- No correlated platform activity data showing they were ever in the field that day
- Potentially multiple accounts claiming from the same household IP or device

**Our ML model is trained specifically on this contrast** — the "too perfect" GPS signal is itself a fraud indicator.

---

### 6.2 The Data — Beyond GPS Coordinates

Phoenix will analyze the following multi-signal data points to detect coordinated fraud:

**Device & Network Signals:**
- IP address geolocation vs. declared delivery zone (mismatch = flag)
- WiFi network type at claim time (home broadband vs. mobile data — spoofers tend to use home WiFi)
- Device fingerprint (browser user-agent, screen resolution, timezone) for account linkage across syndicate members
- GPS signal stability index — real outdoor GPS drifts; spoofed GPS is pixel-perfect

**Behavioral Signals:**
- Platform activity history: Was the user receiving delivery orders in their declared zone in the 2 hours before the weather event? (Sourced via optional platform API integration or self-declared shift logs)
- Claim velocity: How many users from the same zone, registered within the same 72-hour window, are claiming simultaneously? (Syndicate signature)
- Account age at first claim: New accounts claiming on their first weather event are high-risk

**Temporal & Geographic Clustering:**
- Graph analysis: Are multiple flagged accounts connected by shared device IDs, phone numbers, or IP addresses? (Social graph of fraud ring)
- Zone saturation index: If 80%+ of registered users in a single zone all claim in the same 10-minute window, the system pauses auto-payouts for that zone and escalates to review — this is statistically improbable for genuine events which produce staggered claim times

**Weather Cross-Validation:**
- Phoenix never relies solely on user-reported location. The weather event must be independently confirmed by ≥ 2 sources (IMD + Open-Meteo + BBMP sensor) for the user's *declared registration zone* — not their GPS-reported location at claim time.
- This is the key architectural defense: **the trigger is zone-based, not GPS-based**. A spoofer faking a GPS location inside a Red Alert zone gains nothing if they were not registered in that zone before the event was forecast.

---

### 6.3 The UX Balance — Handling Flagged Claims Fairly

The greatest risk of an anti-fraud system is penalizing honest workers. A delivery partner with a genuine network drop during a storm will exhibit some of the same signals as a spoofer (connectivity loss, location instability). Phoenix handles this with a **tiered response system** — not a binary block.

**Tier 1 — Auto-Pay (Score < 0.3):**
No friction. Payout processes within 2 hours. Worker is notified via SMS.

**Tier 2 — Soft Flag (Score 0.3–0.7):**
Worker receives an SMS: *"Your claim is being verified. We'll confirm within 4 hours. No action needed from you."*
- System attempts passive verification: checks if their delivery platform account shows recent zone activity.
- If passive check passes → payout releases automatically. Worker never knew there was a flag.
- If passive check inconclusive → worker receives a single, simple verification request: a one-tap "Confirm I was working in [Zone] today" button in the app. No selfie, no forms.
- Network drop is explicitly treated as a neutral signal — connectivity loss during a storm is *expected* and does not increase the fraud score.

**Tier 3 — Hard Flag (Score > 0.7):**
Payout is held. Worker is notified: *"Your claim requires a short verification. Our team will contact you within 24 hours."*
- A human reviewer checks the account within 24 hours.
- Worker can submit optional evidence: a photo of their location (not GPS-tagged, just visual context).
- If verified legitimate → payout releases with an apology credit (₹20 goodwill).
- If fraud confirmed → account suspended; flagged for platform-level reporting.

**Anti-Penalization Principles:**
- Workers are never told they are "suspected of fraud." Language is always neutral ("verification required").
- First-time flags do not affect the worker's base premium.
- If a worker's claim is held and later verified as legitimate, they receive priority processing on their next 3 claims (auto-pay regardless of score, with a manual spot-check).
- Honest workers with 6+ months of clean history receive a "Trusted Partner" badge — their fraud score threshold is relaxed to 0.5 for auto-pay.

---

## 7. Tech Stack & Development Plan

### Tech Stack

| Layer | Technology |
|---|---|
| Frontend | HTML5, CSS3, Vanilla JavaScript (mobile-first PWA) |
| Backend | Python 3.11 (Flask) |
| Database | PostgreSQL (user data, claims, zones) |
| ML/AI | Python — scikit-learn, XGBoost, pandas, NumPy |
| Weather APIs | IMD API, Open-Meteo (free tier), BBMP Open Data |
| Payments | Razorpay UPI API (sandbox) |
| Hosting | Render (backend) + GitHub Pages / Netlify (frontend) |
| Version Control | GitHub |

### Development Plan

**Phase 1 (Current — ends March 20):** Ideation, architecture, README, UI wireframes.

**Phase 2 (Weeks 3–4):** Core backend — user registration, zone mapping, weather trigger engine, PostgreSQL schema, basic fraud rule layer.

**Phase 3 (Weeks 5–6):** ML model training — premium calculator + fraud detection. Frontend build — registration flow, dashboard, claim status page.

**Phase 4 (Weeks 7–8):** Integration — Razorpay sandbox payouts, IMD live feed, end-to-end claim flow. Testing with simulated weather events and synthetic fraud scenarios.

**Phase 5 (Final):** Polish, demo video, pitch deck. Stress-test the fraud detection model against simulated syndicate attacks.

---

*Phoenix — Because every delivery partner deserves a safety net that actually pays out.*
