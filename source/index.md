---
title: API Reference Rotterdampas

toc_footers:
  - <a href='http://yipyip.nl'>Copyright YipYip 2015</a>
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

The following document is written to enable the developers of Intermediad to implement the API for the Rotterdampas apps. This document is guided by the interaction and visual design created by IN10, and the preliminary database diagram supplied by IN10. This document is still a work in progress and therefor can and will be changed during development. However, this document will be used as the guide to write tests and validate the implementation so changes must always be commited in this document.

## RESTful

This API is designed to confirm to some RESTful best practices as described [here](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api), while omitting some of the more advanced features to keep the develop time at a minumum.

## JSON

All requests and respons bodies (except when specifically stated otherwise) are sent and received in valid JSON. Text fields will always be displayed as-is and not be parsed, so they should not contain HTML. All requests should be encoded as UTF-8.

# Authentication

Authentication is not part of the spec right now. Intermediad is still researching how authentication can be done on the implementation platform.

# Cache

Caching should be used whenever possible. This reduces server load significantly. It is recommended to at least use caching with a "Last Modified" header and / or ETag. This will reduce server time since the body does not have to be calculated, and since we only request the HEAD, bandwidth is also saved.

# Versioning

The API should be versioned in order to allow for multiple versions of the app. This way we can migrate to a new version without disabling functionality for users that have not updated yet. The versioning scheme should be relatively simple. For example: `/api/v1/actions`

The latest published version of the api can be retrieved using the `/api/{version}/about` call.

# Optional Global Call Parameters

## Paging

All requests can be done with the optional `page` and `page_size` parameter to specify pagination. `page` is the zero-based index of the page to retrieve and `page_size` the maximum number of items.

# General

## Get API status

Returns the version status of the API, consisting of the minimal supported version and the highest published version.

> Reponse

```json
{
  "latest_api_version": 4,
  "minimal_api_version": 1
}
```

### Request

`GET /api/{version}/about`


# User management

<aside class="warning">
User management will be specified when the authentication research has concluded.
</aside>

## Login

## Registration

## Password reset

# Actions

## Get All Actions

> Reponse

```json
{
  "meta": {
    "page": 0,
    "page_size": 30,
    "total_records": 4000
  },
  "actions": [
    {
      "id": 1,
      "title": "Zin van bewegen",
      "pillar": "EtenDrinken",
      "thumbnail": "http://example.com/example.jpg",
      "short_description": "Letterlijk tussen de zee- en binnenvaartschepen ontdek jij het Rotterdamse havengebied. De indrukwekkende skyline glijdt aan je voorbij. En dan werven, dokken en oneindig veel containers…",
      "is_user_wishlist_item": true,
      "has_user_consumed_action": true,
      "has_user_shared_experience": true,
      "start_date":"2015-12-09T19:33:00 +0000",
      "end_date": "2015-12-13T19:33:00 +0000",
      "offers": {
        "type": 1,
        "offer": [
          {
            "title": "Voor 5 euro naar de dierentuin",
            "percentage": 25.0,
            "amount": 23.0
          }
        ]
      },
      "partner": {
        "id": 1,
        "name": "Spido",
        "url": "http://spido.nl",
        "phone_number": "010 42984039",
        "email_address": "example@spido.nl",
        "street": "Botenlaan",
        "zipcode": "2343KJ",
        "street_number": "45",
        "region": "Rotterdam"
      },
      "locations": [
        {
          "id": 1,
          "title": "Ballenbak",
          "street": "Jan Luykenlaan",
          "zipcode": "2343KJ",
          "street_number": "8",
          "region": "Rotterdam",
          "latitude": 4.0000,
          "longitude": 51.0000
        }
      ]
    }
  ]
}
```
> The date is formatted following ISO 8601: `yyyy-MM-dd'T'HH:mm:ssZZZZZ`

> Offer.type is an enum: 1 = or, 2 = and

This returns the basic information for all the actions that are active right now.
<aside class="success">
Remember — The results of this call can also be changed by using one of the global call parameters.
</aside>

### Request

`GET /api/{version}/actions`

### Query Parameters

Parameter | Optional | Default | Description
--------- | -------- | ------- | -----------
latitude | true | - | If set, the actions are returned based on distance from the latitude & longitude. Only applies if both are set.
longitude | true | - | If set, the actions are returned based on distance from the latitude & longitude. Only applies if both are set.
filter | true | - | If set, the actions are filtered based on a predefined ruleset.
search | true | - | If set, results are returned based on fuzzy text matching. Ordered by match descending. Overrides all other sorting options.
meta | true | false | If set, the body will not contain the results but just the meta data for this call.


#### Filter

<aside class="warning">
Filters are still a work in progress and open for discussion.
</aside>

Example — Filters are based on an Enum e.g. "Eten & Drinken" = 1, "Zonder kinderen" = 2 and are sent as a comma seperated string.
These enums will translate to the "Pijler", Boolean flags of the "Acties", location regions and "Aanbiedingen" values.

`GET /api/{version}/actions?filter=1,3,6`

## Get Action

> Response

```json
{
  "id": 1,
  "title": "Zin van bewegen",
  "pillar": "EtenDrinken",
  "thumbnail": "http://example.com/example.jpg",
  "images":[
    "http://example.com",
    "http://example.com"
  ],
  "short_description": "Letterlijk tussen de zee- en binnenvaartschepen ontdek jij het Rotterdamse havengebied. De indrukwekkende skyline glijdt aan je voorbij. En dan werven, dokken en oneindig veel containers…",
  "availability_type": 1, (Inwisselbaarheid)
  "more_information_url": "http://example.com",
  "more_information_phone_number": "010 403948372",
  "more_information_email": "john@doe.com",
  "reservation_url": "http://example.com",
  "reservation_phone_number": "010 384839284",
  "reservation_email": "john@doe.com",
  "flags":{
    "is_indoors": true,
    "is_nice_weather": false,
    "is_bad_weather": false,
    "is_same_day_consumable": false,
    "is_child_friendly": true,
    "is_fun_without_children": false
  },
  "tags":[
    {
      "title": "waterpret"
    }
  ],
  "average_review": 4.6,
  "total_reviews": 4000,
  "is_user_wishlist_item": true,
  "has_user_consumed_action": true,
  "has_user_shared_experience": true,
  "start_date":"2015-12-09T19:33:00 +0000",
  "end_date": "2015-12-13T19:33:00 +0000",
  "offers": {
    "type": 1,
    "offer": [
      {
        "title": "Voor 5 euro naar de dierentuin",
        "percentage": 25.0,
        "amount": 23.0,
        "tariffs":[
          {
            "description": "volwassenen",
            "type": 1,
            "tariff": "€22,00 > €55,00",
            "minimum_age": 12,
            "maximum_age": 18
          }
        ]
      }
    ]
  },
  "partner": {
    "id": 1,
    "name": "Spido",
    "url": "http://spido.nl",
    "phone_number": "010 42984039",
    "email_address": "example@spido.nl",
    "street": "Botenlaan",
    "zipcode": "2343KJ",
    "street_number": "45",
    "region": "Rotterdam"
  },
  "locations": [
    {
      "id": 1,
      "title": "Ballenbak",
      "street": "Jan Luykenlaan",
      "zipcode": "2343KJ",
      "street_number": "8",
      "region": "Rotterdam",
      "latitude": 4.0000,
      "longitude": 51.0000
    }
  ]
}
```

### Request

`GET /api/{version}/actions/{id}`

### Query Parameters

Parameter | Optional | Default | Description
--------- | -------- | ------- | -----------
user_review | true | false | If set, only returns the review belonging to this user.

# Reviews

## Get reviews

> Response

```json
{
  "meta": {
    "page": 0,
    "page_size": 30,
    "total_records": 4000,
    "average_review": 5.4
  },
  "reviews": [
    {
      "id": 3,
      "author": "Robin Kruijt",
      "user_thumbnail": "http://example.com/example.jpg",
      "image_url": "http://example.com",
      "rating": 4.8,
      "title": "Prachtig uitzicht",
      "description": "Prachtig uitzicht, heerlijk eten.",
      "post_date": "2015-12-13T19:33:00 +0000",
      "comment_url": "http://example.com",
      "is_user_review": true
    }
  ]
}
```

Returns all reviews of an "Actie". Sorted by date added, descending.

### Request

`GET /api/{version}/actions/{id}/reviews`


## Post review

> Request

```json
{
  "title": "Hallo",
  "description": "Beste dag van mijn leven",
  "rating": 4.8
}
```
> Description is optional.

> Response

```json
{
  "id": 3,
  "author": "Robin Kruijt",
  "user_thumbnail": "http://example.com/example.jpg",
  "image_url": "",
  "rating": 4.8,
  "title": "Prachtig uitzicht",
  "description": "Prachtig uitzicht, heerlijk eten.",
  "post_date": "2015-12-13T19:33:00 +0000",
  "comment_url": "http://example.com",
  "is_user_review": true
}
```

Creates a review and returns it.

### Request

`POST /api/{version}/actions/{id}/reviews`


## Post review image

Creates an image belonging to the review.

### Request

A multipart request containing an image under fieldname "image".

`POST /api/{version}/actions/{id}/reviews/{id}/images`


# User

## Get current authenticated user

> Response

```json
{
  "id": 1,
  "pasnumber": 5,
  "username": "timpelgrim",
  "first_name": "Tim",
  "last_name": "Pelgrim",
  "user_thumbnail": "http://example.com/example.jpg",
  "currently_active": true,
  "amount_reviews": 16,
  "amount_actions_consumed": 12
}
```


Returns all data specific to the logged in user. Excluding wishlists, devices and savings.

### Request
`GET /api/{version}/users/me`

## Post Device id

> Request

```json
{
  "device_id": "ahasdjhdaskjd-asdjkaskjd-sad-asd-ascscx-zxc",
  "type": "android"
}
```
> Type can be "android" or "ios"

> Response

> A status code should be enough.


Register your device id for notifications.

### Request
`POST /api/{version}/users/devices`

## Get reviews

> Response

```json
{
  "meta": {
    "page": 0,
    "page_size": 30,
    "total_records": 4000,
    "average_review": 5.4
  },
  "reviews": [
    {
      "id": 3,
      "author": "Robin Kruijt",
      "user_thumbnail": "http://example.com/example.jpg",
      "image_url": "http://example.com",
      "rating": 4.8,
      "title": "Prachtig uitzicht",
      "description": "Prachtig uitzicht, heerlijk eten.",
      "post_date": "2015-12-13T19:33:00 +0000",
      "comment_url": "http://example.com",
      "is_user_review": true,
      "action": {
        "id": 1
      }
    }
  ]
}
```
> The action field includes just an ID, but could include more data if the view requires it. (To be discussed)

Returns all reviews for the current authenticated user.

### Request
`GET /api/{version}/users/reviews`

## Get settings

> Response

```json
{
  "notification_last_minute": true,
  "notification_review_invitation": true,
  "notification_personal_tips": true,
  "notification_action": true
}
```

Returns all settings for the current authenticated user.

### Request
`GET /api/{version}/users/settings`

## Update settings

> Request & Response

```json
{
  "notification_last_minute": true,
  "notification_review_invitation": true,
  "notification_personal_tips": true,
  "notification_action": true
}
```

Returns all settings for the current authenticated user.

### Request
`PUT /api/{version}/users/settings`

## Get savings

> Response

```json
{
  "meta": {
    "page": 0,
    "page_size": 30,
    "total_savings": 23.48
  },
  "savings":[
    {
      "id": 1,
      "date_of_use": "2015-12-09T19:33:00 +0000",
      "actual_savings": 12.30,
      "calculated_savings": 12.50,
      "action":{
        "id": 1,
        "title": "Zin van bewegen",
        "pillar": "EtenDrinken",
        "thumbnail": "http://example.com/example.jpg",
        "short_description": "Letterlijk tussen de zee- en binnenvaartschepen ontdek jij het Rotterdamse havengebied. De indrukwekkende skyline glijdt aan je voorbij. En dan werven, dokken en oneindig veel containers…",
        "is_user_wishlist_item": true,
        "has_user_consumed_action": true,
        "has_user_shared_experience": true,
        "start_date":"2015-12-09T19:33:00 +0000",
        "end_date": "2015-12-13T19:33:00 +0000",
        "offers": {
          "type": 1,
          "offer": [
            {
              "title": "Voor 5 euro naar de dierentuin",
              "percentage": 25.0,
              "amount": 23.0
            }
          ]
        },
        "partner": {
          "id": 1,
          "name": "Spido",
          "url": "http://spido.nl",
          "phone_number": "010 42984039",
          "email_address": "example@spido.nl",
          "street": "Botenlaan",
          "zipcode": "2343KJ",
          "street_number": "45",
          "region": "Rotterdam"
        },
        "locations": [
          {
            "id": 1,
            "title": "Ballenbak",
            "street": "Jan Luykenlaan",
            "zipcode": "2343KJ",
            "street_number": "8",
            "region": "Rotterdam",
            "latitude": 4.0000,
            "longitude": 51.0000
          }
        ]
      }
    }
  ]
}
```

Returns all savings for the current authenticated user.

### Request
`GET /api/{version}/users/savings`

## Add saving

A user can add a finished "Actie" and its savings manually.

> Request

```json
{
  "action_id": 1,
  "amount": 23.00
}
```

> Response

```json
{
  "id": 1,
  "date_of_use": "2015-12-09T19:33:00 +0000",
  "actual_savings": 23.00,
  "calculated_savings": 23.00,
  "action":{
    "id": 1,
    "title": "Zin van bewegen",
    "pillar": "EtenDrinken",
    "thumbnail": "http://example.com/example.jpg",
    "short_description": "Letterlijk tussen de zee- en binnenvaartschepen ontdek jij het Rotterdamse havengebied. De indrukwekkende skyline glijdt aan je voorbij. En dan werven, dokken en oneindig veel containers…",
    "is_user_wishlist_item": true,
    "has_user_consumed_action": true,
    "has_user_shared_experience": true,
    "start_date":"2015-12-09T19:33:00 +0000",
    "end_date": "2015-12-13T19:33:00 +0000",
    "offers": {
      "type": 1,
      "offer": [
        {
          "title": "Voor 5 euro naar de dierentuin",
          "percentage": 25.0,
          "amount": 23.0
        }
      ]
    },
    "partner": {
      "id": 1,
      "name": "Spido",
      "url": "http://spido.nl",
      "phone_number": "010 42984039",
      "email_address": "example@spido.nl",
      "street": "Botenlaan",
      "zipcode": "2343KJ",
      "street_number": "45",
      "region": "Rotterdam"
    },
    "locations": [
      {
        "id": 1,
        "title": "Ballenbak",
        "street": "Jan Luykenlaan",
        "zipcode": "2343KJ",
        "street_number": "8",
        "region": "Rotterdam",
        "latitude": 4.0000,
        "longitude": 51.0000
      }
    ]
  }
}
```


### Request
`POST /api/{version}/users/savings`

## Update saving

A user can update a finished "Actie" and its savings manually.

> Request

```json
{
  "action_id": 1,
  "amount": 23.00
}
```
> All fields are optional.

> Response

```json
{
  "id": 1,
  "date_of_use": "2015-12-09T19:33:00 +0000",
  "actual_savings": 23.00,
  "calculated_savings": 23.00,
  "action":{
    "id": 1,
    "title": "Zin van bewegen",
    "pillar": "EtenDrinken",
    "thumbnail": "http://example.com/example.jpg",
    "short_description": "Letterlijk tussen de zee- en binnenvaartschepen ontdek jij het Rotterdamse havengebied. De indrukwekkende skyline glijdt aan je voorbij. En dan werven, dokken en oneindig veel containers…",
    "is_user_wishlist_item": true,
    "has_user_consumed_action": true,
    "has_user_shared_experience": true,
    "start_date":"2015-12-09T19:33:00 +0000",
    "end_date": "2015-12-13T19:33:00 +0000",
    "offers": {
      "type": 1,
      "offer": [
        {
          "title": "Voor 5 euro naar de dierentuin",
          "percentage": 25.0,
          "amount": 23.0
        }
      ]
    },
    "partner": {
      "id": 1,
      "name": "Spido",
      "url": "http://spido.nl",
      "phone_number": "010 42984039",
      "email_address": "example@spido.nl",
      "street": "Botenlaan",
      "zipcode": "2343KJ",
      "street_number": "45",
      "region": "Rotterdam"
    },
    "locations": [
      {
        "id": 1,
        "title": "Ballenbak",
        "street": "Jan Luykenlaan",
        "zipcode": "2343KJ",
        "street_number": "8",
        "region": "Rotterdam",
        "latitude": 4.0000,
        "longitude": 51.0000
      }
    ]
  }
}
```


### Request
`POST /api/{version}/users/savings`


## Delete saving

A user can delete a finished "Actie" and its savings manually.


### Request
`DELETE /api/{version}/users/savings/{id}`

# Wishlists

## Get Wishlists

> Response

```json
{
  "meta": {
    "page": 0,
    "page_size": 30,
    "total_records": 1200
  },
  "wishlists":[
    {
      "id": 1,
      "title": "Zomervakantie",
      "actions": [
        {
          "id": 1,
          "title": "Zin van bewegen",
          "pillar": "EtenDrinken",
          "thumbnail": "http://example.com/example.jpg",
          "short_description": "Letterlijk tussen de zee- en binnenvaartschepen ontdek jij het Rotterdamse havengebied. De indrukwekkende skyline glijdt aan je voorbij. En dan werven, dokken en oneindig veel containers…",
          "is_user_wishlist_item": true,
          "has_user_consumed_action": true,
          "has_user_shared_experience": true,
          "start_date":"2015-12-09T19:33:00 +0000",
          "end_date": "2015-12-13T19:33:00 +0000",
          "offers": {
            "type": 1,
            "offer": [
              {
                "title": "Voor 5 euro naar de dierentuin",
                "percentage": 25.0,
                "amount": 23.0
              }
            ]
          },
          "partner": {
            "id": 1,
            "name": "Spido",
            "url": "http://spido.nl",
            "phone_number": "010 42984039",
            "email_address": "example@spido.nl",
            "street": "Botenlaan",
            "zipcode": "2343KJ",
            "street_number": "45",
            "region": "Rotterdam"
          },
          "locations": [
            {
              "id": 1,
              "title": "Ballenbak",
              "street": "Jan Luykenlaan",
              "zipcode": "2343KJ",
              "street_number": "8",
              "region": "Rotterdam",
              "latitude": 4.0000,
              "longitude": 51.0000
            }
          ]
        }
      ]
    }
  ]
}

```

Returns all wishlists including a few of their actions.

### Request

`GET /api/{versions}/users/wishlists`

### Query Parameters

Parameter | Optional | Default | Description
--------- | -------- | ------- | -----------
amount_actions | true | 3 | If set, returns this amount of actions per wishlist.


## Get Wishlist

> Response

```json
{
  "id": 1,
  "title": "Zomervakantie",
  "actions": [
    {
      "id": 1,
      "title": "Zin van bewegen",
      "pillar": "EtenDrinken",
      "thumbnail": "http://example.com/example.jpg",
      "short_description": "Letterlijk tussen de zee- en binnenvaartschepen ontdek jij het Rotterdamse havengebied. De indrukwekkende skyline glijdt aan je voorbij. En dan werven, dokken en oneindig veel containers…",
      "is_user_wishlist_item": true,
      "has_user_consumed_action": true,
      "has_user_shared_experience": true,
      "start_date":"2015-12-09T19:33:00 +0000",
      "end_date": "2015-12-13T19:33:00 +0000",
      "offers": {
        "type": 1,
        "offer": [
          {
            "title": "Voor 5 euro naar de dierentuin",
            "percentage": 25.0,
            "amount": 23.0
          }
        ]
      },
      "partner": {
        "id": 1,
        "name": "Spido",
        "url": "http://spido.nl",
        "phone_number": "010 42984039",
        "email_address": "example@spido.nl",
        "street": "Botenlaan",
        "zipcode": "2343KJ",
        "street_number": "45",
        "region": "Rotterdam"
      },
      "locations": [
        {
          "id": 1,
          "title": "Ballenbak",
          "street": "Jan Luykenlaan",
          "zipcode": "2343KJ",
          "street_number": "8",
          "region": "Rotterdam",
          "latitude": 4.0000,
          "longitude": 51.0000
        }
      ]
    }
  ]
}
```

Returns a specific wislist and all of its actions.

### Request
`GET /api/{versions}/users/wishlists/{id}`

## Post Wishlist

> Request

```json
{
  "name": "Zomervakantie"
}
```

> Response

```json
{
  "id": 1,
  "title": "Zomervakantie",
  "actions": []
}
```

Creates a new wishlist.

### Request
`POST /api/{versions}/users/wishlists`

## Update Wishlist

> Request

```json
{
  "name": "Zomervakantie"
}
```

> Response

```json
{
  "id": 1,
  "title": "Zomervakantie",
  "actions": []
}
```

Updates the wishlist and its parameters. 

### Request
`PUT /api/{versions}/users/wishlists/{id}`

## Add action to wishlist

```json
{
  "action_id": 1
}
```
Adds an action to a wishlist

### Request

`POST /api/{versions}/users/wishlists/{id}`

## Delete Wishlist

Deletes an authenticated users wishlist.

### Request
`DELETE /api/{versions}/users/wishlists/{id}`

## Remove action from wishlist

```json
{
  "action_id": 1
}
```

Removes an action from a wishlist

### Request

`DELETE /api/{versions}/users/wishlists/{id}`