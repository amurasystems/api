
POS custom commands
==============


Custom commands
-----------


| Command               | Args       | Description                                                              |
|-----------------------|------------|--------------------------------------------------------------------------|
| addProduct            | idProduct  | add a new product to the sale                                            |
| addDiscount           | idDiscount | add a new discount to the sale                                           |
| addComments           |            | add a comment to the current line                                        |
| addPublicComments     |            | add a public comment to the current line. It will be shown in the ticket |
| addDescription        |            | append a text to the description of all or selected lines                |
| splitLine             |            | split a line into multiple lines                                         |
| setClient             |            | assign the client to all or selected lines                               |
| setCoupon             |            | set a cupon to the current line                                          |
| generateInvoice       |            | create an invoice from the current sale                                  |
| showLastSales         |            |                                                                          |
| showParkedSales       |            |                                                                          |
| cancelAccountPayments |            |                                                                          |
| printSale             |            |                                                                          |
| parkSale              |            |                                                                          |
| newSale               |            |                                                                          |
| printSale             |            |                                                                          |
| openDrawer            |            | Open the cash register drawer                                            |




External functions 
-----------
This functions depend on other plugins


| Command | Args                                           | Description |
|---------|------------------------------------------------|-------------|
| exec    | ["billing.newVoucher", idVoucherType]          |             |
| exec    | ["billing.newMembership" [, idMembershipType]] |             |
| exec    | ["timetable.newReservation", idReservation]    |             |
