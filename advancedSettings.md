
Advanced Settings
============================

**bookings.rememberType**  
Value: true/false  
Prevent remember the reservation type for a client in the timetable

**bookings.preventPastDates**  
Value: true/false  
Prevent making reservations on past dates

**bookings.allowAnyName**  
Value: true/false  
Allow anonymous reservations in admin

**ebookings.membershipMode**  
Value: true/false  
Show the bookings view in membership mode.

**ebookings.showPastHours**  
Value: true/false  
Show past hours when browsing the current day.

**billing.preventPastChanges**  
Value: true/false  
Prevent changing past sales (past refers to useDate)

**billing.dueDateAsUseDate**  
Value: true/false              
Set saleline dueDate as createDate or useDate by default

**billing.cancelInvoiceTickets**  
Value: true/false  
If the system cancels existing tickets when generating an invoice.

**billing.autoSetOnCredit**
Value: true/false  
Automatically add a line as on credit when a client has onCredit enabled.

**billing.requirePermissionToUpdatePastSales**
Value: true/false  
Require changePastUsedateSales permission to update sales with useDate in the past.

**eshop.thankyouPage**
Value: a url  
Plugin: consumer  
The url to redirect to after a successful purchase.  

**selectRowsByPage**
Value: true/false   
Plugin: slib   
Public: true   
Select rows all rows int the page versus all rows in the search.

**ebookings.membershipClientTag**  
Value: string  
Show a list of clients (with that tag) to pick one or a just a text box to enter free form text.

**billing.paymentAfterUseDate**
Value: true/false
Don't generate a payment when any line has useDate in the future. They payment will be saved as a payment on account.
When the date comes and you try to pay again (with the partial or full payment on account) the payment will be generated.

