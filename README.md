# PrismBot — Fully Automated 24/7 Crypto Trading System

یک سیستم تریدینگ کاملاً خودکار، ماژولار و مقیاس‌پذیر برای بازار کریپتو که از Paper Trading شروع می‌کند و به راحتی به Live تبدیل می‌شود.

## ویژگی‌های کلیدی

- **ماژولار**: لایه‌های جداگانه برای داده، استراتژی، ریسک، اجرا و مانیتورینگ
- **چنداستراتژی (Hybrid)**: ترکیب استراتژی‌های قانون‌محور + فیلتر ML/Sentiment
- **Paper → Live**: سوئیچ ساده بین حالت آزمایشی و واقعی
- **ریسک قوی**: Kill-switch، محدودیت دراودان، حداکثر اندازه پوزیشن
- **۲۴/۷**: آماده اجرا روی VPS با Docker
- **قابل گسترش**: اضافه کردن استراتژی، بازار یا بروکر جدید بسیار ساده است

## ساختار پروژه

```
prismbot/
├── config/                 # تنظیمات YAML
├── src/
│   ├── data/               # دریافت و پردازش داده (CCXT)
│   ├── strategies/         # استراتژی‌ها (Hybrid)
│   ├── portfolio/          # تخصیص سرمایه و Ensemble
│   ├── risk/               # مدیریت ریسک و Kill-switch
│   ├── execution/          # Paper + Live (CCXT)
│   ├── monitoring/         # لاگ، متریک، تلگرام
│   └── core/               # Orchestrator اصلی
├── scripts/                # backtest, live, train
├── docker-compose.yml
├── Dockerfile
└── .env.example
```

## نصب سریع

### ۱. کلون و آماده‌سازی

```bash
cd prismbot
python -m venv .venv
source .venv/bin/activate   # Linux/Mac
# یا .venv\Scripts\activate در Windows
pip install -r requirements.txt
cp .env.example .env
```

### ۲. تنظیمات

فایل `.env` را ویرایش کنید:

```env
MODE=paper                  # paper یا live
EXCHANGE=binance
API_KEY=your_key_here
API_SECRET=your_secret_here
TELEGRAM_BOT_TOKEN=         # اختیاری
TELEGRAM_CHAT_ID=
```

فایل‌های `config/*.yaml` را مطابق نیاز تنظیم کنید (سرمایه، ریسک، جفت‌ارزها، پارامترهای استراتژی).

### ۳. اجرا با Docker (پیشنهادی برای VPS)

```bash
docker compose up -d --build
```

یا بدون Docker:

```bash
python -m src.core.live
```

## حالت‌های اجرا

| حالت     | دستور                          | توضیح                          |
|----------|--------------------------------|--------------------------------|
| Paper    | `MODE=paper python -m src.core.live` | شبیه‌سازی بدون پول واقعی     |
| Live     | `MODE=live python -m src.core.live`  | معاملات واقعی (احتیاط!)      |
| Backtest | `python scripts/backtest.py`   | تست روی داده تاریخی           |

## مدیریت ریسک (اجباری)

- حداکثر دراودان روزانه و کلی
- حداکثر اندازه هر پوزیشن
- Kill-switch (فایل `KILL` یا دستور تلگرام)
- Circuit breaker روی نوسان یا قطع اتصال

برای فعال کردن Kill-switch:

```bash
touch KILL
# یا از طریق تلگرام: /kill
```

## اضافه کردن استراتژی جدید

1. فایل جدید در `src/strategies/my_strategy.py` بسازید
2. از `BaseStrategy` ارث‌بری کنید
3. در `config/strategies.yaml` ثبت کنید
4. ربات را ریستارت کنید

جزئیات بیشتر در `docs/`.

## هشدار مهم

- **همیشه با Paper Trading شروع کنید**
- معاملات واقعی ریسک از دست دادن سرمایه دارد
- هیچ‌وقت API Key با دسترسی برداشت را در ربات قرار ندهید (فقط Trade)
- این پروژه آموزشی و تحقیقاتی است و تضمین سود نمی‌دهد

## پشتیبانی و گسترش

- مستندات: پوشه `docs/`
- تست‌ها: `pytest tests/`
- لاگ‌ها: `logs/`

ساخته شده با معماری ماژولار برای مالکیت کامل کاربر.

## Desktop UI (Tauri + React + Rust)

یک داشبورد دسکتاپ مدرن برای مانیتورینگ و کنترل ربات.

### پیش‌نیازها
- Node.js 18+
- Rust (rustup)
- ربات پایتون در حال اجرا (API روی پورت 8765)

### اجرا در حالت توسعه

```bash
# ترمینال ۱ — ربات پایتون
cd prismbot
source .venv/bin/activate
pip install -r requirements.txt
python -m src.core.live

# ترمینال ۲ — UI
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

خروجی در `ui/src-tauri/target/release/` قرار می‌گیرد.

### قابلیت‌های UI
- نمایش Equity، Daily PnL، Drawdown، Uptime
- لیست پوزیشن‌های باز
- لیست استراتژی‌های فعال و وزن‌ها
- مشاهده لاگ‌های زنده
- فعال/غیرفعال کردن Kill-Switch
- پشتیبانی از حالت Paper و Live

API وضعیت روی `http://127.0.0.1:8765` در دسترس است.
