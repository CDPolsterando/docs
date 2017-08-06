---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - python
  - javascript

toc_footers:
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

 # includes:
  # - errors 

search: true
---

# Introduction

## Polsterando Pricing Tool

All the endpoints/parameters/variables/... are in english.


# Authentication

We protect the API Endpoints with a [JWT Token](https://jwt.io/). The token needs to be provided to every request or the HTTP Status Code `401` is returned. On the server we use [Flask-JWT](https://pythonhosted.org/Flask-JWT/)?


Header: `Authorization: Bearer eyJhbGc...`


<aside class="notice">
You <strong>must</strong> provide a JWT Token to get data.
</aside>


### HTTP Request

This endpoint creates a token.

`POST /api/auth`

```
POST /api/auth
Content-Type: application/json

{
    "username": "joe",
    "password": "pass"
}
```


> The above command returns JSON structured like this:

```json
{
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZGVudGl0eSI6MSwiaWF0IjoxNDQ0OTE3NjQwLCJuYmYiOjE0NDQ5MTc2NDAsImV4cCI6MTQ0NDkxNzk0MH0.KPmI6WSjRjlpzecPvs3q_T3cJQvAgJvaQAPtk1abC_E"
}
```

# Products

This endpoint returns a list of all the categories with their products.

`GET /api/products`

> Returns the following structure:

```json
[
  {
    "name": "Polstermöbel",
    "products": [
      {
        "name": "Sessel",
        "single_price_net": 41.18,
        "single_price_gross": 49.0,
        "single_time": 45,

        "has_option_qm": false,
        "has_option_removable_pillows": true
      }
    ]
  }
]
```

# Location

This endpoint calculates which location is the nearest to the customer.

If the location could not be found return an HTTP Status Code of `400`.

### HTTP Request

`GET /api/location`

### Query Parameters

Parameter | Required | Description
--------- | -------- | -----------
zip       | yes      | The zip code of the customer.

> Example Request:

`GET /api/location?zip=70173`

> Returns the following structure:

```json
{
  "city": "Stuttgart",
  "driving_distance": 3, // in km
  "driving_duration": 7 // in minutes
}
```


# Scripts

Returns all the scripts.

### HTTP Request

`GET /api/scripts`

> Returns the following structure:

```json
{
  "reinigunsverfahren": "Lorem ipsum dolor sit amet, consetetur.",
  "preis": "Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam."
  ...
}
```

# Constants

Returns all the constants that are required to calculate the price.

`GET /api/constants`

> Returns the following structure:

```json
{
  "wage_ride": 10, // all in euro
  "wage_work": 15,

  "flat_rate_extra_work": 5,
  "flat_rate_cleaning_supplies": 15,

  "cost_per_order": 45,
  "price_per_kilometer": 0.16,

  "minimum_margin": 0.05 // 5%
}
```


# Discounts

Returns all the quantity discounts.

The dicount applies if following formula applies:

`if (min < price < max) price = price - discount`

`GET /api/discounts`

> Returns the following structure:

```json
[
  {
    "min": 40, // euro
    "max": 50, // euro
    "discount": 10 // euro
  }
]
```

# Saved Contracts

## POST

Inserts the Contract into the DB.

### HTTP Request

`POST /api/contract`

> POST the following structure

```json
{
  "name": "Name",
  "zip": 12345,
  "objects": [
    {
      "name": "Sessel",
      "single_price_net": 41.18,
      "single_price_gross": 49.0,
      "single_time": 45,
      "qm": 10,
      "removable_pillows_small": false,
      "removable_pillows_large": false
      // possibly even more
    },
    // ...
  ],
  "note": "note",

  "driving_duration": 1,
  "driving_distance": 1,
  "negotiated_price": 409.87,
  // possibly even more (city, employee)
}
```

> Returns the following structure:

```json
{
  "id": "xyz-123-XYZ"
}
```


## GET

Gets all the contracts from the database.

### HTTP Request

`GET /api/contracts`

### Query Parameters

Parameter | Required | Description
--------- | -------- | -----------
zip       | yes      | The zip code of the customer.
name      | yes      | The full name of the customer.



> Returns the following structure:

```json
[
  {
    "id": "xyz-123-XYZ",
    "inserted_at": 1501693511547,
    "data": {
      // follows the schema of the frontend
      // -> see `POST /api/contract`
    }
  }
]
```


# HTTP Codes

If an error occures the corresponding status code is returned.


Code      | Meaning
--------- | --------
200       | OK -- Everything is fine. The data is returned.
400       | Bad Request -- The request could not be understood by the server due to malformed syntax. The client SHOULD NOT repeat the request without modifications.
401       | Unauthorized -- The JWT Token is not provided.
500       | Internal Server Error -- We had a problem with our server. Try again later.