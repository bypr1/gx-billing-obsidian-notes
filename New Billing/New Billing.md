## Requirement
- Service Location
- Product Variants
- Credit Balance
- Latest Billing Statement
- From Date
- To Date
- Product Bill Date? [[Q&A#Q#BS]]
- Creator Info
- Date (Opt)
- Due Date (Opt)
- 
## Flow

1. **Validate requirements**
2. **Calculate and set dates**
    - Use provided dates or calculate based on latest billing statement
	1. **Set billing date** (current date or override)
	    - Validate it's not before latest billing statement date
	2. **Set due date** (calculate or use override)
	    - Also sets last reminder date (1 day before due date)
3. **Cancel failed Midtrans transaction bills??**
4. **Generate invoices for product variants** 
5. **Process latest billing statement** (if exists)
    - Get all invoices from latest statement
    - **Void unpaid invoices**:
        - Check if discount unused or invoice unsettled
        - Void each unpaid invoice
    - Get all assigned, non-canceled payments from latest invoices
6. **Convert payment balance to credit balance** (if payment balance > 0)
    - Move payment balance to credit balance
    - Reset payment balance to 0
7. **Get all unstated invoices** in the billing period
    - Exclude canceled invoices
8. **Calculate billing statement totals**
    - Calculate balance forwarded and remaining credit balance
9. **Filter invoices**
    - Exclude discount invoices that are already used
    - Build filtered invoice list
10. **Get forwarded invoices** (if balance forwarded > 0)
    - Find invoices with overpayments from previous periods
    - Include only invoices with positive balance (payment > amount)
11. **Calculate new charge amount**
    - Sum amounts from filtered invoices (excluding forwarded)
12. **Check if should prevent next bill date update**
    - If no package invoice created and next bill date is in future
13. **Create billing statement record**
    - Calculate total amount due (new charge + balance forwarded)
    - Calculate total with credit balance deduction
    - Save with all calculated amounts
14. **Assign billing statement to invoices and bills**
    - Link filtered invoices to billing statement
    - Mark forwarded invoices appropriately
    - Link bills to billing statement
15. **Save billing statement reminders**
    - Create reminder records based on billing cycle
16. **Save activity log** (billing statement created)
17. **Update service location payment balance**
    - Set payment balance to total amount due
18. **Update service location next bill date** (if not prevented)
    - Calculate next bill date from product variant
    - Update service location
19. **Process credit balance payment** (if enabled)
    - Auto-apply credit balance if sufficient to cover balance due
20. **Return updated service location, credit balance and billing statement**


