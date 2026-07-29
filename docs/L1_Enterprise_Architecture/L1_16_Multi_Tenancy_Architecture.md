<div dir="rtl">

# معماری چند‑مستأجری (Multi-Tenancy Architecture)

**نام پلتفرم:** Enterprise AI Context & Automation Platform

**نسخه:** 1.0

**وضعیت:** پیش‌نویس

**سند مرجع نام‌گذاری:** `Naming_And_Terminology_Glossary`

---

## ۱. هدف سند

چند‑مستأجری (Multi-Tenancy) یکی از الزامات محوری این پلتفرم است (طبق اصل «Enterprise First» در `Vision_and_Strategy` و محدوده فاز ۲ نقشه راه در `Roadmap_And_Product_Phasing`)، اما تاکنون در بیش از ده سند مختلف به‌صورت پراکنده و جزئی ذکر شده (مدل انتخابی در `ADR-004`، الزامات Isolation در تک‌تک اسناد دامنه، سطح زیرساخت در `Deployment_Environment_Topology` بخش ۴) بدون آنکه یک سند واحد، تصمیمات و الزامات آن را یکجا تجمیع کند.

این سند مرجع واحد برای تمام تصمیمات معماری Multi-Tenancy است: مدل Isolation در هر لایه، چرخه عمر Tenant، مدل Quota، معیار Migration به استقرار اختصاصی و مدل Cost Allocation. هدف آن این است که هیچ تیم توسعه‌دهنده دامنه مجبور نباشد به‌طور مستقل و بدون هماهنگی، تصمیم بگیرد که «Isolation در این دامنه دقیقاً به چه شکل پیاده‌سازی شود».

---

## ۲. دامنه سند

- **مدل Isolation در هر لایه معماری:** Compute، Network، و هر یک از انواع ذخیره‌سازی (رابطه‌ای، برداری، Cache، Object Storage، Message Queue)
- **چرخه عمر Tenant:** از Provisioning تا Offboarding و حذف کامل داده
- **مدل Quota و محدودیت مصرف per-Tenant:** در سطح API، مدل هوش مصنوعی، ذخیره‌سازی و اجرای Tool
- **معیار تصمیم برای Dedicated Deployment:** چه زمانی یک Tenant از زیرساخت مشترک خارج و مستقل می‌شود
- **مدل Cost Allocation:** Chargeback/Showback در سطح Tenant

جزئیات پیاده‌سازی فنی دقیق (مانند نوع دقیق Partitioning در هر پایگاه داده) در اسناد طراحی سطح پایین‌تر هر مؤلفه تدوین می‌شود. این سند سطح **تصمیم معماری و الزامات** را تعریف می‌کند.

---

## ۳. اصول طراحی

| اصل | توضیح |
| :--- | :--- |
| **جداسازی پیش‌فرض (Isolation by Default)** | هر داده، منبع یا رویداد جدیدی که در پلتفرم تعریف می‌شود، به‌صورت پیش‌فرض باید فیلد `tenant_id` داشته باشد؛ عدم وجود این فیلد باید در فرآیند Code Review رد شود. |
| **مدل ترکیبی (Hybrid Isolation)** | طبق `ADR-004`، پلتفرم از یک مدل ترکیبی پیروی می‌کند: زیرساخت مشترک با `tenant_id` برای اکثریت Tenantها، و امکان استقرار کاملاً اختصاصی برای Tenantهای با نیاز امنیتی/مقیاس بالا. |
| **عدم نشت میان‌مستأجری (No Cross-Tenant Leakage)** | هیچ Query، Cache Key، Log Entry یا پیام Event نباید بدون فیلتر صریح `tenant_id` قابل دسترسی باشد؛ این الزام حتی برای مسیرهای اضطراری (Break-Glass Access، طبق `Data_Governance_and_Compliance` بخش ۱۰) اعمال می‌شود. |
| **مقیاس‌پذیری مستقل هر Tenant** | مصرف منابع یک Tenant پرترافیک نباید روی کیفیت سرویس سایر Tenantها اثر بگذارد (Noisy Neighbor Problem)؛ این الزام مبنای مدل Quota در بخش ۶ است. |
| **قابلیت مهاجرت بدون Downtime** | تغییر یک Tenant از حالت Shared به Dedicated (یا برعکس) باید بدون قطع سرویس برای کاربران آن Tenant ممکن باشد. |
| **شفافیت هزینه** | هر Tenant باید بتواند مصرف واقعی خود (محاسبات، ذخیره‌سازی، مصرف مدل) را مشاهده کند؛ این الزام هم برای مصارف داخلی سازمان و هم برای مدل احتمالی SaaS چندسازمانی معتبر است. |

---

## ۴. مدل Isolation در هر لایه

| لایه | سطح Isolation پیش‌فرض | مکانیزم | یادداشت |
| :--- | :--- | :--- | :--- |
| **Compute (Kubernetes)** | Logical (Namespace مشترک با محدودیت Resource Quota per-Tenant) | Kubernetes ResourceQuota + LimitRange به ازای گروه Tenant | برای Tenant با سطح Dedicated: Namespace اختصاصی یا Cluster جداگانه |
| **Network** | Logical (Network Policy) | Kubernetes NetworkPolicy برای محدودسازی ترافیک بین Podهای Tenantهای مختلف | برای Dedicated: VPC/Subnet اختصاصی |
| **پایگاه داده رابطه‌ای (Operational DB)** | Logical (فیلد `tenant_id` + Row-Level Security) | ستون اجباری `tenant_id` در تمام جداول + اعمال RLS در سطح پایگاه داده (نه فقط در کد اپلیکیشن) | RLS به‌عنوان لایه دفاع دوم، حتی اگر کد اپلیکیشن فیلتر را فراموش کند |
| **پایگاه داده برداری (Vector DB)** | Logical (Partition/Collection به ازای Tenant یا فیلتر Metadata) | بسته به فناوری انتخابی (طبق `ADR-002`)؛ استفاده از Collection مجزا برای Tenantهای بزرگ، فیلتر Metadata برای Tenantهای کوچک | تصمیم بین Collection مجزا و فیلتر مشترک باید بر اساس حجم داده Tenant اتخاذ شود (آستانه در بخش ۷) |
| **Object Storage** | Logical (Prefix/Bucket به ازای Tenant) | مسیر `/{tenant_id}/...` با Policy دسترسی مبتنی بر IAM | برای Dedicated: Bucket اختصاصی |
| **Cache (Redis)** | Logical (Key Prefix) | تمام کلیدها با پیشوند `tenant:{tenant_id}:` | ریسک Noisy Neighbor در Cache مشترک؛ نیاز به Quota حافظه per-Tenant |
| **Message Broker (Kafka)** | Logical (فیلد در Payload + احتمالاً Topic مجزا برای رویدادهای پرحجم) | `tenant_id` در Event Envelope (طبق `Event_&_Webhook_Domain_Architecture` بخش ۴.۲.۱) الزامی است | برای Tenant با حجم رویداد بسیار بالا، Topic اختصاصی قابل بررسی است |
| **Secrets (Vault)** | Physical (Path مجزا) | `secret/{tenant_id}/...` با Policy دسترسی مجزا در سطح Vault | استثناء نسبت به سایر لایه‌ها: به‌دلیل حساسیت، از ابتدا Physical Isolation در سطح Path اعمال می‌شود، نه فقط فیلتر منطقی |

---

## ۵. چرخه عمر Tenant (Tenant Lifecycle)

```text
Provisioning Requested
        │
        ▼
Provisioning (ایجاد tenant_id، تخصیص Namespace/Quota اولیه، ایجاد Identity Realm)
        │
        ▼
Active (مصرف عادی، پایش مستمر Quota)
        │
        ├── Upgrade to Dedicated (در صورت عبور از آستانه بخش ۷)
        │
        ├── Suspended (تعلیق موقت — نقض Policy یا عدم پرداخت، دسترسی مسدود اما داده حفظ می‌شود)
        │       │
        │       └── Reactivation (بازگشت به Active)
        │
        ▼
Offboarding Requested (توسط مشتری یا سازمان)
        │
        ▼
Data Export Window (دوره‌ای که Tenant می‌تواند داده‌های خود را طبق حق قابلیت حمل داده در `Data_Governance_and_Compliance` بخش ۱۱ دریافت کند)
        │
        ▼
Secure Purge (حذف امن تمام داده Tenant از تمام لایه‌های بخش ۴، طبق `Business_Rule_Catalog` BR‑012)
        │
        ▼
Tenant Archived (فقط متادیتای حداقلی برای الزامات قانونی/حسابرسی نگهداری می‌شود)
```

| وضعیت | حداکثر مدت مجاز | اقدام در صورت عبور از مدت |
| :--- | :--- | :--- |
| **Suspended** | ۹۰ روز (قابل تنظیم بر اساس قرارداد) | انتقال خودکار به Offboarding |
| **Data Export Window** | ۳۰ روز پس از درخواست Offboarding | آغاز خودکار Secure Purge |

---

## ۶. مدل Quota و محدودیت مصرف

| نوع Quota | سطح اعمال | مرجع پیاده‌سازی |
| :--- | :--- | :--- |
| **Rate Limit درخواست API** | هر Tenant / هر کاربر | `API_&_Integration_Domain_Architecture` بخش ۴.۴ (این سند فقط الزام «باید per-Tenant باشد» را اضافه می‌کند) |
| **محدودیت مصرف Token مدل** | هر Tenant | `Responsible_AI_Guidelines` BR‑041؛ این سند تأکید می‌کند این محدودیت باید در سطح Tenant، نه فقط در سطح کلی پلتفرم، قابل تنظیم باشد |
| **محدودیت تعداد Agent همزمان** | هر Tenant | `Business_Rule_Catalog` BR‑030 |
| **Resource Quota محاسباتی (CPU/Memory)** | هر Tenant (در سطح Namespace Kubernetes) | `Infrastructure_&_Operations_Domain_Architecture` بخش ۴.۳ |
| **سقف ذخیره‌سازی (Storage Quota)** | هر Tenant | تعیین سقف حجم Object Storage و تعداد رکورد Vector DB به ازای هر Tenant؛ در صورت عبور، هشدار به مدیر Tenant و امکان محدودسازی Ingestion جدید |

**قاعده تعیین‌کننده:** هر Quota جدیدی که در آینده به هر دامنه اضافه شود، باید از ابتدا در سطح Tenant قابل تنظیم طراحی شود، نه فقط در سطح کلی سیستم؛ این الزام باید در Checklist طراحی هر Component جدید لحاظ شود.

---

## ۷. معیار تصمیم برای Dedicated Deployment

Tenant باید از حالت Shared به Dedicated منتقل شود اگر **حداقل یکی** از شرایط زیر برقرار باشد:

| معیار | آستانه پیشنهادی |
| :--- | :--- |
| حجم داده نمایه‌شده (Knowledge Objects) | بیش از ۵۰۰٬۰۰۰ سند |
| نیاز امنیتی/قانونی صریح (مثلاً الزام Residency داده در منطقه جغرافیایی خاص) | هر مقدار (مستقل از حجم) |
| نرخ درخواست پایدار | بیش از ۱۰٪ از ظرفیت کل خوشه مشترک به‌طور مستمر |
| توافق قراردادی SLA اختصاصی بالاتر از SLA عمومی پلتفرم | مطابق بند قرارداد |

فرآیند این Migration باید بدون Downtime باشد (طبق اصل بخش ۳) و شامل: کپی داده به زیرساخت اختصاصی → تأیید یکپارچگی → سوئیچ ترافیک → آزادسازی منابع در زیرساخت مشترک.

---

## ۸. مدل Cost Allocation (Chargeback/Showback)

- هر رویداد مصرف قابل اندازه‌گیری (فراخوانی مدل، اجرای Tool، حجم Storage، درخواست API) باید با `tenant_id` برچسب‌گذاری شود تا امکان تجمیع هزینه به ازای Tenant فراهم شود.
- گزارش هزینه ماهانه per-Tenant باید از داده‌های موجود در `Monitoring_and_Observability_Overview` (بخش ۷، فیلد `Cost`) تولید شود.
- این سند مدل قیمت‌گذاری یا مدل تجاری Chargeback را تعیین نمی‌کند (خارج از حوزه فنی)؛ صرفاً تضمین می‌کند داده لازم برای چنین تصمیمی از ابتدا جمع‌آوری می‌شود.

---

## ۹. قوانین کسب‌وکار مرتبط

| شناسه پیشنهادی | عنوان | شرح | اولویت |
| :--- | :--- | :--- | :--- |
| BR‑113 | الزام فیلد Tenant در تمام موجودیت‌ها | هر رکورد داده، پیام رویداد و ورودی Cache باید دارای فیلد `tenant_id` باشد. | بحرانی |
| BR‑114 | ممنوعیت Query بدون فیلتر Tenant | هیچ Query در پایگاه داده مشترک نباید بدون شرط `tenant_id` اجرا شود؛ این الزام باید در سطح Code Review و در صورت امکان در سطح RLS پایگاه داده اعمال شود. | بحرانی |
| BR‑115 | دوره حداکثر تعلیق Tenant | یک Tenant با وضعیت Suspended نباید بیش از ۹۰ روز بدون تصمیم‌گیری باقی بماند. | بالا |
| BR‑116 | حذف امن پس از Offboarding | داده‌های یک Tenant باید حداکثر ۳۰ روز پس از پایان دوره Data Export، به‌طور کامل و امن حذف شوند. | بحرانی |

> این شناسه‌ها باید طبق فرآیند بخش ۶ سند `Business_Rule_Catalog` رسماً ثبت شوند.

---

## ۱۰. ارتباط با سایر اسناد (References)

| سند | نوع ارتباط |
| :--- | :--- |
| `Architecture_Decisions_Log` (ADR‑004) | مرجع تصمیم اولیه انتخاب مدل ترکیبی چند‑مستأجری که این سند جزئیات اجرایی آن را تفصیل می‌دهد |
| `Deployment_Environment_Topology` | مرجع بخش ۴ (چند‑مستأجری) که این سند آن را به سطح تصمیم کامل بسط می‌دهد |
| `Data_Governance_and_Compliance` | مرجع حق حذف و قابلیت حمل داده که چرخه Offboarding (بخش ۵) بر اساس آن طراحی شده |
| `Infrastructure_&_Operations_Domain_Architecture` | مرجع Resource Quota سطح Kubernetes که بخش ۶ به آن ارجاع می‌دهد |
| `API_&_Integration_Domain_Architecture` | مرجع Rate Limiting که باید سطح per-Tenant را از این سند دریافت کند |
| `Data_Model_And_Knowledge_Schema_Overview` | مرجع فیلد `tenant_id` که در فراداده استاندارد (بخش ۴ آن سند) قبلاً تعریف شده و این سند الزام کاربرد سراسری آن را تثبیت می‌کند |
| `Business_Rule_Catalog` | مرجع ثبت رسمی قوانین کسب‌وکار پیشنهادشده در بخش ۹ |

---

## ۱۱. نتیجه‌گیری

این سند تمام تصمیمات پراکنده Multi-Tenancy را که پیش‌تر در حداقل ده سند به‌صورت جزئی و بدون هماهنگی ذکر شده بودند، در یک مرجع واحد تجمیع می‌کند. با وجود این سند، هر تیم توسعه‌دهنده یک دامنه جدید یا مؤلفه جدید می‌تواند مستقیماً به بخش ۴ (مدل Isolation) و بخش ۶ (مدل Quota) مراجعه کند تا الزامات چند‑مستأجری را از ابتدا و به‌طور صحیح در طراحی خود لحاظ کند، بدون نیاز به تصمیم‌گیری مستقل و بالقوه ناسازگار.

**مسئولیت به‌روزرسانی:** تیم معماری به همراه تیم زیرساخت و تیم امنیت مسئول بازبینی دوره‌ای این سند، به‌ویژه هنگام افزودن هر لایه ذخیره‌سازی یا Component جدید به پلتفرم هستند.

</div>