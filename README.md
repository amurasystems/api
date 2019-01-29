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
 - [Make reservations](#makereservations)
 - [Confirm reservations](#confirmreservations)
 - [Cancel reservations](#cancelreservations)

 ## Admin API
 - [Tenant](#tenant)
 - [Reservations](#reservations)
 - [Clients](#clients)
 - [Products](#products)
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
$ curl -i https://mt.golfmanager.app/amura/api/tenants \
   -u 5Jvm8sCtVr:b31aT5bScxk46aT
```

Example response

```json
["demo1","demo2","demo3"]
```


<h2 id="availability">Search availability</h2>

List available slots.

Method: GET

| Argument   | Type     | Required | Description                             |
|------------|----------|----------|-----------------------------------------|
| tenant     | string   | yes      | The start of the search period          |
| start      | datetime | yes      | The start of the search period          |
| end        | datetime | yes      | The end of the search period            |
| slots      | int      | no       | The number of green fees (default is 1) |
| idResource | int      | no       | To filter slots by a resource           |

Example:

```bash
curl https://mt.golfmanager.app/amura/api/searchAvailability \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=test \
 -d start="2019-01-23T16:20:00.000Z" \
 -d end="2019-01-23T17:20:00.000Z" \
 -d slots=2
```

Response:

Returns a list of slots:

| Argument   | Type     | Description                                     |
|------------|----------|-------------------------------------------------|
| id         | int      | The reservation type id                         |
| max        | int      | The maximum number of slots allowed             |
| min        | int      | The minimum number of slots allowed             |
| multiple   | int      | In case this type must be reserved in multiples |
| price      | float    | The price per slot including taxes              |
| start      | datetime | The date of this slot                           |
| idResource | int      | The resource of this slot                       |

Example:

```json
[
  {
    "id": 1,
    "max": 4,
    "min": null,
    "multiple": null,
    "name": "Green Fee 18 hoyos",
    "price": 50,
    "start": "2018-11-09T10:00:00+02:00",
    "idResource": 1
  },
  {
    "id": 3,
    "max": 4,
    "min": null,
    "multiple": null,
    "name": "Green Fee 9 hoyos",
    "price": 35,
    "start": "2018-11-09T10:00:00+02:00",
    "idResource": null
  },
  {
    "id": 33,
    "max": 4,
    "min": 2,
    "multiple": 2,
    "name": "Pack 2 GF+ Buggy",
    "price": 59,
    "start": "2018-11-09T10:00:00+02:00",
    "idResource": null
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
curl https://mt.golfmanager.app/amura/api/makeReservation \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=test \
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
curl https://mt.golfmanager.app/amura/api/confirmReservation \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=test \
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
curl https://mt.golfmanager.app/amura/api/cancelReservation \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=test \
 -d 'ids=[234]'
```

Response:

If it succeeded or the error.

Example:

```json
{ "success": true }
``` 



<h2 id="tenant">Tenant</h2>

Returns general information about the tenant like phone, address, etc...

Methbod: GET

Example:

```bash
curl https://mt.golfmanager.app/amura/api/tenant \
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


### Reservations

List clients

Method: GET

| Argument | Type     | Required | Description                |
|----------|----------|----------|----------------------------|
| tenant   | string   | yes      | Tenant name                |
| start    | datetime | yes      | Start date and time filter |
| end      | datetime | yes      | End date and time filter   |

Example:

```bash
curl https://mt.golfmanager.app/amura/api/reservations \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=test \
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
        "idResource": 3,
        "idType": 1,
        "noShow": 0,
        "online": 0,
        "paid": 0,
        "start": "2019-01-25T16:54:49.25905354Z"
    },
    ...
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
curl https://mt.golfmanager.app/amura/api/clients \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=test \
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
curl https://mt.golfmanager.app/amura/api/products \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=test \
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
    "id": 1,
    "name": "Test product"
  },
  {
    "id": 2,
    "name": "Test product 2"
  }
]
```

### newSale

Creates new sale

Method: GET

| Argument   | Type   | Required | Description    |
|------------|--------|----------|----------------|
| tenant     | string | yes      | Tenant name    |
| idProduct  | int    | yes      | The product id |
| idClient   | int    | yes      | The client id  |
| parentName | int    | yes      | Referer name   |
| idParent   | int    | yes      | Referer id     |

Example:

```bash
curl https://mt.golfmanager.app/amura/api/newSale \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=test \
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
curl https://mt.golfmanager.app/amura/api/cancelSales \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=test \
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
curl https://mt.golfmanager.app/amura/api/blockout \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=test \
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
curl https://mt.golfmanager.app/amura/api/blockout \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=test \
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