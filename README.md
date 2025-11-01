# x-research-curator-ar

منصة ذكاء اصطناعي للبحث العلمي - تجمع وتحلل المصادر الأكاديمية الغربية وتولّد مسودات تغريدات عربية رصينة في السياسة والاقتصاد والعلاقات الدولية والشرق الأوسط

## تنويه مهم حول التوليد الذكي
- يتم توليد التغريدات عبر Perplexity API بشكل صريح، وليس عبر OpenAI.
- يرجى التأكد من ضبط مفتاح البيئة: PERPLEXITY_API_KEY في ملف .env.
- لتسمية الخدمة وتوحيدها داخل الكود، يجب تعديل الملفات البرمجية لتكون الخدمة باسم: services/perplexity_tweet_generator.py بدل أي مرجع باسم openai.

## مثال تكامل Python مع Perplexity API لطباعة تغريدة علمية بالعربية
يوضح المثال التالي كيفية استدعاء واجهة Perplexity للطباعة السريعة لتغريدة عربية علمية. استبدل YOUR_PERPLEXITY_API_KEY بمفتاحك:

```python
import os
import requests

API_KEY = os.getenv("PERPLEXITY_API_KEY", "YOUR_PERPLEXITY_API_KEY")
ENDPOINT = "https://api.perplexity.ai/chat/completions"

headers = {
    "Authorization": f"Bearer {API_KEY}",
    "Content-Type": "application/json",
}

payload = {
    "model": "llama-3.1-sonar-small-128k-chat",  # اختر الموديل المناسب لحسابك
    "messages": [
        {
            "role": "system",
            "content": "أنت مساعد خبير ينتج تغريدات عربية علمية قصيرة وموجزة مع وسوم مناسبة.",
        },
        {
            "role": "user",
            "content": (
                "ولّد تغريدة عربية علمية عن تأثير الذكاء الاصطناعي على أسواق العمل في الشرق الأوسط، "
                "مع تضمين 2-3 وسوم مناسبة دون روابط، وبأسلوب رصين ومختصر (حد أقصى 280 حرفًا)."
            ),
        },
    ],
    "temperature": 0.5,
    "max_tokens": 180,
}

resp = requests.post(ENDPOINT, headers=headers, json=payload, timeout=60)
resp.raise_for_status()

data = resp.json()
# استخرج النص من البنية القياسية لاستجابة الدردشة
content = data.get("choices", [{}])[0].get("message", {}).get("content", "")
print(content)
```

نموذج مخرجات متوقعة:
```
🧠 تقارير حديثة تشير إلى أن الذكاء الاصطناعي سيعيد تشكيل مهارات سوق العمل في الشرق الأوسط، مع تركيز متزايد على الوظائف التقنية والتحليلية. الاستثمار في إعادة التأهيل والتعلم المستمر سيحدد التنافسية مستقبلًا. #الذكاء_الاصطناعي #أسواق_العمل #الشرق_الأوسط
```

## 📋 خطة العمل السريعة
### Sprint 0: إعداد البنية الأساسية (الأسبوع الأول)
- ✅ إنشاء مستودع المشروع
- 🔄 إعداد بيئة التطوير (Docker, PostgreSQL)
- 📦 إعداد FastAPI وهياكل البيانات الأساسية
- 🔌 تكامل أساسي مع pgvector للبحث الدلالي

### Sprint 1: النواة الوظيفية (الأسبوع الثاني)
- 🔍 تطوير محرك البحث الأكاديمي
- 🤖 تكامل Perplexity API لتوليد التغريدات
- 📊 واجهة إدارة أساسية
- ⚡ إعداد Celery للمهام غير المتزامنة

## 🏗️ الهيكل البرمجي
```
x-research-curator-ar/
├── app/
│   ├── api/
│   │   ├── endpoints/
│   │   │   ├── research.py      # نقاط البحث العلمي
│   │   │   ├── tweets.py        # إدارة التغريدات
│   │   │   └── integrations.py  # التكاملات الخارجية
│   │   └── deps.py              # التبعيات المشتركة
│   ├── core/
│   │   ├── config.py            # إعدادات التطبيق
│   │   ├── security.py          # الأمان والمصادقة
│   │   └── database.py          # إعدادات قاعدة البيانات
│   ├── models/
│   │   ├── research.py          # نماذج البحث العلمي
│   │   ├── tweets.py            # نماذج التغريدات
│   │   └── sources.py           # نماذج المصادر
│   ├── services/
│   │   ├── research_engine.py           # محرك البحث العلمي
│   │   ├── ai_curator.py                # منسق الذكاء الاصطناعي
│   │   ├── perplexity_tweet_generator.py# مولد التغريدات (Perplexity)
│   │   └── integrations/
│   │       ├── notion.py                # تكامل Notion
│   │       ├── slack.py                 # تكامل Slack
│   │       └── zapier.py                # تكامل Zapier
│   └── workers/
│       └── celery_app.py                # مهام Celery
├── docker-compose.yml
├── Dockerfile
├── requirements.txt
└── README.md
```

## 🛠️ التقنيات المستخدمة
- Backend: FastAPI (Python)
- قاعدة البيانات: PostgreSQL + pgvector
- المهام غير المتزامنة: Celery + Redis
- الذكاء الاصطناعي: Perplexity API لتوليد المحتوى
- التكاملات: Notion, Slack, Zapier
- النشر: Docker & Docker Compose
- البحث العلمي: arXiv, PubMed, Google Scholar APIs

## 📱 نموذج تغريدة علمية
### المدخلات:
موضوع: "تأثير الذكاء الاصطناعي على أسواق العمل في الشرق الأوسط"

مصادر: ["MIT Technology Review", "Nature AI", "Middle East Economic Survey"]

الوسوم: ["#الذكاء_الاصطناعي", "#أسواق_العمل", "#الشرق_الأوسط"]

### النتيجة (مثال):
`🧠 دراسة حديثة من MIT تكشف: الذكاء الاصطناعي قد يخلق فرص عمل جديدة مع تحول في المهارات المطلوبة. 📊 الحل: الاستثمار في التدريب وإعادة التأهيل. #الذكاء_الاصطناعي #أسواق_العمل #الشرق_الأوسط`

## 🚀 تعليمات التشغيل
### متطلبات النظام
- Docker و Docker Compose
- Python 3.9+
- PostgreSQL 14+ مع pgvector

### التشغيل السريع
```
# استنساخ المستودع
git clone https://github.com/khaliiid501/x-research-curator-ar.git
cd x-research-curator-ar

# إنشاء ملف البيئة
cp .env.example .env

# تحرير المتغيرات البيئية
# أضف PERPLEXITY_API_KEY بدلاً من OPENAI_API_KEY
nano .env

# تشغيل المنصة
docker-compose up -d

# الوصول للواجهة
# API: http://localhost:8000
# Documentation: http://localhost:8000/docs
```

### متغيرات البيئة المطلوبة
```
# Database
POSTGRES_USER=curator
POSTGRES_PASSWORD=your_secure_password
POSTGRES_DB=research_curator

# APIs
PERPLEXITY_API_KEY=your_perplexity_key
NOTION_API_KEY=your_notion_key
SLACK_BOT_TOKEN=your_slack_token
ZAPIER_WEBHOOK_URL=your_zapier_webhook

# Security
SECRET_KEY=your_secret_key_here
ALGORITHM=HS256
```

## x-research-curator-ar (English)

🔬 AI-Powered Arabic Scientific Research Curator

An intelligent platform that aggregates and analyzes Western academic sources to generate high-quality Arabic tweets in politics, economics, international relations, and Middle Eastern studies.

- Generation is explicitly powered by Perplexity API (not OpenAI).
- Service file should be named services/perplexity_tweet_generator.py.

### Quick Start
```
git clone https://github.com/khaliiid501/x-research-curator-ar.git
cd x-research-curator-ar
cp .env.example .env
# Add PERPLEXITY_API_KEY to .env
docker-compose up -d
# Access: http://localhost:8000/docs
```

### Python Integration Example (English)
```python
import os
import requests

API_KEY = os.getenv("PERPLEXITY_API_KEY")
ENDPOINT = "https://api.perplexity.ai/chat/completions"

headers = {"Authorization": f"Bearer {API_KEY}", "Content-Type": "application/json"}
payload = {
    "model": "llama-3.1-sonar-small-128k-chat",
    "messages": [
        {"role": "system", "content": "You write concise Arabic scientific tweets."},
        {"role": "user", "content": "اكتب تغريدة عربية علمية موجزة عن الحوسبة الكمية وتأثيرها المتوقع على التشفير."},
    ],
}

print(requests.post(ENDPOINT, headers=headers, json=payload).json()["choices"][0]["message"]["content"]) 
```

**المساهمة مرحب بها | Contributions Welcome**
هذا مشروع مفتوح المصدر يهدف لخدمة المجتمع العلمي العربي
This open-source project aims to serve the Arabic scientific community

📧 Contact: khalid@example.com  |  🐙 GitHub: @khaliiid501

Built with ❤️ for the Arabic scientific community
