# Introduction

## Background of Personal Finance Management

Managing personal finances has historically relied on manual bookkeeping: paper ledgers, spreadsheets, or simple mental estimates of monthly spending. Over the last decade, this landscape has shifted substantially. Digital banking, card-based payments, and buy-now-pay-later services have fragmented spending across more channels than ever before, while simultaneously generating the transaction-level data needed to observe that spending in far greater detail than a paper ledger ever could. Personal finance management (PFM) software emerged to close this gap: software that ingests transaction data, organizes it, and presents it back to the user in a form that supports better financial decisions.

## Importance of Expense Tracking

Expense tracking, specifically, is the practice of systematically recording what money is spent, on what, and when. It is distinct from budgeting in that it is fundamentally a record-keeping and awareness activity rather than a restriction-based one. Academic work on financial self-regulation frames expense tracking as a form of self-monitoring: a behavior that increases financial awareness and, in turn, shapes financial choices even without an explicit rule being enforced [18], [19]. This makes expense tracking a foundational behavior underlying nearly every other personal finance activity, including budgeting, saving, and debt repayment.

## Growth of Digital Finance Applications

The market for digital personal finance tools has grown substantially. Financial account aggregation, the technology underpinning most modern expense trackers, now connects roughly one in three adults in the United States to at least one financial application through a single aggregation provider [1]. Review platforms covering this category in 2026 list dozens of actively maintained budgeting and expense-tracking applications, ranging from zero-based budgeting tools to automated, dashboard-style aggregators [5], [6], [7]. This growth has been driven by three converging trends: the near-universal availability of smartphone cameras (enabling receipt scanning), the maturity of bank-data aggregation APIs (enabling automatic transaction import), and advances in machine learning that allow software to categorize and interpret spending with far less manual configuration than earlier rule-based systems required.

## Purpose of the Research

This report has two purposes. First, it investigates how modern expense-tracking applications work: the data-collection methods they use, the features they commonly offer, the role artificial intelligence plays in categorization, and the behavioral research describing how people actually use these tools day to day. Second, it applies these findings to the design of **ExpenseIQ**, a portfolio-scale expense tracking application, and documents the reasoning behind each significant design decision — from the choice of automatic AI-driven categorization, to the specific backend and client frameworks selected for implementation.

---

# Problem Statement

Despite the proliferation of expense-tracking software, several persistent problems motivate continued research and development in this space.

**Poor financial awareness.** Many individuals cannot accurately estimate their own monthly spending by category. Because digital payments abstract away the physical sensation of spending cash, small discretionary purchases accumulate largely unnoticed until they surface as a surprising total at the end of the month.

**Difficulty remembering purchases.** Manually reconstructing a month's transactions from memory is unreliable. Even highly motivated users struggle to recall incidental purchases (a coffee, a parking fee, a subscription renewal) unless they are captured at, or very close to, the point of purchase.

**Overspending.** Research on financial self-regulation shows that discretionary spending — the most flexible and therefore most overspent category of a household budget — responds measurably to the simple act of tracking, independent of whether an explicit budget limit is imposed [18], [19]. This suggests overspending is, in part, an attention problem rather than purely a willpower problem, which has direct implications for how a tracking application should be designed.

**Lack of spending insight.** Even where transaction records exist (bank statements, card statements), they are typically presented in a raw, uncategorized, chronological format that is not conducive to identifying patterns. Turning a list of transactions into an insight — "you are spending 30% more on dining out than three months ago" — requires categorization, aggregation, and visualization that raw statements do not provide.

**Complex budgeting tools.** Several existing tools address the problems above but introduce a new one: complexity. Zero-based budgeting systems, in particular, require significant upfront configuration and ongoing manual categorization discipline, which is one of the most commonly cited reasons users abandon budgeting software before habits form [7], [8].

---

# Research Objectives

This research is guided by the following objectives:

1. **Understand how modern expense trackers work** — specifically, the technical mechanisms by which they acquire, process, and present transaction data.
2. **Investigate common features** across leading expense-tracking and budgeting applications, distinguishing features that are near-universal from those that differentiate specific products.
3. **Evaluate AI-based categorization** as an alternative to manual or purely rule-based categorization, including its accuracy, cost, and failure modes.
4. **Research OCR technologies** as applied specifically to receipts, including common sources of extraction error and how applications validate and correct extracted data.
5. **Design a realistic portfolio application** — ExpenseIQ — that applies the findings above within a scope that is achievable as an individually built software project, while still demonstrating professional architecture and technology choices.

---

# Literature Review

To ground the design of ExpenseIQ in the current state of the market, six widely used expense-tracking and budgeting applications were reviewed: **YNAB**, **Monarch Money**, **Expensify**, **PocketGuard**, **Spendee**, and **Empower**. Rather than treating these as an undifferentiated feature checklist, each is considered on its own terms, including where it falls short.

**YNAB (You Need A Budget)** implements zero-based budgeting, in which every unit of income is assigned a job before it is spent. Review platforms consistently rate it highly for goal-setting and intentional spending, and it is frequently described as the strongest option for individuals actively trying to change their spending behavior rather than merely observe it [5], [7]. Its principal weakness is the same as its strength: zero-based budgeting demands sustained manual engagement, which is a poor fit for users who want an automated, low-effort tool. YNAB also carries a recurring subscription cost with a comparatively short free trial relative to some competitors.

**Monarch Money** takes the opposite philosophy: broad account aggregation (banking, credit cards, loans, investments, and real estate) presented through a single, customizable dashboard, with support for flexible or category-based budgeting rather than a single rigid method [9]. It is frequently cited as the strongest option for couples or shared households due to its native multi-user design [7], [8]. Its main criticism in comparative reviews is that its flexible, "adjust as you go" philosophy can undermine accountability — transactions can be hidden or reclassified in ways that reduce the discipline enforced by stricter systems [7].

**Expensify** is oriented toward business and reimbursement workflows rather than personal budgeting, but its receipt-processing technology is directly relevant to ExpenseIQ's design. Its SmartScan feature uses OCR to extract merchant, date, amount, and currency from a photographed receipt in seconds, and independent analysis of large review samples places its extraction accuracy at approximately 99% [13], [10], [11]. Expensify's weakness for a personal-use case is scope: its feature set (policy enforcement, multi-level approval workflows, corporate card reconciliation) is built for organizational expense reporting and is unnecessary overhead for an individual tracking personal spending [12].

**PocketGuard** differentiates itself with a single, prominent "safe-to-spend" figure: a daily number representing what a user can spend after bills, savings goals, and obligations are accounted for [7], [8]. This makes it unusually approachable for users who find category-based budgeting overwhelming. Its tradeoff is limited customization; users who want granular control over categories or budgeting methodology tend to find it restrictive [7].

**Spendee** is consistently rated as the strongest option for budgeting beginners, largely due to a visual, chart-driven interface that reveals spending patterns without requiring the user to commit to a specific budgeting method upfront [8], [9]. Because it does not enforce a methodology, it is well suited to the "understand before you commit" stage of financial habit-building, though this same openness means it offers less structure than YNAB or PocketGuard for users who already know they need behavioral guardrails.

**Empower** (formerly Personal Capital) is positioned less as a day-to-day expense tracker and more as a wealth-tracking dashboard, combining spending visibility with net worth, investment, and retirement account tracking [6]. It is a useful reference point for ExpenseIQ in one respect: it demonstrates that expense data becomes substantially more valuable when unified with a broader financial picture, even though ExpenseIQ's v1 scope deliberately excludes investment tracking.

**Table 1** summarizes the comparative positioning of the applications reviewed.

| Application | Primary Focus | Key Strength | Key Limitation |
|---|---|---|---|
| YNAB | Zero-based budgeting | Strong behavior change / intentionality | High manual effort; subscription cost |
| Monarch Money | Full account aggregation | Shared/couples use; flexible budgeting | Flexibility can reduce accountability |
| Expensify | Business expense reporting | Near-99% OCR accuracy (SmartScan) | Overbuilt for personal use |
| PocketGuard | Cash-flow snapshot | Single, simple "safe-to-spend" number | Limited customization |
| Spendee | Visual spending awareness | Beginner-friendly, low commitment | Little structural guidance |
| Empower | Net worth + spending | Unifies spending with broader wealth picture | Not optimized for daily expense logging |

---

# Expense Trackers vs. Budget Applications

Although marketed almost interchangeably, expense trackers and budget applications solve different problems, and conflating them is a common source of poor product-design decisions.

An **expense tracker** is fundamentally backward-looking. Its core question is "where did my money go?" It succeeds if it produces an accurate, well-categorized historical record of spending, typically visualized as totals and trends. A tracker does not need to know what a user *intends* to spend; it only needs to know what they *did* spend.

A **budget application** is forward-looking. Its core question is "where should my money go?" It requires the user (or the system) to define spending limits or allocations ahead of time, and its value comes from comparing actual spending against that plan, flagging deviations as they occur.

In practice, the distinction matters for three reasons:

1. **Onboarding cost.** A tracker can be useful from the very first logged transaction. A budget app requires upfront configuration — defining categories, setting limits, deciding an allocation method — before it produces any value, which raises the barrier to sustained use [7].
2. **Failure mode.** A tracker's worst failure is an inaccurate or incomplete historical record. A budget app's worst failure is a plan the user cannot realistically follow, which produces guilt and app abandonment rather than simply incomplete data [8].
3. **Behavioral mechanism.** Research on financial self-regulation found that persistent tracking reduces the share of discretionary spending, but does not reliably improve adherence to a fixed monthly budget [18], [19]. In other words, the tracking behavior and the budgeting behavior produce measurably different outcomes, and a product that blends them without distinguishing the two risks under-delivering on both.

ExpenseIQ is deliberately scoped as an **expense tracker first**, with budgeting features treated as a clearly separated future enhancement rather than a core v1 requirement. This mirrors the design logic of Spendee and PocketGuard more than YNAB.

---

# Data Collection Techniques

Three data-collection methods dominate the expense-tracking category. Each carries distinct advantages, disadvantages, privacy implications, and implementation complexity.

## Manual Entry

Manual entry requires the user to type transaction details — amount, merchant, category, date — directly into the application. It is the oldest and conceptually simplest method, requiring no external integration.

*Advantages*: zero infrastructure dependency, complete user control, no data-sharing with third parties, and a documented behavioral benefit — the act of manually recording a purchase appears to itself reduce discretionary spending, independent of any categorization or reporting feature built on top of it [18], [19].

*Disadvantages*: the highest ongoing effort of the three methods, and the one most prone to abandonment. Users who fall behind on entry rarely go back and reconstruct missed transactions accurately.

*Privacy implications*: minimal — no external party needs access to financial accounts.

*Implementation complexity*: low. A single CRUD (create-read-update-delete) API surface over a transactions table is sufficient.

## Receipt OCR (Optical Character Recognition)

OCR-based entry allows a user to photograph a paper or digital receipt; software then extracts structured fields (merchant, date, amount, sometimes line items) automatically.

*Advantages*: substantially lower manual effort than typing every field, and higher data accuracy than manual transcription for well-captured receipts, since OCR removes the human copying step where transposed digits and misread decimals commonly occur [15]. Well-implemented OCR systems can reach effective accuracy above 99% for standard retail receipts once confidence-based review is applied to uncertain extractions [13].

*Disadvantages*: image quality dependency. Faded thermal paper, creased receipts, and poor lighting are well-documented causes of extraction error, and even mature OCR pipelines fall back to requesting user confirmation when confidence is low [14], [15], [17].

*Privacy implications*: moderate — the receipt image itself may need to be transmitted to a cloud OCR provider, though this is generally a lower privacy exposure than full bank-account access.

*Implementation complexity*: moderate. Requires integration with an OCR/document-intelligence provider and a validation step to reconcile machine-extracted fields with what the user can confirm.

## Bank Synchronization

Bank sync uses a financial data aggregator (most commonly Plaid, alongside providers such as MX, Yodlee, Tink, and TrueLayer) to connect directly to a user's bank account and automatically retrieve transaction history.

*Advantages*: by far the lowest ongoing user effort — once linked, transactions populate automatically, typically refreshed nightly and often including pre-applied merchant categorization and enrichment [1], [4]. This is why bank sync underlies most of the highest-rated, fully-automated budgeting apps such as Monarch Money and PocketGuard [6], [7].

*Disadvantages*: it introduces a dependency on a third-party aggregator's reliability and coverage, and it is the method most exposed to user distrust, since it requires authenticating with, or granting token-based access to, an external party rather than the bank directly [3].

*Privacy implications*: the highest of the three methods. Although modern aggregators use OAuth-based authentication and never expose raw banking credentials to the connecting application, the aggregator itself gains visibility into the full transaction history of every linked account, and that data now exists with a second custodian beyond the bank itself [2], [3].

*Implementation complexity*: high. Requires a signed partnership or developer agreement with an aggregation provider, secure token storage, webhook handling for real-time updates, and compliance considerations around holding financial data on behalf of users [3], [4].

**Table 2** compares the three methods directly.

| Method | User Effort | Data Accuracy | Privacy Exposure | Implementation Complexity |
|---|---|---|---|---|
| Manual Entry | High | Depends on user diligence | Lowest | Low |
| Receipt OCR | Low–Medium | High (with confidence-based validation) | Moderate | Moderate |
| Bank Sync (Aggregator) | Lowest | High (bank-sourced) | Highest | High |

For ExpenseIQ's initial scope, **manual entry and receipt OCR** were selected, deliberately deferring bank synchronization. This decision is expanded upon in the Application Design Decisions section.

---

# Artificial Intelligence in Expense Tracking

## Merchant Categorization

Assigning a category (groceries, dining, transport, etc.) to a transaction is the single feature that most determines whether an expense tracker feels intelligent or feels like a glorified spreadsheet. Three broad approaches exist.

**Rule-based systems** match transaction descriptions against a fixed set of keyword patterns (e.g., any merchant string containing "uber" or "lyft" maps to Transport). These are cheap, fast, and fully deterministic, but brittle: they fail silently on any merchant name not anticipated in advance, and they require ongoing manual maintenance as new merchants appear.

**Machine learning classifiers** trained on historical transaction-category pairs can generalize beyond an explicit keyword list, learning, for instance, that merchant names containing certain patterns common to grocery chains predict a "Groceries" label even for previously unseen merchants. This requires either a large labeled training dataset or a pretrained model, and comes with the general accuracy/interpretability tradeoffs of any ML classifier.

**Large Language Models (LLMs)** can perform this same classification task with no task-specific training data at all, by being given the target categories directly in the prompt and asked to select the best match for a given merchant string and amount. This makes LLM-based categorization particularly attractive for a smaller-scale application that does not have millions of historical transactions to train a bespoke classifier on.

## Cost Considerations

Calling a hosted LLM for every single transaction is both slower and more expensive than necessary, especially at scale, since the same merchant (e.g., a specific coffee shop) recurs constantly across a user's transaction history. The pattern adopted for ExpenseIQ — and consistent with how automated categorization is generally described in the aggregator literature, where enrichment is applied once and then reused across future transactions from the same merchant [1], [4] — is a cascading design:

1. Check a merchant-to-category lookup table first (fast, free, deterministic).
2. Only if no match exists, call an LLM to classify the transaction.
3. Persist the LLM's answer back into the lookup table so the same merchant is resolved instantly next time.

This means the proportion of transactions requiring an actual model call shrinks continuously as the lookup table matures, converging the system's real-world cost toward the low end of the range regardless of transaction volume.

## Accuracy and User Corrections

No categorization system — rule-based, ML, or LLM — will be perfectly accurate on every transaction, particularly for ambiguous merchants (a big-box retailer that sells both groceries and electronics, for example). The categorization research reviewed consistently emphasizes that automated categorization should be paired with an easy correction mechanism, since AI-assisted receipt tools already surface corrections as a first-class workflow step rather than an edge case [14], [17]. ExpenseIQ's design reflects this directly: every automatically assigned category remains user-editable after the fact, and any correction the user makes overwrites the stored merchant mapping — meaning the system's accuracy strictly improves with real usage rather than staying fixed at deployment-time performance.

**Figure 1** illustrates the resulting pipeline.

```{=latex}
\begin{figure}[H]
\centering
\begin{lstlisting}[basicstyle=\ttfamily\small, frame=none, backgroundcolor=\color{white}]
 Merchant string in (from OCR or manual entry)
             |
             v
   Normalize (lowercase, strip trailing
        store IDs / numbers)
             |
             v
   Look up in MerchantCategoryMap  --match found--> return category
             |                                       (fast, no cost)
          no match
             |
             v
   Call LLM with a constrained prompt,
   forcing one of a fixed category
   enum as JSON output
             |
             v
   Persist merchant -> category
   mapping for future lookups
             |
             v
   Return category to caller;
   user may override, which updates
   the stored mapping
\end{lstlisting}
\caption{Hybrid rule-based / LLM merchant categorization pipeline}
\end{figure}
```

---

# OCR Technology

## What OCR Is

Optical Character Recognition (OCR) is a decades-old technology that converts the visual representation of text — in a scanned document, a PDF, or a photograph — into machine-readable, editable text [20]. Applied to receipts specifically, OCR must do more than transcribe raw characters: it must identify which characters correspond to which semantic field (merchant name, date, subtotal, tax, total) within a document whose layout varies by retailer and point-of-sale system [21].

## How Receipts Are Processed

Modern receipt OCR pipelines generally follow a consistent sequence [16], [21]:

1. **Capture** — the receipt enters the system, most commonly via a mobile photo taken at the point of purchase, though digital receipts (email confirmations, order receipts) can also be parsed directly from their source format.
2. **Preprocessing** — the image is enhanced (deskewed, contrast-adjusted, cropped) to compensate for creases, folds, or poor lighting before character recognition is attempted [17].
3. **Character recognition** — the OCR engine identifies individual characters and assembles them into words and lines.
4. **Field extraction** — the recognized text is mapped onto semantic fields (merchant, date, line items, subtotal, tax, total) using either template matching for known formats or a general-purpose model for unfamiliar layouts [21].
5. **Postprocessing / validation** — extracted values are checked for internal consistency (do the line items sum to the subtotal? does the date fall within a plausible range?), and low-confidence fields are flagged rather than silently accepted [21].
6. **User confirmation** — the extracted, pre-filled data is presented back to the user for a final confirmation or correction before being saved as a transaction.

## Common OCR Errors

The literature on receipt OCR consistently identifies image quality as the dominant source of error: faded thermal-paper receipts, creased or crumpled paper, and low-contrast photography are the most frequently cited causes of misread characters [14], [15], [17]. Beyond image quality, ambiguous formatting — receipts that omit a clearly labeled "total" line, or that use unfamiliar abbreviations for line items — causes field-extraction errors even when character recognition itself is accurate.

## Data Validation and User Confirmation

Because no OCR system is completely error-free, responsible implementations do not treat extracted data as final. Instead, extracted fields are used to **pre-fill** a transaction entry form that the user reviews before saving — the same pattern used in Expensify's SmartScan workflow, where a virtual assistant flags unclear or missing fields and may prompt the user with a follow-up question rather than guessing silently [10]. ExpenseIQ adopts this same principle: OCR output is a high-confidence *suggestion*, never a silent, unreviewable write to the transaction record.

---

# User Behaviour Research

Understanding how people actually use expense-tracking software day to day is arguably more important to good product design than any individual feature, since a feature nobody sustains using has no value regardless of its sophistication.

## Daily Tracking Habits and Weekly Reviews

Expense-tracking behavior in practice tends to combine a lightweight, frequent touchpoint (glancing at recent transactions, confirming a receipt scan) with a less frequent, more deliberate review session in which the user actually examines category totals and trends. This two-tier rhythm — brief and frequent for capture, longer and periodic for reflection — reduces the total time cost of tracking while still surfacing patterns regularly enough to inform decisions.

## Habit Formation and Financial Awareness

The self-regulation literature distinguishes **automated tracking** (data collected passively through bank accounts or financial tools) from **active tracking** (data entered manually by the user), and finds that automated tracking, while convenient, is associated with lower attention and reduced financial self-awareness compared to active tracking, which requires more engagement from the user [19]. This is a genuinely important, somewhat counterintuitive finding: the *more automatic* a tracking method is, the *less* it appears to improve the user's underlying financial awareness, even though it is the method most likely to produce a complete and accurate transaction record.

This creates a real design tension. Fully automatic categorization (as ExpenseIQ implements) maximizes completeness and minimizes user effort, but a design that removes all touchpoints with the user risks also removing the awareness benefit that active engagement provides. ExpenseIQ's approach to resolving this tension — surfacing the AI-assigned category prominently and inviting correction rather than hiding the categorization step entirely — is intended to preserve a degree of active engagement even within an automated workflow.

## Psychological Effects

Persistent tracking is associated with a measurable reduction in the share of discretionary spending, even though it does not reliably improve adherence to a fixed monthly budget figure [18], [19]. This suggests the psychological mechanism at work is closer to *attention* than to *restriction*: simply seeing spending clearly appears to change behavior, independent of whether a hard limit is being enforced. This is consistent with survey-based findings in the same research showing tracking is linked to an increase in budget slack and to users making budget adjustments in response to what tracking reveals, rather than tracking functioning as a rigid enforcement mechanism [19].

## Reasons Users Abandon Tracking

The most commonly cited reason budgeting and tracking apps are abandoned is not a missing feature but excessive friction relative to the value delivered day to day — rigid, manual systems that demand constant categorization discipline are harder to sustain than apps that automate the bulk of the work while still giving the user visibility and control [7], [8]. This directly informs ExpenseIQ's categorization strategy: automation is used to minimize the ongoing effort required to keep the tracker current, while user-editable categories preserve the sense of control that purely automated systems can otherwise remove.

---

# Privacy and Security

## Bank Aggregators and the Plaid Architecture

Financial account aggregation works by inserting a trusted intermediary between a consumer-facing application and a financial institution. Rather than an application storing a user's actual bank credentials, the user authenticates through the aggregator (increasingly via OAuth 2.0 for institutions that support it, with legacy credential-based methods still used for banks that do not) [2], [3]. Once authenticated, the aggregator issues the connecting application an access token scoped to specific data (balances, transaction history, identity information), which the application uses to request data without ever handling the user's actual banking credentials directly [3].

This architecture has a specific privacy consequence worth stating plainly: the aggregator itself becomes a second custodian of the user's full transaction history, in addition to the bank. Switching between two applications that both rely on the same aggregator changes who is displaying the data, but does not change who technically has access to the underlying raw feed [2]. This is precisely the concern that has driven a documented, non-trivial segment of privacy-conscious users toward manual-entry or local-first tracking tools instead of aggregator-based sync.

## Data Ownership

A recurring theme across financial-technology privacy literature is that "data ownership" in aggregator-based systems is layered rather than singular: the bank holds the authoritative account of record, the aggregator holds a synchronized copy sufficient to serve enrichment and normalization, and the end-user application typically holds a further copy or cache for its own display purposes [4]. Each layer represents a separate point of potential exposure in the event of a breach, which is why data-minimization (storing only what is functionally necessary, for only as long as necessary) is a standard recommendation in this space rather than an optional hardening measure.

## GDPR and POPIA Considerations

Because ExpenseIQ is designed and documented from South Africa, the **Protection of Personal Information Act (POPIA)** is the directly applicable data-protection law, while the EU's **General Data Protection Regulation (GDPR)** remains the internationally recognized benchmark that POPIA was substantially modeled on [22], [23]. Both frameworks share a common accountability-based structure — lawful basis for processing, a named responsible party (an "Information Officer" under POPIA, a "Data Protection Officer" under GDPR for qualifying organizations), and data-subject rights including access, correction, and deletion [23], [24]. They differ meaningfully in a few respects relevant to a financial application: POPIA extends certain protections to juristic persons (companies) in addition to natural persons, where GDPR protects only natural persons; POPIA does not include an explicit data-portability right in the way GDPR does; and the maximum administrative penalties differ substantially — up to roughly R10 million under POPIA versus up to €20 million or 4% of global annual turnover under GDPR [22], [24].

**Table 3** summarizes the comparison.

| Aspect | GDPR (EU) | POPIA (South Africa) |
|---|---|---|
| Scope | Natural persons only | Natural and juristic persons |
| Data portability | Explicit right | Not explicitly provided |
| Compliance officer | DPO, required for certain organizations | Information Officer, required by default |
| Maximum penalty | Up to €20M or 4% of global turnover | Up to R10 million |
| Underlying principles | Lawfulness, purpose limitation, minimization, accuracy, storage limitation, integrity/confidentiality, accountability | Accountability, processing limitation, purpose specification, further processing limitation, information quality, openness, security safeguards, data subject participation |

For ExpenseIQ, the practical implication is that even a portfolio-scale project handling financial transaction data should follow data-minimization and purpose-limitation principles as a matter of good practice, independent of whether the project is formally within POPIA's enforcement scope at its current, non-commercial stage.

## JWT Authentication

Given that ExpenseIQ's backend exposes a stateless HTTP API, authentication is implemented using **JSON Web Tokens (JWT)**. On successful login, the API issues a signed token containing the user's identity claim; this token is attached to the `Authorization` header of subsequent requests, and the API validates its signature and expiry on every call rather than maintaining server-side session state. This keeps the `ExpensesController` fully stateless, which simplifies horizontal scaling and keeps the layered architecture described later in this report consistent, since no session store needs to sit alongside `ApplicationDbContext`.

## Encryption

Two encryption boundaries are relevant to ExpenseIQ. **In transit**, all client-to-API traffic is served over HTTPS/TLS, ensuring transaction data and authentication tokens cannot be intercepted on the network. **At rest**, PostgreSQL's built-in encryption-at-rest capabilities (or disk-level encryption provided by the hosting platform) protect stored transaction data, while receipt images — the most sensitive artifact in the system, since a photographed receipt can incidentally capture card numbers or other identifying information — are stored in access-controlled object storage rather than directly in the application database.

---

# Application Design Decisions

Every architectural and technology choice made for ExpenseIQ is a direct consequence of the research findings above. This section documents and justifies each one.

## Manual Entry

Manual entry is retained as a fallback input method even though it is not the primary categorization path, because the behavioral research is explicit that active, manually-driven entry carries an awareness benefit that fully automated collection does not replicate [19]. Supporting manual entry also means ExpenseIQ has no hard dependency on OCR succeeding for every transaction — a receipt that fails to scan cleanly can always be entered directly.

## OCR (Receipt Scanning)

OCR was chosen over bank synchronization as ExpenseIQ's primary automated capture method for v1. This reflects the data-collection comparison in Table 2: OCR delivers a large reduction in manual effort relative to typing every transaction, without introducing the aggregator-level privacy exposure or implementation complexity that bank sync requires [1], [3]. It is also the mechanism most directly demonstrable in a portfolio context, since it involves integrating a genuine third-party AI service rather than relying on stubbed or mocked data.

## AI Categorization

Fully automatic categorization was chosen over a purely user-driven dropdown, based on the finding that low-friction, mostly-automated systems are more sustainable in daily use than systems that demand constant manual categorization discipline [7], [8]. Choosing an LLM-based fallback over training a bespoke ML classifier was a pragmatic decision appropriate to project scale: ExpenseIQ does not have — and, as a new application, cannot yet have — the large labeled transaction history a custom classifier would need to train effectively, whereas an LLM can perform the same classification task immediately, with no training data of its own.

## Merchant Normalization and Cached Mappings

Normalizing merchant strings (lowercasing, stripping trailing store or register numbers) before lookup, and caching every resolved merchant-to-category mapping, directly addresses the cost concern raised in the AI section above. It also produces a system whose behavior improves over time purely as a byproduct of usage, without requiring any separate retraining or maintenance step — an important property for a project that will not have a dedicated operations team maintaining it after launch.

## ASP.NET Core (Backend Framework)

ASP.NET Core was selected for the backend API for three reasons: first, its layered dependency-injection model maps directly onto the Controller → Service → Repository separation already adopted in ExpenseIQ's architecture; second, its first-class Entity Framework Core integration provides a mature, well-documented object-relational mapper for PostgreSQL; and third, it is a widely recognized, professionally relevant framework choice for a portfolio project intended to demonstrate production-realistic backend engineering.

## Flutter, SwiftUI, Tauri, and Next.js (Client Options)

ExpenseIQ's backend is intentionally client-agnostic — a REST API with no server-rendered views — which allows the client layer to be selected independently based on the specific portfolio goal being pursued:

- **Flutter** is the recommended default where a single codebase needs to target both iOS and Android with genuinely native-feeling performance, and where the developer wants to demonstrate cross-platform mobile competency.
- **SwiftUI** is the appropriate choice specifically when the goal is to demonstrate native iOS development skill, since it produces a more idiomatic, platform-integrated experience than a cross-platform framework can on iOS alone, at the cost of an Android build entirely.
- **Tauri** is a lightweight option for a desktop client, bundling a web-technology frontend into a small native desktop binary — useful if a desktop presence is desired without the resource overhead of a framework like Electron.
- **Next.js** is the recommended choice for a web client, given its strong support for server-side rendering, API routing, and a mature deployment ecosystem, making it a reasonable pick if the primary demonstration surface is a browser-accessible dashboard rather than a mobile app.

Because all four communicate with the same ASP.NET Core API over HTTP, the choice among them does not affect the backend design at all — it only affects which portfolio narrative (mobile cross-platform, native iOS, lightweight desktop, or web dashboard) the finished project tells.

---

# Proposed System

## Goals

ExpenseIQ's v1 goal is to provide an individual user with a low-friction way to capture, categorize, and understand their personal spending, without requiring the configuration overhead of a full budgeting system. Concretely:

- Reduce the effort required to log a transaction to "take a photo" or, at most, a short manual form.
- Categorize every transaction automatically, while remaining fully user-correctable.
- Present a clear, visual monthly and category-level summary of spending.

## Features (v1 Scope)

- Manual expense entry (amount, merchant, date, note)
- Receipt photo capture with OCR-based auto-fill
- Fully automatic AI-assisted categorization via the merchant-mapping and LLM-fallback pipeline described above
- Monthly total and month-over-month comparison
- Category breakdown visualization (bar/pie chart)
- A fixed set of six categories for v1: Groceries, Dining, Transport, Bills, Shopping, Other

Explicitly deferred to future versions: bank synchronization, budgets and spending alerts, multi-currency support, and shared/multi-user access — each excluded for the reasons discussed in the Data Collection Techniques and Application Design Decisions sections.

## Architecture Overview

ExpenseIQ follows a conventional layered backend architecture, chosen specifically because each layer maps to a single, testable responsibility.

```{=latex}
\begin{figure}[H]
\centering
\begin{lstlisting}[basicstyle=\ttfamily\small, frame=none, backgroundcolor=\color{white}]
     Client (Flutter / SwiftUI / Tauri / Next.js)
                    |
                    v
             ExpensesController      (HTTP endpoints, auth via JWT)
                    |
                    v
              ExpenseService         (validation, OCR + AI orchestration)
                    |
                    v
             ExpenseRepository       (Entity Framework Core)
                    |
                    v
           ApplicationDbContext
                    |
                    v
              PostgreSQL Database
\end{lstlisting}
\caption{ExpenseIQ layered backend architecture}
\end{figure}
```

`ExpenseService` is the only layer aware of the OCR provider and the LLM-based categorization fallback; `ExpenseRepository` remains a thin data-access layer with no business logic, and `ExpensesController` is limited to request/response handling and authentication.

## High-Level Workflow

A single end-to-end transaction, from receipt photo to a saved, categorized record, proceeds as follows:

```{=latex}
\begin{figure}[H]
\centering
\begin{lstlisting}[basicstyle=\ttfamily\small, frame=none, backgroundcolor=\color{white}]
  1. User photographs a receipt (or enters a transaction manually)
             |
             v
  2. Client uploads image to ExpensesController
             |
             v
  3. ExpenseService sends the image to the OCR provider
             |
             v
  4. OCR returns merchant, date, amount (with confidence scores)
             |
             v
  5. ExpenseService runs the categorization pipeline
     (merchant lookup -> LLM fallback if unresolved)
             |
             v
  6. Pre-filled transaction is returned to the client for confirmation
             |
             v
  7. User confirms (or edits) -> ExpenseRepository saves via EF Core
             |
             v
  8. Dashboard updates: monthly total, category breakdown
\end{lstlisting}
\caption{ExpenseIQ end-to-end transaction workflow}
\end{figure}
```

---

# Conclusion

## Summary of Research Findings

This research examined how contemporary expense-tracking applications acquire, process, and present financial data, and how people actually use these tools once installed. Three data-collection methods — manual entry, receipt OCR, and bank synchronization — were evaluated on effort, accuracy, privacy exposure, and implementation complexity, with each shown to occupy a distinct point on the effort-versus-privacy tradeoff curve. Categorization was found to be the feature most responsible for an application feeling intelligent, with a cascading rule-lookup-then-LLM-fallback design offering a practical middle ground between brittle keyword rules and the cost of training a bespoke classifier. Behavioral research surfaced a genuinely important tension: fully automated tracking maximizes completeness but appears to reduce the financial self-awareness benefit that more active, manually-engaged tracking provides [19] — a finding with direct design implications rather than merely academic interest.

## Lessons Learned

The clearest lesson from this research is that expense-tracking software succeeds or fails less on any single feature than on whether its ongoing effort-to-value ratio survives contact with a busy week. Every design decision made for ExpenseIQ — automatic categorization, OCR-first capture, deferred bank sync, a deliberately small v1 feature set — was made in service of keeping that ratio favorable, rather than in service of maximizing feature count.

## Why ExpenseIQ Addresses the Identified Problems

ExpenseIQ's design directly answers each problem identified in the Problem Statement: poor financial awareness and lack of insight are addressed through automatic categorization paired with clear monthly and category-level visualizations; difficulty remembering purchases is addressed through OCR-based capture at or near the point of purchase; overspending is addressed indirectly, consistent with the research finding that visibility itself changes discretionary spending even without an explicit budget cap [18], [19]; and the complexity that causes abandonment of full budgeting tools is avoided by scoping ExpenseIQ as a tracker first, deferring budgeting features to a clearly separated future phase.

## Future Enhancements

Beyond v1, the most natural extensions are, in rough order of priority: bank synchronization via an aggregator such as Plaid, once the categorization pipeline has matured on manually- and OCR-captured data; budget limits and threshold alerts, built as a distinct layer on top of the existing category model rather than woven into it; custom, user-defined categories, replacing the fixed six-category enum; multi-currency normalization; and shared, multi-user household access, following the pattern established by Monarch Money and Spendee in the literature review.

---

# References

[1] Plaid, "How financial account aggregators fuel fintech solutions," *Plaid Resources*, 2025. [Online]. Available: https://plaid.com/resources/fintech/financial-account-aggregator/

[2] Plaid, "Aggregation – Plaid Exchange Reference," *Plaid Docs*, n.d. [Online]. Available: https://plaid.com/plaid-exchange/docs/reference/aggregation/

[3] Itexus, "Plaid API integration: How fintech apps connect bank accounts," *Itexus Blog*, 2025. [Online]. Available: https://itexus.com/how-to-use-plaid-api-integration-a-comprehensive-guide-for-fintech-startups/

[4] OpenBankingTracker, "Banking data aggregation APIs (2026): Compare Plaid, Tink, TrueLayer, MX, Salt Edge, Finicity, Yodlee," 2026. [Online]. Available: https://www.openbankingtracker.com/banking-data-aggregation

[5] Forbes Advisor, "Best budgeting apps of 2026," *Forbes*, 2026. [Online]. Available: https://www.forbes.com/financial-services/best-budgeting-apps-2/

[6] NerdWallet, "The best budget apps for 2026," *NerdWallet*, 2026. [Online]. Available: https://www.nerdwallet.com/blog/finance/budgeting-saving-tools/

[7] Ramsey Solutions, "Budgeting apps comparison 2026," 2026. [Online]. Available: https://www.ramseysolutions.com/budgeting/budgeting-apps-comparison

[8] The Penny Hoarder, "The best budgeting apps of 2026: Free and paid options compared," 2026. [Online]. Available: https://www.thepennyhoarder.com/budgeting/best-budgeting-apps/

[9] SpotSaaS, "Spendee vs Monarch Money vs PocketGuard: Pricing, features and best fit," 2026. [Online]. Available: https://www.spotsaas.com/compare/spendee-vs-monarch-money-vs-pocketguard

[10] Expensify, "Track expenses in a snap with Expensify's receipt scanning app," *use.expensify.com*, 2026. [Online]. Available: https://use.expensify.com/receipt-scanning-app

[11] TechRepublic, "Expensify review: Smart expense tracking for teams," 2026. [Online]. Available: https://www.techrepublic.com/article/expensify-review/

[12] Capterra, "Expensify software pricing, alternatives & more 2026," 2026. [Online]. Available: https://www.capterra.com/p/97594/Expensify/

[13] SaaSPricePulse, "Expensify true cost 2026: SmartScan limits & fees," 2026. [Online]. Available: https://www.saaspricepulse.com/tools/expensify

[14] Melasoft, "What is OCR for receipts and how does it work?" 2026. [Online]. Available: https://www.melasoft.com/post/what-is-ocr-for-receipts-and-how-does-it-work

[15] DocuClipper, "What is receipt OCR and why is it important?" 2025. [Online]. Available: https://www.docuclipper.com/blog/receipt-ocr/

[16] Lido, "Using OCR for receipt recognition: Complete guide for 2026," 2026. [Online]. Available: https://www.lido.app/blog/ocr-for-receipt-recognition

[17] Alaan, "Understanding how receipt scanning works: Methods and efficiency," 2025. [Online]. Available: https://www.alaan.com/blog/receipt-scanning-methods-efficiency

[18] Y. Zhang, "How does expense-tracking inform financial behaviors?" *Consumer Interests Annual*, 2023. [Online]. Available: https://www.consumerinterests.org/assets/docs/CIA/CIA2023/ZhangYilingCIA2023.pdf

[19] Y. Zhang, "The role of expense-tracking as a financial self-regulatory behavior," Working Paper, University of Wisconsin–Madison, 2023. [Online]. Available: https://asset.library.wisc.edu/1711.dl/63XZYPDLKMY638S/R/file-908a3.pdf

[20] Docsumo, "Guide to using OCR for receipt recognition & data extraction," 2025. [Online]. Available: https://www.docsumo.com/blogs/ocr/for-receipts

[21] Lido, "Using OCR for receipt recognition: Complete guide for 2026," 2026. [Online]. Available: https://www.lido.app/blog/ocr-for-receipt-recognition

[22] Securiti, "GDPR vs. POPIA: Comparing South African version," 2021. [Online]. Available: https://securiti.ai/gdpr-vs-popia/

[23] Michalsons, "GDPR vs POPIA | Compare the GDPR with the POPI Act?" 2025. [Online]. Available: https://www.michalsons.com/blog/gdpr-mean-popi-act/19959

[24] BigID, "POPIA vs. GDPR: Key differences explained," 2026. [Online]. Available: https://bigid.com/blog/popia-vs-gdpr/