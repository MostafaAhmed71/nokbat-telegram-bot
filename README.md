# nokbat-telegram-bot

**Telegram Bot لمدارس نخبة الشمال** — بوت تليجرام متكامل مع Gemini AI، Supabase backend، quizzes، challenges، cron jobs، admin panel + web dashboard.

> الاسم المقترح: **`nokbat-telegram-bot`**
> المجلد الحالي: `D:\teleg bot\`
> آخر تعديل: **2026-04-23**

---

## 🎯 الهدف من المشروع

بوت تليجرام شامل للمدارس يخدم:
- 🎓 **الطلاب**: استعلام عن الدرجات، المواد، اللجان، AI quizzes، تحديات يومية.
- 👨‍🏫 **المعلمين**: رفع محتوى (Word/PDF)، استعلام عن الطلاب.
- 👨‍💼 **الأدمن**: لوحة تحكم inline داخل تليجرام + web dashboard.
- 🤖 **AI**: محادثات مع Gemini + MiniSearch للبحث في محتوى المكتبة.

---

## 🛠️ التقنيات المستخدمة

### Core
- **Node.js >= 18**
- **Telegraf 4.16** (Telegram bot framework)
- **Express 5.1** (web dashboard)
- **PM2** (process manager)
- **node-cron 4.2** (scheduled tasks)

### AI & Search
- **@google/generative-ai 0.24** (Gemini API)
- **MiniSearch 7.2** (full-text search)

### Backend
- **Supabase JS 2.49** (database + auth)
- **csv-parse 5.6** (CSV import)
- **xlsx 0.18** (Excel import)
- **multer 2.0** (file uploads)
- **mammoth 1.12** (Word → text)
- **pdf-parse 2.4** (PDF → text)

### Config
- **dotenv 16.4**
- **ecosystem.config.cjs** (PM2 config)

---

## 📦 هيكل المشروع

```
teleg bot/
├── index.js                       # Main entry (starts bot + cron jobs)
├── bot.js                         # Bot builder (Telegraf instance)
├── package.json                   # "nokbat_alshamal_bot" v1.0.0
├── package-lock.json
├── ecosystem.config.cjs           # PM2 config (bot + admin web)
├── DEPLOY.md                      # Deployment guide
├── handlers/                      # Telegram handlers
│   ├── admin.js                   # Admin panel logic
│   ├── adminWebParity.js          # Admin panel - web parity
│   ├── diagnosticChallenge.js     # Challenge diagnostics
│   ├── results.js                 # Results reporting
│   ├── student.js                 # Student interactions
│   └── teacher.js                 # Teacher interactions
├── services/                      # Backend services
│   ├── adminExcel.js              # Excel handling
│   ├── adminReportCron.js         # Admin scheduled reports
│   ├── challengesCron.js          # Daily challenges
│   ├── chunkText.js               # Text chunking for AI
│   ├── contentLibrary.js          # Content storage
│   ├── examsCron.js               # Exam schedules
│   ├── extractText.js             # Document text extraction
│   ├── gemini.js                  # Gemini AI wrapper
│   ├── searchIndex.js             # MiniSearch index
│   └── supabase.js                # Supabase client
├── scripts/                       # Utility scripts
│   └── import-from-csv.js         # CSV data import
├── web/                           # Web admin dashboard
│   └── server.js                  # Express admin web
├── supabase/                      # DB migrations
│   └── migrations/
│       └── phase2_results.sql
├── data/                          # Local data (probably gitignored)
└── utils/                         # Utility functions
```

---

## 🚀 طريقة التشغيل

### 1) المتطلبات
- **Node.js 18+**
- **Telegram Bot Token** (من [@BotFather](https://t.me/BotFather))
- **Supabase project** (URL + ANON_KEY + SERVICE_KEY)
- **Gemini API Key** ([aistudio.google.com](https://aistudio.google.com))
- **PM2** (للـ production): `npm install -g pm2`

### 2) Install
```bash
cd "D:\teleg bot"
npm install
```

### 3) Setup `.env`
```bash
cp .env.example .env
# عدّل القيم:
# BOT_TOKEN=***
# SUPABASE_URL=***
# SUPABASE_ANON_KEY=***
# SUPABASE_SERVICE_KEY=***
# GEMINI_API_KEY=***
# ADMIN_TELEGRAM_ID=***
```

### 4) Supabase Migration
شغّل `supabase/migrations/phase2_results.sql` على Supabase SQL Editor.

### 5) Development
```bash
npm run dev
# Bot يعمل + daily challenge ensured
```

### 6) Production (PM2)
```bash
pm2 start ecosystem.config.cjs
pm2 save
pm2 logs nokbat_alshamal_bot
pm2 logs nokbat_admin_web
```

### 7) Data Import
```bash
npm run import:csv
# بيستورد data من CSV للـ Supabase
```

---

## 📝 أوامر مفيدة

| الأمر | الوظيفة |
|---|---|
| `npm start` | تشغيل البوت |
| `npm run dev` | تشغيل مع --watch (auto-reload) |
| `npm run import:csv` | استيراد CSV للـ Supabase |
| `npm run web` | تشغيل admin web dashboard |
| `pm2 start ecosystem.config.cjs` | تشغيل production |
| `pm2 logs` | مشاهدة logs |
| `pm2 stop` | إيقاف |

---

## 🤖 الـ Features الرئيسية

### للطلاب
- 📚 استعلام عن **الدرجات** والـ subjects.
- 🪑 استعلام عن **لجنة الامتحان** (`formatStudentCommittee`).
- 🤖 **AI Subject Helper** (`aiSubjectsKeyboard`).
- 📝 **AI Quiz** مع difficulty levels.
- 🎯 **Daily Challenge** (عبر cron).
- 🔍 البحث في المكتبة (MiniSearch).

### للمعلمين
- 📤 رفع محتوى (Word/PDF/صور).
- 🔍 استعلام عن الطلاب بالاسم.

### للأدمن
- 🎛️ **Inline admin panel** في تليجرام (`adminPanelKeyboard`).
- 📊 **Scheduled admin reports** (cron daily).
- 📁 استقبال صور + documents من الأدمن.
- 🌐 **Web dashboard** على port منفصل.
- 📥 **Excel import** (adminExcel.js).

### Cron Jobs (تلقائي)
- `startExamsCron(bot)` — جدولة الامتحانات.
- `startChallengesCron(bot)` — التحديات اليومية.
- `startAdminReportCron(bot)` — تقارير الأدمن.
- `ensureDailyChallenge()` — التأكد من وجود تحدي يومي.

---

## 📅 آخر تعديل

**2026-04-23**

---

## ⚠️ ملاحظات مهمة

### 🔒 Secrets في `.env`
كل الـ API keys (Bot Token, Supabase, Gemini) في `.env`. **متجيش .env في الـ git!**

### 🤖 Gemini API Rate Limits
- Gemini Free tier: 60 requests/min.
- الـ bot فيه AI quizzes + search → ممكن يوصل للحد بسرعة.
- لو محتاج أكتر: upgrade لـ paid tier.

### 🗄️ Supabase Tables
الـ migrations في `supabase/migrations/`. لازم تشغّلها كلها بالترتيب.

### 🔐 Admin Auth
- `ADMIN_TELEGRAM_ID` في `.env` بيحدد مين الأدمن.
- `isAdmin()` و `registerAdmin()` functions في `handlers/admin.js`.

### 📦 PM2 Setup
`ecosystem.config.cjs` بيشغّل الـ bot والـ web dashboard معاً. لو محتاج توقف واحدة بس:
```bash
pm2 stop nokbat_alshamal_bot
pm2 stop nokbat_admin_web
```

### 🌐 Admin Web
`web/server.js` بيكون على port منفصل (مش 3000 — شوف الـ config). للـ production، حط nginx reverse proxy + SSL.

### 📁 File Uploads
`multer` بيتعامل مع uploads. لو الملفات كبيرة، محتاج storage (مش filesystem).

### 🔄 Cron Times
الـ cron jobs (`examsCron`, `challengesCron`, `adminReportCron`) شغّالة بشكل افتراضي. عدّل الـ timings في الـ files لو محتاج.

### 🚀 Deploy
شوف `DEPLOY.md` للتفاصيل الكاملة (VPS + PM2 + Nginx).

### مفيش tests — `npm test` بيقول "no test specified" (placeholder).

### الـ Bot اسمه: `nokbat_alshamal_bot` (نخبة الشمال).