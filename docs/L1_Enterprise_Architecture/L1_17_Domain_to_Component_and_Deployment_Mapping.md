<div dir="rtl">

# نگاشت دامنه‌ها به کامپوننت‌ها و واحدهای استقرار (Domain to Component and Deployment Mapping)

**نام پلتفرم:** Enterprise AI Context & Automation Platform

**نسخه:** 1.0

**وضعیت:** پیش‌نویس

**سند مرجع نام‌گذاری:** `Naming_And_Terminology_Glossary`

---

## ۱. هدف سند

سند `Component_Map_And_Responsibilities` هفت Component معماری سطح بالا را تعریف می‌کند و سند `Domain_Landscape` هجده Domain کسب‌وکاری را تعریف می‌کند. نگاشت بین این دو سطح تاکنون فقط به‌صورت یک جدول خلاصه در `Context_Map` (بخش ۸) بیان شده که سطح انتزاع آن برای تصمیم‌گیری استقرار (Deployment) کافی نیست.

این سند حلقه گمشده بین **معماری Domain-Driven** (لایه ۲) و **معماری استقرار** (`Deployment_Environment_Topology`) است. هدف آن پاسخ صریح به این پرسش برای هر یک از ۱۸ دامنه است: *«این دامنه در عمل به چند سرویس قابل‌استقرار (Deployable Service) تبدیل می‌شود و چرا؟»*

این سند برای تیم DevOps، معماران و رهبران فنی تیم‌های توسعه، پیش از شروع پیاده‌سازی هر دامنه، مرجع تصمیم‌گیری است.

---

## ۲. اصول نگاشت

| اصل | توضیح |
| :--- | :--- |
| **واحد استقرار مستقل از واحد Domain است** | یک Domain непременно به یک Service معادل نیست؛ ممکن است چند Domain کوچک درون یک Service ادغام شوند یا یک Domain به چند Service تقسیم شود. |
| **معیار تفکیک: نرخ تغییر، الگوی بار، و حساسیت SLA** | دامنه‌هایی با نرخ تغییر بالا، الگوی مقیاس‌دهی متفاوت، یا SLA حیاتی، باید Service مستقل داشته باشند. |
| **معیار ادغام: هم‌بار بودن و وابستگی زمان‌کامپایل مشترک** | دامنه‌هایی که همیشه با هم Deploy و Scale می‌شوند و به ندرت مستقل تغییر می‌کنند، می‌توانند در یک Service ادغام شوند تا از سربار عملیاتی غیرضروری Microservices جلوگیری شود. |
| **لایه‌های Cross-Cutting، Service نیستند** | دامنه‌هایی مانند مشاهده‌پذیری یا امنیت که به‌صورت افقی روی همه سرویس‌ها اعمال می‌شوند، معمولاً به‌صورت Sidecar، Library مشترک یا زیرساخت مجزا (نه یک Business Service) پیاده‌سازی می‌شوند. |

---

## ۳. جدول نگاشت کامل (۱۸ دامنه)

### ۳.۱. لایه دسترسی و تعامل

| دامنه | Component (از `Component_Map`) | واحد استقرار پیشنهادی | نوع | دلیل |
| :--- | :--- | :--- | :--- | :--- |
| **API و یکپارچه‌سازی** | Interface & Integration Layer | `api-gateway-service` | Microservice مستقل | نقطه ورود واحد؛ نیاز به مقیاس‌دهی افقی مستقل از بار ورودی؛ SLA بحرانی (۹۹.۹۵٪) |
| **رویداد و Webhook** | Interface & Integration Layer | `event-webhook-service` | Microservice مستقل | الگوی بار متفاوت (Async/Bursty) نسبت به API Gateway؛ نیاز به Retry Queue مجزا |
| **اتصال‌دهنده (Connector)** | Interface & Integration Layer | `connector-runtime-service` (+ Worker Pool مجزا برای هر نوع Connector پرمصرف) | Microservice + Worker Pool | حجم پردازش متغیر (Batch سنگین)؛ نیاز به Scale-to-zero در بازه‌های بی‌کاری |

### ۳.۲. لایه هسته هوشمند

| دامنه | Component | واحد استقرار پیشنهادی | نوع | دلیل |
| :--- | :--- | :--- | :--- | :--- |
| **عامل‌ها و هماهنگی** | Agent Orchestration Engine | `agent-orchestration-service` | Microservice مستقل | قلب تصمیم‌گیری سیستم؛ نرخ تغییر بسیار بالا (منطق Planning/Routing به‌مرور توسعه می‌یابد) |
| **دانش و زمینه** | Knowledge & Context Engine | `knowledge-context-service` | Microservice مستقل | الگوی بار متفاوت (CPU/Memory سنگین برای Embedding)؛ نیاز به Worker مجزا برای Indexing |
| **حافظه جلسه (Session)** | Memory Management Engine | `session-memory-service` | Microservice مستقل + Cache (Redis) | نیاز به تأخیر بسیار پایین (< 10ms) و TTL کوتاه؛ مقیاس‌دهی بر اساس تعداد کاربران همزمان. |
| **حافظه بلندمدت (Long-Term)** | Memory Management Engine | `long-term-memory-service` | Microservice مستقل + Vector/Relational DB | نیاز به جستجوی معنایی، ذخیره‌سازی حجیم و TTL بلندمدت؛ الگوی بار (Batch/Query) کاملاً متفاوت از Session. |
| **ابزارها و اقدامات** | Action & Tool Engine | `tool-execution-service` (+ `sandbox-runtime` مجزا) | Microservice + Isolated Runtime | نیاز امنیتی به ایزوله‌سازی اجرای Sandbox، مجزا از منطق Registry/Catalog |
| **مدیریت مدل‌ها** | Model Management Engine | `model-management-service` | Microservice مستقل | نیاز به مدیریت مستقل Provider Adapterها و Fallback؛ چرخه انتشار متفاوت از Orchestration |
| **مدیریت جریان کار** | Action & Tool Engine (Sub-layer) | `workflow-engine-service` | Microservice مستقل (اما هم‌گروه استقراری با Tool) | طبق اصلاح مرزبندی در `Tool_Domain_Architecture` بخش ۴.۵؛ منطق State Machine پیچیده و نرخ تغییر متفاوت از Execution Engine توجیه‌کننده Service مجزا است، هرچند از نظر Component به همان بلوک تعلق دارد |

### ۳.۳. لایه داده و هوش مصنوعی

| دامنه | Component | واحد استقرار پیشنهادی | نوع | دلیل |
| :--- | :--- | :--- | :--- | :--- |
| **مهندسی داده** | Data Layer (زیرساخت) | `data-pipeline-orchestrator` (+ Spark/Flink Cluster جدا) | Service + Managed Cluster | ماهیت Batch/Streaming کاملاً متفاوت از سرویس‌های Request/Response؛ نیازمند زیرساخت پردازش توزیع‌شده اختصاصی |
| **یادگیری ماشین** | Model Management Engine (منبع‌دهنده) | `ml-training-platform` (جدا از سرویس Inference) | Service مستقل + GPU Cluster | چرخه عمر کاملاً متفاوت (Batch/Offline) از Model Management که Real-time است |
| **مدیریت دانش** | Knowledge & Context Engine (Shared Kernel) | `knowledge-graph-service` | Microservice مستقل | مالک انحصاری گراف دانش (طبق اصلاح Shared Kernel)؛ پایگاه داده گرافی اختصاصی نیازمند Service جدا |
| **مدیریت ویژگی‌ها** | Feature Management Domain (جدید) | `feature-management-service` | Microservice مستقل + Online Cache (Redis) | الگوی بار دوگانه (Batch سنگین برای Offline، و Latency حساس برای Online) توجیه‌کنندهٔ Service جدا با دو زیرساخت متفاوت است. |
| **مدیریت فراداده و دودمان** | Metadata & Lineage Domain (جدید) | `metadata-lineage-service` | Microservice مستقل + پایگاه داده گرافی (برای Lineage) | نیاز به عنوان مرجع واحد حقیقت برای تمام Schemaها و Lineage؛ مصرف‌شونده توسط مهندسی داده، رویداد و حاکمیت. |

### ۳.۴. لایه زیرساخت و حاکمیت

| دامنه | Component | واحد استقرار پیشنهادی | نوع | دلیل |
| :--- | :--- | :--- | :--- | :--- |
| **هویت و دسترسی** | Governance & Platform Services | `identity-service` | Microservice مستقل، بحرانی | وابستگی بحرانی همه دامنه‌ها؛ SLA بالاتر از میانگین (۹۹.۹۵٪)؛ نیاز به مقیاس‌دهی و Hardening مستقل |
| **امنیت و حریم خصوصی** | Governance & Platform Services | `security-service` (Encryption/Secrets) + Library مشترک (DLP/Masking تزریق‌شده در سایر Serviceها) | Service + Shared Library | بخشی از این دامنه (KMS/Secrets) باید Service مستقل باشد؛ بخشی دیگر (Masking Logic) باید به‌صورت Library در همه سرویس‌ها تزریق شود، نه یک Service مرکزی که هر درخواست از آن عبور کند (گلوگاه عملکردی) |
| **حاکمیت و انطباق** | Governance & Platform Services | `policy-engine-service` (PDP) + PEP به‌صورت Library/Sidecar در هر Service | Service + Sidecar Pattern | طبق معماری PDP/PEP خودِ سند `Governance_&_Compliance_Domain_Architecture`؛ PDP مرکزی است اما PEP باید توزیع‌شده باشد |
| **حسابرسی (Audit)** | Governance & Platform Services | `audit-service` | Microservice مستقل | نیاز به ذخیره‌سازی غیرقابل‌تغییر (WORM Storage)، امضای دیجیتال و دوره نگهداری طولانی (۱ سال) برای انطباق (Compliance)؛ الگوی بار و الزامات امنیتی متفاوت از Logging و Policy Engine. |
| **زیرساخت و عملیات** | زیرساخت (Kubernetes) | زیرساخت پایه (Cluster, CI/CD) — **نه یک Business Service** | Platform Infrastructure | این دامنه بستر اجرای همه Serviceهای دیگر است، خودش یک Service کسب‌وکاری نیست |
| **مشاهده‌پذیری (Logging/Metrics)** | زیرساخت (Observability Stack) | Observability Stack (Elasticsearch/Loki برای Logs، Prometheus برای Metrics) — **نه یک Business Service** | Platform Infrastructure | ماهیت Cross-Cutting؛ Logging به‌عنوان یک زیرساخت عملیاتی در نظر گرفته شده و جدا از Audit Service مدیریت می‌شود. |
| **هوش مصنوعی مسئولانه** | Governance & Platform Services | `responsible-ai-service` (سبک) | Microservice سبک + Scheduled Job (با قابلیت ادغام در آینده) | حجم تعامل Real-time پایین است؛ در صورت نیاز به کاهش هزینه‌های عملیاتی، می‌تواند به‌عنوان کتابخانه در Governance ادغام شود. در حال حاضر به‌عنوان سرویس مستقل برای وضوح مسئولیت‌ها نگهداری می‌شود. |
| **درخواست‌های موضوع داده** | Governance & Platform Services | `data-subject-request-service` | Microservice مستقل | نیاز به ذخیره‌سازی اختصاصی برای ردیابی درخواست‌ها و گزارش‌ها؛ چرخه عمر مستقل از سایر سرویس‌ها |

---

## ۴. طبقه‌بندی الگوی استقرار

برای شفافیت بیشتر، دامنه‌ها بر اساس الگوی استقرار به سه گروه تقسیم می‌شوند:

| گروه | تعریف | اعضا |
| :--- | :--- | :--- |
| **Business Microservice** | سرویس مستقل با API/Event Contract مشخص، چرخه Deploy مستقل | API و یکپارچه‌سازی، رویداد و Webhook، اتصال‌دهنده، عامل‌ها، دانش و زمینه، حافظه و تجربه، ابزارها، مدیریت جریان کار، مدیریت مدل‌ها، مدیریت دانش، هویت و دسترسی، حاکمیت و انطباق (PDP)، هوش مصنوعی مسئولانه |
| **Data/ML Platform** | زیرساخت پردازشی تخصصی، غالباً Batch یا مبتنی بر Cluster | مهندسی داده، یادگیری ماشین |
| **Cross-Cutting Infrastructure / Sidecar** | نه یک Service کسب‌وکاری؛ به‌صورت Library، Sidecar یا زیرساخت پایه در همه جا حاضر است | زیرساخت و عملیات، مشاهده‌پذیری، بخشی از امنیت و حریم خصوصی (Masking/DLP)، بخشی از حاکمیت (PEP) |

---

## ۵. پیامدهای این نگاشت برای شمارش سرویس‌ها

بر اساس جدول بالا، تخمین اولیه تعداد **Business Microservice مستقل** برای MVP و توسعه Enterprise به شرح زیر است (این عدد صرفاً راهنمای برنامه‌ریزی ظرفیت تیم است، نه الزام قطعی):

- ۱۷ Business Microservice مستقل
- ۲ Data/ML Platform (با زیرساخت Cluster اختصاصی)
- ۳ Cross-Cutting Infrastructure Layer (زیرساخت، مشاهده‌پذیری، بخشی از امنیت/حاکمیت به‌صورت Sidecar)

این عدد باید مبنای برنامه‌ریزی تیم‌های DevOps برای طراحی CI/CD Pipeline (طبق `DevOps_and_CICD_Pipeline`) و ظرفیت Kubernetes Cluster (طبق `Deployment_Environment_Topology`) قرار گیرد.

---

## ۶. ارتباط با سایر اسناد (References)

| نام سند | نوع ارتباط |
| :--- | :--- |
| `Domain_Landscape` | مرجع ۱۸ دامنه‌ای که این سند نگاشت استقراری آن‌ها را مشخص می‌کند |
| `Component_Map_And_Responsibilities` | مرجع ۷ Component معماری که ستون دوم جدول بخش ۳ به آن‌ها ارجاع می‌دهد |
| `Context_Map` | مرجع نگاشت اولیه و درشت‌دانه دامنه به Component (بخش ۸) که این سند آن را تفصیل می‌دهد |
| `Deployment_Environment_Topology` | مرجع توپولوژی Kubernetes که واحدهای استقرار این سند بر روی آن پیاده‌سازی می‌شوند |
| `DevOps_and_CICD_Pipeline` | مرجع خط لوله CI/CD که باید به ازای هر واحد استقرار این سند، یک Pipeline مستقل داشته باشد |
| `Tool_Domain_Architecture` | مرجع مرزبندی Tool/Workflow که تصمیم استقرار مجزای `workflow-engine-service` بر اساس آن اتخاذ شده |
| `Governance_&_Compliance_Domain_Architecture` | مرجع الگوی PDP/PEP که تصمیم استقرار `policy-engine-service` + Sidecar بر اساس آن اتخاذ شده |

---

## ۷. نتیجه‌گیری

این سند حلقه گمشده بین طراحی Domain-Driven (چه چیزی باید ساخته شود) و طراحی استقرار (چگونه و در چند سرویس ساخته شود) را پر می‌کند. بدون این نگاشت صریح، ریسک بالایی وجود دارد که دامنه‌های بزرگ (به‌ویژه دامنه‌های لایه زیرساخت و حاکمیت) به‌صورت یک Monolith درشت‌دانه پیاده‌سازی شوند که دقیقاً نقض اصل Microservices انتخاب‌شده در `ADR-001` است.

**مسئولیت به‌روزرسانی:** تیم معماری به همراه تیم DevOps مسئول بازبینی این نگاشت پیش از شروع هر فاز جدید از `Roadmap_And_Product_Phasing` هستند، چون تفکیک/ادغام Serviceها ممکن است با رشد بار واقعی تغییر کند.

</div>