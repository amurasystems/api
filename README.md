# Golfmanager API

## searchAvailability

List available slots.

Authentication: public

| Argument | Type     | Required | Description |
|----------|----------|----------|-------------|
| start    | datetime | yes      | The start of the search period |
| end      | datetime | yes      | The end of the search period   |
| slots    | int      | no       | The number of green fees (default is 1)   |

Example:

```bash
curl https://demo.golfmanager.es/amura/consumerbookings/searchAvailability.api \
 -d start="2018-11-09 10:00" \
 -d end="2018-11-09 12:00" \
 -d slots=2
```

Response:

Return a list of slots:

| Argument | Type     | Required | Description |
|----------|----------|----------|-------------|
| id          | int           | yes      | The reservation type id |
| max         | int           | no       | The maximum number of slots allowed  |
| min         | int           | no       | The minimum number of slots allowed  |
| multiple    | int           | no       | In case this type must be reserved in multiples   |
| price       | float         | yes      | The price per slot including taxes  |
| start       | datetime      | yes      | The date of this slot  |

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
    "start": "2018-09-09T10:00:00+02:00"
  },
  {
    "id": 3,
    "max": 4,
    "min": null,
    "multiple": null,
    "name": "Green Fee 9 hoyos",
    "price": 35,
    "start": "2018-09-09T10:00:00+02:00"
  },
  {
    "id": 33,
    "max": 4,
    "min": 2,
    "multiple": 2,
    "name": "Pack 2 GF+ Buggy",
    "price": 59,
    "start": "2018-09-09T10:00:00+02:00"
  }
]
```