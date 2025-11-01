# x-research-curator-ar

منصة ذكاء اصطناعي للبحث العلمي - تجمع وتحلل المصادر الأكاديمية الغربية وتولّد مسودات تغريدات عربية رصينة في السياسة والاقتصاد والعلاقات الدولية والشرق الأوسط

> 📌 ملحوظة هامة: المنصة مصممة للتكامل المستقبلي مع Zapier وSlack وNotion لتسهيل الأتمتة وتبسيط سير العمل.


تصميم واجهة المستخدم (Frontend) بأسلوب الزجاج السائل وأقسام التكامل
-------------------------------------------------------------------

يشرح هذا القسم تصميم الواجهة الأمامية بأسلوب الزجاج السائل (Glassmorphism) مع تقسيم واضح للوحات ونماذج الإدخال والتكاملات. الهدف هو واجهة نظيفة تدعم الإنتاجية، قابلة للتوسّع، وتتكامل بسلاسة مع خدمات Notion وSlack وZapier وواجهة الـ API الخلفية.

الخريطة العامة للأقسام
- بطاقات بزجاج سائل (Glassmorphism Cards): لعرض الملخصات، المصادر، ومخرجات الذكاء الاصطناعي.
- لوحة التغريدات العلمية (Tweets Board): أعمدة لحالات التغريدات (Draft, To Review, Approved, Scheduled, Published) مع سحب وإفلات.
- لوحة حالة التكامل (Integrations Status): حالة اتصال Notion/Slack/Zapier ومفاتيح التهيئة الأساسية.
- نموذج إدخال تغريدة (Tweet Composer): موضوع/مصادر/وسوم + زر توليد عبر Perplexity API.
- لوحة الإدارة (Admin Console): إعدادات الأمان، حدود المعدّل، مفاتيح البيئة، والتحكّم في التشغيلات الدورية.

تصميم بصري: Glassmorphism
- خلفية: تدرّج ناعم + ضبابية خلفية للبطاقات.
- بطاقات: زجاج نصف شفاف، حدود خفيفة، ظل ناعم، زوايا مستديرة.
- ألوان: محايدة مع تباين واضح للوضعين الفاتح/الداكن.

CSS مختصر لبطاقة زجاج سائل
```css
.glass-card {
  backdrop-filter: blur(12px) saturate(160%);
  -webkit-backdrop-filter: blur(12px) saturate(160%);
  background: rgba(255, 255, 255, 0.12);
  border: 1px solid rgba(255, 255, 255, 0.28);
  border-radius: 16px;
  box-shadow: 0 8px 24px rgba(0,0,0,0.15);
  padding: 16px;
}
:root[data-theme="dark"] .glass-card {
  background: rgba(20, 20, 20, 0.35);
  border-color: rgba(255, 255, 255, 0.18);
}
```

React مختصر لبطاقة + قسم التغريدات
```tsx
// components/GlassCard.tsx
import React from 'react';
export const GlassCard: React.FC<React.PropsWithChildren<{ title?: string; footer?: React.ReactNode }>> = ({ title, footer, children }) => (
  <div className="glass-card">
    {title && <div style={{fontWeight:600, marginBottom:8}}>{title}</div>}
    <div>{children}</div>
    {footer && <div style={{marginTop:12}}>{footer}</div>}
  </div>
);

// components/TweetsBoard.tsx
import React from 'react';
import { GlassCard } from './GlassCard';

type Tweet = { id: string; title: string; tags?: string[]; status: 'Draft'|'To Review'|'Approved'|'Scheduled'|'Published' };
const COLUMNS: Tweet['status'][] = ['Draft','To Review','Approved','Scheduled','Published'];

export const TweetsBoard: React.FC<{ data: Tweet[]; onMove:(id:string, to:Tweet['status'])=>void }>=({data,onMove})=>{
  return (
    <div style={{display:'grid', gridTemplateColumns:'repeat(5,1fr)', gap:16}}>
      {COLUMNS.map(col=> (
        <GlassCard key={col} title={col}>
          <div onDragOver={e=>e.preventDefault()} onDrop={e=>{
            const id = e.dataTransfer.getData('text/plain');
            onMove(id, col);
          }} style={{minHeight:220, display:'flex', flexDirection:'column', gap:8}}>
            {data.filter(t=>t.status===col).map(t=> (
              <div key={t.id} draggable onDragStart={e=>e.dataTransfer.setData('text/plain', t.id)}
                   style={{padding:12, borderRadius:12, background:'rgba(255,255,255,0.08)', border:'1px solid rgba(255,255,255,0.2)'}}>
                <div style={{fontWeight:600}}>{t.title}</div>
                <div style={{opacity:0.8, fontSize:12}}>{t.tags?.map(x=>`#${x}`).join(' ')}</div>
              </div>
            ))}
          </div>
        </GlassCard>
      ))}
    </div>
  );
}
```

نموذج إدخال التغريدة (Composer)
- الحقول: topic, sources[], hashtags[], temperature, max_tokens.
- أزرار: Generate (Perplexity), Save Draft, Send to Review.
- تحقّق: 280 حرفًا، منع روابط غير مرغوبة، التحقق من الوسوم.

مثال React مختصر للنموذج
```tsx
// components/TweetComposer.tsx
import React, { useState } from 'react';
export const TweetComposer: React.FC<{ onGenerate:(p:{topic:string;sources:string[];hashtags:string[]})=>Promise<string> }>=({onGenerate})=>{
  const [topic,setTopic]=useState('');
  const [sources,setSources]=useState<string[]>([]);
  const [hashtags,setHashtags]=useState<string[]>([]);
  const [draft,setDraft]=useState('');
  return (
    <div className="glass-card">
      <input placeholder="الموضوع" value={topic} onChange={e=>setTopic(e.target.value)} />
      <input placeholder="مصدر (اضغط Enter)" onKeyDown={e=>{ if(e.key==='Enter'){ setSources([...sources, (e.target as HTMLInputElement).value]); (e.target as HTMLInputElement).value=''; }}} />
      <input placeholder="وسم (اضغط Enter)" onKeyDown={e=>{ if(e.key==='Enter'){ setHashtags([...hashtags, (e.target as HTMLInputElement).value.replace('#','')]); (e.target as HTMLInputElement).value=''; }}} />
      <div style={{display:'flex', gap:8, marginTop:8}}>
        <button onClick={async()=> setDraft(await onGenerate({topic,sources,hashtags}))}>Generate</button>
        <button>Save Draft</button>
        <button>Send to Review</button>
      </div>
      <textarea placeholder="المسودة" value={draft} onChange={e=>setDraft(e.target.value)} maxLength={280} />
    </div>
  );
}
```

لوحة حالة التكامل (Notion/Slack/Zapier)
- تعرض الحالة (Connected/Not configured/Failed) ومفاتيح البيئة المقروءة فقط.
- إجراءات سريعة: اختبار الاتصال، إعادة الإرسال، فتح السجلات.

```tsx
// components/IntegrationsStatus.tsx
import React from 'react';
import { GlassCard } from './GlassCard';

type Status = 'Connected'|'Not configured'|'Failed';
export const IntegrationsStatus: React.FC<{ status:{ notion:Status; slack:Status; zapier:Status } }>=({status})=> (
  <div style={{display:'grid', gridTemplateColumns:'repeat(3,1fr)', gap:16}}>
    {(['notion','slack','zapier'] as const).map(k=> (
      <GlassCard key={k} title={k.toUpperCase()} footer={<div style={{display:'flex', gap:8}}>
        <button>Test</button><button>Retry</button><button>Logs</button>
      </div>}>
        <div style={{fontSize:14}}>Status: {status[k]}</div>
        <div style={{opacity:0.7, fontSize:12}}>Configured via .env / config.py</div>
      </GlassCard>
    ))}
  </div>
);
```

لوحة الإدارة (Admin Console)
- تبديل التكاملات + ضبط الحدود (rate limit) + جدولة مهام Celery.
- إدارة مفاتيح البيئة عبر Secrets (عرض مقنّع).

خطوات الربط مع API لكل قسم
1) نموذج الإدخال (Perplexity)
- Endpoint: POST /api/tweets/generate
- Body: { topic, sources[], hashtags[] }
- Action: يعيد نص المسودة + بيانات التحليل.

2) لوحة التغريدات
- GET /api/tweets?status=Draft|To%20Review|...
- PATCH /api/tweets/{id} { status }
- Websocket/Server-Sent Events اختياري للتحديث اللحظي.

3) التكامل مع Notion/Slack/Zapier
- Notion: POST /api/integrations/notion/pages  { title, content, tags, database_id }
- Slack: POST /api/integrations/slack/message  { channel, text }
- Zapier: POST /api/integrations/zapier/trigger { event, payload }

4) لوحة الإدارة
- GET/PUT /api/admin/settings  (limits, toggles, ids)
- POST /api/admin/tasks/publish_to_integrations

ملاحظات قابلية التوسّع والتكامل المعرفي
- تصميم مركزي للمكوّنات (GlassCard/TweetsBoard) يسهل إعادة الاستخدام.
- فصل طبقة الخدمات عن الواجهة عبر واجهات واضحة يجعل إضافة مصادر/تكاملات جديدة بسيطًا.
- دعم مخازن معرفة خارجية (Notion/Docs) عبر وصلات موحّدة يسهّل التوسّع المعرفي.

---

تنويه مهم حول التوليد الذكي
- يتم توليد التغريدات عبر Perplexity API بشكل صريح، وليس عبر OpenAI.
- يرجى التأكد من ضبط مفتاح البيئة: PERPLEXITY_API_KEY في ملف .env.
- لتسمية الخدمة وتوحيدها داخل الكود، يجب تعديل الملفات البرمجية لتكون الخدمة باسم: services/perplexity_tweet_generator.py بدل أي مرجع باسم openai.

مثال تكامل Python مع Perplexity API لطباعة تغريدة علمية بالعربية
يوضح المثال التالي كيفية استدعاء واجهة Perplexity للطباعة السريعة لتغريدة عربية علمية. استبدل YOUR_PERPLEXITY_API_KEY بمفتاحك:
```python
import os, requests
API_KEY = os.getenv("PERPLEXITY_API_KEY", "YOUR_PERPLEXITY_API_KEY")
ENDPOINT = "https://api.perplexity.ai/chat/completions"
headers = {"Authorization": f"Bearer {API_KEY}", "Content-Type": "application/json"}
payload = {
  "model": "llama-3.1-sonar-small-128k-chat",
  "messages": [
    {"role": "system", "content": "أنت مساعد خبير ينتج تغريدات عربية علمية قصيرة وموجزة مع وسوم مناسبة."},
    {"role": "user", "content": "ولّد تغريدة عربية علمية عن تأثير الذكاء الاصطناعي على أسواق العمل في الشرق الأوسط، مع تضمين 2-3 وسوم مناسبة دون روابط، وبأسلوب رصين ومختصر (حد أقصى 280 حرفًا)."}
  ],
  "temperature": 0.5,
  "max_tokens": 180
}
resp = requests.post(ENDPOINT, headers=headers, json=payload, timeout=60)
resp.raise_for_status()
print(resp.json().get("choices", [{}])[0].get("message", {}).get("content", ""))
```

📋 خطة العمل السريعة
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

🏗️ الهيكل البرمجي
```
x-research-curator-ar/
├── app/
│   ├── api/
│   │   ├── endpoints/
│   │   │   ├── research.py # نقاط البحث العلمي
│   │   │   ├── tweets.py   # إدارة التغريدات
│   │   │   └── integrations.py # التكاملات الخارجية
│   │   └── deps.py          # التبعيات المشتركة
│   ├── core/
│   │   ├── config.py   # إعدادات التطبيق
│   │   ├── security.py  # الأمان والمصادقة
│   │   └── database.py  # إعدادات قاعدة البيانات
│   ├── models/
│   │   ├── research.py # نماذج البحث العلمي
│   │   ├── tweets.py   # نماذج التغريدات
│   │   └── sources.py  # نماذج المصادر
│   ├── services/
│   │   ├── research_engine.py            # محرك البحث العلمي
│   │   ├── ai_curator.py                 # منسق الذكاء الاصطناعي
│   │   ├── perplexity_tweet_generator.py # مولد التغريدات (Perplexity)
│   │   └── integrations/
│   │       ├── notion.py # تكامل Notion
│   │       ├── slack.py  # تكامل Slack
│   │       └── zapier.py # تكامل Zapier
│   └── workers/
│       └── celery_app.py # مهام Celery
├── docker-compose.yml
├── Dockerfile
├── requirements.txt
└── README.md
```

🛠️ التقنيات المستخدمة
- Backend: FastAPI (Python)
- قاعدة البيانات: PostgreSQL + pgvector
- المهام غير المتزامنة: Celery + Redis
- الذكاء الاصطناعي: Perplexity API لتوليد المحتوى
- التكاملات: Notion, Slack, Zapier
- النشر: Docker & Docker Compose
- البحث العلمي: arXiv, PubMed, Google Scholar APIs

خطوات الربط البرمجي مع Notion وSlack وZapier
- موضحة أعلاه ضمن لوحة التكامل وروابط API الخلفية، مع أمثلة services/integrations/*.py.

x-research-curator-ar (English)

🔬 AI-Powered Arabic Scientific Research Curator
An intelligent platform that aggregates and analyzes Western academic sources to generate high-quality Arabic tweets in politics, economics, international relations, and Middle Eastern studies.

> Important: Platform designed for Zapier, Slack, Notion integrations.

### Quick Start
```bash
git clone https://github.com/khaliiid501/x-research-curator-ar.git
cd x-research-curator-ar
cp .env.example .env
# Add PERPLEXITY_API_KEY to .env
docker-compose up -d
# Access: http://localhost:8000/docs
```
