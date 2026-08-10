# PrismBot — سیستم تریدینگ کاملاً خودکار ۲۴/۷ کریپتو

<p align="center">
  <strong>ماژولار • مقیاس‌پذیر • Paper-to-Live</strong><br>
  یک سیستم تریدینگ خودکار برای اجرای مداوم ۲۴/۷ در بازار کریپتو
</p>

<p align="center">
  <a href="README.md">🇬🇧 English</a> ·
  <a href="#ویژگی‌ها">ویژگی‌ها</a> ·
  <a href="#معماری">معماری</a> ·
  <a href="#شروع-سریع">شروع سریع</a> ·
  <a href="#مدیریت-ریسک">مدیریت ریسک</a> ·
  <a href="#رابط-کاربری-دسکتاپ">Desktop UI</a>
</p>

---

## معرفی

**PrismBot** یک سیستم تریدینگ کاملاً خودکار، ماژولار و مقیاس‌پذیر برای بازار کریپتو است که از **Paper Trading** شروع می‌کند و با یک تغییر ساده در حالت اجرا، امکان حرکت به سمت **Live Trading** را فراهم می‌کند.

معماری پروژه لایه‌های داده، استراتژی، تخصیص سرمایه، مدیریت ریسک، اجرا، مانیتورینگ و Orchestration را از یکدیگر جدا می‌کند تا توسعه و نگهداری سیستم ساده‌تر باشد.

> ⚠️ **هشدار:** PrismBot یک پروژه آموزشی و تحقیقاتی است. معاملات واقعی با ریسک از دست دادن سرمایه همراه هستند و هیچ تضمینی برای سوددهی وجود ندارد.

---

## ویژگی‌ها

- 🧩 **معماری ماژولار** — لایه‌های جداگانه برای داده، استراتژی، ریسک، اجرا و مانیتورینگ
- 🧠 **پشتیبانی از چند استراتژی Hybrid** — ترکیب استراتژی‌های قانون‌محور با فیلترهای ML/Sentiment
- 🔄 **Paper → Live** — تغییر ساده بین معاملات آزمایشی و واقعی
- 🛡️ **کنترل‌های ریسک** — Kill-Switch، محدودیت Drawdown و حداکثر اندازه پوزیشن
- ⚡ **آماده اجرای ۲۴/۷** — مناسب برای استقرار روی VPS با Docker
- 🔌 **قابل توسعه** — امکان افزودن استراتژی، بازار یا Broker/Exchange جدید

---

## معماری

```text
┌──────────────────────────────────────────────────────────┐
│                       PrismBot                            │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Data ──► Strategies ──► Portfolio ──► Risk ──►         │
│                                                Execution  │
│                                                    │     │
│                                                    ▼     │
│                                               Monitoring │
│                                                          │
│                 Core Orchestrator                        │
└──────────────────────────────────────────────────────────┘
```

### اجزای اصلی

| بخش | مسئولیت |
|---|---|
| `data/` | دریافت و پردازش داده‌های بازار با CCXT |
| `strategies/` | استراتژی‌های معاملاتی Hybrid |
| `portfolio/` | تخصیص سرمایه و منطق Ensemble |
| `risk/` | مدیریت ریسک و Kill-Switch |
| `execution/` | اجرای Paper و Live با CCXT |
| `monitoring/` | لاگ، متریک و اتصال Telegram |
| `core/` | Orchestrator اصلی سیستم |

---

## ساختار پروژه

```text
prismbot/
├── config/                 # تنظیمات YAML
├── src/
│   ├── data/               # دریافت و پردازش داده (CCXT)
│   ├── strategies/         # استراتژی‌های Hybrid
│   ├── portfolio/          # تخصیص سرمایه و Ensemble
│   ├── risk/               # مدیریت ریسک و Kill-Switch
│   ├── execution/          # اجرای Paper + Live (CCXT)
│   ├── monitoring/         # لاگ، متریک و Telegram
│   └── core/               # Orchestrator اصلی
├── scripts/                # backtest, live, train
├── ui/                     # رابط کاربری دسکتاپ
├── docker-compose.yml
├── Dockerfile
└── .env.example
```

---

## شروع سریع

### ۱. کلون و آماده‌سازی

```bash
cd prismbot
python -m venv .venv

# Linux / macOS
source .venv/bin/activate

# Windows
# .venv\Scriptsctivate

pip install -r requirements.txt
cp .env.example .env
```

### ۲. تنظیمات

فایل `.env` را ویرایش کنید:

```env
MODE=paper
EXCHANGE=binance
API_KEY=your_key_here
API_SECRET=your_secret_here
TELEGRAM_BOT_TOKEN=
TELEGRAM_CHAT_ID=
```

فایل‌های `config/*.yaml` را بر اساس سرمایه، تنظیمات ریسک، جفت‌ارزها و پارامترهای استراتژی پیکربندی کنید.

### ۳. اجرا با Docker

روش پیشنهادی برای VPS:

```bash
docker compose up -d --build
```

### ۴. اجرا بدون Docker

```bash
python -m src.core.live
```

---

## حالت‌های اجرا

| حالت | دستور | کاربرد |
|---|---|---|
| 🧪 **Paper** | `MODE=paper python -m src.core.live` | شبیه‌سازی معاملات بدون سرمایه واقعی |
| 🔴 **Live** | `MODE=live python -m src.core.live` | معاملات در بازار واقعی |
| 📈 **Backtest** | `python scripts/backtest.py` | تست روی داده‌های تاریخی |

> **چرخه پیشنهادی:** Backtest → Paper Trading → Live Trading

---

## مدیریت ریسک

کنترل‌های ریسک بخش اجباری سیستم هستند:

- حداکثر Drawdown روزانه و کلی
- حداکثر اندازه پوزیشن
- **Kill-Switch** از طریق فایل `KILL` یا Telegram
- **Circuit Breaker** برای نوسان غیرعادی یا قطع ارتباط

### Kill-Switch اضطراری

فایل `KILL` را ایجاد کنید:

```bash
touch KILL
```

یا از طریق Telegram:

```text
/kill
```

---

## افزودن استراتژی جدید

1. یک فایل جدید در `src/strategies/` ایجاد کنید.
2. از `BaseStrategy` ارث‌بری کنید.
3. آن را در `config/strategies.yaml` ثبت کنید.
4. ربات را Restart کنید.

مستندات بیشتر در پوشه `docs/` قرار دارد.

---

## رابط کاربری دسکتاپ

### Tauri + React + Rust

PrismBot دارای یک داشبورد دسکتاپ برای مانیتورینگ و کنترل ربات است.

### پیش‌نیازها

- Node.js 18+
- Rust از طریق `rustup`
- Python Trading Bot در حال اجرا
- Status API روی پورت `8765`

### اجرای Development

**ترمینال ۱ — Python Bot**

```bash
cd prismbot
source .venv/bin/activate
pip install -r requirements.txt
python -m src.core.live
```

**ترمینال ۲ — UI**

```bash
cd ui
npm install
npm run tauri dev
```

### ساخت نسخه نهایی

```bash
cd ui
npm install
npm run tauri build
```

خروجی Build:

```text
ui/src-tauri/target/release/
```

### قابلیت‌های UI

- نمایش Equity، Daily PnL، Drawdown و Uptime
- نمایش پوزیشن‌های باز
- نمایش استراتژی‌های فعال و وزن آن‌ها
- مشاهده لاگ‌های زنده
- کنترل Kill-Switch
- پشتیبانی از حالت Paper / Live

Status API:

```text
http://127.0.0.1:8765
```

---

## نکات امنیتی

- **همیشه با Paper Trading شروع کنید.**
- معاملات واقعی ممکن است باعث از دست رفتن سرمایه شوند.
- برای API Key ربات، دسترسی برداشت فعال نکنید.
- تا حد امکان فقط مجوزهای موردنیاز Trading را فعال کنید.
- Secretها را در `.env` نگهداری کنید و هرگز در Git Commit نکنید.
- قبل از فعال‌کردن Live Mode، محدودیت‌های ریسک را بررسی کنید.

---

## توسعه و تست

اجرای تست‌ها:

```bash
pytest tests/
```

لاگ‌ها:

```text
logs/
```

مستندات:

```text
docs/
```

---

## سلب مسئولیت

PrismBot با هدف آموزشی و تحقیقاتی ارائه شده است.

محتوای این Repository نباید به‌عنوان مشاوره مالی، پیشنهاد سرمایه‌گذاری یا تضمین عملکرد معاملاتی تلقی شود.

**پیش از فعال‌سازی Live Trading، سیستم، استراتژی‌ها، رفتار Execution و کنترل‌های ریسک را به‌صورت مستقل اعتبارسنجی کنید.**

---

<p align="center">
  ساخته‌شده با معماری ماژولار برای مالکیت کامل و توسعه‌پذیری پروژه.
</p>
