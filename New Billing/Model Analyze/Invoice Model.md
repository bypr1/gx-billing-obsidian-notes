
## Invoice
- CustomerId
- CustomerServiceLocationId
- CustomerServiceLocationInfoId
- ProformaInvoiceId
- Number
- TypeId
- Version? [[Q&A#Q]]
- StatusId
- 
- ActiveBillingStatementId
- 
- BaseAmount
- ValueAddedTax
- WithHoldingTaxArt23
- Discount
- Amount
- CreatedAt
- UpdatedAt
- VoidedAt

## InvoiceItem
- Id
- Description
- TypeId
- CategoryId
- ReferenceId
- ReferenceType
- BaseAmount
- ValueAddedTax
- WithHoldingTaxArt23
- Discount
- Amount

## Payment
- Id
- MethodId (credit balance, cash, dll)
- Amount
- Description
- Remark
- Number
- CreatedAt
- UpdatedAt
- SettledAt
- PaidAt? (kalau dia ngirim pas pending juga, harus tau kapan dipaid)
- LockedAt?
- CancelledAt

## InvoicePayment
- InvoiceId
- PaymentId

## InvoiceActivity
Atau mau pake useActivity?