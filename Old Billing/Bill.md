## Normal Bill Flow

1. **Prepare repositories** (if not already prepared)
    - Initialize bill repository with transaction
2. **Validate service location** (unless skipped)
    - Check if service location is active and valid
3. **Determine tax inclusion settings**
    - if ForceIncludeTax is set, include tax without formula
    - if ExcludeTax is set, exclude tax
    - else, validate product variant exists and use service location's tax setting
    - if UseConsideredIsIncludeFormula is set, override to include tax
4. **Calculate bill amount**
    - if internet subscription fee and service location is free of charge, amount = 0
    - if WHT Art 23 Tax bill, convert amount to negative
    - if discount fee bill, convert amount to negative
    - if using considered formula, calculate: amount * 10 / DPP percentage
    - else, use provided amount
5. **Create bill record** with calculated amount
6. **Create bill sales representative links** (if provided)
7. **Save activity log** (bill created)
8. **Auto-create invoice** (if enabled)
    - Generate invoice containing this bill
    - Link invoice to bill

## ProductBill Function Flow

1. **Prepare repositories** with transaction
2. **Determine product variant ID and alias**
    - Get from ProductVariant or BSProductVariant
    - Validate at least one is provided
3. **Check bill history for duplicate**
    - Query if bill for this service location, product variant, month, and year already exists
    - If exists, throw error (already billed)
4. **Calculate bill amount**
    - if amount is provided, use it
    - if service location is free of charge, amount = 0
    - else, get actual monthly fee from product variant
5. **Set bill date**
    - Use provided BillDate or current date
6. **Set description**
    - Use provided description or generate default
7. **Create bill** using Bill function
    - Type: Internet Subscription Fee
    - Product Type: Internet
    - Accounting Type: Debit
8. **Create service location bill history record**
    - Store bill subscription history for this month/year

## MultipleProductBill Function Flow

1. **Prepare repositories** with transaction
2. **Determine product variant ID and alias**
    - Get from ProductVariant or BSProductVariant
    - Validate at least one is provided
3. **Set defaults for bill date and year**
    - if BillDate is not specified, use current date
    - if Year is not specified, use current year (mark as not overwritten)
4. **Adjust year if start month is in the past** (and year wasn't explicitly set)
5. **Set default total months** (minimum 1)
6. **Calculate month-year mapping**
    - Loop through total months starting from start month
    - Group months by year (handle year transitions)
    - Build array of {Year, Months[]} structures
7. **Check bill history for duplicates**
    - For each year-month combination
    - Query if bill for this service location, product variant, months, and year already exists
    - If exists, throw error (already billed)
8. **Calculate total amount**
    - if service location is free of charge, amount = 0
    - else, get actual monthly fee from product variant
    - Multiply by total months
9. **Create single bill** for all months using Bill function
    - Type: Internet Subscription Fee
    - Product Type: Internet
    - Accounting Type: Debit
    - Flag as non-monthly bill (if > 1 month)
10. **Create service location bill history records**
    - For each year-month combination, create separate history entry
    - Store monthly amount (not total)

## AddOnBill Function Flow

1. **Prepare repositories** with transaction
2. **Set defaults for bill date and year**
    - if BillDate is not specified, use current date
    - if Year is not specified, use current year (mark as not overwritten)
3. **Validate product variants are add-ons only**
    - Check that no internet product is in the list
    - If internet product found, throw error
4. **Loop through each product variant**
    1. **Calculate amount for this add-on**
        - if service location is free of charge, amount = 0
        - if IsProrate flag is set, calculate prorated add-on fee
        - else, calculate: actual monthly fee * total months * quantity
    2. **Adjust year if start month is in the past** (and year wasn't explicitly set)
    3. **Create bill** using Bill function
        - Type: Add-ons
        - Product Type: Others
        - Accounting Type: Debit
    4. **Add bill to collection**
5. **Return array of bills** (one per add-on variant)
## InactiveBill Function Flow

1. **Prepare repositories** with transaction
2. **Set defaults for bill date and year**
    - if BillDate is not specified, use current date
    - if Year is not specified, use current year (mark as not overwritten)
3. **Adjust year if month is in the past** (and year wasn't explicitly set)
4. **Set default total months** (minimum 1)
5. **Calculate month-year mapping**
    - Loop through total months starting from specified month
    - Group months by year (handle year transitions)
    - Build array of {Year, Months[]} structures
6. **Check bill history for duplicates**
    - For each year-month combination
    - Query if bill for this service location, product variant, months, and year already exists
    - If exists, throw error (already billed)
7. **Get inactive bill fee setting**
    - Retrieve from billing settings (temporary inactive bill fee)
    - Multiply by total months for total amount
8. **Create bill** using Bill function
    - Type: Internet Subscription Fee
    - Product Type: Internet
    - Accounting Type: Debit
    - Use considered formula for tax inclusion
9. **Create service location bill history records**
    - For each year-month combination, create separate history entry