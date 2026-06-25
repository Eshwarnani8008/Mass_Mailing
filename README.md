<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:6C63FF,40:A855F7,70:00C9A7,100:FF6B6B&height=200&section=header&text=Enterprise%20Mailing%20Platform&fontSize=42&fontColor=ffffff&fontAlignY=40&desc=Mass%20Mailing%20%7C%20AI%20Risk%20Engine%20%7C%20Microservices&descAlignY=62&descSize=18&animation=fadeIn" width="100%"/>

</div>

<div align="center">

![Python](https://img.shields.io/badge/Python-3.11+-FFD43B?style=for-the-badge&logo=python&logoColor=blue)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![React](https://img.shields.io/badge/React-TypeScript-61DAFB?style=for-the-badge&logo=react&logoColor=black)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white)

[![License](https://img.shields.io/badge/License-MIT-6C63FF?style=flat-square)](LICENSE)
[![Made by](https://img.shields.io/badge/Built%20by-Pilli%20Eshwar-00C9A7?style=flat-square)](https://linkedin.com/in/pilli-eshwar)

</div>

---

## 🚀 What Is This?

An **enterprise-grade mass mailing & email intelligence platform** built with a microservices architecture. Designed to handle 100K+ subscriber lists, automated campaign pipelines, AI-powered risk scoring, and real-time analytics — all running via Docker.

---

## 🏗️ Architecture

```
enterprise-platform/
├── services/                    # 12 Python FastAPI microservices
│   ├── api-gateway/             # Central routing & rate limiting
│   ├── auth-service/            # JWT auth + RBAC
│   ├── mass-mailing-backend/    # Core campaign engine
│   ├── ai-risk-engine/          # ML-based bounce/spam scoring
│   ├── verification-service/    # Email validation (DNS, SMTP, MX)
│   ├── bulk-processing-service/ # 500K-row CSV ingestion
│   ├── analytics-service/       # KPI dashboards (Open Rate, CTR)
│   ├── suppression-service/     # Bounce/unsubscribe management
│   ├── reputation-engine/       # Domain reputation tracking
│   ├── billing-service/         # Stripe subscription management
│   ├── webhook-service/         # Delivery event webhooks
│   ├── notification-service/    # SMS + email alerts
│   ├── audit-service/           # Compliance logging
│   ├── tenant-service/          # Multi-tenant management
│   ├── smtp-analysis-service/   # SMTP diagnostics
│   └── dns-intelligence-service/# DNS record analysis
│
├── frontend/
│   ├── customer-portal/         # React + TypeScript (Vite)
│   └── admin-portal/            # React admin dashboard
│
├── database/
│   ├── schemas/                 # PostgreSQL schema migrations
│   └── seeds/                   # Dev seed data
│
├── observability/               # Prometheus + Grafana + Loki + Jaeger
├── tests/                       # Unit, integration, E2E, load, security
└── docker-compose.yml           # Full stack orchestration
```

---

## ✨ Key Features

| Feature | Details |
|:---|:---|
| 📧 **Mass Mailing Engine** | Bulk campaign delivery via SMTP with rate limiting |
| 🤖 **AI Risk Engine** | ML models for bounce, spam, complaint & inbox scoring |
| ✅ **Email Verification** | MX/DNS/SMTP validation, disposable domain detection |
| 📊 **Real-time Analytics** | Open rate, CTR, conversion tracking with Grafana |
| 🔒 **Auth & RBAC** | JWT + multi-role access control |
| 🏢 **Multi-Tenancy** | Isolated tenant data and billing |
| 📦 **Bulk Processing** | Up to 500K rows per CSV upload |
| 🚫 **Suppression Lists** | Automatic bounce/unsubscribe management |
| 💳 **Billing** | Stripe-powered subscription plans |
| 🔗 **Webhooks** | Real-time delivery event notifications |
| 🔍 **Observability** | Prometheus + Grafana + Loki + Jaeger tracing |

---

## 🛠️ Tech Stack

**Backend:** Python 3.11+, FastAPI, SQLAlchemy, Alembic, Celery

**Frontend:** React, TypeScript, Vite, Tailwind CSS

**Data:** PostgreSQL, Redis, MinIO (S3-compatible)

**ML:** scikit-learn (bounce, spam, complaint, inbox models)

**DevOps:** Docker, Docker Compose, Prometheus, Grafana, Loki, Jaeger, OpenTelemetry

---

## ⚡ Quick Start

### Prerequisites
- Docker & Docker Compose
- Python 3.11+
- Node.js 18+

### 1. Clone & Configure
```bash
git clone https://github.com/Eshwarnani8008/enterprise-platform.git
cd enterprise-platform

# Copy environment template and fill in your values
cp .env.example .env
```

### 2. Start the Platform
```bash
docker-compose up -d
```

### 3. Run Migrations
```bash
make migrate
```

### 4. Access the Apps

| Service | URL |
|:---|:---|
| 🌐 Customer Portal | http://localhost:3000 |
| 🔧 Admin Portal | http://localhost:3001 |
| 🔌 API Gateway | http://localhost:8000 |
| 📊 Grafana | http://localhost:3002 |
| 🔍 Jaeger Tracing | http://localhost:16686 |

---

## 🔒 Security — Environment Variables

**Never commit `.env` files.** Copy `.env.example` and fill in real values locally.

```bash
cp .env.example .env
# Edit .env with your real credentials
```

Key variables to configure:

| Variable | Description |
|:---|:---|
| `DATABASE_URL` | PostgreSQL connection string |
| `JWT_SECRET` | Secret for JWT token signing |
| `SMTP_PASS` | SMTP password for sending |
| `STRIPE_SECRET_KEY` | Stripe API key for billing |
| `MINIO_SECRET_KEY` | Object storage secret |

---

## 🧪 Testing

```bash
# Unit tests
pytest tests/unit/

# Integration tests
pytest tests/integration/

# End-to-end
pytest tests/e2e/

# Load testing (Locust)
locust -f tests/load/locustfile.py

# Security tests
pytest tests/security/
```

---

## 📈 Impact

| Metric | Result |
|:---|:---|
| 📦 Subscriber records managed | **100,000+** |
| 📉 Email bounce rate reduction | **40%** |
| 🎯 Campaign targeting improvement | **30%** |
| ⚡ Manual work eliminated | **100%** via API automation |

---

## 👨‍💻 Built By

**Pilli Eshwar** — Python Developer & Data Analyst at Narvee Technologies

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/pilli-eshwar)
[![Email](https://img.shields.io/badge/Email-Hire%20Me-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:pillieshwar@gmail.com)

---

<div align="center">
<img src="https://capsule-render.vercel.app/api?type=waving&color=0:FF6B6B,50:6C63FF,100:00C9A7&height=100&section=footer" width="100%"/>
</div>
