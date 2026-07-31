<div dir="rtl">
# سند نقشه مستندات (Documentation Map)

**نام پلتفرم:** Enterprise AI Context & Automation Platform

**نسخه:** 1.0

**وضعیت:** پیش‌نویس

---

## ۱. هدف سند

این سند، **نقشه راهنمای مستندات** پلتفرم را ارائه می‌دهد و به‌عنوان نقطه ورود به مجموعه مستندات معماری و طراحی عمل می‌کند. هدف آن ایجاد یک نمای کلی از ساختار مستندات، دسته‌بندی موضوعات، و ارائه راهنمایی برای تیم‌های مختلف (محصول، معماری، توسعه، عملیات) در مورد اینکه برای هر نیاز باید به کدام سند مراجعه کنند، است.

این سند، چارچوب سازمان‌دهی دانش فنی و معماری را تعریف می‌کند و تضمین می‌کند که تمام اطلاعات به‌صورت منطقی، منسجم و قابل دسترس ساختاردهی شده‌اند.

---

## ۲. اصول سازمان‌دهی مستندات

ساختار مستندات بر اساس اصول زیر طراحی شده است:

| اصل | توضیح |
| :--- | :--- |
| **جداسازی سطوح انتزاع** | مستندات از سطح استراتژیک (چشم‌انداز، اهداف) تا سطح عملیاتی (استقرار، DevOps) سازمان‌دهی شده‌اند تا هر خواننده بتواند به‌سرعت به سطح مناسب دسترسی پیدا کند. |
| **تفکیک دامنه‌ها** | معماری دامنه (Domain Architecture) به‌صورت مستقل و با مرزهای مشخص مستند شده است تا امکان توسعه و نگهداری مستقل هر دامنه فراهم شود. |
| **تمرکز بر سرفصل‌های سراسری** | موضوعات سراسری مانند امنیت، حاکمیت داده، و هوش مصنوعی مسئولانه در بخش‌های جداگانه و متمرکز جمع‌آوری شده‌اند تا از پراکندگی و تکرار جلوگیری شود. |
| **تفکیک تصمیمات معماری** | تصمیمات کلان معماری (ADRs) به‌صورت متمرکز در بخش حاکمیت (Governance) ثبت شده‌اند تا پیشینه و دلیل هر تصمیم به‌خوبی مستند باشد. |
| **قابلیت رهگیری و ارجاع** | تمام اسناد با ارجاعات متقابل به یکدیگر متصل شده‌اند تا خواننده بتواند به‌راحتی بین مفاهیم مرتبط جابه‌جا شود. |

---

## ۳. ساختار مستندات

ساختار مستندات به شش بخش اصلی تقسیم شده است که هر بخش نماینده یک سطح یا حوزه مشخص از معماری و طراحی است.

```text
Enterprise-AI-Platform
│
├── 00_Governance                 # حاکمیت و تصمیمات کلان
│
├── 01_Vision_and_Strategy        # چشم‌انداز، استراتژی و اهداف محصول
│
├── 02_Enterprise_Architecture    # معماری کلان و اصول طراحی
│
├── 03_Domain_Architecture        # معماری تخصصی هر دامنه
│
├── 04_Cross_Cutting_Architecture # معماری سراسری (امنیت، حاکمیت داده، و ...)
│
└── 05_Runtime_and_Operations     # معماری عملیاتی و استقرار
```

---

## ۴. شرح بخش‌های مستندات

### ۴.۱. بخش ۰۰: حاکمیت (Governance)

این بخش شامل مستندات سطح حاکمیتی، شامل نقشه مستندات، اصول معماری، واژه‌نامه، و ثبت تصمیمات کلان معماری (ADRs) است.

| نام سند | توضیح | مخاطب اصلی |
| :--- | :--- | :--- |
| `Documentation_Map.md` | **این سند.** نقشه راهنمای کل مجموعه مستندات. | همه تیم‌ها |
| `Architecture_Principles_And_Decisions.md` | اصول کلان معماری و چارچوب تصمیم‌گیری. | معماران، مدیران فنی |
| `Naming_And_Terminology_Glossary.md` | واژه‌نامه رسمی و استاندارد نام‌گذاری برای تمام مفاهیم و کامپوننت‌ها. | همه تیم‌ها |
| `ADR/` | پوشه ثبت تصمیمات معماری (Architecture Decision Records). | معماران، توسعه‌دهندگان |
| `ADR/ADR_Index.md` | فهرست تمام ADRها به‌همراه خلاصه و وضعیت هرکدام. | معماران، مدیران فنی |
| `ADR/ADR_*.md` | هر ADR شامل زمینه (Context)، گزینه‌ها، تصمیم نهایی و دلایل انتخاب. | معماران، توسعه‌دهندگان |

---

### ۴.۲. بخش ۰۱: چشم‌انداز و استراتژی (Vision and Strategy)

این بخش شامل مستندات سطح استراتژیک است که چرایی و جهت‌گیری کلان پلتفرم را تعریف می‌کنند.

| نام سند | توضیح | مخاطب اصلی |
| :--- | :--- | :--- |
| `Vision_and_Strategy.md` | چشم‌انداز بلندمدت، مأموریت، اهداف کسب‌وکار و اصول راهبردی پلتفرم. | مدیران، محصول، معماران |
| `High_Level_PRD.md` | نیازمندی‌های سطح بالای محصول و شرح چرخه اصلی عملکرد سیستم (AI Loop). | محصول، معماری، توسعه |
| `User_Personas_and_Use_Cases.md` | شناسایی پرسوناهای کاربری، ذی‌نفعان و سناریوهای اصلی استفاده از پلتفرم. | محصول، طراحی تجربه، توسعه |
| `Product_Roadmap.md` | نقشه راه توسعه محصول، فازبندی قابلیت‌ها و اولویت‌بندی بر اساس ارزش کسب‌وکاری. | مدیران، محصول، معماری |

---

### ۴.۳. بخش ۰۲: معماری سازمانی (Enterprise Architecture)

این بخش شامل مستندات سطح معماری کلان است که ساختار کلی سیستم، کامپوننت‌ها، یکپارچه‌سازی‌ها، مدل داده، و اهداف کیفی را تعریف می‌کند.

| نام سند | توضیح | مخاطب اصلی |
| :--- | :--- | :--- |
| `Architecture_Overview.md` | نمای کلی معماری، لایه‌ها، اصول طراحی و چرخه پردازش درخواست. | همه تیم‌ها |
| `Component_Map_And_Responsibilities.md` | نقشه کامپوننت‌های اصلی و مرز مسئولیت هرکدام. | معماران، توسعه‌دهندگان |
| `Integration_Boundaries_And_Tooling_Framework.md` | مرزهای یکپارچه‌سازی با سیستم‌های خارجی و چارچوب ابزارها (Tooling Framework). | معماران، توسعه‌دهندگان یکپارچه‌سازی |
| `Data_Model_And_Knowledge_Schema_Overview.md` | مدل مفهومی داده، فراداده، گراف دانش، و طرحواره دانش. | معماران داده، توسعه‌دهندگان |
| `Non_Functional_Requirements_And_SLA.md` | نیازمندی‌های غیرعملکردی (Performance, Scalability, Availability, و ...) و اهداف SLA. | معماران، توسعه‌دهندگان، عملیات |
| `Multi_Agent_Collaboration_Model.md` | مدل همکاری چند-Agent، نقش‌ها، الگوهای هماهنگی و پروتکل ارتباطی. | معماران، توسعه‌دهندگان AI |
| `API_Contract_Overview.md` | مرور قراردادهای API بین کامپوننت‌ها، ساختار استاندارد درخواست/پاسخ و اصول امنیتی. | معماران، توسعه‌دهندگان |
| `Versioning_And_Compatibility_Strategy.md` | استراتژی نسخه‌بندی و سازگاری برای تمام موجودیت‌های نسخه‌پذیر (API، مدل، دانش، و ...). | معماران، توسعه‌دهندگان |
| `Multi_Tenancy_Architecture.md` | معماری چند-مستأجری، مدل‌های ایزوله‌سازی، چرخه عمر Tenant و مدیریت هزینه. | معماران، توسعه‌دهندگان، عملیات |
| `Transaction_And_Compensation_Strategy.md` | استراتژی مدیریت تراکنش‌های توزیع‌شده و عملیات جبرانی (Orchestrated Saga). | معماران، توسعه‌دهندگان |
| `Domain_to_Component_and_Deployment_Mapping.md` | نگاشت دامنه‌ها به کامپوننت‌ها و واحدهای استقرار (Deployment Units). | معماران، DevOps |
| `Risk_And_Failure_Mode_Analysis.md` | تحلیل ریسک‌ها، سناریوهای خرابی و راهبردهای تنزل کنترل‌شده سرویس. | معماران، عملیات، SRE |

---

### ۴.۴. بخش ۰۳: معماری دامنه (Domain Architecture)

این بخش شامل مستندات تخصصی هر دامنه (Bounded Context) است. هر دامنه دارای یک پوشه مجزا و یک یا چند سند است که معماری، مسئولیت‌ها، رویدادها و قوانین آن دامنه را شرح می‌دهد.

#### ۴.۴.۱. پوشه Access_and_Integration (دسترسی و تعامل)

| نام سند | توضیح |
| :--- | :--- |
| `API_and_Integration_Domain.md` | معماری دامنه API و یکپارچه‌سازی (API Gateway، نسخه‌بندی، SDK). |
| `Connector_Domain.md` | معماری دامنه اتصال‌دهنده‌ها (Connector Registry، Data Ingestion، Credential Management). |
| `Event_and_Webhook_Domain.md` | معماری دامنه رویداد و Webhook (Event Publisher، Webhook Registry، امنیت). |

#### ۴.۴.۲. پوشه Intelligent_Core (هسته هوشمند)

| نام سند | توضیح |
| :--- | :--- |
| `Agent_Domain.md` | معماری دامنه عامل‌ها (Agent Orchestrator، Planner، Reviewer، Capability Registry). |
| `Workflow_Domain.md` | معماری دامنه مدیریت جریان کار (Workflow Engine، State Manager، Orchestrated Saga). |
| `Tool_Domain.md` | معماری دامنه ابزارها (Tool Registry، Execution Engine، Sandbox، Compensation). |
| `Knowledge_and_Context_Domain.md` | معماری دامنه دانش و زمینه (Ingestion، Indexing، Retrieval، RAG، Graph Version Management). |
| `Knowledge_Management_Domain.md` | معماری دامنه مدیریت دانش (Ontology، Knowledge Graph، Entity Extraction). |
| `Memory_and_Experience_Domain.md` | معماری دامنه حافظه و تجربه (Session Memory، Long-Term Memory، Shared Agent Context). |
| `Model_Management_Domain.md` | معماری دامنه مدیریت مدل‌ها (Model Registry، Router، Fallback Strategy). |

#### ۴.۴.۳. پوشه Data_and_ML (داده و یادگیری ماشین)

| نام سند | توضیح |
| :--- | :--- |
| `Data_Engineering_Domain.md` | معماری دامنه مهندسی داده (Data Lake، Warehouse، Pipelines). |
| `Metadata_and_Lineage_Domain.md` | معماری دامنه مدیریت فراداده و دودمان (Schema Registry، Lineage Service). |
| `Feature_Management_Domain.md` | معماری دامنه مدیریت ویژگی‌ها (Feature Registry، Online/Offline Serving). |
| `Machine_Learning_Domain.md` | معماری دامنه یادگیری ماشین (Training، Evaluation، Model Registry، Monitoring). |

#### ۴.۴.۴. پوشه Platform_and_Governance (زیرساخت و حاکمیت)

| نام سند | توضیح |
| :--- | :--- |
| `Identity_and_Access_Domain.md` | معماری دامنه هویت و دسترسی (Authentication، Authorization، Token Management). |
| `Infrastructure_and_Operations_Domain.md` | معماری دامنه زیرساخت و عملیات (Kubernetes، CI/CD، Resource Management). |
| `Security_and_Privacy_Domain.md` | معماری دامنه امنیت و حریم خصوصی (Encryption، Secrets Management، DLP). |
| `Observability_Domain.md` | معماری دامنه مشاهده‌پذیری (Metrics، Logs، Traces، Alerting). |
| `Governance_and_Compliance_Domain.md` | معماری دامنه حاکمیت و انطباق (Policy Engine، Audit Service، Compliance Reporting). |
| `Responsible_AI_Domain.md` | معماری دامنه هوش مصنوعی مسئولانه (Bias Evaluation، Explainability، Human-in-the-Loop). |

**اسناد تکمیلی سطح دامنه:**

علاوه بر اسناد تخصصی هر دامنه، اسناد زیر نمای کلی و روابط بین دامنه‌ها را ارائه می‌دهند:

| نام سند | توضیح |
| :--- | :--- |
| `Domain_Landscape.md` | نقشه سلسله‌مراتبی دامنه‌ها در چهار لایه اصلی. |
| `Domain_Overview.md` | نمای کلی و جامع از تمام دامنه‌ها، شامل شرح مسئولیت‌ها و فایل‌های پیشنهادی. |
| `Context_Map.md` | نقشه کانتکست دامنه‌ها، شامل الگوهای تعامل و قراردادهای بین آن‌ها. |
| `Domain_Dependency_Map.md` | نقشه وابستگی دامنه‌ها، شامل وابستگی‌های مستقیم، غیرمستقیم و بحرانی. |
| `Business_Rule_Catalog.md` | فهرست تمام قوانین کسب‌وکار (Business Rules) در سراسر پلتفرم. |
| `Domain_Events_Catalog.md` | فهرست تمام رویدادهای دامنه (Domain Events) و قراردادهای آن‌ها. |

---

### ۴.۵. بخش ۰۴: معماری سراسری (Cross-Cutting Architecture)

این بخش شامل مستندات موضوعات سراسری است که در تمام دامنه‌ها و لایه‌ها جاری هستند.

| نام سند | توضیح | مخاطب اصلی |
| :--- | :--- | :--- |
| `Security_and_Privacy_Architecture.md` | معماری یکپارچه امنیت و حریم خصوصی (Zero Trust، Encryption، IAM). | معماران امنیت، توسعه‌دهندگان |
| `Data_Governance_and_Compliance.md` | چارچوب حاکمیت داده و انطباق (Data Classification، Retention، Right to be Forgotten). | معماران داده، حاکمیت داده، توسعه‌دهندگان |
| `Responsible_AI_Guidelines.md` | رهنمودهای هوش مصنوعی مسئولانه (Fairness، Transparency، Explainability). | تیم AI، محصول، اخلاق |
| `Plugin_And_Marketplace_Governance.md` | معماری حاکمیت افزونه‌ها و بازارچه (Extension Trust Model، Capability-Based Access). | معماران، توسعه‌دهندگان، محصول |
| `Monitoring_and_Observability_Overview.md` | مرور معماری نظارت و مشاهده‌پذیری (Metrics، Logs، Traces، Alerting). | عملیات، SRE، توسعه‌دهندگان |

---

### ۴.۶. بخش ۰۵: عملیات و زمان اجرا (Runtime and Operations)

این بخش شامل مستندات مرتبط با استقرار، محیط‌ها و فرآیندهای عملیاتی است.

| نام سند | توضیح | مخاطب اصلی |
| :--- | :--- | :--- |
| `Deployment_Environment_Topology.md` | توپولوژی استقرار، جداسازی محیط‌ها، معماری Kubernetes و Disaster Recovery. | DevOps، SRE، معماران |
| `DevOps_and_CICD_Pipeline.md` | معماری DevOps و خط لوله CI/CD، شامل فرآیند Build، Test، Security Scan و Deploy. | DevOps، توسعه‌دهندگان |

---

## ۵. راهنمای مخاطبان

| نقش | اسناد کلیدی |
| :--- | :--- |
| **مدیران ارشد و مدیران محصول** | `Vision_and_Strategy.md`، `High_Level_PRD.md`، `Product_Roadmap.md`، `User_Personas_and_Use_Cases.md` |
| **معماران ارشد و معماران راهکار** | `Architecture_Principles_And_Decisions.md`، `Architecture_Overview.md`، `Component_Map_And_Responsibilities.md`، `ADR/`، `Domain_Landscape.md`، `Context_Map.md` |
| **توسعه‌دهندگان (Backend/Frontend)** | `API_Contract_Overview.md`، `Integration_Boundaries_And_Tooling_Framework.md`، `Data_Model_And_Knowledge_Schema_Overview.md`، `Domains/` (دامنه مرتبط) |
| **توسعه‌دهندگان هوش مصنوعی و علم داده** | `Multi_Agent_Collaboration_Model.md`، `Domains/Intelligent_Core/`، `Domains/Data_and_ML/`، `Responsible_AI_Guidelines.md` |
| **متخصصین امنیت و حاکمیت داده** | `Security_and_Privacy_Architecture.md`، `Data_Governance_and_Compliance.md`، `Domains/Platform_and_Governance/` |
| **مدیران عملیات و SRE** | `Non_Functional_Requirements_And_SLA.md`، `Risk_And_Failure_Mode_Analysis.md`، `Deployment_Environment_Topology.md`، `DevOps_and_CICD_Pipeline.md`، `Monitoring_and_Observability_Overview.md` |
| **تیم DevOps** | `Deployment_Environment_Topology.md`، `DevOps_and_CICD_Pipeline.md`، `Domain_to_Component_and_Deployment_Mapping.md` |

---

## ۶. ارتباط با سایر اسناد (References)

| نام سند | نوع ارتباط |
| :--- | :--- |
| `Naming_And_Terminology_Glossary.md` | مرجع واژه‌نامه و استاندارد نام‌گذاری که در تمام اسناد استفاده شده است. |
| `Architecture_Principles_And_Decisions.md` | مرجع اصول معماری که تمام تصمیمات و طراحی‌ها بر اساس آن‌ها شکل گرفته‌اند. |
| `ADR_Index.md` | مرجع تمام تصمیمات معماری ثبت‌شده که پیشینه و دلایل انتخاب هر راهکار را ارائه می‌دهد. |
| `Domain_Landscape.md` | مرجع دامنه‌ها که تمام اسناد دامنه به آن ارجاع می‌دهند. |

---

## ۷. نتیجه‌گیری

این سند، نقشه راهنمای مجموعه مستندات پلتفرم را ارائه می‌دهد و به تمام تیم‌ها کمک می‌کند تا به‌سرعت سند مورد نیاز خود را پیدا کنند. ساختار مستندات بر اساس اصول تفکیک سطوح انتزاع، تفکیک دامنه‌ها، تمرکز بر سرفصل‌های سراسری و ثبت تصمیمات کلان طراحی شده است تا مجموعه‌ای منسجم، قابل نگهداری و قابل ارجاع ایجاد شود.

همان‌طور که پلتفرم تکامل می‌یابد، این نقشه نیز باید به‌روزرسانی شود تا منعکس‌کننده تغییرات در ساختار و محتوای مستندات باشد.

**مسئولیت به‌روزرسانی:** تیم معماری به همراه تیم‌های محصول و توسعه مسئول بازبینی دوره‌ای این سند و به‌روزرسانی آن بر اساس تغییرات در ساختار مستندات یا اضافه شدن اسناد جدید هستند.

**ثبات سند:** این سند باید با تغییرات در مجموعه مستندات هم‌گام باشد. هر تغییر در ساختار یا نام اسناد باید در این سند منعکس شود تا به‌عنوان مرجع معتبر باقی بماند.

---
</div>
