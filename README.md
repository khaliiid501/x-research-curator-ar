# x-research-curator-ar
منصة ذكاء اصطناعي للبحث العلمي - تجمع وتحلل المصادر الأكاديمية الغربية وتولّد مسودات تغريدات عربية رصينة في السياسة والاقتصاد والعلاقات الدولية والشرق الأوسط

> **📌 ملحوظة هامة:** المنصة مصممة للتكامل المستقبلي مع **Zapier** و**Slack** و**Notion** لتسهيل الأتمتة وتبسيط سير العمل.

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
content = data.get("choices", [{}])[0].get("message", {}).get("content", "")
print(content)
```

**نموذج مخرجات متوقعة:**

`🧠 تقارير حديثة تشير إلى أن الذكاء الاصطناعي سيعيد تشكيل مهارات سوق العمل في الشرق الأوسط، مع تركيز متزايد على الوظائف التقنية والتحليلية. الاستثمار في إعادة التأهيل والتعلم المستمر سيحدد التنافسية مستقبلًا. #الذكاء_الاصطناعي #أسواق_العمل #الشرق_الأوسط`

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
`x-research-curator-ar/
├── app/
│   ├── api/
│   │   ├── endpoints/
│   │   │   ├── research.py           # نقاط البحث العلمي
│   │   │   ├── tweets.py             # إدارة التغريدات
│   │   │   └── integrations.py       # التكاملات الخارجية
│   │   └── deps.py                   # التبعيات المشتركة
│   ├── core/
│   │   ├── config.py                 # إعدادات التطبيق
│   │   ├── security.py               # الأمان والمصادقة
│   │   └── database.py               # إعدادات قاعدة البيانات
│   ├── models/
│   │   ├── research.py               # نماذج البحث العلمي
│   │   ├── tweets.py                 # نماذج التغريدات
│   │   └── sources.py                # نماذج المصادر
│   ├── services/
│   │   ├── research_engine.py        # محرك البحث العلمي
│   │   ├── ai_curator.py             # منسق الذكاء الاصطناعي
│   │   ├── perplexity_tweet_generator.py # مولد التغريدات (Perplexity)
│   │   └── integrations/
│   │       ├── notion.py             # تكامل Notion
│   │       ├── slack.py              # تكامل Slack
│   │       └── zapier.py             # تكامل Zapier
│   └── workers/
│       └── celery_app.py             # مهام Celery
├── docker-compose.yml
├── Dockerfile
├── requirements.txt
└── README.md`

## 🛠️ التقنيات المستخدمة
- **Backend:** FastAPI (Python)
- **قاعدة البيانات:** PostgreSQL + pgvector
- **المهام غير المتزامنة:** Celery + Redis
- **الذكاء الاصطناعي:** Perplexity API لتوليد المحتوى
- **التكاملات:** Notion, Slack, Zapier (للأتمتة وتسهيل سير العمل)
- **النشر:** Docker & Docker Compose
- **البحث العلمي:** arXiv, PubMed, Google Scholar APIs

> **🔗 التكامل المستقبلي:** تم تصميم المنصة لدعم Zapier وSlack وNotion لتسهيل الأتمتة الكاملة وتبسيط إدارة سير العمل البحثي.

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
```bash
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
```env
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

## خطوات الربط البرمجي مع Notion وSlack وZapier
يوضح هذا القسم دور كل خدمة، متطلبات الإعداد في .env وapp/core/config.py، وأمثلة بايثون مختصرة لاستخدامها داخل services/integrations.

- دور الخدمات باختصار:
  - Notion: حفظ المخرجات (أبحاث، تغريدات) في قاعدة معرفة ولوحات Kanban.
  - Slack: إرسال تنبيهات ومراجعات سريعة للفريق داخل قنوات محددة.
  - Zapier: تشغيل أتمتة خارجية (مثل جدولة النشر، إرسال إلى Google Sheets، الخ).

- متطلبات .env وconfig.py:
  - ملف .env أعلاه يحتوي على NOTION_API_KEY وSLACK_BOT_TOKEN وZAPIER_WEBHOOK_URL.
  - في app/core/config.py أضف/تأكد من الآتي:
    ```python
    from pydantic import BaseSettings

    class Settings(BaseSettings):
        NOTION_API_KEY: str
        SLACK_BOT_TOKEN: str
        ZAPIER_WEBHOOK_URL: str
        # ... بقية الإعدادات الموجودة

        class Config:
            env_file = ".env"

    settings = Settings()
    ```

### 1) Notion
- المتطلبات: أنشئ Integration من https://www.notion.so/my-integrations وأعطِ الإذن لقاعدة/صفحة معينة، واحصل على database_id.
- مثال Python مختصر (services/integrations/notion.py):
```python
# pip install notion-client
from notion_client import Client
from app.core.config import settings

notion = Client(auth=settings.NOTION_API_KEY)

def create_tweet_page(database_id: str, title: str, content: str, tags: list[str] | None = None):
    props = {
        "Name": {"title": [{"text": {"content": title}}]},
        "Status": {"select": {"name": "To Review"}},
    }
    if tags:
        props["Tags"] = {"multi_select": [{"name": t} for t in tags]}

    return notion.pages.create(
        parent={"database_id": database_id},
        properties=props,
        children=[{"object": "block", "type": "paragraph", "paragraph": {"rich_text": [{"text": {"content": content}}]}}],
    )
```
- الاستخدام:
```python
# داخل منسق العمل بعد توليد التغريدة
create_tweet_page(database_id="YOUR_DB_ID", title="تغريدة: الذكاء الاصطناعي", content="النص...", tags=["AI", "ME"])
```

### 2) Slack
- المتطلبات: أنشئ Slack App، فعّل Bot Token Scopes مثل chat:write، أضِف التطبيق إلى القناة، واحصل على SLACK_BOT_TOKEN.
- مثال Python مختصر (services/integrations/slack.py):
```python
# pip install slack_sdk
from slack_sdk import WebClient
from slack_sdk.errors import SlackApiError
from app.core.config import settings

client = WebClient(token=settings.SLACK_BOT_TOKEN)

def send_message(channel: str, text: str):
    try:
        return client.chat_postMessage(channel=channel, text=text)
    except SlackApiError as e:
        # يفضّل تسجيل الخطأ بدلاً من طباعته في الإنتاج
        raise RuntimeError(f"Slack error: {e.response['error']}")
```
- الاستخدام:
```python
send_message(channel="#research-curation", text="تم إنشاء مسودة تغريدة جديدة للمراجعة.")
```

### 3) Zapier
- المتطلبات: أنشئ Zap من نوع Catch Hook واحصل على ZAPIER_WEBHOOK_URL.
- مثال Python مختصر (services/integrations/zapier.py):
```python
import json
import requests
from app.core.config import settings

def trigger_zap(payload: dict):
    resp = requests.post(settings.ZAPIER_WEBHOOK_URL, data=json.dumps(payload), headers={"Content-Type": "application/json"}, timeout=30)
    resp.raise_for_status()
    return resp.json() if resp.content else {"ok": True}
```
- الاستخدام:
```python
trigger_zap({"event": "tweet_draft_created", "tweet_id": 123, "topic": "AI & Jobs"})
```

### ملاحظات الأمان والتنفيذ
- لا تحفظ المفاتيح داخل المستودع. استخدم .env وSecrets في بيئات النشر.
- فعّل حدود المعدّل retries والـ timeouts لكل تكامل.
- راقب السجلات (logging) وتجنب طباعة الأسرار.
- استخدم صلاحيات أقل ضرورة لـ Notion وSlack (least privilege).

### ربط لوحة الإدارة واستخدام Celery
- في واجهة الإدارة، أضف عناصر تحكم لتفعيل/تعطيل كل تكامل وتحديد معرفات مثل database_id واسم القناة.
- استخدم Celery للمهام غير المتزامنة الثقيلة مثل:
  - إنشاء صفحات Notion عند وصول دفعات نتائج كبيرة.
  - إرسال تنبيهات Slack الدورية أو الجماعية.
  - استدعاء Zapier لسلاسل أتمتة متعددة.
- مثال Celery (app/workers/celery_app.py):
```python
from celery import Celery
from app.services.integrations.notion import create_tweet_page
from app.services.integrations.slack import send_message
from app.services.integrations.zapier import trigger_zap

celery = Celery(__name__, broker="redis://redis:6379/0", backend="redis://redis:6379/0")

@celery.task
def publish_to_integrations(tweet: dict):
    if tweet.get("to_notion"):
        create_tweet_page(database_id=tweet["notion_db"], title=tweet["title"], content=tweet["content"], tags=tweet.get("tags"))
    if tweet.get("to_slack"):
        send_message(channel=tweet.get("slack_channel", "#general"), text=f"مسودة جديدة: {tweet['title']}")
    if tweet.get("to_zapier"):
        trigger_zap({"event": "tweet_published", "title": tweet["title"]})
```
- من لوحة الإدارة، استدعِ المهمة كالتالي:
```python
from app.workers.celery_app import publish_to_integrations
publish_to_integrations.delay({
  "title": "تأثير الذكاء الاصطناعي على سوق العمل",
  "content": "النص...",
  "tags": ["AI", "Work"],
  "to_notion": True, "notion_db": "YOUR_DB_ID",
  "to_slack": True, "slack_channel": "#research-curation",
  "to_zapier": True
})
```

---

# x-research-curator-ar (English)

🔬 **AI-Powered Arabic Scientific Research Curator**

An intelligent platform that aggregates and analyzes Western academic sources to generate high-quality Arabic tweets in politics, economics, international relations, and Middle Eastern studies.

> **📌 Important Note:** The platform is designed for future integration with **Zapier**, **Slack**, and **Notion** to facilitate automation and streamline workflows.

- Generation is explicitly powered by **Perplexity API** (not OpenAI).
- Service file should be named `services/perplexity_tweet_generator.py`.

### Quick Start
```bash
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

📧 **Contact:** khalid50154@gmail.com | 🐙 **GitHub:**
