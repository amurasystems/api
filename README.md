# API Reference

## Authentication

Authentication is performed via HTTP Basic Auth. 

All API requests must be made over HTTPS. Calls made over plain HTTP will fail. API requests without authentication will also fail.

## Errors

The API uses conventional HTTP response codes to indicate the success or failure of an API request. In general: Codes in the 2xx range indicate success. Codes in the 4xx range indicate an error that failed given the information provided (e.g., a required parameter was omitted, a reservation failed, etc.).

## Handling errors

Our API raise exceptions for many reasons, such as a failed charge, invalid parameters, authentication errors, and network unavailability. We recommend writing code that gracefully handles all possible API exceptions.

## Dates

The date format is ISO 8601 and can be passed in UTC or in the local time of the tenant. 

For example:

2018-10-09T09:00:00.000Z

This is equivalent for a tenant timezone "Europe/Madrid": 

2018-11-09T10:00:00+01:00 

---------------------------

 - [Tenants](#tenants)

## Consumer API

 - [Tenant](#tenant)
 - [Search availability](#availability)
 - [Availability types](#availabilitytypes)
 - [Make reservations](#makereservations)
 - [Confirm reservations](#confirmreservations)
 - [Cancel reservations](#cancelreservations)
 - [Sale information](#saleinfo)

 ## Admin API
 - [Tenant](#tenant)
 - [Reservation types](#reservationtypes)
 - [Resources](#resources)
 - [Teesheet Rules](#teesheetrules)
 - [Reservations](#reservations)
 - [Clients](#clients)
 - [ClientGroups](#clientgroups)
 - [Products](#products)
 - [Prices](#prices)
 - [Save price](#saveprice)
 - [Discounts](#discounts)
 - [Save discount](#savediscount)
 - [New sale](#newSale)
 - [Cancel sales](#cancelSales)
 - [Blockout](#blockout)
 - [Cancel blockout](#cancelblockout)

 
---------------------------


<h2 id="tenants">Tenants</h2>

Returns the tenants this key has access to.

Method: GET

Example request

```
$ curl -i https://mt.golfmanager.es/amura/api/tenants \
   -u 5Jvm8sCtVr:b31aT5bScxk46aT
```

Example response

```json
["demo1","demo2","demo3"]
```


<h2 id="availability">Search availability</h2>

List available slots.

Method: GET

| Argument | Type     | Required | Description                                        |
|----------|----------|----------|----------------------------------------------------|
| tenant   | string   | yes      |                                                    |
| start    | datetime | yes      | The start of the search period                     |
| end      | datetime | yes      | The end of the search period                       |
| slots    | int      | no       | The number of green fees (default is 1)            |
| tags     | string[] | no       | Array of tags that the reservation types must have |

List of valid tags: 

    18holes
    9holes
    tee1
    tee10
    buggy
    trolley
    electric-trolley


Example:

```bash
curl https://mt.golfmanager.es/amura/api/searchAvailability \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d start="2019-01-23T16:20:00.000Z" \
 -d end="2019-01-23T17:20:00.000Z" \
 -d slots=2
```

Example searching only for reservations that contain 9 or 18 holes:

```bash
curl https://mt.golfmanager.es/amura/api/searchAvailability \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d start="2019-01-23T16:20:00.000Z" \
 -d end="2019-01-23T17:20:00.000Z" \
 -d tags=["9holes", "18holes"]
 -d slots=2
```

Response:

Returns a list of slots:

| Argument               | Type          | Optional | Description                                                          |
|------------------------|---------------|----------|----------------------------------------------------------------------|
| id                     | int           | no       | The reservation type id.                                             |
| max                    | int           | no       | The maximum number of slots allowed.                                 |
| min                    | int           | no       | The minimum number of slots allowed.                                 |
| multiple               | int           | no       | In case this type must be reserved in multiples.                     |
| price                  | float         | no       | The price per slot including taxes.                                  |
| start                  | datetime      | no       | The date of this slot.                                               |
| tags                   | string[]      | no       | Array of tags to filter types.                                       |
| minCancelAdvance       | datetime      | yes      | Limit date to cancel the reservation.                                |
| maxReservationsPerSale | int           | yes      | The maximum number of reservations in a sale.                        |
| maxReservationsPerDay  | int           | yes      | The maximum number of reservations that can be made in the same day. |
| maxActiveReservations  | int           | yes      | the maximum number of reservations that can be made in advance.      |
| onCredit               | bool          | yes      | If it is a sale  on credit.                                          |
| dueDate                | dueDate       | yes      | The sale due date if it is on credit.                                |
| prepayPercent          | prepayPercent | yes      | The % to pay when making the reservation.                            |

See above the list of valid tags.

Example:

```json
[
  {
    "id": 1,
    "max": 4,
    "min": null,
    "multiple": null,
    "name": "Green Fee 18 holes",
    "price": 50,
    "start": "2018-11-09T10:00:00+02:00",
    "tags": [
      "18holes", 
      "tee1"
    ]
  },
  {
    "id": 3,
    "max": 4,
    "min": null,
    "multiple": null,
    "name": "Green Fee 9 holes",
    "price": 35,
    "start": "2018-11-09T10:00:00+02:00",
    "tags": [
      "9holes"
    ]
  },
  {
    "id": 33,
    "max": 4,
    "min": 2,
    "multiple": 2,
    "name": "Pack 2 GF+ Buggy",
    "price": 59,
    "start": "2018-11-09T10:00:00+02:00",
    "tags": [
      "18holes", 
      "buggy"
    ]
  }
]
```



 - [Availability types](#availabilitytypes)
<h2 id="availabilitytypes">List reservation types</h2>

Methbod: GET

Arguments: optionally an array of tags to return only results containing those tags.

Example:

```bash
curl https://mt.golfmanager.es/amura/api/availabilityTypes \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo 
```

Or filtering by tag:

```bash
curl https://mt.golfmanager.es/amura/api/availabilityTypes \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d tags=["18holes", "9holes"]
```

Response:

```json
[
    {
        "description": "Precio por persona",
        "id": 1,
        "max": null,
        "min": null,
        "multiple": null,
        "name": "GF18",
        "tags": [
            "18holes"
        ]
    },
    {
        "description": "Oferta sólo Online!",
        "id": 2,
        "max": null,
        "min": null,
        "multiple": null,
        "name": "GF9",
        "tags": [
            "9holes"
        ]
    }
]
``` 












<h2 id="makereservations">Make reservations</h2>

Methbod: POST

Arguments: an array of reservation objects. Each reservations must specify:

| Argument | Type     | Required | Description                                                                   |
|----------|----------|----------|-------------------------------------------------------------------------------|
| idType   | int      | yes      | The "id" field obtained from searchAvailability. This is the reservation type |
| start    | datetime | yes      | The date of this slot                                                         |
| timeout  | datetime | no       | The date when it will be released if it is not confirmed                      |
| name     | string   | no       | The client's name                                                             |
| email    | string   | no       | The client's email                                                            |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/makeReservation \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d 'reservations=[{"idType":3,"start":"2018-11-09T10:00:00%2B02:00"}]'
```

Response:

Return reservations with their ID in case they need to be canceled. It also returns the sale information.

Example:

```json
{  
   "reservations": [
     { "id":234, "idType":3, "start":"2018-11-09T10:00:00+02:00" }
   ],
   "cart":{  
      "idSale":5448,
      "quantity":1,
      "total":10,
      "units":1
   }
}
``` 


<h2 id="confirmreservations">Confirm reservations</h2>

Confirm reservations that where created with a timeout.

Methbod: POST

Example:

```bash
curl https://mt.golfmanager.es/amura/api/confirmReservation \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d 'ids=[234]'
```

Response:

If it succeeded or the error.

Example:

```json
{ "success": true }
``` 


<h2 id="cancelreservations">Cancel reservations</h2>

Cancel reservations by id.

Methbod: POST

Example:

```bash
curl https://mt.golfmanager.es/amura/api/cancelReservation \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d 'ids=[234]'
```

Response:

If it succeeded or the error.

Example:

```json
{ "success": true }
``` 



<h2 id="saleinfo">Sale information</h2>

Show information about a sale by id.

Methbod: GET

Example:

```bash
curl https://mt.golfmanager.es/amura/api/sale \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d 'id=34'
```

Response:

```json
{
    "lines": [
        {
            "canceled": false,
            "createDate": "2019-01-29T19:04:46.618880302Z",
            "description": "GF18, Tee 1, 31-01-2019 10:00, 3LG1B, John Smith",
            "onCredit": false,
            "paid": false,
            "prepayPercent": 0,
            "temp": true,
            "total": 0
        }
    ]
}
``` 

















# Admin





<h2 id="tenant">Tenant</h2>

Returns general information about the tenant like phone, address, etc...

Methbod: GET

Example:

```bash
curl https://mt.golfmanager.es/amura/api/tenant \
 -d tenant=demo \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT
```

Response:

Example:

```json
{
    "country": "Spain",
    "email": "info@golfmanager.com",
    "image": "392416535538319349sA1PFATw5.png",
    "latitude": "40.2269911",
    "logo": "3899613330138759642L7prno4J.png",
    "longitude": "-3.2406688",
    "name": "Demo Golf Club",
    "phone": "636236052",
    "province": "Madrid",
    "street": "Orense 64",
    "town": "Madrid",
    "web": "https://golfmanager.com",
    "zipCode": "28020",
    "timezoneName": "Atlantic/Canary",
}
```





<h2 id="reservationtypes">Reservation Types</h2>

List reservation types

Method: GET

| Argument | Type   | Required | Description                                                 |
|----------|--------|----------|-------------------------------------------------------------|
| tenant   | string | yes      | Tenant name                                                 |
| offset   | int    | no       | The offset of the first row to be returned                  |
| count    | int    | no       | The maximum number of rows to be returned. (default is 100) |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/reservationtypes \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo
```

Response:

Example:

```json
[
    {
        "clientGroupName": null,
        "description": null,
        "end": null,
        "id": 1,
        "idClientGroup": null,
        "idProduct": 1,
        "idResource": 1,
        "idResourceType": 1,
        "max": null,
        "min": null,
        "multiple": null,
        "name": "GF18",
        "online": true,
        "priority": 0,
        "productName": "Test Product",
        "resourceName": "Tee test",
        "resourceTypeName": "Green fees",
        "start": null,
        "tags": "18holes",
        "weekDays": 127
    }
]
```

<h2 id="resources">Resources</h2>

List resources

Method: GET

| Argument | Type   | Required | Description                                                 |
|----------|--------|----------|-------------------------------------------------------------|
| tenant   | string | yes      | Tenant name                                                 |
| offset   | int    | no       | The offset of the first row to be returned                  |
| count    | int    | no       | The maximum number of rows to be returned. (default is 100) |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/resources \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo
```

Response:

Example:

```json
[
    {
        "id": 1,
        "idResourceType": 1,
        "name": "Tee 1",
        "resourceTypeName": "Green fees"
    },
    {
        "id": 2,
        "idResourceType": 1,
        "name": "Tee 10",
        "resourceTypeName": "Green fees"
    }
]
```


<h2 id="teesheetRules">Teesheet Rules</h2>

List slots

Method: GET

| Argument | Type     | Required | Description                    |
|----------|----------|----------|--------------------------------|
| tenant   | string   | yes      | Tenant name                    |
| start    | datetime | yes      | The start of the search period |
| end      | datetime | yes      | The end of the search period   |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/teesheetRules \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d start="2019-01-01T23:00:00.000Z" \
 -d end="2019-01-02T17:23:00.000Z"
```

Response:

Example:

```json
{
    "resourceTypes": [
        {
            "id": 2,
            "name": "Buggies",
            "online": false,
            "slots": 2,
            "stock": true
        },
        {
            "id": 1,
            "name": "Green fees",
            "online": false,
            "slots": 4,
            "stock": false
        }
    ],
    "resources": [
        {
            "id": 1,
            "name": "Tee 1",
            "notOnline": false
        }
    ],
    "slots": [
        {
            "day": "2019-01-01",
            "intervals": [
                {
                    "end": null,
                    "endTime": "14:00",
                    "id": 1,
                    "idResourceType": 1,
                    "name": "test",
                    "online": false,
                    "priority": 0,
                    "start": null,
                    "startTime": "09:00",
                    "weekDays": 127
                },
                {
                    "end": null,
                    "endTime": "22:00",
                    "id": 2,
                    "idResourceType": 1,
                    "name": "test",
                    "online": false,
                    "priority": 0,
                    "start": null,
                    "startTime": "14:00",
                    "weekDays": 127
                }
            ],
            "types": [
                {
                    "end": null,
                    "endTime": null,
                    "id": 1,
                    "idResource": 1,
                    "idResourceType": 1,
                    "max": null,
                    "min": null,
                    "multiple": null,
                    "name": "GF18",
                    "online": false,
                    "priority": 0,
                    "start": null,
                    "startTime": null,
                    "tags": [
                        "18holes"
                    ],
                    "weekDays": 127
                },
                {
                    "end": null,
                    "endTime": null,
                    "id": 2,
                    "idResource": null,
                    "idResourceType": 1,
                    "max": null,
                    "min": null,
                    "multiple": null,
                    "name": "GF9",
                    "online": false,
                    "priority": 0,
                    "start": null,
                    "startTime": null,
                    "tags": [
                        "9holes"
                    ],
                    "weekDays": 127
                }
            ]
        },
        {
            "day": "2019-01-02",
            "intervals": [
                {
                    "end": null,
                    "endTime": "14:00",
                    "id": 1,
                    "idResourceType": 1,
                    "name": "test",
                    "online": false,
                    "priority": 0,
                    "start": null,
                    "startTime": "09:00",
                    "weekDays": 127
                },
                {
                    "end": null,
                    "endTime": "22:00",
                    "id": 2,
                    "idResourceType": 1,
                    "name": "test",
                    "online": false,
                    "priority": 0,
                    "start": null,
                    "startTime": "14:00",
                    "weekDays": 127
                }
            ],
            "types": [
                {
                    "end": null,
                    "endTime": null,
                    "id": 1,
                    "idResource": 1,
                    "idResourceType": 1,
                    "max": null,
                    "min": null,
                    "multiple": null,
                    "name": "GF18",
                    "online": false,
                    "priority": 0,
                    "start": null,
                    "startTime": null,
                    "tags": [
                        "18holes"
                    ],
                    "weekDays": 127
                },
                {
                    "end": null,
                    "endTime": null,
                    "id": 2,
                    "idResource": null,
                    "idResourceType": 1,
                    "max": null,
                    "min": null,
                    "multiple": null,
                    "name": "GF9",
                    "online": false,
                    "priority": 0,
                    "start": null,
                    "startTime": null,
                    "tags": [
                        "9holes"
                    ],
                    "weekDays": 127
                }
            ]
        }
    ],
    "stocks": [
        {
            "end": null,
            "id": 1,
            "idResourceType": 2,
            "name": "Summer",
            "priority": 0,
            "start": null,
            "stock": 25
        }
    ]
}
```





### Reservations

List reservations

Method: GET

| Argument | Type     | Required | Description                       |
|----------|----------|----------|-----------------------------------|
| tenant   | string   | yes      | Tenant name                       |
| start    | datetime | yes      | Start date and time search period |
| end      | datetime | yes      | End date and time search period   |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/reservations \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d start=2018-12-14T08:00:00%2B01:00 \
 -d end=2018-12-14T18:00:00%2B01:00
```

Response:

Return a list of reservations

Example:

```json
[
    {
        "checkin": 0,
        "confirmDate": null,
        "createDate": "2019-01-25T15:54:49.25924646Z",
        "end": "2019-01-25T17:04:49.25905354Z",
        "id": 1,
        "idClientGroup": 1,
        "idProduct": 34,
        "idResource": 3,
        "idType": 1,
        "noShow": 0,
        "online": 0,
        "paid": 0,
        "start": "2019-01-25T16:54:49.25905354Z",
        "total": 50
    }
]
```









<h2 id="clientgroups">Client groups</h2>

List client groups

Method: GET

| Argument | Type   | Required | Description                                                 |
|----------|--------|----------|-------------------------------------------------------------|
| tenant   | string | yes      | Tenant name                                                 |
| offset   | int    | no       | The offset of the first row to be returned                  |
| count    | int    | no       | The maximum number of rows to be returned. (default is 100) |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/clientGroups \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo
```

Response:

Example:

```json
[
    {
        "id": 1,
        "name": "Agents"
    }
]
```



### Clients

List clients

Method: GET

| Argument | Type   | Required | Description                                                 |
|----------|--------|----------|-------------------------------------------------------------|
| tenant   | string | yes      | Tenant name                                                 |
| search   | string | no       | Search clients by text                                      |
| offset   | int    | no       | The offset of the first row to be returned                  |
| count    | int    | no       | The maximum number of rows to be returned. (default is 100) |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/clients \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d search=a \
 -d limit=500
```

Response:

Return a list of clients:

| Argument | Type   | Description      |
|----------|--------|------------------|
| id       | int    | The client id    |
| name     | string | The client name  |
| email    | string | The client email |

Example:

```json
[
  {
    "id": 1,
    "name": "John Doe",
    "email": "johndoe@test.com"
  },
  {
    "id": 2,
    "name": "Jane Doe",
    "email": "janedoe@test.com"
  }
]
```

### Products

List products

Method: GET

| Argument | Type   | Required | Description                                                 |
|----------|--------|----------|-------------------------------------------------------------|
| tenant   | string | yes      | Tenant name                                                 |
| search   | string | no       | Search products by text                                     |
| offset   | int    | no       | The offset of the first row to be returned                  |
| count    | int    | no       | The maximum number of rows to be returned. (default is 100) |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/products \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d search=a \
 -d limit=500
```

Response:

Return a list of products:

| Argument | Type   | Description      |
|----------|--------|------------------|
| id       | int    | The product id   |
| name     | string | The product name |

Example:

```json
[
  {
    "basePercent": null,
    "id": 1,
    "idBaseProduct": null,
    "idSubfamily": 1,
    "name": "Test Product 1",
    "price": 50
  }
]
```


### Prices

List prices

Method: GET

| Argument | Type   | Required | Description                                                 |
|----------|--------|----------|-------------------------------------------------------------|
| tenant   | string | yes      | Tenant name                                                 |
| offset   | int    | no       | The offset of the first row to be returned                  |
| count    | int    | no       | The maximum number of rows to be returned. (default is 100) |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/prices \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo
```

Response:

Example:

```json
[
    {
        "channel": null,
        "end": null,
        "endTime": null,
        "holiday": false,
        "id": 1,
        "idAgeGroup": null,
        "idClient": null,
        "idClientGroup": null,
        "idFamiliySales": null,
        "idMembership": null,
        "idProduct": 1,
        "maxAdvance": null,
        "maxSales": null,
        "minAdvance": null,
        "minSales": null,
        "name": "Twilight",
        "price": 100,
        "priority": 0,
        "start": null,
        "startTime": null,
        "weekdays": 0
    }
]
```


<h2 id="saveprice">Save price</h2>

Save a price. If it has an id it will update it, otherways it will create a new one.

Method: POST

| Argument | Type | Required | Description        |
|----------|------|----------|--------------------|
| data     | json | yes      | The object as json |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/savePrice \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d data="{\"idProduct\":1,\"name\":\"Twilight\",\"price\":100,\"priority\":0,\"weekDays\":127}"
```

Response:

The ID if it is created. Nothing if it is an update.




### Discounts

List discounts

Method: GET

| Argument | Type   | Required | Description                                                 |
|----------|--------|----------|-------------------------------------------------------------|
| tenant   | string | yes      | Tenant name                                                 |
| offset   | int    | no       | The offset of the first row to be returned                  |
| count    | int    | no       | The maximum number of rows to be returned. (default is 100) |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/discounts \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo
```

Response:

Example:

```json
[
    {
        "channel": null,
        "end": null,
        "endTime": null,
        "holiday": false,
        "id": 1,
        "idAgeGroup": null,
        "idClient": null,
        "idClientGroup": null,
        "idFamiliySales": null,
        "idMembership": null,
        "idProduct": 1,
        "maxAdvance": null,
        "maxSales": null,
        "minAdvance": null,
        "minSales": null,
        "name": "Twilight",
        "percent": 10,
        "priority": 0,
        "rounding": null,
        "start": null,
        "startTime": null,
        "weekdays": 0
    }
]
```





<h2 id="savediscount">Save discount</h2>

Save a discount. If it has an id it will update it, otherways it will create a new one.

Method: POST

| Argument | Type | Required | Description        |
|----------|------|----------|--------------------|
| data     | json | yes      | The object as json |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/saveDiscount \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d data="{\"idProduct\":1,\"name\":\"Twilight\",\"percent\":100,\"priority\":0,\"weekDays\":127}"
```

Response:

The ID if it is created. Nothing if it is an update.

















### newSale

Creates new sale

Method: POST

| Argument   | Type   | Required | Description    |
|------------|--------|----------|----------------|
| tenant     | string | yes      | Tenant name    |
| idProduct  | int    | yes      | The product id |
| idClient   | int    | yes      | The client id  |
| parentName | int    | yes      | Referer name   |
| idParent   | int    | yes      | Referer id     |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/newSale \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d idProduct=1 \
 -d idClient=1 \
 -d parentName="Test platform" \
 -d idParent=1
```

Response:

Return SaleLine id:

| Argument   | Type | Description     |
|------------|------|-----------------|
| saleLineId | int  | The SaleLine id |

Example:

```json
[
  {
    "saleLineId": 1
  },
  {
    "saleLineId": 2
  }
]
```

### cancelSales

Cancel multiple sales

Method: POST

| Argument    | Type   | Required | Description                 |
|-------------|--------|----------|-----------------------------|
| tenant      | string | yes      | Tenant name                 |
| idSaleLines | int[]  | yes      | Array with the saleline ids |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/cancelSales \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d idLines=[1,2]
```

### blockout

Block slots in the occupation table 

Method: POST

| Argument   | Type     | Required | Description                            |
|------------|----------|----------|----------------------------------------|
| tenant     | string   | yes      | Tenant name                            |
| idResource | int      | yes      | The resource id                        |
| start      | datetime | yes      | Start date and time for blocking slots |
| end        | datetime | yes      | End date and time for blocking slots   |
| name       | string   | no       | Name to label the blocked slots        |
| bgColor    | string   | no       | Background color for slots             |
| fgColor    | string   | no       | Foreground color for slots             |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/blockout \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d idResource=1 \
 -d start=2018-12-14T08:00:00%2B01:00 \
 -d end=2018-12-14T18:00:00%2B01:00 \
 -d name=Test tournament \
 -d bgColor=#000000 \
 -d fgColor=#ffffff \
```


Response:

The object created with its id

Example:

```json
{
    "id": 26,
    "idResource": 1,
    "start": "2018-12-14T08:00:00%2B01:00"
    "end": "2018-12-14T18:00:00%2B01:00",
    "name": null,
    "bgColor": null,
    "fgColor": null,
}
```

<h2 id="cancelblockout">Cancel blockout</h2>

Cancel a blockout by ID 

Method: POST

| Argument | Type   | Required | Description            |
|----------|--------|----------|------------------------|
| tenant   | string | yes      | Tenant name            |
| id       | int    | yes      | The blockout object id |

Example:

```bash
curl https://mt.golfmanager.es/amura/api/blockout \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=demo \
 -d id=1
```



---------------------------

## Terms of Service

- Thank you for using Golfmanager! When you develop on the Golfmanager Platform,
you agree to be bound by the following terms, so please read them carefully. 

- The API is provided as-is and without any warranty whatsoever. Golfmanager is not liable for any 
damage due to unavailable or incorrect APIs.

- Whilst Golfmanager uses all reasonable endeavours to correct any errors or omissions on the
Golfmanager Platform as soon as practicable once they have been brought to Golfmanager's
attention, Golfmanager makes no promises, guarantees, representations or warranties of
any kind whatsoever (express or implied).

- Abuse or excessively frequent requests to Golfmanager via the API may result in 
the temporary or permanent suspension of your Account's access to the API. Golfmanager,
in our sole discretion, will determine abuse or excessive usage of the API. We will make
a reasonable attempt to warn you via email prior to suspension.

- Golfmanager has the right to change these General API Terms and Conditions and 
any Specific API Terms and Conditions.