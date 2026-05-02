
## Refund
Masuk credit balance

## Discount vs Discount Used
discount dapet dari bisnis

## Balance Forwarded
B


## Bikin Billing
Pas ada tagihan

## Billing Statement
Hanya aktif 1 unpaid, harus void dulu baru create ulang

Invoice Revision

## Product Item Invoice
Prorate + Unblock

## Setting Dynamic
Inactive
Change Ownership
Maintenance Fee Dll

Billing S

Billing Statement Activity:
UpdatedBy
Amount


Billing Statement
Get Data
Logic
WalletRepo
BIllingRepo

## else
perlu notif? ketika



Next:
Tanya apakah ada melakukan pembayaran sekaligus 3 bulan, dia monthly
Tampilan untuk perpajakan

Apakah ada invoice yang isinya campur? (Subscription + Fee) atau (Item Warehouse + Fee)
Customer tanpa billing automated?


MY:
Apakah billing dan invoice mesti dipisah modulnya? karena billing tidak bisa berdiri sendiri meskipun invoice bisa?
kalaupun dipisah gimana dengan preload gormnya, apakah pakai pendekatan beda? atau ada pengecualian?


# Q
## BS
1. Masih perlukah opsi product bill?
	- Jika sudah pakai konsep baru harusnya opsi untuk override certain month pada bisa hilang
2. Bolehkah setelah billing statement dikirim edit invoice didalamnya
	- karena jika pakai gxdocument, isian dari link emailnya pasti berubah
		- jika tidak boleh dan harus generate ulang keseluruhan, maka harus ada pemberitahuan di gxdocument kalau sudah void

## Inv
1. Cancel failed midtrans payment

## Sloc
1. Masih adakah opsi untuk konsep lama 
	1. Sloc pingin digeneratekan invoice per bulan dengan tanggal billing yang sama
		1. Jika iya maka setiap bulan memakai sistem prorate
		2. Masih bisakah generate untuk start date beda dengan bill date
			1. ex: 1 - 30 april, bill date 25
			2. else: bill date 25, 25 maret - 24 april (prorate 31)
2. Apakah ada kemungkinan billing cycle nday diubah?
3. Boleh inactive satu bagian saja? inactive internet tapi transvision active