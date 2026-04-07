# ⚡ DataPulse — Dashboard with API Data

> **Problem 3 Solution** — RD Tech Innovation Practical Assignment  
> A data visualization dashboard built with **Django + Chart.js**.

[![Python](https://img.shields.io/badge/Python-3.11-blue?logo=python)](https://python.org)
[![Django](https://img.shields.io/badge/Django-4.2-green?logo=django)](https://djangoproject.com)
[![Chart.js](https://img.shields.io/badge/Chart.js-4.4-orange?logo=chartdotjs)](https://chartjs.org)
[![Render](https://img.shields.io/badge/Deploy-Render.com-purple)](https://render.com)

---

## 📸 Features

| Feature | Details |
|---|---|
| **User Growth Chart** | Line chart — cumulative users + monthly new signups (dual Y-axis) |
| **Sales Figures Chart** | Bar + line combo — revenue, expenses, profit per month |
| **Recent Activity Table** | Last 10 system events with icons, badges, user, timestamp |
| **KPI Stat Cards** | Animated count-up for Total Users, Revenue, Conversion Rate, Active Today |
| **Loading State** | Spinner + skeleton placeholders while fetching |
| **Error State** | Red error card with Retry button per section |
| **Empty State** | Friendly "no data" message per section |
| **Demo Controls** | Toggle Normal / Error / Empty states from the UI |
| **Live Clock** | Real-time clock in the topbar |
| **Refresh Button** | Re-fetch all data on demand |

---

## 🏗️ Architecture

```
dashboard_project/
├── dashboard_project/       ← Django project config
│   ├── settings.py          ← env-based config, whitenoise, allowed hosts
│   ├── urls.py              ← root URL routing
│   └── wsgi.py              ← WSGI entry point (gunicorn)
│
├── dashboard/               ← Main Django app
│   ├── views.py             ← 1 HTML view + 4 JSON API endpoints
│   ├── urls.py              ← App URL patterns
│   └── templates/
│       └── dashboard/
│           └── index.html   ← Dashboard page (loading/error/empty states)
│
├── templates/
│   └── base.html            ← Layout, Chart.js CDN, Google Fonts, sidebar
│
├── static/
│   ├── css/style.css        ← Dark glassmorphism UI, responsive layout
│   └── js/dashboard.js     ← Fetch, Chart.js, state management
│
├── manage.py
├── requirements.txt
├── Procfile                 ← gunicorn (Render.com)
├── render.yaml              ← Infrastructure-as-code for Render
└── .env.example             ← Environment variable template
```

### API Endpoints

| Method | URL | Description |
|---|---|---|
| `GET` | `/` | Dashboard HTML page |
| `GET` | `/api/kpis/` | KPI summary data |
| `GET` | `/api/user-growth/` | 12-month user growth data |
| `GET` | `/api/sales/` | 12-month sales data |
| `GET` | `/api/activity/` | Recent 10 activity events |

All API endpoints accept `?error=1` or `?empty=1` query params to simulate UI states.

### Data Flow

```
Browser → Django View → JsonResponse (mock data)
                           ↓
             dashboard.js fetches via fetch()
                           ↓
             Renders Chart.js + activity table
             (with loading/error/empty states)
```

---

## 🚀 Local Setup

### Prerequisites
- Python 3.11+
- pip

### Steps

```bash
# 1. Clone the repository
git clone https://github.com/YOUR_USERNAME/datapulse-dashboard.git
cd datapulse-dashboard

# 2. Create and activate a virtual environment
python -m venv venv

# Windows
venv\Scripts\activate
# macOS/Linux
source venv/bin/activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Configure environment
copy .env.example .env   # Windows
# cp .env.example .env   # macOS/Linux
# Edit .env if needed

# 5. Run migrations (creates SQLite DB)
python manage.py migrate

# 6. Collect static files (optional for dev)
python manage.py collectstatic --noinput

# 7. Start development server
python manage.py runserver
```

Open **http://127.0.0.1:8000/** in your browser.

---

## ☁️ Deploy on Render.com

### Option A — Using `render.yaml` (recommended)

1. Push this project to a **public GitHub repository**
2. Go to [render.com](https://render.com) → **New** → **Blueprint**
3. Connect your GitHub repo
4. Render reads `render.yaml` and auto-configures everything
5. Click **Apply** → your app deploys in ~2 minutes

### Option B — Manual

1. Go to [render.com](https://render.com) → **New** → **Web Service**
2. Connect your GitHub repo
3. Configure:
   - **Environment**: Python
   - **Build Command**: `pip install -r requirements.txt && python manage.py migrate --noinput && python manage.py collectstatic --noinput`
   - **Start Command**: `gunicorn dashboard_project.wsgi`
4. Add **Environment Variables**:
   - `SECRET_KEY` → generate a random value
   - `DEBUG` → `False`
   - `ALLOWED_HOSTS` → `.onrender.com`
5. Deploy!

---

## 🧪 Testing UI States

Use the **Demo Controls** bar on the dashboard to switch between:
- **Normal** — Full data with charts and activity
- **Error** — Simulates API failures with retry buttons
- **Empty** — Simulates no data available

Or test directly via URL:
```
http://127.0.0.1:8000/api/user-growth/?error=1
http://127.0.0.1:8000/api/sales/?empty=1
```

---

## 📐 Assumptions & Design Decisions

1. **Mock data** — All data is generated at request time in `views.py` using Python's `random` module. No external API calls, no database reads for chart data. This keeps the project self-contained and deploy-ready.
2. **No npm/Node** — Chart.js loaded via CDN (`cdn.jsdelivr.net`). No build step needed.
3. **SQLite** — Used for Django auth/sessions only. The free Render tier resets the filesystem, so no persistent data is stored.
4. **Whitenoise** — Serves static files directly from Django/gunicorn without needing a separate CDN or nginx.
5. **python-decouple** — Reads `SECRET_KEY` and `DEBUG` from environment variables, with sensible defaults for local development.
6. **ALLOWED_HOSTS = `*`** (default dev) — Set to `.onrender.com` in production via `render.yaml`.

---

## 📦 Dependencies

| Package | Version | Purpose |
|---|---|---|
| Django | 4.2.16 | Web framework |
| gunicorn | 21.2.0 | Production WSGI server |
| whitenoise | 6.7.0 | Static file serving |
| python-decouple | 3.8 | Environment variable management |

---

## 📄 License

MIT
