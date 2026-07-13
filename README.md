# MyTadika — School–Parent Engagement System

A full-stack kindergarten (tadika) management platform connecting **parents**, **teachers**, and **admins** in one place — academic tracking, health monitoring, classroom management, messaging, fees, and two purpose-built AI modules that turn raw data into actionable guidance instead of just numbers.

This is a Final Year Project (FYP) submission.

## Roles

- **Parent** — view their child's academic progress, health records, classroom activity, memory box photos/videos, pay fees, and message the teacher or school admin.
- **Teacher** — manage their classroom(s), record academic/health data, post announcements and classwork, share memory box updates, and communicate with parents.
- **Admin** — manage accounts, classrooms, students, fees, and school-wide events; oversee academic and health records across the school.

## Core Features

- **Academic tracking** — term scores, grades, and subject performance per student.
- **Health & Nutrition monitoring** — growth measurements (weight, height), BMI, and allergy profiles per child.
- **Classroom management** — announcements, classwork/assignments, student enrollment, and per-classroom rosters.
- **Messaging** — parent ↔ teacher and parent/teacher ↔ admin chat, with photo/file sharing.
- **Memory Box** — teachers share classroom photos/videos; parents can react and comment.
- **Events & calendar** — school events and public holidays.
- **Fees** — fee creation, bulk actions, manual and online payment (Stripe / ToyyibPay).
- **Notifications** — role-aware notifications across the app.
- **In-app Help chatbot** — a rule-based assistant (keyword-matched intents) that answers "how do I…" questions per role, available as a floating widget or a dedicated full-page Help section.

## AI Modules

### 1. Academic Progress AI
Classifies a child's performance into four developmental domains — **Language & Literacy**, **STEM & Logic**, **Creative Arts**, **Physical & Movement** — as a **Strength**, **Developing**, or **Emerging** area, using a **Random Forest** model trained on each domain's score trend, classwork completion rate, and how it compares to the child's own other domains that term (self-relative, not compared against other children). An absolute-score floor guarantees a failing grade can never be mislabeled a Strength. Each classification comes paired with a specific, practical improvement tip.

- Served as a standalone **Python/FastAPI** microservice (`AI/academic/`)
- Falls back to an equivalent rule-based classifier if the microservice is offline, so the feature never hard-fails

### 2. Health & Nutrition Support
Computes each child's **BMI** and classifies nutritional status (normal / moderate / severe) using an **XGBoost** model trained on WHO growth z-score indicators (weight-for-age, height-for-age, BMI-for-age), flagging risks such as stunting, wasting, or severe acute malnutrition. Generates dietary and physical-activity advice that is automatically filtered against the child's recorded allergies.

- Served as a standalone **Python/FastAPI** microservice (`AI/`)
- Advice content is grounded in Malaysian Dietary Guidelines, WHO, USDA, and CDC guidance

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Java, Spring Boot, Spring Security, Spring Data JPA (Hibernate) |
| Frontend | Static HTML + Tailwind CSS + vanilla JavaScript (no build step) |
| Database | PostgreSQL (hosted on Supabase) |
| AI services | Python, FastAPI, scikit-learn, XGBoost |
| Payments | Stripe, ToyyibPay |
| File storage | Supabase Storage |
| Auth | Spring Security + BCrypt password hashing |

## Project Structure

```
MyTadika/
├── mytadika-backend/        # Spring Boot application (backend API + serves the frontend)
│   └── src/main/
│       ├── java/com/mytadika/   # Controllers, services, repositories, models
│       └── resources/static/    # Frontend pages (parent/, teacher/, admin/, components/)
├── AI/                        # Health & Nutrition AI microservice + datasets/notebooks
│   └── academic/               # Academic Progress AI microservice + dataset/model
└── docs/                      # Planning & design docs
```

## Running the Project

**Backend + frontend** (this serves the whole app on `http://localhost:8080`):
```bash
cd mytadika-backend
mvn spring-boot:run
```

**Academic AI microservice** (optional — the app falls back to rule-based logic if this isn't running):
```bash
cd AI/academic/api
pip install -r requirements.txt
python -m uvicorn main:app --host 127.0.0.1 --port 8002
```

**Health AI microservice** (optional, same fallback behavior):
```bash
cd AI/api
pip install -r requirements.txt
python -m uvicorn main:app --host 127.0.0.1 --port <configured-port>
```

Configure database and third-party credentials in `mytadika-backend/src/main/resources/application.properties` before running.
