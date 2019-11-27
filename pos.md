
POS custom commands
==============


Custom commands
-----------

| Command               | Args       | Description                                                              |
| --------------------- | ---------- | ------------------------------------------------------------------------ |
| addProduct            | idProduct  | add a new product to the sale                                            |
| addDiscount           | idDiscount | add a new discount to the sale                                           |
| addComments           |            | add a comment to the current line                                        |
| addPublicComments     |            | add a public comment to the current line. It will be shown in the ticket |
| addDescription        |            | append a text to the description of all or selected lines                |
| splitLine             |            | split a line into multiple lines                                         |
| setClient             |            | assign the client to all or selected lines                               |
| addDescription        |            | add a description to all or selected lines                               |
| addPublicComments     |            | add comments that will be printed on the ticket to all or selected lines |
| setCoupon             |            | set a cupon to the current line                                          |
| splitLine             |            | split a line in multiple amounts                                         |
| generateInvoice       |            | create an invoice from the current sale                                  |
| showLastSales         |            |                                                                          |
| showParkedSales       |            |                                                                          |
| cancelAccountPayments |            |                                                                          |
| printSale             |            |                                                                          |
| parkSale              |            |                                                                          |
| newSale               |            |                                                                          |
| printSale             |            |                                                                          |
| openDrawer            |            | Open the cash register drawer                                            |
| executeAction         |            | Open a url (an action. For a redirect prefix the url with 'URL:')        |





External functions 
-----------
This functions depend on other plugins


| Command | Args                                        | Description                                         |
| ------- | ------------------------------------------- | --------------------------------------------------- |
| exec    | ["billing.newVoucher", idVoucherType]       |                                                     |
| exec    | ["billing.addTag"*, ["Left handed"]*]       | Add a tag to the selected lines.                    |
| exec    | ["billing.newMembership", idMembershipType] | Create a new membership                             |
| exec    | ["timetable.newReservation", idReservation] | Create a new reservation linked to the selected one |
| exec    | ["fandb.sendToPrinter"]                     | Food and Beverage: send to kitchen printer          |
