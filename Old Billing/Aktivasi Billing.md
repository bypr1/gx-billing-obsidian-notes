
## Flow
1. Get Info Service Location
2. Check If Latest Billing is Awaiting Settlement (must not)
3. Get Info of Subscribed Product Variants
	- Their internet subscription
	- Their add on
4. Get Info Latest Billing Statement
	- Check Awaiting Settlement (if still awaiting skips)
	- 
5. From that Service Location
	- Retrieve their internet subscription product
	- Retrieve their add on subscription product 
6. From the payload, check the date:
	- if Billing StartDate is not specified, use current time
	- if Billing StartNewProductDate is not specified, use billing StartDate instead
7. Mark the service location as being generated, so no multiple bill statement can be generated at same time
8. Update Service Location StartDate, Status (to active), Priority
9. Update Service Location Product Variant's internet subscription's StartNewProductDate
10. Begin Generate Billing Statement
11. Begin Generate Files
	1. Invoice PDF
	2. Receipt PDF
	3. Billing Statement PDF
	4. Billing Statement ZIP
