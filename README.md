# API Reference

## Authentication

Authentication is performed via HTTP Basic Auth. 

All API requests must be made over HTTPS. Calls made over plain HTTP will fail. API requests without authentication will also fail.

## Errors

The API uses conventional HTTP response codes to indicate the success or failure of an API request. In general: Codes in the 2xx range indicate success. Codes in the 4xx range indicate an error that failed given the information provided (e.g., a required parameter was omitted, a charge failed, etc.).

## Handling errors

Our API raise exceptions for many reasons, such as a failed charge, invalid parameters, authentication errors, and network unavailability. We recommend writing code that gracefully handles all possible API exceptions.

## Dates

Dates are passed in UTC as ISO 8601. For example: 2018-10-23T16:20:00.000Z

---------------------------

## Api

 - [Ping](#ping)
 <!-- - [Availability](#availability)
 - [Make a pre reservation](#preReservation)
 - [Confirm a reservation](#confirm)
 - [Cancel a reservation](#cancelReservation) -->
 - [Clients](#clients)
 - [Products](#products)
 - [New sale](#newSale)
 - [Cancel sales](#cancelSales)
 - [Blockout](#blockout)



---------------------------


<h2 id="ping">Ping</h2>

Test your credentials.

Example request

```
$ curl -i https://mt.golfmanager.app/amura/api/ping \
   -u 5Jvm8sCtVr:b31aT5bScxk46aT \
   -d tenant=demo
```

Example response

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Date: Tue, 18 Dec 2018 17:46:52 GMT
Content-Length: 5

"OK"
```

<!-- 

<h2 id="availability">Availability</h2>

List available slots.

| Argument | Type     | Required | Description                             |
|----------|----------|----------|-----------------------------------------|
| tenant   | string   | yes      | The start of the search period          |
| start    | datetime | yes      | The start of the search period          |
| end      | datetime | yes      | The end of the search period            |
| slots    | int      | no       | The number of green fees (default is 1) |

Example:

```bash
curl https://mt.golfmanager.app/amura/ebookings/searchAvailability \
 -d start="2018-10-23T16:20:00.000Z" \
 -d end="2018-10-23T17:20:00.000Z" \
 -d slots=2
```

Response:

Return a list of slots:

| Argument | Type     | Required | Description                                     |
|----------|----------|----------|-------------------------------------------------|
| id       | int      | yes      | The reservation type id                         |
| max      | int      | no       | The maximum number of slots allowed             |
| min      | int      | no       | The minimum number of slots allowed             |
| multiple | int      | no       | In case this type must be reserved in multiples |
| price    | float    | yes      | The price per slot including taxes              |
| start    | datetime | yes      | The date of this slot                           |

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
    "start": "2018-11-09T10:00:00+02:00"
  },
  {
    "id": 3,
    "max": 4,
    "min": null,
    "multiple": null,
    "name": "Green Fee 9 hoyos",
    "price": 35,
    "start": "2018-11-09T10:00:00+02:00"
  },
  {
    "id": 33,
    "max": 4,
    "min": 2,
    "multiple": 2,
    "name": "Pack 2 GF+ Buggy",
    "price": 59,
    "start": "2018-11-09T10:00:00+02:00"
  }
]
```



### makeReservation

Make a reservation. The reservation needs to be confirmed before a period of time configured by the tenant. After that it will be automatically released.

Authentication: public or private depending on the tenant configuration

Arguments: an array of reservation objects. Each reservations must specify:

| Argument | Type     | Required | Description                             |
|----------|----------|----------|-----------------------------------------|
| type     | int      | yes      | The id obtained from searchAvailability |
| start    | datetime | yes      | The date of this slot                   |

Example:

```bash
curl https://mt.golfmanager.app/amura/ebookings/makeReservation \
 -d 'reservations=[{"idType":3,"start":"2018-11-09T08:20:00.000Z"}]'
```

Response:

Return the sale information to complete the payment if it needs to be prepaid 

Example:

```json
{  
   "cart":{  
      "idSale":5448,
      "quantity":1,
      "total":10,
      "units":1
   }
}
``` -->

### Clients

List clients

Authentication: private

| Argument | Type   | Required | Description                                  |
|----------|--------|----------|----------------------------------------------|
| tenant   | string | yes      | Club name                                    |
| search   | string | no       | Search clients by text                       |
| limit    | int    | no       | Limit the number of clients (default is 100) |

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

Authentication: private

| Argument | Type   | Required | Description                                   |
|----------|--------|----------|-----------------------------------------------|
| tenant   | string | yes      | Club name                                     |
| search   | string | no       | Search products by text                       |
| limit    | int    | no       | Limit the number of products (default is 100) |

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

Authentication: private

| Argument   | Type   | Required | Description    |
|------------|--------|----------|----------------|
| tenant     | string | yes      | Club name      |
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

Authentication: private

| Argument    | Type   | Required | Description                 |
|-------------|--------|----------|-----------------------------|
| tenant      | string | yes      | Club name                   |
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

Authentication: private

| Argument   | Type     | Required | Description                                                |
|------------|----------|----------|------------------------------------------------------------|
| tenant     | string   | yes      | Club name                                                  |
| idResource | int      | yes      | The resource id                                            |
| start      | datetime | yes      | UTC initial date and inital hour for blocking slots        |
| end        | datetime | yes      | UTC end data and end hour for searching slots availability |
| name       | string   | no       | Name to label the blocked slots                            |
| bgColor    | string   | no       | Background color for slots                                 |
| fgColor    | string   | no       | Foreground color for slots                                 |

Example:

```bash
curl https://mt.golfmanager.app/amura/api/blockout \
 -u 5Jvm8sCtVr:b31aT5bScxk46aT \
 -d tenant=test \
 -d idResource=1 \
 -d start=2018-12-14T08:00:00+01:00 \
 -d end=2018-12-14T18:00:00+01:00 \
 -d name=Test tournament \
 -d bgColor=#000000 \
 -d fgColor=#ffffff \
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