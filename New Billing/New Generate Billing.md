## Condition/Requirement
- Scheduled
	- Based on ServiceLocation NextBillDate
		- H-5 before end cycle
- Generate Manual
	- Customized Due Date
	- Customized Billing Date
	- Customize Notify User
## Flow
1. **Get service location info by UUID**
2. **Check validity of service location (schedule etc)**
3. **Get Credit Balance from Wallet**
4. **Check if latest billing statement is awaiting settlement** (must not be)
5. **Get product variants for this service location**
6. **Get latest billing statement**
7. **Check generation lock from the service location** (from GeneratedAt)
    - Verify no concurrent billing generation is happening (unless Force flag is set)
8. **Assemble options**
	- Service Location
	- Product Variants
	- Credit Balance
	- Latest Billing Statement
	- From Date
	- To Date
	- Product Bill Date? [[Q&A#Q#BS]]
	- Creator Info
9. **Generate billing statement** [[New Billing]]
	- This will returning
		- BillingStatement
		- Payment Associated
		- Credit Balance
10. **Save to the billing and wallet database**
11. **Generate documents**
12. **Notify Customer if willing**
13. **Return billing statement and payments**


