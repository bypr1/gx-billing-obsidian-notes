
## Customer
- ID
- Number
- UUID
- Name
- BranchId
- PriorityId
- Address

## CustomerContact
- CustomerId
- Contact
- TypeId
- IsPrimary

## CustomerServiceLocation
- CustomerId
- CircuitId
- LocationId
- StatusId
- IsAlsoNotifyCustomer *includeCustomerInNotification*
- NextBillDate
- GenerateBillingAt
- Email
- Phone
- HasValueAddedTax
- HasWithHoldingTaxArt23
- HasFreeOfCharge
- 

## CustomerServiceLocationProductVariant
- Id
- UUID
- CustomerServiceLocationId
- ProductCariantId
- BillingCycleId
- StartNewProductDate
- NextBillDate
- Alias
- RecurringFee

## CustomerServiceLocationProductVariantBill
- Id
- CustomerServiceLocationProductVariantId
- FromDate
- ToDate
- Amount
- 

## ProductVariantBillingCycle
- Id
- NDays
- NMonth
- Discount
- Name

## CustomerServiceLocationInfo
Info daripada service location yang bisa diperbarui berada disini
- Id
- CustomerId
- CustomerServiceLocationId
- CustomerName
- LocationNickname
- LocationAddress
- IsNewest