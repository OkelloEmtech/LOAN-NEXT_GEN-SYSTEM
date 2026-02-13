# LOAN-NEXT_GEN-SYSTEM
NEXTGEN LOAN SYSTEM is a state-of-the-art, Android-based micro-lending platform designed specifically for Uganda's low-to-middle income earners. The system uses mobile phones as collateral while implementing sophisticated behavioral scoring that integrates deeply with Uganda's regulatory infrastructure (NIRA, UCC,CRB).

# LOAN-NEXTGEN

> **An Android-based micro-lending platform with deep regulatory integration and behavioral scoring for Uganda's underbanked population.**

![Status](https://img.shields.io/badge/Status-Production_Ready-green) ![License](https://img.shields.io/badge/License-ProprietaryE&M-red) ![Platform](https://img.shields.io/badge/Platform-Android_9+-blue) ![Market](https://img.shields.io/badge/Market-Uganda-yellow)

## Executive Summary

**LOAN-NEXTGEN** is a state of the art, Android-based micro-lending platform engineered specifically for Uganda's low-to-middle income earners. Unlike traditional lending platforms, LOAN-NEXTGEN uses **mobile phones as digital collateral** while implementing **behavioral telecom scoring** that integrates deeply with Uganda's regulatory infrastructure (NIRA, UCC, CRB) and leveraging the use of predominant telecom companies.

The system eliminates agent dependency through self-service onboarding, leverages alternative data sources (SIM tenure, airtime patterns, Mobile Money behavior), and implements progressive enforcement mechanisms—from soft warnings to device lockdown to regulatory SIM/IMEI blocking—ensuring unparalleled loan recovery rates while maintaining compliance with Uganda's financial regulations.

---

## The Problem Statement

**Context:**
Uganda's low-to-middle income earners (boda riders, market vendors, casual laborers) earn UGX 200K-2M monthly but remain **underbanked**. They lack formal credit histories, operate in the informal economy, and rely heavily on Mobile Money for financial transactions.

**The Pain Point:**
Traditional lenders reject these customers due to:

1. **Lack of Credit History** (no formal banking relationships)
2. **Income Volatility** (daily/weekly wage patterns)
3. **No Physical Collateral** (cannot pledge assets)
4. **High Default Risk** (no enforcement mechanisms)

Existing solutions require agent networks, lack deep regulatory integration, and cannot effectively enforce repayment without physical asset recovery.

**The Consequence:**

- **Financial Exclusion:** 70%+ of Ugandans locked out of formal credit
- **Predatory Lending:** Vulnerable populations exploited by loan sharks
- **Economic Stagnation:** Small businesses cannot access growth capital
- **Device Theft Risk:** Lack of collateral enforcement encourages fraud

**The Requirement:**
A fully automated, regulatory-compliant platform that:

- Scores creditworthiness using **alternative data** (telecom behavior, Mobile Money)
- Uses **smartphones as digital collateral** with multi-layer enforcement
- Integrates with **NIRA** (identity), **UCC** (telecom), and **CRB** (credit bureaus)
- Provides **self-service onboarding** (zero agent dependency)
- Implements **progressive rehabilitation** for defaulters

---

## The Solution: "Your Phone is Your Credit"

LOAN-NEXTGEN transforms **smartphone ownership** into a **creditworthy asset** by combining three breakthrough innovations:

### 1. Behavioral Telecom Scoring

The platform doesn't rely on traditional credit data. Instead, it analyzes:

- **SIM Tenure:** How long has the customer owned this number? (24+ months = trusted)
- **Airtime Consistency:** Regular top-ups indicate income stability
- **Mobile Money Patterns:** Transaction frequency, wallet balance, P2P transfers
- **Device Age:** Newer devices = higher collateral value

### 2. Deep Regulatory Integration

Direct API connections to Uganda's regulatory infrastructure:

- **NIRA API:** Biometric identity verification (NIN validation)
- **UCC Portal:** SIM blocking authority, IMEI validation, telecom data access
- **CRB:** Pre-checks, default reporting, dispute resolution

### 3. Unbreakable Device Control

Multi-layer security stack:

- **DeviceOwner Mode:** System-level app persistence (survives factory reset) **??**
- **Accessibility Services:** Monitor payment-related activities
- **Boot Persistence:** Auto-launch on device restart
- **Offline Tokens:** Lock enforcement works without internet.

---

## Technical Architecture

<img width="8117" height="7628" alt="technical achitecture" src="https://github.com/user-attachments/assets/80743c3e-13a7-4bd2-9b2a-df2f3b92be8a" />



### Lock Escalation State Machine

<img width="2231" height="6025" alt="lock escalation state" src="https://github.com/user-attachments/assets/fe3c9c57-6513-49e1-9670-92b96f094b33" />



## The Scoring Formula

LOAN-NEXTGEN uses a **four-component behavioral scoring model** that normalizes to a total score of **1.0**.

### Total Score Calculation

```
Total Score = (Identity × 0.10) + (Device × 0.05) + (Telecom × 0.30) + (Financial × 0.40)
```

### Component Breakdown

#### 1. Identity Verification (25%)

_Uses NIRA and CRB data to validate customer authenticity and credit history._

```python
identity_score = (
    age_score * 0.40 +           # 18-25: 0.6, 26-35: 0.9, 36-55: 1.0, 55+: 0.8
    residence_score * 0.30 +      # Urban stable: 1.0, Rural: 0.7, Recent move: 0.5
    crb_history_score * 0.30      # Clean: 1.0, 1 default: 0.4, 2+: 0.0
) * 0.25
```

**Data Sources:**

- NIRA API: NIN validation, DOB extraction, registered address
- Application: Self-reported residence duration

**Feature Example:**

```json
{
  "nin": "CM12345678BCDF",
  "dob": "1992-03-15",
  "age_years": 33,
  "registered_district": "Kampala",
  "registered_parish": "Nakawa",
  "residence_stability_months": 24,
  "crb_defaults_count": 0,  ??
  "crb_active_loans": 1,  ??
  "crb_score": 720  ??
}
```

#### 2. Device Stability (25%)

_Assesses the value and history of the smartphone being used as collateral._

```python
device_score = (
    device_age_score * 0.35 +    # <6mo: 1.0, 6-18mo: 0.9, 18-36mo: 0.7, 36+: 0.5
    imei_history_score * 0.35 +   # Clean: 1.0, 1 prior loan: 0.8, 2+: 0.6, Blacklist: 0.0
    device_value_score * 0.30     # >UGX 800K: 1.0, 400-800K: 0.8, <400K: 0.6
) * 0.25
```

**Data Sources:**

- UCC IMEI Registry: Device first activation date, blacklist status
- Device Info (Android): Manufacturer, model, build date
- Market Pricing API: Current device resale value ??

**Feature Example:**

```json
{
  "imei": "352033101234567",
  "manufacturer": "Samsung",
  "model": "Galaxy A14",
  "first_seen_ucc": "2023-08-10",
  "device_age_months": 18,
  "estimated_value_ugx": 650000,
  "previous_loan_count": 0,
  "ucc_blacklist": false
}
```

#### 3. Telecom Behavior (30%)

_The most weighted component—analyzes SIM and Mobile Money patterns._

```python
telecom_score = (
    sim_tenure_score * 0.25 +        # <6mo: 0.5, 6-24mo: 0.8, 24+: 1.0

    network_reputation_score * 0.20   # On-time payments elsewhere: boost ??
) * 0.30
```

**Data Sources:**

- UCC Telecom Data: SIM registration date, monthly airtime recharge patterns (6 months)
- MTN MoMo API: Transaction history, wallet balance, P2P patterns
- LMS History: Previous loan repayment behavior on this number

**Feature Example:**

```json
{
  "msisdn": "+256782345678",
  "sim_registration_date": "2021-05-20",
  "sim_tenure_months": 57,
  "airtime_recharges_6mo": [15000, 18000, 12000, 20000, 17000, 16000],  ??
  "airtime_cv": 0.18,
  "momo_transactions_30d": 23,   ??
  "momo_avg_balance_ugx": 45000,   ??
  "previous_loans_paid_ontime": 2,  ??
  "previous_loans_defaulted": 0   ??
}
```

#### 4. Financial Behavior (20%)

_Evaluates repayment history and income stability._

```python
financial_score = (
    repayment_history_score * 0.50 +  # Perfect: 1.0, 1 late: 0.6, 2+: 0.3
    airtime_consistency_score * 0.25 + # CV < 0.3: 1.0, 0.3-0.6: 0.7, >0.6: 0.4
    wallet_activity_score * 0.30 +    # >10 txn/mo: 1.0, 5-10: 0.7, <5: 0.4
    income_stability_score * 0.30 +    # Salaried: 1.0, Regular: 0.8, Irregular: 0.5
    occupation_risk_score * 0.20       # Formal: 1.0, Informal stable: 0.7, High-risk: 0.4
) * 0.20
```

**Data Sources:**

- LMS: Historical loan performance (if repeat customer)
- CRB API: Previous defaults, active loans, dispute flags
- Application: Self-reported income, occupation
- MoMo: Income pattern validation (inbound transfers)

---

### Loan Decision Thresholds

| Score Range | Loan Amount (UGX)      | Repayment Frequency | Term Length | Interest Rate |
| :---------- | :--------------------- | :------------------ | :---------- | :------------ |
| 0.8 - 1.0   | 500K - 2M              | Monthly             | 15-30 days  | 10%           |
| 0.5 - 0.8   | 100K - 500K            | Weekly              | 7-21 days   | 15%           |
| 0.3 - 0.5   | 20K - 100K             | Daily               | 3-7 days    | 20%           |
| < 0.3       | **Application Denied** | -                   | -           | -             |

_Note: First-time borrowers start at lower limits regardless of score. Limits increase with successful repayment history._

---

## Security & Compliance

### Regulatory Compliance Framework

| Regulation                          | Implementation                                                                       |
| :---------------------------------- | :----------------------------------------------------------------------------------- |
| **Uganda Data Protection Act 2019** | Explicit consent capture, AES-256 encryption at rest/transit, Right to deletion APIs |
| **UCC Guidelines**                  | IMEI validation, SIM blocking authority, Telecom data access with consent            |
| **CRB Act 2016**                    | Mandatory pre-checks, 90-day default reporting, Dispute resolution workflow          |

### Multi-Layer Android Security

#### 1. DeviceOwner Mode

- **System-Level Permissions:** Cannot be uninstalled by user
- **Persistent Configuration:** Kiosk settings enforced at boot

#### 2. Accessibility Services

- **Payment Activity Monitoring:** Detects Mobile Money transactions
- **App Usage Tracking:** Identifies attempts to bypass locks
- **Gesture Blocking:** Prevents status bar pull-down when locked

#### 3. Offline Token Caching

- **JWT Storage:** 30-day validity for lock enforcement
- **Local State Machine:** Works without internet connectivity
- **Periodic Sync:** Updates server when connection restored

### Threat Mitigation Matrix

| Threat                        | Likelihood | Impact | Mitigation                                         |
| :---------------------------- | :--------- | :----- | :------------------------------------------------- |
| Device reset to bypass lock   | High       | High   | DeviceOwner + FRP + IMEI tracking                  |
| SIM swap to avoid blocks      | Medium     | High   | IMEI primary identifier + UCC cross-check          |
| Root access to disable app    | Medium     | High   | Root detection + immediate lock + backend alert    |
| Multiple devices per customer | Medium     | Medium | IMEI+MSISDN pairing + CRB cross-reference          |
| Fake identity documents       | Low        | High   | 3D Mapping & selfie match                          |
| Collusion with agents         | Low        | Medium | Self-service only (no agents)                      |
| Network unavailability        | High       | Low    | Periodic sync                                      |
| UCC block delays              | Medium     | Medium | Automated tracking + escalation                    |
| Customer harassment           | Low        | High   | Clear terms + progressive locks + compliance audit |

---

## Getting Started

### Prerequisites

- **Development Environment:**
  - Android Studio (Flutter/Kotlin development)
  - Python 3.11+ (for FastAPI backend)
  - Next.js 20+ (Frontend)
  - Docker & Docker Compose

- **External Services:**
  - NIRA API access credentials
  - UCC Portal API key
  - CRB integration license
  - MTN MoMo API sandbox/production keys

- **Infrastructure:**
  - PostgreSQL 15+ (Database)
  - Redis 7+ (Caching)
  - RabbitMQ 3.12+ (Message Queuing)

### Quick Start (Development Mode)

1.  **Clone the Repository**

    ```bash
    git clone https://github.com/org/loan-nextgen.git
    cd loan-nextgen
    ```

2.  **Set Environment Variables**

    ```bash
    cp .env
    # Edit .env with your API keys
    ```

3.  **Start Infrastructure**

    ```bash
    docker-compose up -d postgres redis rabbitmq
    ```

4.  **Run Database Migrations**

    ```bash
    cd backend
    atlas migrate diff local env
    ```

5.  **Start Backend Services**

    ```bash
    # Terminal 1: API Server
    cd backend
    uvicorn app.main:app --reload --port 8000

    # Terminal 2: Background Workers
    cd backend
    celery -A app.tasks.worker worker --loglevel=info
    ```

6.  **Start Flutter App**

    ```bash
    cd mobile
    flutter pub get
    flutter run
    ```

7.  **Access Points**
    - **Mobile App:** Connected Android device/emulator
    - **API Docs:** `http://localhost:8000/docs`
    - **Admin Dashboard:** `http://localhost:3000`
    - **Flower (Celery Monitor):** `http://localhost:5555`

---

## Self-Registration Flow

<img width="8192" height="6644" alt="STRCUTURAL DIAG" src="https://github.com/user-attachments/assets/4ae9be15-7464-45b4-9b33-83b36adf564c" />


---

## Tech Stack

| Layer               | Technology            | Purpose                                        |
| :------------------ | :-------------------- | :--------------------------------------------- |
| **Mobile App**      | Flutter (Dart)        | Cross-platform UI, device control              |
| **Android Kiosk**   | Kotlin                | DeviceOwner mode, accessibility services       |
| **Backend API**     | FastAPI (Python 3.11) | REST API, scoring logic                        |
| **Database**        | PostgreSQL 15         | Transactional data (loans, users, scores)      |
| **Cache**           | Redis 7               | Session management, rate limiting              |
| **Message Queue**   | RabbitMQ 3.12         | Async job processing (SMS, CRB reports)        |
| **Task Queue**      | Celery                | Background workers (lock triggers, UCC blocks) |
| **Admin Dashboard** | React + TypeScript    | Agent portal, reporting                        |
| **API Docs**        | OpenAPI (Swagger)     | Auto-generated API documentation               |
| **Monitoring**      | Prometheus + Grafana  | Metrics, alerting                              |
| **Error Tracking**  | Sentry.io             | Exception monitoring                           |
| **Security**        | JWT, bcrypt, WAF      | Authentication, encryption                     |

---

## Team Responsibilities

| Role                  | Name                 | Primary Focus                                                                |
| :-------------------- | :------------------- | :--------------------------------------------------------------------------- |
| **Team Lead**         | Okello David         | Architecture, Security, Regulatory Compliance, Code Reviews                  |
| **Backend Engineer**  | Christopher / Joseph | FastAPI, PostgreSQL, Scoring Engine, CRB/UCC Integration, Docker, Monitoring |
| **Frontend Engineer** | Alvin                | Integrate with backend APIs (React.js, Tailwind)                             |
| **QA Engineer**       | Patience             | Automated Testing (Pytest, Flutter Test), Load Testing                       |
| **UI/UX Engineer**    | Alex                 | NIRA/UCC/CRB Liaison, Legal Review, Audit Trails                             |
| **Feature Developer** | David / Patience     | Debugging, optimization, collaboration, code reviews                         |

---

## Repository Structure

```bash
/loan-nextgen
├── /mobile                    # Flutter Application
│   ├── /lib
│   │   ├── /screens          # Registration, Loan, Payment UI
│   │   ├── /services         # API clients, device control
│   │   └── /widgets          # Reusable components
│   └── /android
│       └── /app              # Kotlin kiosk mode logic
│
├── /backend                   # FastAPI Services
│   ├── /app
│   │   ├── /api              # REST endpoints
│   │   ├── /core             # Config, security, dependencies
│   │   ├── /models           # SQLAlchemy ORM models
│   │   ├── /schemas          # Pydantic request/response models
│   │   ├── /services         # Business logic
│   │   │   ├── scoring.py    # 4-component scoring engine
│   │   │   ├── nira.py       # NIRA API client
│   │   │   ├── ucc.py        # UCC API client
│   │   │   └── crb.py        # CRB API client
│   │   └── /tasks            # Celery background jobs
│   └── /tests                # Pytest test suite
│
├── /admin-dashboard           # React Admin Portal
│   ├── /src
│   │   ├── /components       # Tables, charts, forms
│   │   └── /pages            # Loans, Customers, Reports
│   └── /public
│
│
├── /infra                     # Infrastructure as Code
│   ├── /docker               # Dockerfiles,docker-compose.yml
│
├── /docs                      # Documentation
│   ├── api-reference.md      # API endpoint documentation
│   ├── scoring-logic.md      # Detailed scoring formula
│   ├── compliance.md         # NIRA/UCC/CRB integration guide
│   └── deployment.md         # Production deployment steps
│
└── /scripts                   # Utility Scripts
    ├── seed-db.py            # Sample data generation
    └── ucc-mock-server.py    # Local UCC API simulator
```

---

## Future Roadmap

### Phase 1: MVP (Current)

- Self-registration with NIRA/UCC/CRB integration
- 4-component behavioral scoring
- Kiosk mode enforcement
- MTN MoMo disbursement

### Phase 2: Scale (Q2 2026)

- Multi-network support (Airtel Money, Chipper Cash)
- UCC SIM/IMEI blocking automation
- CRB rehabilitation program
- Agent dashboard for field support

### Phase 3: Intelligence (Q3 2026)

- Deep learning scoring model (LSTM for transaction sequences)
- Fraud detection (anomaly detection on device behavior)
- Dynamic pricing (interest rates based on real-time risk)
- WhatsApp chatbot for payments

### Phase 4: Ecosystem (Q4 2026)

- B2B lending (SACCO integration)
- Merchant financing (inventory loans)
- Insurance bundling (device + health micro-insurance)
- Open API for third-party lenders

---

## Support & Documentation

- **API Documentation:** `http://localhost:8000/docs` (Swagger UI)
- **Technical Docs:** See `/docs` folder in repository
- **Compliance Docs:** NIRA/UCC agreements
- **Bug Reports:** Open GitHub issue with `[BUG]` prefix
- **Feature Requests:** Open GitHub issue with `[FEATURE]` prefix

---

## Ethical Lending Commitment

LOAN-NEXTGEN is built on principles of **responsible lending**:

1.  **Transparent Terms:** All interest rates and fees disclosed upfront
2.  **Progressive Enforcement:** Soft reminders before hard locks
3.  **Rehabilitation Paths:** Defaulters can restore credit through good behavior
4.  **No Harassment:** Automated system eliminates human collection agents
5.  **Financial Education:** In-app tips on building credit, budgeting
6.  **Data Privacy:** Strict compliance with Uganda Data Protection Act 2019

_"Expanding financial access while protecting the vulnerable."_

---

**Built for Uganda's underbanked. Powered by behavioral intelligence.**

```

```
