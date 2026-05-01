## Normal Billing Statement
1. **Initialize all repositories with transaction**
    - Bill, Invoice, BillingStatement, BillingStatementReminder, Midtrans, Payment, ServiceLocation repos
2. **Validate service location status** (if not ignored)
    - Must be available to generate billing
3. **Validate product variants provided**
    - At least one product variant (internet or add-ons) must exist
4. **Validate schedule** (if FollowSchedule is true)
    - Must be valid date to generate statement now
5. **Set product bill date** (defaults to current time if not provided)
6. **Calculate and set billing period dates** (from/to dates)
    - Use provided dates or calculate based on latest billing statement
    - Store timestamps for queries
7. **Set billing date** (current date or override)
    - Validate it's not before latest billing statement date
8. **Set due date** (calculate or use override)
    - Also sets last reminder date (1 day before due date)
9. **Cancel failed Midtrans transaction bills**
10. **Generate product bills and invoices** (if CreateProductBillInvoice is true)
    - Get months to generate based on service location and product variant
    - **If totalMonths > 0**:
        - Determine year (increment if start month is in past)
        - **If single month**:
            - Check if temporary inactive → create inactive bill
            - Otherwise → create product bill
        - **If multiple months**:
            - Create multiple product bill (unless temporary inactive)
        - **If not free of charge and not temporary inactive**:
            - Create billing cycle discount bill (if discount > 0)
            - Create additional discount bill (if discount > 0)
        - Create add-on bills (if not temporary inactive)
        - Generate invoices for each bill type (product, inactive, add-on)
11. **Invoice any uninvoiced bills** in the billing period
    - Find bills not yet billed or invoiced
    - Generate "other bills" invoice
12. **Process latest billing statement** (if exists)
    - Get all invoices from latest statement
    - **Void unpaid invoices**:
        - Check if discount unused or invoice unsettled
        - Void each unpaid invoice
    - Get all assigned, non-canceled payments from latest invoices
13. **Convert payment balance to credit balance** (if payment balance > 0)
    - Move payment balance to credit balance
    - Reset payment balance to 0
14. **Get all unstated invoices** in the billing period
    - Exclude canceled invoices
15. **Calculate billing statement totals**
    - Process latest payments (lock and calculate payment since last bill)
    - Process invoices (calculate previous balance from settled invoices)
    - Process discount invoices from latest statement
    - Calculate balance forwarded and remaining credit balance
16. **Filter invoices**
    - Exclude discount invoices that are already used
    - Build filtered invoice list
17. **Get forwarded invoices** (if balance forwarded > 0)
    - Find invoices with overpayments from previous periods
    - Include only invoices with positive balance (payment > amount)
18. **Calculate new charge amount**
    - Sum amounts from filtered invoices (excluding forwarded)
19. **Check if should prevent next bill date update**
    - If no package invoice created and next bill date is in future
20. **Create billing statement record**
    - Calculate total amount due (new charge + balance forwarded)
    - Calculate total with credit balance deduction
    - Save with all calculated amounts
21. **Assign billing statement to invoices and bills**
    - Link filtered invoices to billing statement
    - Mark forwarded invoices appropriately
    - Link bills to billing statement
22. **Save billing statement reminders**
    - Create reminder records based on billing cycle
23. **Save activity log** (billing statement created)
24. **Update service location payment balance**
    - Set payment balance to total amount due
25. **Update service location next bill date** (if not prevented)
    - Calculate next bill date from product variant
    - Update service location
26. **Process credit balance payment** (if enabled)
    - Auto-apply credit balance if sufficient to cover balance due
27. **Return updated service location and billing statement**

## Activation Billing Statement
1. **Get Service Location from options**
2. **Set billing period defaults**
    - if FirstMonth is not specified, use current month
    - if FirstYear is not specified, use current year
3. **Initialize all repositories with transaction context**
    - Bill Repository
    - Invoice Repository
    - Payment Repository
    - Billing Statement Repository
    - Billing Statement Reminder Repository
    - Service Location Repository
4. **Set billing date (current date) and calculate due date**
	1. **Determine due date**
	    - if overrideDueDate is provided, use it
	    - else, calculate due date from service location and product variant
	2. **Calculate last reminder date**
	    - Set to 1 day before due date at midnight
5. **Retrieve before-activation invoices**
    - Get existing unpaid invoices (not void, not stated)
6. **Generate activation bills invoices**
    1. **Setup Fee bills and invoice** (if SetupFee > 0)
    2. **Prorated Fee invoice** (if monthly product and prorateFee > 0)
        - Calculate prorate fee if not provided
	        1. **Parse start date parameter**
			    - if startDate is provided, normalize it to midnight of that day (preserve timezone) else use current date/time
			2. **Get days in the month** of the parsed start date
			3. **Calculate cost per day**
			    - Get actual monthly fee divided by days in month (floored)
			4. **Calculate total remaining days** in that month
			    - Formula: (days in month - start day) + 1
			    - This includes the start day itself
			5. **Calculate prorated amount**
			    - Multiply cost per day by total remaining days
			6. **Round up to nearest 100** and return
        - Generate prorated add-on bills/invoice (if add-ons exist)
    3. **Deposit invoice** (if Deposit > 0)
    4. **Subscription bills/invoice** (if product is non-monthly OR if monthly and current day >= BillNDate)
        - Determine months to generate
        - Generate single month or multiple months product bills
        - Add billing cycle discount bill (if applicable)
        - Add additional discount bill (if applicable)
        - Add add-on subscription bills (if add-ons exist)
        - Combine into subscription invoice
    5. **Other bills/invoice** (from payload OtherBills)
        - Can be inventory items or custom bills
        - Handle tax settings (withTax, taxIncluded)
7. **Validate invoices exist** (activation or before-activation)
8. **Auto-pay with credit balance** (if credit balance >= total invoice amount)
    - Pay all activation invoices with credit balance
9. **Merge before-activation invoices with activation invoices**
10. **Calculate invoice payments and balances**
    - Get payments for all invoices
    - Identify paid/settled invoices
    - Calculate payment amount for balance forwarded
    - Calculate remaining credit balance amount
11. **Filter invoices** (exclude discount invoices that were already used)
    - Calculate new charge amount
12. **Create billing statement**
13. **Assign billing statement to filtered invoices and bills**
14. **Save billing statement reminders** (based on due date)
15. **Save activity log** (billing statement created)
16. **Update service location payment balance**
17. **Process credit balance payment** (for remaining unpaid invoices)
18. **Update service location next bill date**
    - Based on product variant billing cycle
    - Consider if subscription bills were generated

## ChangeProductBillingStatement Function Flow
1. **Initialize repositories with transaction**
    - Bill, Invoice, BillingStatement, Payment repos
2. **Set initial flags**
    - Should generate new product bill: true
    - Should prevent next bill date update: false
    - Initialize alteration invoices list
3. **Handle old monthly product** (if old product billing cycle is monthly)
    - Get previous month's subscription invoice
    - **If previous invoice exists**:
        - Cancel the old invoice and remove its payments
        - Calculate how much of old package was already used (prorated)
        - Create prorated bill for the old product (partial month usage)
        - **If previous invoice was already paid**:
            - Auto-pay the new prorated invoice using credit balance
4. **Handle old non-monthly product** (if old product is annual/quarterly/etc.)
    - **If subscription isn't ending exactly on change date**:
        - Validate old and new billing cycles match
        - Calculate complex proration:
            - Days remaining until next billing date
            - Daily cost for both old and new products
            - Apply discounts (billing cycle + additional)
            - Calculate remaining months in the cycle
            - Calculate extra fee for remaining months
        - Create prorate bill and remaining months bill
        - Generate invoice for product change
        - Skip generating new product bill
        - Prevent next bill date update
5. **Generate new product subscription bills** (if not skipped)
    - **If new product is monthly**:
        - Calculate prorated fee for partial month
        - Create prorated subscription bill for new product
    - **If new product is non-monthly**:
        - Get months to generate based on billing cycle
        - Create multiple-month subscription bill
        - Add billing cycle discount (if applicable)
        - Add additional discount (if applicable)
    - Generate subscription invoice from bills
    - **If monthly and change date is after bill date**: also generate next full month's bill
6. **Validate alteration invoices created**
    - Ensure at least one invoice was generated
7. **Determine billing timing**
    - **If change date is in the future**: bill now, schedule next bill date
    - **Otherwise**: use change date as billing date
8. **Get latest billing statement** for this service location
9. **Call main BillingStatement function** to create the statement
    - Pass in new product variant
    - Pass override dates and flags
    - Enable credit balance processing
10. **Return updated service location and billing statement**