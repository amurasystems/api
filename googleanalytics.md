
# Google Analytics

## Tracking ID

El código de identificación de Google Analytics. Tiene el formato: *UA-XXXXXXXXXX-1*

## Global Code

El código que se ejecuta en la página

Configuración mínima:

```javascript
window.dataLayer = window.dataLayer || [];
function gtag(){dataLayer.push(arguments);}
gtag('js', new Date())

// Se ejecuta cada vez que se muestra una página
S.addEventListener("setView", function(event) {
    gtag('config', 'UA-XXXXXXXXXX-1', {
        'page_path': event.path
    })
})
```

## Eventos

En el campo Global Code se puede añadir código para enviar eventos. Por ejemplo:

Al confirmar una venta

```javascript
S.addEventListener("eshop.saleConfirm", function(event) {
    gtag('event', 'purchase', {
        'value': event.cart.lines.sum(t => t.total),
        'transaction_id': event.cart.idSale,
        'currency': 'EUR',
    })
})
```

Al eliminar una venta del carrito

```javascript
S.addEventListener("eshop.deleteLine", function(event) {
    gtag('event', 'remove_from_cart', {
        'value': event.line.productName,
        'transaction_id': event.line.id,
    })
})
```

Al mostrar un error al confirmar la venta, por ejemplo "tarjeta sin fondos"

```javascript
S.addEventListener("eshop.saleConfirmError", function(event) {
    gtag('event', 'saleConfirmError', {
        'value': event.error,
        'transaction_id': event.cart.idSale,
        'currency': 'EUR',
    })
})
```


Ejemplo completo para medir productos

```javascript
window.dataLayer = window.dataLayer || [];
function gtag(){dataLayer.push(arguments);}
gtag('js', new Date())

// Se ejecuta cada vez que se muestra una página
S.addEventListener("setView", function(event) {
    gtag('config', 'UA-XXXXXXXXXX-1', {
        'page_path': event.path
    })
})

// Example to send cart items
// Check: https://developers.google.com/analytics/devguides/collection/gtagjs/enhanced-ecommerce
S.addEventListener("eshop.saleConfirm", function(event) {
    var cart = event.cart
    var lines = cart.lines

    // To inspect the cart object:
    // console.log(cart)

    let items = []    
    for(var i = 0; i < lines; i++) {
        var line = lines[i]
        switch(line.idProduct) {
            case 49: // GF 18
            case 55: // CARRO ELECTRICO 18
                items.push({
                    id: line.id,
                    name: line.description,
                    list_position: 1,
                    quantity: line.quantity,
                    price: line.total // or line.price for unit price
                })
                break
        }
    }

    gtag('event', 'purchase', {
        value: cart.total,
        currency: 'EUR',
        transaction_id: cart.idSale,
        items: items
    })
})
```
