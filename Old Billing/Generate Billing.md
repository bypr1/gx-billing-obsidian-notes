
## Flow

1. **Get service location info by UUID**
    - Validate service location exists
2. **Check if latest billing statement is awaiting settlement** (must not be)
3. **Get product variants for this service location**
    - Separate internet subscription from add-ons
4. **Get latest billing statement** (ordered by ID DESC)
5. **Build billing statement options**
    - Set service location, product variants, latest billing statement
    - Set override options from form (month, year, follow schedule, etc.)
    - Set creator info
6. **Parse optional dates from form**
    - FromDate: billing period start
    - ToDate: billing period end
    - ProductBillDate: date for product bill generation
    - BillingDate: override billing date
    - DueDate: payment due date
        - if provided, also calculate grace date (due date + 1 day) and expiration date (first day of next month)
7. **Check generation lock**
    - Verify no concurrent billing generation is happening (unless Force flag is set)
    - Lock acquired, defer unlock
8. **Begin database transactions** (CRM and CST databases)
    1. **Generate billing statement** via interactor [[Billing Statement#Normal Billing Statement]]
    2. **If not committing** (preview mode):
        - Generate temporary PDF
        - Return error to rollback transaction
    3. **If committing**:
        1. Get all invoices for this billing statement
        2. Collect invoice UUIDs
        3. Get all payments for these invoices
        4. Collect payment UUIDs
        5. **Update CST service location balance**
            - Send billing statement and payment data
            - Get updated balance info
        6. **Sync CRM service location** with CST balance data
        7. Attach service location to billing statement
9. **If commit and should create PDF/ZIP files**:
    1. **Push async workflow** for file generation:
        1. Generate invoice PDFs
        2. Generate receipt PDFs
        3. Generate billing statement PDF
        4. Generate billing statement ZIP
    2. **If not preventing email**:
        1. Send notification (billing statement ready)
        2. Mark email as sent
10. **Return billing statement and payments**

## 