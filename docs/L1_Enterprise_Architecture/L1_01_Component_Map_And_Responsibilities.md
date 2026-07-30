
<div dir="rtl">

# سند معماری کامپوننت‌ها و مسئولیت‌ها (Component Map & Responsibilities)

**نام پلتفرم:** Enterprise AI Context & Automation Platform

**نسخه:** 1.2

**وضعیت:** پیش‌نویس بازبینی‌شده

**سند مرجع نام‌گذاری:** `Naming_And_Terminology_Glossary`

---

# ۱. مقدمه و هدف سند

این سند ساختار کامپوننت‌های اصلی پلتفرم را مشخص کرده و مرز مسئولیت (Boundary of Responsibility) هر Component را تعیین می‌کند. این تفکیک باعث می‌شود:

- مسئولیت هر Component به‌وضوح مشخص باشد.
- وابستگی (Coupling) بین اجزا کاهش یابد.
- توسعه و تغییر هر بخش به‌صورت مستقل‌تر انجام شود.
- مقیاس‌پذیری و نگهداری سیستم در سطح سازمانی (Enterprise) ساده‌تر گردد.

این سند در سطح **معماری سطح‑بالای کامپوننت‌ها** (High‑Level Component Architecture) تهیه شده است؛ جزئیات طراحی داخلی، پیاده‌سازی و ساختار کد در اسناد تخصصی‌تر هر Component بررسی می‌شوند.

---

# ۲. اصول تعریف مرز کامپوننت (Component Boundary Principles)

## Separation of Concerns (تفکیک دغدغه‌ها)

هر Component باید مسئولیت مشخص و قابل‌تعریفی داشته باشد و از ورود به حیطه‌ی مسئولیت سایر Componentها جلوگیری شود.

## Single Responsibility (مسئولیت یگانه)

هر Component باید مالک یک قابلیت اصلی باشد و تغییرات مرتبط با آن قابلیت را مدیریت کند.

## Loose Coupling (وابستگی کم)

Componentها باید از طریق Interface، Event یا قراردادهای مشخص (Contracts) با یکدیگر تعامل داشته باشند تا تغییر در یک Component تأثیر محدودی بر دیگران داشته باشد.

## Extensibility (قابلیت توسعه)

ساختار Componentها باید امکان افزودن قابلیت‌های جدید مانند Agent، Connector، Model و Tool را بدون ایجاد تغییرات گسترده در هسته سیستم فراهم کند.

## Clear Ownership (مالکیت مشخص)

هر قابلیت، داده و فرآیند باید دارای مالک مشخص باشد تا مسئولیت‌ها و تصمیم‌گیری‌ها در طول چرخه عمر سیستم شفاف باقی بماند.

---

# ۳. نقشه کلان کامپوننت‌ها (Component Map Diagram)

```text
+--------------------------------------------------------------------------------+
|                          Interface & Integration Layer                         |
|                                                                                |
|  API Gateway | Event Ingestion | SDK | External System Interface               |
+-----------------------------------------+--------------------------------------+
                                          |
                                          ▼
+--------------------------------------------------------------------------------+
|                         Agent Orchestration Engine                             |
|                                                                                |
|  Intent Analysis | Planning | Agent Runtime | Decision Making | Routing        |
+---------------+-------------------------+-------------------------+------------+
                |                         |                         |
                ▼                         ▼                         ▼
+--------------------------+  +------------------------+  +--------------------------+
|  Knowledge & Context     |  |   Model Management     |  |     Action & Tool        |
|  Engine                  |  |   Engine               |  |     Engine               |
|                          |  |                        |  |                          |
| Data Ingestion           |  | Model Registry         |  | Tool Registry            |
| Document Processing      |  | Model Selection        |  | Tool Execution           |
| Indexing                 |  | Provider Management    |  | Workflow Execution       |
| Semantic Search          |  | Model Configuration    |  | Sandbox                  |
| Context Assembly         |  |                        |  | External Actions         |
+-------------+------------+  +------------+-----------+  +-------------+------------+
              |                            |                            |
              └────────────────────────────┼────────────────────────────┘
                                           ▼
+--------------------------------------------------------------------------------+
|                           Memory Management Engine                             |
|                                                                                |
| Session Memory | Long-Term Memory | Episodic Memory | Experience Storage       |
+--------------------------------------------------------------------------------+


==================================================================================
                    Cross-Cutting Governance & Platform Services
==================================================================================

Security | Policy Management | Guardrails | Audit | Monitoring | Cost Control
```

---

# ۴. شرح وظایف و مرز مسئولیت کامپوننت‌ها (Component Responsibilities & Boundaries)

## ۴.۱ Interface & Integration Layer (لایه تعامل و یکپارچه‌سازی)

**مسئولیت اصلی:** نقطه ورود استاندارد تمامی تعاملات خارجی با پلتفرم شامل کاربران، سیستم‌ها، APIها، Webhookها و Eventها. دریافت، اعتبارسنجی اولیه و هدایت درخواست‌ها به لایه هوشمندی سیستم.

**وظایف کلیدی:**
- مدیریت API Gateway
- دریافت درخواست‌ها و Eventها
- تبدیل ورودی‌ها به ساختار استاندارد داخلی
- کنترل محدودیت نرخ (Rate Limiting) و محدودیت ترافیک (Throttling)
- اعتبارسنجی اولیه درخواست‌ها و اعمال خط‌مشی‌های اولیه (Initial Policy Enforcement)

**مرز مسئولیت:** این Component مسئول تحلیل درخواست، تصمیم‌گیری هوشمند، تولید Context، اجرای Tool یا مدیریت Agent نیست. وظیفه اصلی آن انتقال امن و استاندارد درخواست به Orchestration Engine است.

---

## ۴.۲ Agent Orchestration Engine (موتور هماهنگی و ارکستراسیون Agent)

**مسئولیت اصلی:** هسته هماهنگی و مدیریت چرخه اجرای Agentها؛ تبدیل یک درخواست به مجموعه‌ای از تصمیم‌ها و مراحل اجرایی.

**وظایف کلیدی:**
- تحلیل هدف (Intent Analysis)
- برنامه‌ریزی وظیفه (Task Planning) و تجزیه وظیفه (Task Decomposition)
- مدیریت زمان اجرای Agent (Agent Runtime) و هماهنگی بین Agentها (برای جزئیات بیشتر به سند `Multi_Agent_Collaboration_Model` مراجعه کنید)
- انتخاب Context، Model و Tool مناسب
- مدیریت جریان اجرا (Execution Flow) و اعمال خط‌مشی‌های حفاظتی (Guardrail)

**مرز مسئولیت:** مستقیماً به منابع داده متصل نمی‌شود، مستقیماً عملیات خارجی اجرا نمی‌کند و مسئول مدیریت Storage نیست. وظیفه آن تصمیم‌گیری و هماهنگی بین Componentهای تخصصی است. همچنین این Component (Agent Orchestration Engine) مجاز به برقراری ارتباط مستقیم با Graph Query Service (موجود در دامنه مدیریت دانش) نیست و تمام درخواست‌های مربوط به داده‌های گراف دانش باید از طریق Knowledge & Context Engine انجام شود.

---

## ۴.۳ Knowledge & Context Engine (موتور دانش و Context)

**مسئولیت اصلی:** مدیریت چرخه عمر دانش و ایجاد Context مناسب برای مدل‌های هوش مصنوعی.

**وظایف کلیدی:**
- **دریافت داده (Data Ingestion):** اتصال به منابع مختلف، دریافت اسناد، داده‌های سازمانی، مخزن‌های کد و اطلاعات عملیاتی
- **پردازش دانش (Knowledge Processing):** پردازش محتوا، تقسیم‌بندی متون (Chunking)، استخراج فراداده (Metadata Extraction)، تولید نمایش عددی (Embedding Generation)، نمایه‌سازی (Indexing)
- **بازیابی دانش (Knowledge Retrieval):** جستجوی معنایی (Semantic Search)، جستجوی برداری (Vector Search)، جستجوی ترکیبی (Hybrid Retrieval)، پرس‌وجوی گراف دانش (Knowledge Graph Query) و مونتاژ Context (Context Assembly) — برای جزئیات گراف دانش به سند `Knowledge_Graph_Design` مراجعه کنید.

**مرز مسئولیت:** تصمیم اجرایی نمی‌گیرد، Tool اجرا نمی‌کند و گردش‌کار سازمانی را مدیریت نمی‌کند. وظیفه آن فقط فراهم کردن دانش و Context موردنیاز سیستم است.

---

## ۴.۴ Model Management Engine (موتور مدیریت مدل)

**مسئولیت اصلی:** مدیریت اتصال به مدل‌های هوش مصنوعی و انتخاب مدل مناسب برای هر درخواست، به‌گونه‌ای که پلتفرم مستقل از ارائه‌دهنده (Provider‑Agnostic) باقی بماند.

**وظایف کلیدی:**
- **ثبت‌نام مدل (Model Registry):** ثبت و نگهداری مدل‌های در دسترس (مدل‌های ابری، مدل‌های محلی، مدل‌های متن‌باز، مدل‌های Embedding و Reranking)
- **انتخاب و مسیریابی مدل (Model Selection & Routing):** انتخاب مدل بر اساس نوع وظیفه، کیفیت موردنیاز، سرعت، هزینه و محدودیت پنجره Context
- **مدیریت ارائه‌دهنده (Provider Management):** مدیریت یکپارچه چند ارائه‌دهنده از طریق آداپتورهای استاندارد (Standard Adapters)
- **پیکربندی مدل (Model Configuration):** تنظیم پارامترهای اجرایی، محدودیت مصرف و استراتژی جایگزین (Fallback Strategy) در صورت خرابی یا کاهش دسترس‌پذیری

**مرز مسئولیت:** این Component تصمیم نمی‌گیرد چه اقدامی باید انجام شود؛ فقط مشخص می‌کند درخواست پردازشی توسط کدام مدل اجرا شود. منبع دانش سازمانی نیست و جایگزین Knowledge & Context Engine نمی‌شود. همچنین مستقیماً Tool اجرا نمی‌کند.

---

## ۴.۵ Action & Tool Engine (موتور اجرای عملیات و ابزار)

**مسئولیت اصلی:** مدیریت و اجرای امن عملیات از طریق Toolها و گردش‌کارها در سیستم‌های خارجی.

**وظایف کلیدی:**
- **مدیریت Tool (Tool Management):** ثبت‌نام Tool (Tool Registry)، مدیریت فراداده Tool (Tool Metadata)، کشف قابلیت (Capability Discovery)
- **مدیریت اجرا (Execution Management):** اجرای Toolها، مدیریت فراخوانی API، اجرای گردش‌کار (Workflow)، اجرا در محیط ایزوله (Sandbox)
- **مدیریت قابلیت اطمینان (Reliability Management):** تکرار مجدد (Retry)، مدیریت خطا (Error Handling)، مدیریت تراکنش (Transaction Management)، بازگشت به حالت قبل (Rollback)

**مرز مسئولیت:** بدون درخواست معتبر و مجاز از Orchestration Engine عملیاتی انجام نمی‌دهد. تصمیم نمی‌گیرد چه عملی انجام شود و Intent کاربر را تحلیل نمی‌کند. توالی، شرط و منطق چندمرحله‌ای گردش‌کار را نیز مدیریت نمی‌کند؛ این مسئولیت به‌طور کامل بر عهده دامنه مدیریت جریان کار (Workflow) است که به‌عنوان یک مصرف‌کننده تک‌مرحله‌ای از این Engine استفاده می‌کند (شرح کامل در `Workflow_Domain_Architecture`، بخش ۴.۵ همان سند).

---

## ۴.۶ Memory Management Engine (موتور مدیریت حافظه)

**مسئولیت اصلی:** مدیریت وضعیت، تجربیات و اطلاعات تاریخی موردنیاز سیستم برای حفظ Context و بهبود عملکرد آینده.

**وظایف کلیدی:**
- **حافظه جلسه (Session Memory):** تاریخچه تعامل جاری، Context جلسه، وضعیت وظیفه جاری
- **حافظه بلندمدت (Long‑Term Memory):** تجربیات گذشته، تصمیم‌های قبلی، دانش تولیدشده، الگوهای استخراج‌شده
- **مدیریت تجربه و بازخورد (Experience & Feedback Management):** ثبت بازخورد کاربران، ثبت نتایج عملیات، ذخیره تجربیات برای استفاده آینده

**مرز مسئولیت:** تصمیم‌گیری نمی‌کند، جایگزین Knowledge & Context Engine نیست و منبع حقیقت کسب‌وکاری (Source of Truth) نیست. وظیفه آن فراهم کردن اطلاعات تاریخی و تجربی برای سایر Componentها است.

---

## ۴.۷ Governance & Platform Services (سرویس‌های حاکمیت و پلتفرم)

> **یادداشت هم‌راستاسازی معماری:** این عنوان، برخلاف شش Component دیگر این سند، یک واحد معماری تکی نیست بلکه چتری برای چند سرویس مستقل با مالکیت و چرخه استقرار جداگانه است (Identity، Security، Governance/Policy، Observability، Responsible AI). جزئیات تفکیک در سند `Domain_to_Component_and_Deployment_Mapping` آمده است. جدول بخش ۵ همین سند («تعاملات بین کامپوننت‌ها») این عنوان را همچنان به‌صورت یک ردیف واحد نشان می‌دهد چون از منظر Contract بیرونی (سایر Componentها با آن به‌صورت یک مقصد منطقی تعامل دارند)، اما از منظر استقرار داخلی چند Service مجزاست.

**مسئولیت اصلی:** ایجاد کنترل، امنیت، انطباق و قابلیت مشاهده برای تمامی جریان‌های سیستم؛ به‌صورت سراسری (Cross‑Cutting) تمام Componentها را پوشش می‌دهد.

**وظایف کلیدی:**

- **ایمنی هوش مصنوعی و حفاظت‌ها (AI Safety & Guardrails):** اعتبارسنجی ورودی (Prompt Validation)، اعتبارسنجی خروجی (Output Validation)، جلوگیری از تزریق دستور (Prompt Injection) و نشت داده (Data Leakage)، کنترل استفاده از Tool
- **حسابرسی و ردیابی (Audit & Traceability):** ثبت رویدادهای مهم، ردپای اجرا (Execution Trace)، تصمیمات مهم Agent، فعالیت کاربران و سیستم‌ها
- **کنترل هزینه و منابع (Cost & Resource Control):** پایش مصرف توکن، پایش مصرف مدل، ردیابی هزینه (Cost Tracking)، کنترل منابع

> تفصیل بیشتر این سه زیرمجموعه (۹ سرویس عملیاتی) در سند `Architecture_Overview_Enterprise_AI_Platform` (بخش ۶) آمده است.

**مرز مسئولیت:** منطق کسب‌وکاری اجرا نمی‌کند، جایگزین Orchestration Engine نیست و عملیات خارجی انجام نمی‌دهد. وظیفه آن کنترل، نظارت و اعمال خط‌مشی (Policy) روی جریان اجرای سیستم است.

---

# ۵. جدول خلاصه تعاملات بین کامپوننت‌ها (Component Interaction Summary)

جزئیات API، قرارداد رویداد (Event Contract) و پروتکل ارتباطی در اسناد طراحی سطح پایین‌تر مشخص خواهند شد.

| کامپوننت فرستنده | کامپوننت گیرنده | نوع تعامل / داده تبادل‌شده |
| :--- | :--- | :--- |
| **Interface & Integration Layer** | **Agent Orchestration Engine** | ارسال درخواست استانداردشده شامل Context کاربر، فراداده و اطلاعات جلسه |
| **Agent Orchestration Engine** | **Knowledge & Context Engine** | درخواست بازیابی اطلاعات و ساخت Context بر اساس Intent، Task و نیاز Agent |
| **Knowledge & Context Engine** | **Agent Orchestration Engine** | ارسال Context غنی‌شده شامل اطلاعات بازیابی‌شده، فراداده و منابع مرتبط |
| **Agent Orchestration Engine** | **Model Management Engine** | درخواست انتخاب و فراخوانی مدل مناسب بر اساس Task، هزینه و عملکرد |
| **Model Management Engine** | **Agent Orchestration Engine** | پاسخ مدل، نتیجه استدلال و اطلاعات مصرف مدل |
| **Agent Orchestration Engine** | **Action & Tool Engine** | ارسال دستور اجرای Tool شامل شناسه Tool، پارامترها و Context اجرا |
| **Action & Tool Engine** | **Agent Orchestration Engine** | ارسال نتیجه اجرای عملیات شامل وضعیت، خروجی، خطا و فراداده اجرا |
| **Agent Orchestration Engine** | **Memory Management Engine** | ذخیره Context مهم، وضعیت Task، تصمیمات و تجربه تولیدشده |
| **Action & Tool Engine** | **Memory Management Engine** | ثبت نتایج عملیات، خطاها و تجربیات اجرایی |
| **Knowledge & Context Engine** | **Memory Management Engine** | ثبت دانش جدید یا اطلاعات قابل استفاده در تعاملات آینده |
| **تمامی Componentها** | **Governance & Platform Services** | ارسال رویداد، ردپا، متریک‌ها و درخواست بررسی خط‌مشی‌ها و محافظت‌ها |

---

# ۶. ارتباط با سایر اسناد (References)

| نام سند | نوع ارتباط |
| :--- | :--- |
| `Naming_And_Terminology_Glossary` | مرجع واژه‌نامه و استاندارد نام‌گذاری مورد استفاده در این سند |
| `Domain_to_Component_and_Deployment_Mapping` | مرجع نگاشت دقیق هر یک از ۱۸ دامنه به واحد استقرار مستقل، به‌ویژه تفکیک زیرسرویس‌های Governance & Platform Services |
| `Domain_to_Component_and_Deployment_Mapping` | مرجع نگاشت دقیق هر یک از ۱۸ دامنه به واحد استقرار مستقل، شامل دلیل فنی تفکیک یا ادغام سرویس‌ها |
| `Architecture_Overview_Enterprise_AI_Platform` | نمای کلی معماری که این سند جزئیات کامپوننت‌های آن را تشریح می‌کند |
| `Vision_and_Strategy` | چشم‌انداز و اصول راهبردی که مرزهای مسئولیت بر اساس آن‌ها تعریف شده است |
| `High_Level_PRD_Enterprise_AI_Platform` | نیازمندی‌های عملکردی که وظایف هر Component بر اساس آن‌ها مشخص شده است |
| `Multi_Agent_Collaboration_Model` | مدل همکاری چند‑Agent که در Agent Orchestration Engine به آن ارجاع داده شده است |
| `Knowledge_Graph_Design` | طراحی گراف دانش که در Knowledge & Context Engine به آن اشاره شده است |
| `Component_Design_Agent_Orchestration_Engine` | جزئیات طراحی و پیاده‌سازی Agent Orchestration Engine |
| `Component_Design_Knowledge_Context_Engine` | جزئیات طراحی و پیاده‌سازی Knowledge & Context Engine |
| `Component_Design_Model_Management_Engine` | جزئیات طراحی و پیاده‌سازی Model Management Engine |
| `Component_Design_Action_Tool_Engine` | جزئیات طراحی و پیاده‌سازی Action & Tool Engine |
| `Component_Design_Memory_Management_Engine` | جزئیات طراحی و پیاده‌سازی Memory Management Engine |
| `Governance_And_Platform_Services_Design` | جزئیات طراحی سرویس‌های حاکمیت و پلتفرم |

---

# ۷. نتیجه‌گیری

این سند نقشه کامپوننت‌های اصلی پلتفرم و مرزهای مسئولیت هرکدام را به‌صورت شفاف تعریف می‌کند. با رعایت اصول تفکیک دغدغه‌ها، وابستگی کم و مالکیت مشخص، بستری فراهم می‌شود که توسعه، نگهداری و مقیاس‌پذیری سیستم در سطح سازمانی به‌صورت مؤثر انجام شود. تمام اسناد طراحی سطح پایین‌تر باید با این تفکیک‌بندی و مرزهای تعیین‌شده همسو باشند.

</div>