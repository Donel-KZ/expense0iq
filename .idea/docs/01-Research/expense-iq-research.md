# ExpenseIQ
## Research & Application Design

**Author:** Donel Kia Nzambi
**Institution:** Eduvos
**Version:** 1.0
**Date:** July 2026

---

## Table of Contents

1. Introduction
2. Problem Statement
3. Research Objectives
4. Literature Review
5. Expense Trackers vs Budget Applications
6. Data Collection Techniques
7. Artificial Intelligence in Expense Tracking
8. OCR Technology
9. User Behaviour Research
10. Privacy and Security
11. Application Design Decisions
12. Proposed System: ExpenseIQ
13. Conclusion
14. References

---

## 1. Introduction

### 1.1 Background of Personal Finance Management

Personal finance management has always required some form of record keeping, from handwritten ledgers to spreadsheet budgets to, more recently, dedicated mobile applications. What has changed is not the underlying need — knowing where money goes — but the friction involved in meeting it. Smartphones, cloud storage, bank APIs, and increasingly capable machine learning models have collapsed a task that once took an evening with a chequebook into something that can happen automatically in the background of daily life.

This shift matters because personal finance is, at its core, a behavioural problem before it is a technical one. Most people do not overspend because they lack arithmetic ability; they overspend because small, frequent purchases are individually forgettable even when their sum is significant. Digital tools do not solve this by being more accurate calculators than a person with a notebook — they solve it, when they succeed, by making the invisible visible with a level of consistency and low friction manual tracking rarely achieves.

### 1.2 Importance of Expense Tracking

Expense tracking is frequently treated as a subordinate feature of budgeting, but the evidence suggests it is a distinct and independently valuable behaviour. Tracking spending — even without setting any formal budget or spending limit — has been shown to change what people buy. This is consistent with a simple psychological mechanism: attention itself is a form of restraint. When a purchase must be observed, logged, or at minimum acknowledged, it becomes a decision rather than a reflex.

Tracking also serves an emotional function that is easy to overlook in a purely technical discussion. Financial anxiety is frequently driven less by the size of a person's expenses than by uncertainty about them. Not knowing how much has been spent this month is, for many people, more stressful than knowing the number is larger than they'd like. Expense tracking converts an ambiguous, ongoing worry into a concrete, checkable fact, and that conversion alone has value independent of any behaviour change that follows.

### 1.3 Growth of Digital Finance Applications

The digital personal finance category has grown substantially over the past several years. Industry data from early 2026 puts the number of monthly active users of personal finance apps in the United States at roughly ninety-five million, spread across a competitive field of paid and free tools<sup>[1]</sup>. This growth has been driven by three converging trends: the maturity of bank data aggregation infrastructure (chiefly Plaid, alongside competitors such as MX, Yodlee, and Finicity), the near-universal availability of high-quality smartphone cameras suitable for receipt capture, and the recent availability of large language models capable of unstructured classification tasks — such as guessing the category of an ambiguous transaction — that previously required either manual rules or dedicated machine learning pipelines.

The category has also fragmented along philosophical lines rather than consolidating around one dominant approach. Some products, like YNAB, are built around a proactive, zero-based budgeting discipline where a user assigns a job to every unit of currency before spending it. Others, like Monarch Money, take a reactive, dashboard-first approach: connect your accounts, and the app tells you what already happened. This split turns out to matter more than any individual feature comparison, and it directly informed the design decisions discussed later in this report.

### 1.4 Purpose of the Research

The purpose of this research is twofold. First, to build an accurate, evidence-based understanding of how modern expense tracking applications work — their data collection methods, their categorization logic, their use of OCR and AI, and their security architecture — rather than relying on marketing descriptions alone. Second, to translate that understanding into a set of justified design decisions for a real application, ExpenseIQ, so that its architecture reflects deliberate trade-offs rather than default assumptions.

---

## 2. Problem Statement

Despite the size and maturity of the personal finance app market, several persistent problems remain unresolved for a large share of users.

**Poor financial awareness.** Many people do not have an accurate mental model of their own spending. Small, recurring purchases — a daily coffee, a rideshare, a subscription renewal — are individually too small to register as decisions, yet collectively they can represent a significant share of monthly spending. Without a system that surfaces this pattern, awareness stays low regardless of a person's intent to manage money well.

**Difficulty remembering purchases.** Manual tracking, while valuable as a habit-forming exercise, depends on consistent recall and discipline. A missed day of logging often cascades into a missed week, at which point the mental cost of "catching up" exceeds the perceived benefit, and the user abandons the practice entirely.

**Overspending.** Overspending is rarely a single large decision; it is usually the accumulation of many small ones. Tools that only report spending after the fact, at the end of a billing cycle or month, arrive too late to change the behaviour that caused the overspend.

**Lack of spending insight.** Raw transaction lists, even when accurately logged, do not by themselves produce insight. Without categorization, comparison to prior periods, and visualization, a user is left with data but not understanding.

**Complex budgeting tools.** Some of the most powerful tools in the category, such as YNAB, come with a real learning curve. Its zero-based envelope methodology is effective for people willing to adopt it as a discipline, but user reviews consistently describe an initial period of feeling overwhelmed by the system before (if ever) it clicks<sup>[2]</sup>. A tool that is too demanding to onboard into loses users before it can demonstrate any value.

ExpenseIQ is designed as a response to these problems specifically for a lightweight, single-user context — not as a competitor to full financial-dashboard products, but as a focused answer to "where did my money actually go, with the least possible friction."

---

## 3. Research Objectives

This research was guided by the following objectives:

1. **Understand how modern expense trackers work**, including their architecture, ingestion methods, and categorization pipelines.
2. **Investigate common features** across the category to establish a realistic, non-redundant feature baseline for a portfolio-scale application.
3. **Evaluate AI categorization approaches**, from static rule tables to large language model classification, and their respective cost, accuracy, and maintenance trade-offs.
4. **Research OCR technologies** relevant to receipt processing, including their error characteristics and the validation patterns used to compensate for them.
5. **Design a realistic portfolio application** — ExpenseIQ — whose architecture and feature scope are directly justified by the findings above, rather than assumed.

---

## 4. Literature Review

This section reviews eight expense-tracking and budgeting applications that represent the major approaches in the category as of 2026: YNAB, Monarch Money, Expensify, Wallet by BudgetBakers, PocketGuard, Money Manager, Empower, and Spendee.

### 4.1 YNAB (You Need A Budget)

YNAB is built around a proactive, zero-based budgeting philosophy summarised in four rules: give every dollar a job, embrace true expenses, roll with the punches, and age your money<sup>[3]</sup>. Rather than reporting where money went, YNAB requires the user to decide where money will go before it is spent, and to actively rebalance categories when one runs out. Its strength lies in behaviour change: users who commit to the system report a genuinely different relationship with spending, not just better visibility into it. Its weakness is friction — the same discipline that makes it powerful makes it demanding, and its interface has a well-documented learning curve that causes some users to abandon it before the habit takes hold<sup>[2]</sup>. YNAB also offers one of the most generous trial periods in the category (34 days, no card required), suggesting the product itself acknowledges that its value is not obvious in a short first session.

### 4.2 Monarch Money

Monarch Money takes the opposite philosophical stance: it is reactive rather than proactive, positioning itself as a financial dashboard rather than a budgeting discipline<sup>[3]</sup>. It connects to bank, credit, and investment accounts, automatically categorizes transactions, and surfaces net worth, recurring bills, and spending trends without requiring the user to allocate funds in advance. Its strength is low friction and broad scope — one login covers a household's full financial picture, including investments, and one subscription covers multiple household members<sup>[4]</sup>. Its weakness, relative to YNAB, is that awareness without allocation does not guarantee behaviour change; a user can see exactly where their money went and still not adjust their spending going forward, since the app does not require any forward commitment.

### 4.3 Expensify

Expensify is oriented toward expense reporting and reimbursement rather than personal budgeting, and is widely used in business contexts. Its core strength is the ease of generating expense reports from scanned receipts, a workflow reviewers consistently rate highly for straightforwardness and efficiency<sup>[5]</sup>. This makes it a useful reference point specifically for receipt-based OCR workflows, even though its target use case (employee reimbursement) differs from a personal tracker. Its weakness for personal use is that its feature set and interface are built around organisational approval chains, which are unnecessary complexity for an individual user simply trying to understand their own spending.

### 4.4 Wallet by BudgetBakers

Wallet emphasises manual and semi-automated entry with strong category customisation and multi-currency support, positioning itself for users who want more granular control than a fully automated dashboard provides. Its strength is flexibility; its weakness, relative to fully automated tools, is that flexibility comes with more setup and maintenance overhead than a user expects from a "download and go" app.

### 4.5 PocketGuard

PocketGuard distinguishes itself by reducing an entire financial picture to a single number: "In My Pocket," the amount safely available to spend after bills, goals, and savings are accounted for<sup>[6]</sup>. This design is intentionally minimal — rather than asking the user to review category-by-category spending, it collapses the decision into one figure a person can check before making a purchase. Its strengths are simplicity and a genuinely useful debt payoff planning feature; its most consistently reported weakness across independent review sources is unreliable bank synchronisation with several major financial institutions, leading to stale balances and a documented list of problem banks maintained by the company itself<sup>[7]</sup>. This is a useful cautionary data point for any application planning to depend on bank-sync as a primary ingestion method: aggregator reliability is not uniform across institutions, and user trust erodes quickly when a "safe to spend" number is wrong.

### 4.6 Money Manager

Money Manager takes an explicitly manual, ledger-style approach, structured similarly to double-entry bookkeeping. Rather than treating manual entry as an inferior fallback to automation, it treats the discipline of entry as the product's core value proposition — the friction is the feature, not a bug to be engineered away. Its strength is that consistent users tend to develop very high financial self-awareness, an outcome supported by behavioural research discussed in Section 9, which found that active, manual tracking is associated with greater financial self-awareness than automated tracking<sup>[8]</sup>. Its weakness is the same friction that produces this benefit: it is a poor fit for users who want a hands-off tool.

### 4.7 Empower (Personal Dashboard)

Empower's personal finance dashboard focuses heavily on net worth tracking, investment analysis, and retirement planning, with expense categorisation as a secondary feature rather than the primary focus. Its strength is breadth for users who want a single view spanning investments and cash accounts; its weakness for a pure expense-tracking use case is that day-to-day spending insight is not the product's design centre, and the interface reflects that emphasis.

### 4.8 Spendee

Spendee combines manual entry, bank sync, and shared wallets for households, with a visually driven interface emphasising charts and spending breakdowns. Its strength is presentation — spending data is easy to interpret at a glance — while its weakness, shared with several competitors in this space, is a paid tier gate on the automation features (bank sync, shared wallets) that make the product most compelling.

### 4.9 Synthesis

Across all eight applications, a consistent pattern emerges: no product excels at both low friction and high behaviour change simultaneously. Automated tools (Monarch, PocketGuard, Empower) reduce friction and increase visibility but do not reliably change spending on their own. Manual and semi-manual tools (YNAB, Money Manager, Wallet) demand more from the user but are more consistently linked to genuine behaviour change and self-awareness. This tension is the central design tension ExpenseIQ has to resolve, and it directly motivates the hybrid categorisation approach described in Section 7 and Section 11: automate what can be automated reliably (categorisation), while keeping the moment of entry itself — manual or receipt-based — as a deliberate, attention-holding action rather than an invisible background sync.

---

## 5. Expense Trackers vs Budget Applications

The terms "expense tracker" and "budget app" are frequently used interchangeably in casual conversation, but they describe two different orientations toward the same underlying data.

An **expense tracker** is fundamentally backward-looking. Its central question is "where did my money go?" It captures transactions as they occur (or shortly after), assigns them to categories, and presents summaries and trends. Success for an expense tracker is measured by the accuracy and completeness of the historical record, and by how easily a user can extract insight from it — for example, noticing that dining spend has crept up over the last three months.

A **budget app** is forward-looking. Its central question is "where should my money go?" It requires the user (or the app, on the user's behalf) to set intentional limits — per category, per month — and then measures actual spending against those limits. Success for a budget app is measured by adherence: did the user stay within the plan they set.

The distinction matters practically because the two orientations imply different feature priorities and different failure modes. A pure tracker can succeed even if the user never sets a formal limit, simply by making spending visible enough to prompt self-correction — this is the mechanism identified in the behavioural research reviewed in Section 9, where tracking reduced discretionary spending independent of any formal budget<sup>[8]</sup>. A pure budget app, by contrast, fails immediately if its planning step is skipped, since there is no limit to measure adherence against.

Most commercial products in 2026 blend both orientations to some degree — YNAB is budget-first but shows historical spend; Monarch is tracker-first but offers optional budget categories. ExpenseIQ, as scoped in this report, deliberately positions itself as a tracker rather than a budget app for its first version. This is not a limitation so much as a sequencing decision: the research in Section 9 suggests tracking alone produces a measurable behavioural effect, and a tracker is materially simpler to build well than a budgeting system with rollover logic, category reallocation, and goal-tracking. Budgeting features are a natural, well-justified addition for a later version, once the tracking foundation is solid.

---

## 6. Data Collection Techniques

Three ingestion methods account for essentially all data capture in the expense-tracking category: manual entry, receipt OCR, and bank synchronisation through a data aggregator. Each carries distinct trade-offs in convenience, privacy exposure, and implementation complexity.

### 6.1 Manual Entry

**How it works.** The user types transaction details — amount, merchant, category, date, and optionally a note — directly into the app at or after the time of purchase.

**Advantages.** Manual entry requires no external integration, carries no data-sharing privacy exposure beyond the app itself, and — per the behavioural research discussed in Section 9 — is associated with higher financial self-awareness than automated alternatives, precisely because it demands attention at the moment of the transaction<sup>[8]</sup>. It is also the only method that works uniformly for cash transactions, which bank sync and most OCR workflows cannot capture at all.

**Disadvantages.** It depends entirely on user discipline. A missed entry is simply lost data unless the user reconstructs it from memory later, and the research reviewed in Section 9 identifies exactly this friction — the difficulty of sustaining perfect, transaction-by-transaction logging — as the leading cause of abandonment.

**Implementation complexity.** Low. This is a straightforward CRUD (create, read, update, delete) workflow against a single data model, requiring no external service integration.

### 6.2 Receipt OCR

**How it works.** The user photographs a physical or digital receipt; an OCR (Optical Character Recognition) pipeline extracts structured fields — merchant, date, total amount, and sometimes line items — from the image, and pre-fills an entry for the user to confirm.

**Advantages.** OCR substantially reduces the manual effort of logging in-person purchases while still requiring the user to review and confirm the result, which preserves some of the attention benefit of manual entry. It also naturally produces a photographic record of the receipt, useful for returns, warranty claims, or expense reimbursement.

**Disadvantages.** OCR accuracy is good but not perfect. Independent estimates suggest baseline OCR-only extraction gets receipts fully correct only around 85 percent of the time, with AI-enhanced pipelines reducing data-entry mistakes substantially but not eliminating them, particularly on handwritten amounts, unusual receipt layouts, or damaged receipts<sup>[9,10]</sup>. This makes a confirmation step, rather than blind auto-save, a functional requirement rather than a nice-to-have. OCR also cannot capture transactions with no physical or digital receipt, such as informal cash exchanges.

**Implementation complexity.** Moderate. Requires integrating a third-party OCR or document-understanding API, image upload and storage handling, and a review UI that surfaces low-confidence extractions for correction rather than presenting all fields as equally certain.

### 6.3 Bank Synchronisation

**How it works.** The user authenticates with their bank through a data aggregator (most commonly Plaid, alongside competitors such as MX, Yodlee, and Finicity). The aggregator, not the app itself, handles the bank-facing authentication and periodically pulls transaction, balance, and identity data, returning it to the app in a standardised format<sup>[11,12]</sup>.

**Advantages.** Bank sync is the lowest-friction method by a wide margin — once linked, transactions appear automatically with no ongoing user effort, and modern aggregators return data already enriched with merchant name and a suggested category. This is the mechanism behind the "set it and forget it" experience products like Monarch and PocketGuard are built around.

**Disadvantages.** It is the most privacy-sensitive method by construction: a third party (the aggregator) sits between the user's bank and the app, and the user is trusting both the aggregator's and the app developer's data handling practices. This has driven a documented and growing shift among privacy-conscious users toward manual or local-first tracking instead<sup>[13]</sup>. It is also the least reliable method operationally — aggregator connections to specific institutions can and do break, sometimes for extended periods, and PocketGuard's own maintained list of problem banks (including several major institutions) illustrates that this is not a hypothetical risk but an ongoing operational reality across the category<sup>[7]</sup>.

**Implementation complexity.** High. Requires a signed agreement and API integration with an aggregator, secure token storage, webhook or polling infrastructure to detect new transactions, and a compliance posture (see Section 10) appropriate to handling financial account credentials, even indirectly.

### 6.4 Comparison Summary

| Method | Friction | Privacy Exposure | Reliability | Implementation Effort |
|---|---|---|---|---|
| Manual Entry | Highest | Lowest | Depends on user | Low |
| Receipt OCR | Moderate | Low–Moderate | High, with review step | Moderate |
| Bank Sync | Lowest | Highest | Variable by institution | High |

This comparison directly informs the scoping decision in Section 11: for a first version, manual entry and receipt OCR together cover the majority of realistic use cases with a fraction of the privacy exposure and implementation risk that bank synchronisation introduces.

---

## 7. Artificial Intelligence in Expense Tracking

### 7.1 The Categorisation Problem

Every expense tracker must answer the same underlying question for every transaction: which category does this belong to? The naive solutions — asking the user every time, or leaving transactions uncategorised — both fail in practice, the first from friction, the second from producing a tracker with no usable insight. This has led the category toward increasingly automated categorisation, of which three approaches are common.

### 7.2 Rule-Based Systems

The simplest approach is a static lookup table mapping known merchant strings (or substrings) to categories — for example, mapping any transaction description containing "starbucks" to a "Dining" category. This is fast, free to run, fully deterministic, and easy to audit. Its limitation is coverage: any merchant not already in the table produces no categorisation at all, and the table requires ongoing manual maintenance to keep pace with new or renamed merchants.

### 7.3 Machine Learning Classification

A step up in sophistication, some platforms train a classifier on large volumes of historical transaction data, learning statistical associations between transaction features (merchant string patterns, amount, timing) and categories without needing an explicit rule for every merchant. This generalises better than a static table but requires a substantial training dataset and ongoing retraining to remain accurate as merchant naming conventions and consumer spending patterns shift.

### 7.4 Large Language Models

The most recent addition to the category is using a general-purpose large language model as a classifier: given a merchant string and an amount, the model returns a best-guess category from a fixed, constrained set, without any prior training on the specific dataset. This approach requires no training data or ongoing model maintenance, generalises well to merchants never seen before, and can be constrained via prompt design to return only a valid value from a fixed enum — but it comes at a small per-call cost and a small amount of latency, both trivial at the scale of an individual user's transaction volume but non-negligible if called on every transaction of a large user base with no caching layer.

### 7.5 Cost and Accuracy Trade-offs

In practice, the most effective architecture — and the one adopted for ExpenseIQ, detailed fully in Section 11 — is not a choice between these three options but a cascade that uses each where it is strongest: a rule-based lookup table handles the majority of transactions once a user's common merchants have been seen at least once, falling back to an LLM call only for genuinely novel merchants. This keeps steady-state cost near zero while retaining full automatic coverage for new merchants, and each LLM-classified merchant is written back into the lookup table so it is never re-classified by the model again.

### 7.6 The Role of User Correction

No categorisation method, however sophisticated, will be correct one hundred percent of the time — a $4.50 charge at an ambiguous merchant name could plausibly be coffee, a snack, or a small retail purchase, and only the user has the context to know which. Every well-designed automated categorisation system therefore treats its output as a confident default, not a final answer: the category is shown, editable, and any correction the user makes is fed back into the system (the merchant lookup table, in ExpenseIQ's case) so that the same mistake is not repeated for that merchant in future.

---

## 8. OCR Technology

### 8.1 What OCR Is

Optical Character Recognition is a technology that converts an image containing text — a photograph, scan, or PDF — into machine-readable, structured text data<sup>[14]</sup>. Applied to a plain document, OCR alone answers "what characters are on this page." Applied to a receipt specifically, modern pipelines go further, using pattern recognition and, increasingly, machine learning models trained on large receipt datasets to identify not just characters but semantic fields: which numbers represent the total, which line represents the merchant name, and which date corresponds to the transaction rather than, for instance, a printed expiry date on a coupon<sup>[15]</sup>.

### 8.2 How Receipts Are Processed

A typical receipt-processing pipeline runs in stages: image pre-processing (cropping, deskewing, contrast correction), text recognition (converting pixels to characters), and field classification (deciding which recognised text corresponds to which semantic field — merchant, date, total, tax). Because these stages run sequentially, an error introduced early — for example, a poorly cropped or blurry image — tends to cascade forward, meaning image quality at capture time has an outsized effect on final accuracy<sup>[16]</sup>.

Two broad technical strategies exist for the field-classification stage. Template-based systems expect information in fixed, pre-defined positions and struggle badly with unfamiliar layouts — one industry estimate places their failure rate at roughly one in three fields on a typical, messy real-world receipt<sup>[16]</sup>. Machine-learning-based systems instead learn to recognise a field (such as a total amount) by its visual and textual pattern regardless of where it sits on the page, generalising far better across the wide variety of receipt formats different merchants use.

### 8.3 Common OCR Errors

Even well-implemented OCR pipelines are not perfectly accurate. Industry estimates suggest baseline OCR-only extraction from receipts is correct roughly 85 percent of the time, with the majority of remaining errors concentrated in a few predictable categories: handwritten amounts or additions, unusual or non-standard receipt formats, faded thermal-paper printing, and physically damaged or creased receipts<sup>[9,10]</sup>. AI-enhanced pipelines meaningfully reduce these errors relative to OCR alone, with reported reductions in data-entry mistakes as high as ninety percent in enterprise contexts, but do not eliminate them entirely<sup>[10]</sup>.

### 8.4 Data Validation and User Confirmation

Because errors concentrate predictably rather than occurring uniformly at random, well-designed receipt-scanning UX does not present every extracted field with equal confidence. Instead, low-confidence extractions are flagged for review while high-confidence fields are accepted with minimal friction, and the user is always shown the source image alongside the extracted values so a quick visual check is possible before saving<sup>[9]</sup>. This confirmation step is not a workaround for immature technology; it is a permanent, structural feature of any responsible OCR-based expense workflow, since even future improvements in raw accuracy will not eliminate genuinely ambiguous cases such as a discounted total with an unclear final line.

---

## 9. User Behaviour Research

Understanding how expense tracking apps are used in practice — rather than how they are designed to be used — is essential to building a tool people will actually keep using.

### 9.1 Daily and Weekly Rhythms

The usage pattern most consistently associated with long-term retention is not exhaustive, real-time logging but a lighter rhythm: a short daily check-in of roughly two minutes, combined with a longer weekly review of ten to twenty minutes in which the user actually examines category trends rather than just confirming individual entries. Users report the habit beginning to feel automatic, rather than effortful, after roughly four to six weeks of sustained weekly reviews — long enough for genuine patterns in their own spending to become visible to them.

### 9.2 Why Users Abandon Tracking

Abandonment is rarely explained by picking the "wrong" app. The more consistent explanation across user research is friction compounding with an unrealistic standard: users who attempt to log every single transaction with perfect accuracy find that one missed day creates a backlog, the backlog feels effortful to reconstruct, and avoidance sets in. This mirrors long-standing findings in the psychology of recording effort, which identify the burden of consistent recording — separate from any lack of motivation to improve one's finances — as a primary obstacle to sustained tracking<sup>[17]</sup>.

### 9.3 The Behavioural Evidence for Tracking's Effect

The most rigorous evidence available on this question comes from a 2023 study using administrative, person-record-level data from a real transaction-tracking application, covering more than 1,600 users over a two-year period<sup>[8]</sup>. The study found that persistent expense tracking was associated with a measurable decrease in the share of discretionary spending, and separately with an increase in "budget slack" (the gap between planned and actual spending, in the user's favour) — but did **not** find a reliable improvement in strict adherence to a pre-set monthly budget figure. In other words, tracking reliably changes *what* people buy, more than it guarantees they land on any *specific* number. The same study, extended with U.S. survey data, found that financial self-awareness mediates the relationship between tracking and improved saving decisions, and that active, manual tracking methods are associated with higher financial self-awareness than fully automated tracking — automation's convenience appears to come at a small cost in attentiveness<sup>[8]</sup>.

Separately, industry-reported figures on real user cohorts describe a ten-to-twenty percent reduction in discretionary spending within the first month of consistent tracking, a reduction that persists for as long as tracking continues, with no explicit budget or spending restriction required to produce it<sup>[18]</sup>. This is consistent with the academic finding above: the mechanism is attention, not enforcement.

### 9.4 The Emotional Dimension

Beyond behaviour change, tracking research consistently surfaces an emotional effect distinct from any change in spending itself: financial anxiety is reduced substantially by simply knowing the numbers, independent of whether the numbers themselves improve. The dread of an unknown, unexamined financial picture appears to be a meaningfully separate source of stress from the financial picture itself, and resolving that uncertainty has value on its own.

### 9.5 The Privacy-Conscious Minority

Not every user is comfortable linking bank credentials to a third-party aggregator, even a well-regarded one. A documented and apparently growing segment of users has shifted deliberately toward manual tracking, local-first tools, and spreadsheet or CSV-based workflows specifically to avoid aggregator-based data sharing<sup>[13]</sup>. This is a meaningful design constraint: an application that only supports bank-sync ingestion excludes this segment entirely, whereas one that supports manual entry as a first-class method (rather than an afterthought) does not.

---

## 10. Privacy and Security

### 10.1 Bank Aggregators and How They Work

When an application supports bank synchronisation, it very rarely talks to a bank's systems directly. Instead, it integrates with a data aggregator — Plaid is the dominant player, connecting to more than eleven thousand financial institutions, alongside competitors including MX, Yodlee, and Finicity<sup>[11,19]</sup>. The aggregator handles authentication with the bank (increasingly via modern OAuth-based APIs where the bank supports them, falling back to credential-based methods where it does not), normalises each institution's data format into a standard schema, and exposes that standardised data to the requesting application through its own API<sup>[12]</sup>.

Critically, in this model the application developer typically never sees or stores the user's actual bank login credentials — those are handled entirely within the aggregator's systems, with the app receiving only a scoped access token and the resulting transaction data. Aggregators such as Plaid describe their architecture as increasingly "zero-trust": every connection is independently verified and encrypted, and the user can revoke access at any time either through the app or directly through their bank<sup>[20,11]</sup>.

### 10.2 Data Ownership and Third-Party Risk

Even with this architecture, real privacy exposure exists, and it is worth being precise about where. Linking a bank account means trusting three parties in sequence: the bank itself, the aggregator, and the application developer. A user switching between two apps that both use Plaid as their aggregator (for example, from one budgeting app to another) changes who holds a copy of their transaction history, but does not change the underlying dependency on the aggregator itself — the data still passes through and is often retained by that same third party regardless of which application sits on top of it<sup>[13]</sup>. This is precisely the dynamic that has pushed a segment of privacy-conscious users toward manual or local-first tools instead, as discussed in Section 9.5.

### 10.3 Regulatory Considerations: GDPR and POPIA

Any application handling personal financial data needs to account for the data protection regime applicable to its users. In the European context, the General Data Protection Regulation (GDPR) imposes requirements around consent, purpose limitation, and a user's right to access and delete their own data. South African developers building for a South African user base — or, more broadly, for users physically located in South Africa — must additionally account for the Protection of Personal Information Act (POPIA), which came into full effect on 1 July 2021<sup>[21]</sup>. POPIA is structurally similar to GDPR and is built around eight conditions for lawful processing, including accountability, purpose specification, and security safeguards<sup>[22]</sup>. Practically, this means an application should collect only the data it needs for a stated purpose, protect that data with reasonable technical measures (see Section 10.4), and be able to explain to a user what data is held and why. For a portfolio-scale project, formal compliance registration (such as appointing an Information Officer) is unlikely to be necessary, but designing with POPIA's principles in mind — data minimisation, purpose limitation, and basic security hygiene — is good practice regardless of scale, and directly relevant to a South African academic context.

### 10.4 Authentication and Encryption

Two baseline security measures are effectively mandatory for any application handling financial or otherwise sensitive personal data, regardless of scale:

**JWT Authentication.** JSON Web Tokens provide a stateless mechanism for authenticating API requests: after a successful login, the server issues a signed token containing the user's identity and an expiry, which the client then attaches to subsequent requests. The server verifies the signature on each request without needing to query a session store, which scales cleanly and is straightforward to implement in an ASP.NET Core API using the built-in JWT bearer authentication middleware.

**Encryption.** Data should be encrypted both in transit (via TLS/HTTPS for all API traffic — non-negotiable for any application handling financial data) and at rest for anything sensitive stored in the database, particularly if the scope is later extended to store bank access tokens or receipt images containing personal information. Major aggregators such as Plaid use both TLS and AES-256 encryption for stored data as their baseline standard<sup>[20]</sup>, and there is no reason a smaller application should hold itself to a lower bar for the data it does choose to store.

---

## 11. Application Design Decisions

Every architectural and technology choice made for ExpenseIQ traces back to a specific finding from the research above.

### 11.1 Manual Entry

**Decision:** Support manual entry as a first-class, fully-featured ingestion method, not a fallback.

**Justification:** Section 9.5 identifies a real, non-trivial user segment that avoids bank-linked apps entirely for privacy reasons, and Section 6.1 notes manual entry is the only method that captures cash transactions at all. The behavioural research in Section 9.3 additionally suggests manual entry is associated with *higher* financial self-awareness than fully automated tracking, meaning this choice is not merely a compromise to reduce scope — it is arguably the ingestion method most aligned with the outcome the app is trying to produce.

### 11.2 Receipt OCR

**Decision:** Support receipt photo capture with OCR-based pre-fill and mandatory user confirmation before saving.

**Justification:** Section 6.2 and Section 8 together establish that OCR meaningfully reduces logging effort while remaining imperfect (roughly 85 percent baseline accuracy), making a confirmation step a functional requirement rather than an optional nicety. This preserves the attention-holding benefit of manual entry (the user still sees and confirms every value) while removing the tedious part (typing merchant, date, and amount from scratch).

### 11.3 Deliberately Excluding Bank Sync (v1)

**Decision:** Do not implement Plaid or any bank-synchronisation integration in the first version.

**Justification:** Section 6.4's comparison table shows bank sync carries the highest implementation complexity and the highest privacy exposure of the three ingestion methods, for a portfolio-scale project where the two remaining methods already cover the large majority of realistic personal use cases. Section 6.3 additionally documents genuine reliability problems with bank sync in the market (PocketGuard's own maintained list of problem banks), meaning that even a competent implementation would inherit operational fragility outside the developer's control. This is deferred to a clearly scoped future version rather than treated as a permanent exclusion.

### 11.4 AI Categorisation with Merchant Normalisation and Caching

**Decision:** Implement fully automatic categorisation using a cascade: normalise the merchant string, check a persistent merchant-to-category lookup table, and fall back to a constrained LLM classification call only for merchants not already in the table, writing the result back for future reuse.

**Justification:** Section 7.5 identifies this cascade as the architecture that captures the strengths of both rule-based systems (near-zero cost, full determinism, instant response) and LLM classification (full coverage of novel merchants, no manual rule maintenance) while avoiding the weaknesses of either used alone. Merchant normalisation (stripping trailing store numbers, standardising case) is necessary because raw merchant strings from receipts or manual entry are inconsistent even for the same real-world business. User correction, per Section 7.6, feeds directly back into the same lookup table, meaning the system's accuracy improves specifically for this user's actual spending patterns over time, rather than staying static.

### 11.5 ASP.NET Core (Backend)

**Decision:** Build the API layer in ASP.NET Core with Entity Framework Core against PostgreSQL.

**Justification:** This stack provides mature, built-in support for the two security requirements identified in Section 10.4 — JWT bearer authentication and HTTPS enforcement — without third-party dependencies, alongside a strongly-typed data layer (EF Core) well suited to the layered architecture (Controller → Service → Repository) already adopted for this project, which keeps validation logic (Section 11's categorisation cascade included) cleanly separated from data access.

### 11.6 Client Framework Choice: Flutter, SwiftUI, Tauri, or Next.js

**Decision:** The client layer is framework-agnostic by design, with Flutter as the primary recommendation for a single, cross-platform mobile codebase.

**Justification:** Each option serves a different scope:
- **Flutter** produces a single codebase for both iOS and Android, which matters for a portfolio project intended to demonstrate mobile competency without doubling the client-side work.
- **SwiftUI** is the right choice specifically if the goal is to demonstrate native iOS development skill, at the cost of an Android build entirely.
- **Tauri** is appropriate if a lightweight desktop companion app is wanted, since it wraps a web frontend in a native shell with a far smaller footprint than Electron.
- **Next.js** is the right choice for a browser-accessible version, particularly useful for demonstrating the API independent of any mobile app store distribution process.

Because the backend exposes a standard REST API regardless of which client consumes it, this decision can be deferred or revisited without affecting the rest of the architecture — a direct benefit of the layered design adopted from the outset.

---

## 12. Proposed System: ExpenseIQ

### 12.1 Goals

ExpenseIQ aims to deliver an expense tracker that is genuinely low-friction to use daily, automatically categorises transactions without requiring the user to manage a category list, and demonstrates a realistic, defensible full-stack architecture suitable for a professional portfolio.

### 12.2 Features (v1 scope)

- Manual expense entry (amount, merchant, date, note)
- Receipt photo capture with OCR extraction and mandatory confirmation
- Fully automatic category assignment via the merchant-lookup-and-LLM-fallback cascade described in Section 11.4, with user override
- Monthly category breakdown and month-over-month comparison
- A fixed set of six categories for v1: Groceries, Dining, Transport, Bills, Shopping, Other

### 12.3 Architecture Overview

```
Client (Flutter, or Next.js web)
        │
        ▼
ExpensesController      — HTTP endpoints
        │
        ▼
ExpenseService          — validation, categorisation orchestration
        │
        ▼
ExpenseRepository (EF Core)
        │
        ▼
PostgreSQL
```

The categorisation cascade sits inside `ExpenseService`, invoked immediately after OCR extraction on the receipt-capture path, or immediately on save for the manual-entry path — the same function serves both, per Section 11.4.

### 12.4 Data Model (v1)

```
Expense
  Id, UserId, Amount, Category (enum), Merchant,
  Date, Note, ReceiptImageUrl, CreatedAt

MerchantCategoryMap
  Id, MerchantKey (normalised), Category (enum), TimesUsed
```

### 12.5 High-Level Workflow

1. User captures an expense via manual entry or receipt photo.
2. If a receipt photo, OCR extracts merchant, date, and amount; low-confidence fields are flagged for review.
3. `ExpenseService` normalises the merchant string and checks `MerchantCategoryMap` for an existing mapping.
4. If found, the category is applied immediately. If not, a constrained LLM call classifies the transaction, and the result is written back to `MerchantCategoryMap`.
5. The expense, with its assigned category, is saved via `ExpenseRepository` and EF Core to PostgreSQL.
6. The client displays the saved expense with its category, editable by the user; any correction updates `MerchantCategoryMap` for future transactions from that merchant.

---

## 13. Conclusion

This research set out to understand how modern expense-tracking applications work and to use that understanding to justify the design of a real application, ExpenseIQ, rather than assuming a feature set by default.

Several findings shaped the final design directly. First, the literature review of eight established applications (Section 4) surfaced a consistent tension between low friction and genuine behaviour change, with no reviewed product excelling at both simultaneously — a tension ExpenseIQ addresses by keeping manual entry and receipt confirmation as deliberate, attention-holding actions while automating only the categorisation step, not the entry step itself. Second, the behavioural research reviewed in Section 9 provided the strongest justification for scoping ExpenseIQ as a tracker rather than a budget app in its first version: persistent tracking alone, independent of any formal budget, is associated with a measurable reduction in discretionary spending, meaning a well-built tracker delivers real value even before any budgeting feature is added. Third, the comparison of ingestion methods in Section 6 justified excluding bank synchronisation from the initial scope on both privacy and reliability grounds, while retaining manual entry and OCR as fully sufficient for a realistic personal-use case.

A key lesson from this process is that "automatic" and "user input required" are not, in practice, opposite ends of a single spectrum. The categorisation architecture adopted in Section 11.4 is fully automatic from the user's perspective while remaining fundamentally a rule-based system with an AI fallback under the hood — the sophistication is in the cascade, not in defaulting to the most powerful available tool for every transaction.

Future enhancements, sequenced deliberately rather than bundled into v1, include: an optional bank-synchronisation integration (via Plaid or a comparable aggregator) for users who explicitly opt in; budget-setting and adherence tracking, building on the tracking foundation established here; multi-currency support; and household or shared-expense views. Each of these is a natural extension of the architecture already in place, rather than a redesign — a direct consequence of scoping ExpenseIQ's first version around justified, evidence-based decisions rather than an attempt to match the full feature set of mature commercial competitors from day one.

---

## 14. References

[1] envelopebudgeting.com. (2026). *YNAB vs Monarch Money: 2026 Comparison & Review*. Retrieved from https://envelopebudgeting.com/articles/monarch-vs-ynab

[2] Marriage, Kids and Money. (2026). *YNAB vs Monarch (2026): Best Budget App for Families?* Retrieved from https://marriagekidsandmoney.com/monarch-money-vs-ynab/

[3] FinCompareLab. (2026). *YNAB vs Monarch Money 2026: Critical $9 Mistake*. Retrieved from https://www.fincomparelab.com/comparisons/ynab-vs-monarch/

[4] Monarch. (2026). *Monarch vs YNAB: Best YNAB Alternative for 2026*. Retrieved from https://www.monarch.com/compare/ynab-alternative

[5] G2. (2026). *Expensify vs Spendesk Comparison*. Retrieved from https://www.g2.com/compare/expensify-vs-spendesk

[6] The Penny Hoarder. (2026). *PocketGuard Review 2026: A Budgeting App Built Around One Number*. Retrieved from https://www.thepennyhoarder.com/budgeting/pocketguard-review/

[7] CheckThat.ai. (2026). *PocketGuard Reviews 2026: What Users Really Think*. Retrieved from https://checkthat.ai/brands/pocketguard/reviews

[8] Zhang, Y. (2023). *How Does Expense-Tracking Inform Financial Behaviors?* University of Wisconsin working paper / Consumer Interests Annual, 2023. Retrieved from https://www.consumerinterests.org/assets/docs/CIA/CIA2023/ZhangYilingCIA2023.pdf

[9] ExpenVisor. (2025). *OCR Receipt Scanning: Complete Guide to Automated Expense Tracking*. Retrieved from https://www.expenvisor.com/blog/ocr-receipt-scanning-guide

[10] ExpenseVisor. (2025). *Understanding Receipt Scanner Accuracy: Methods, Technologies, and Key Considerations*. Retrieved from https://expensevisor.com/understanding-receipt-scanner-accuracy-methods-technologies-and-key-considerations/

[11] Plaid. (2023). *What Is an Open Banking API? How Apps and Accounts Connect*. Retrieved from https://plaid.com/resources/open-finance/open-banking-api/

[12] SevenSquareTech. (2026). *How Plaid Scaled Open Banking API Integration in Fintech*. Retrieved from https://www.sevensquaretech.com/how-plaid-scaled-open-banking-with-api-integration/

[13] Monavio. (2026). *Monarch Money vs YNAB 2026: Which Is Better?* Retrieved from https://monavio.app/blog/monarch-money-vs-ynab/

[14] Nomi. (2026). *How OCR Helps You Scan Receipts and Manage Expenses Easily*. Retrieved from https://www.nomi.co.uk/blog/scan-receipts-with-ocr/

[15] DoxBox. (2026). *How OCR Receipt Scanning Works for Expense Claims in Small Businesses*. Retrieved from https://doxbox.io/blog/how-ocr-receipt-scanning-works-expense-claims

[16] InvoiceDataExtraction.com. (2026). *Receipt OCR: How It Works, Accuracy, and Key Challenges*. Retrieved from https://invoicedataextraction.com/blog/receipt-ocr-guide

[17] Barsalou, L. W. (1991). Deriving Categories to Achieve Goals. In G. H. Bower (Ed.), *Psychology of Learning and Motivation* (as cited in Zhang, 2023).

[18] Mindful Suite. (2026). *Best Personal Finance Tracker Apps for Every Goal in 2026*. Retrieved from https://www.mindfulsuite.com/reviews/best-personal-finance-tracker-apps

[19] Duality Technologies. (2026). *Future of Financial Data Aggregation: Innovation Meets Privacy*. Retrieved from https://dualitytech.com/blog/financial-data-aggregation/

[20] Bitget Academy. (2026). *Open Finance 2026: Bitget, Plaid & Data Aggregation Explained*. Retrieved from https://www.bitget.com/academy/plaid-fintech-data-aggregation-services-comprehensive-2026-guide-america

[21] Scytale. (2026). *How to Achieve POPIA Compliance: Complete Checklist*. Retrieved from https://scytale.ai/resources/how-to-achieve-popia-compliance-complete-checklist/

[22] Securiti. (2021). *South Africa: POPIA Compliance Checklist*. Retrieved from https://securiti.ai/blog/popia-compliance-checklist/