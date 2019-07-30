
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

// Se ejecuta cada vez que cambias de página
S.addEventListener("executedAction", event => {
    gtag('config', 'UA-XXXXXXXXXX-1', {
        'page_path': event.url
    })
})
```

Para registrar un evento al pedir una página:

```javascript
// Se ejecuta cada vez que cambia de página
S.addEventListener("executedAction", event => {
    gtag('config', 'UA-XXXXXXXXXX-1', {
        'page_path': event.url
    })

    if(event.url.hasPrefix("/consumer/eshop/cart")) {
        gtag('event', 'begin_checkout')
    }
})
```

Eventos al confirmar una venta:

```javascript
// Se ejecuta cuando se confirma una compra 
hooks.add("AmuraEshopSaleConfirmed", null, cart => {
    gtag('event', 'purchase', {
        'value': cart.lines.sum(t => t.total),
        'transaction_id': cart.idSale,
        'currency': 'EUR',
    })

    gtag('event', 'conversion', {
        'value': cart.lines.sum(t => t.total),
        'currency': 'EUR',
        'transaction_id': cart.idSale
    })
})
```