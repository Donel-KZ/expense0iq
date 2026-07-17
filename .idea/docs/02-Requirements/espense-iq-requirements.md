1. Functional Requirements (What the system must do)
   Authentication
   FR-001: The system shall allow users to register using an email address and password.
   FR-002: The system shall allow registered users to log in.
   FR-003: The system shall issue JWT access and refresh tokens after successful authentication.
   FR-004: The system shall allow users to reset forgotten passwords.
   FR-005: The system shall allow users to update their profile information.
   Expense Management

   FR-006: The system shall allow users to create a new expense.
   FR-007: The system shall allow users to edit an existing expense.
   FR-008: The system shall allow users to delete an expense.
   FR-009: The system shall allow users to view the details of an expense.
   FR-010: The system shall allow users to search expenses.
   FR-011: The system shall allow users to filter expenses by category, date, and amount.
   Receipt Scanning
   FR-012: The system shall allow users to upload a receipt image.
   FR-013: The system shall extract merchant name, amount, and transaction date from the receipt using OCR.
   FR-014: The system shall allow users to review and edit extracted information before saving.
   AI Categorization
   FR-015: The system shall normalize merchant names before categorization.
   FR-016: The system shall check for an existing merchant-category mapping.
   FR-017: If no mapping exists, the system shall evaluate predefined categorization rules.
   FR-018: If no rule matches, the system shall request a category suggestion from an AI provider.
   FR-019: The system shall store new merchant-category mappings for future use.
   FR-020: The system shall allow users to override the suggested category.
   FR-021: The system shall update the merchant-category mapping when a user changes the suggested category.
   Dashboard
   FR-022: The system shall display the user's total spending for the current month.
   FR-023: The system shall display recent transactions.
   FR-024: The system shall display spending grouped by category.
   FR-025: The system shall display spending trends over time.
   Analytics
   FR-026: The system shall generate spending summaries.
   FR-027: The system shall display category distribution charts.
   FR-028: The system shall display monthly spending trends.
   Settings
   FR-029: The system shall allow users to change their preferred currency.
   FR-030: The system shall allow users to enable or disable AI categorization.
   FR-031: The system shall allow users to switch between light and dark themes.

2. Non-Functional Requirements (How well the system should perform)
   Performance
   NFR-001: Dashboard data should load in under 2 seconds under normal conditions.
   NFR-002: Creating an expense should complete in under 1 second, excluding OCR or AI processing.
   NFR-003: Search results should be returned in under 1 second for typical user datasets.
   Security
   NFR-004: Passwords shall be securely hashed before storage.
   NFR-005: All authenticated endpoints shall require a valid JWT.
   NFR-006: Users shall only access their own data.
   Reliability
   NFR-007: The system shall recover gracefully from AI service failures by allowing manual category selection.
   NFR-008: OCR failures shall not prevent manual expense entry.
   Usability
   NFR-009: Adding an expense manually should require as few steps as practical.
   NFR-010: The interface shall provide clear validation messages for invalid input.
   Scalability
   NFR-011: The backend shall support multiple client applications (Flutter, SwiftUI, Tauri, and Next.js) through the same REST API.

3. Business Rules
   These are important because they're not just features—they're rules that govern system behavior.
   BR-001: Every expense belongs to exactly one category.
   BR-002: Every expense belongs to exactly one user.
   BR-003: AI suggestions are recommendations only; users make the final decision.
   BR-004: User-confirmed category corrections take precedence over AI suggestions.
   BR-005: Merchant names are normalized before category lookup.
   BR-006: Merchant-category mappings are reused for future transactions.
   BR-007: Receipt scanning is optional when creating an expense.

4. Assumptions and Constraints
   A-001: Internet access is required for OCR and AI categorization.
   A-002: OCR quality depends on the clarity of the uploaded receipt.
   A-003: The application is intended for personal expense tracking (one account per user).
   A-004: The backend is exposed as a REST API for all client platforms.
   A-005: Categories are predefined in v1 and cannot be customized by users.
   Why start here?
   These requirements become the foundation for every other design artifact:
   Requirements
   │
   ▼
   Use Cases
   │
   ▼
   Activity Diagrams
   │
   ▼
   Sequence Diagrams
   │
   ▼
   Class Diagram
   │
   ▼
   Database Design
   │
   ▼
   API Endpoints