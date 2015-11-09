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

All webservices's have a basic authentication. In Acceptatie the credentials are:
```
login name: wsrest2
password: Intermediad!2
```
Authentication in Productie will follow after testing.

# Cache

Caching should be used whenever possible. This reduces server load significantly. It is recommended to at least use caching with a "Last Modified" header and / or ETag. This will reduce server time since the body does not have to be calculated, and since we only request the HEAD, bandwidth is also saved.

# Versioning

The version is determined by the version number that is passed as a parameter in the request.

The API should be versioned in order to allow for multiple versions of the app. This way we can migrate to a new version without disabling functionality for users that have not updated yet. The versioning scheme should be relatively simple. For example: `/api/v1/actions`

The latest published version of the api can be retrieved using the `/api/{version}/about` call.

# Optional Global Call Parameters

## Paging

All requests can be done with the optional `page` and `per_page` parameter to specify pagination. `page` is the zero-based index of the page to retrieve and `per_page` the maximum number of items. Pagination must be passed as a parameter

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

### Get Login

Checks the account name and password.

```
Request endpoint Acceptatie:
GET https://rotterdampas-acc.passcloud.nl/rest/getlogin/
Request endpoint Productie:
GET https://rotterdampas.passcloud.nl/rest/getlogin/
```

```
Authentication in Acceptatie: Basic authentication with a rest user.
username: wsrest2
password: Intermediad!2
```

```
Authentication in Productie will follow after testing.
```

### Parameters

Parameter | Type | Optional | Default | Description
--------- | ---- | -------- | ------- | -----------
pass_owner_code | string | verplicht | - | The code of the 'Organization'.
api_version | float | verplicht | - | The version number of the API.
pass_type_number | integer | verplicht | - | The number of the 'PasSoort'.
account_login | string | verplicht | - | The inlog name of the Pashouder.
account_password | string | verplicht | - | The password of the Pashouder.

> Response: Only returns a status code.

### Status code

Code | Description
---- | -----------
400 | One or more mandatory parameters are empty.
400 | Can't find the PasSoort.
401 | Can't find the organization.
401 | Wrong values in the basic authentication.
401 | Can't verify Pashouder.
404 | Can't find the Pashouder.
500 | No values in the basic authentication.
200 | Everything is ok.


## Registration

Includes all the user info like name, e-mail adress and pass number.

### PutRegister

Activate a Pashouder with a Pass.

#### Request

```
Request endpoint Acceptatie: 
PUT https://rotterdampas-acc.passcloud.nl/rest/putregister/
Request endpoint Productie: 
PUT https://rotterdampas.passcloud.nl/rest/putregister/
```

```
Authentication in Acceptatie: Basic authentication with a rest user.
username: wsrest2
password: Intermediad!2
```

```
Authentication in Productie will follow after testing.
```

#### Parameters

Parameter | Type | Optional | Default | Description
--------- | ---- | -------- | ------- | -----------
pass_owner_code | string | false | - | The code of the 'Organization'.
api_version | float | false | - | The version number of the API.
pass_type_number | integer | false | - | The number of the 'PasSoort'.
account_password | string | false | - | A password for login.
email_address | string | false | - | The emailaddress of the 'Pashouder'. This will be the login name.
birthdate | string | false | - | The date of birth of the 'Pashouder'.
passnumber | long | false | - | The number of the pass that belongs to the 'Pashouder'.


> Response

```
The response will only return a status code.
```

#### Status code

Code | Description
---- | -----------
400 | One or more mandatory parameters are empty.
400 | Can't find the 'PasSoort'.
400 | Error while trying to parse birthdate.
400 | Error while trying to change the password.
401 | Can't find the 'organization' of the pass.
401 | Wrong values in the basic authentication
404 | Can't find a Pashouder with the input parameters.
404 | Can't find a Pas with the 'passnumber' parameter.
500 | No values in the basic authentication.
200 | Everything is ok.




## Update user

Includes all the user info like name, e-mail adress and pass number.

## Update Password

### PutChangePassword

Changes the users password.

### Request

```
Request endpoint Acceptatie: 
PUT https://rotterdampas-acc.passcloud.nl/rest/putchangepassword/
Request endpoint Productie: 
PUT https://rotterdampas.passcloud.nl/rest/putchangepassword/
```

```
Authentication in Acceptatie: Basic authentication with a rest user.
username: wsrest2
password: Intermediad!2
```

```
Authentication in Productie will follow after testing.
```

### Parameters

Parameter | Type | Optional | Default | Description
--------- | ---- | -------- | ------- | -----------
pass_owner_code | string | false | - | The code of the 'Organization'.
api_version | float | false | - | The version number of the API.
pass_type_number | integer | false | - | The number of the 'PasSoort'.
account_password | string | false | - | The old password for login.
account_login | string | false | - | The inlog name of the Pashouder. Is the users email address.
new_password | string | false | - | A new password for login.
passnumber | long | false | - | The number of the pass that belongs to the 'Pashouder'.


> Response

```
The response will only return a status code.
```

### Status code

Code | Description
---- | -----------
400 | One or more mandatory parameters are empty.
400 | Can't find the 'PasSoort'.
400 | An error has occured while changing the password.
401 | Can't find the 'organization' of the pass.
401 | Wrong values in the basic authentication
401 | An error has occured while checking the old password.
404 | Can't find a Pashouder with the input parameters.
500 | No values in the basic authentication.
200 | Everything is ok.



## Forgot Password

### PutForgotPassword

Generates a temporary password and return the value.

### Request

```
Request endpoint Acceptatie: 
PUT https://rotterdampas-acc.passcloud.nl/rest/putforgotpassword/
Request endpoint Productie: 
PUT https://rotterdampas.passcloud.nl/rest/putforgotpassword/
```

```
Authentication in Acceptatie: Basic authentication with a rest user.
username: wsrest2
password: Intermediad!2
```

### Parameters

Parameter | Type | Optional | Default | Description
--------- | ---- | -------- | ------- | -----------
pass_owner_code | string | false | - | The code of the 'Organization'.
api_version | float | false | - | The version number of the API.
pass_type_number | integer | false | - | The number of the 'PasSoort'.
account_login | string | false | - | The inlog name of the Pashouder. Is the users email address.
birthdate | string | false | - | The date of birth of the 'Pashouder'.
passnumber | long | false | - | The number of the pass that belongs to the 'Pashouder'.

> Response

```json
{
  "temp_password": "TG4FmqDtkvZV"
}
```


### Status code

Code | Description
---- | -----------
400 | One or more mandatory parameters are empty.
400 | Can't find the 'PasSoort'.
400 | An error has occured while changing the password.
401 | Can't find the 'organization' of the pass.
404 | Can't find a Pashouder with the input parameters.
500 | Internal server error while creating a temporary 'password'.
200 | Everything is ok.



## Update user photo

Posts an image belonging to the user.

### Request

A multipart request containing an image under fieldname "image".

`POST /api/{version}/users/me/images`

## Get settings

> Response

```json
{
  "notification_last_minute": true,
  "notification_review_invitation": true,
  "notification_personal_tips": true,
  "notification_action": true,
  "email_last_minute": true,
  "email_review_invitation": true,
  "email_personal_tips": true,
  "email_partner_reaction": true,
  "email_pass_information": true,
  "preferences" : {
      "1": 0.0,
      "2": -1.0,
      "3": 1.0
  }
}
```
> preferences are based on the pillar enum and a floating point value from -1.0 to 1.0.

Returns all settings for the current authenticated user.

### Request
`GET /api/{version}/users/me/settings`

## Update settings

> Request & Response

```json
{
  "notification_last_minute": true,
  "notification_review_invitation": true,
  "notification_personal_tips": true,
  "notification_action": true,
  "email_last_minute": true,
  "email_review_invitation": true,
  "email_personal_tips": true,
  "email_partner_reaction": true,
  "email_pass_information": true,
  "preferences" : {
      "1": 0.0,
      "2": -1.0,
      "3": 1.0
  }
}
```
> preferences are based on the pillar enum and a floating point value from -1.0 to 1.0. 

Update settings for the current authenticated user.

### Request
`PUT /api/{version}/users/me/settings`


## Add Device id

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
`POST /api/{version}/users/me/devices`

# Actions

## Get All Actions

```
Request endpoint Acceptatie:
GET https://rotterdampas-acc.passcloud.nl/rest/getallactions/
Request endpoint Productie:
GET https://rotterdampas.passcloud.nl/rest/getallactions/
```

```
Authentication in Acceptatie: Basic authentication with a rest user.
username: wsrest2
password: Intermediad!2
```

```
Authentication in Productie will follow after testing. 
```

> Response

```json
{
  "page_size": 1,
  "meta_all_actions": [{
    "type_": "Standaard",
    "has_user_shared_experience": false,
    "all_actions_locations": [ 	{
		"id_": 545,
		"title": "Amsterdam",
		"street": "straat",
		"zipcode": "4444 ZZ",
		"street_number": 44,
		"region": "Zuid-Holland",
		"latitude": 999999,
		"longitude": 999999,
	}],
    "all_actions_offers": [ 	{
		"title": "Titel"
		"percentage": 4.75,
		"amount": 7.95,
	}],
    "title": "\"gratis toegang tot het Allard Pierson Museum voor Stadspashouders van 4 t/m 16 jaar\"",
    "end_date": 1451602799000,
    "distance": 9.99999999E8,
    "thumbnail": "http://example.com/example.jpg",
    "all_actions_partners": [{
      "region": "Amsterdam",
      "phone_number": "020 525 25 56",
      "zipcode": "1012 GC",
      "street": "Oude Turfmarkt",
      "name": "Allard Pierson Museum",
      "street_number": "127",
      "id_": 100014,
      "url": "http://www.allardpiersonmuseum.nl",
      "email_address": "allard.pierson.museum@uva.nl"
    }],
    "action_type": "A - Jaarkorting",
    "short_description": "kids 4- 16 jaar",
    "pillar": "Cultuur",
    "id_": 30281,
    "has_user_consumed_action": false,
    "is_user_wishlist_item": false,
    "start_date": 1422745200000
  }],
  "page": 1,
  "total_records": 13
}
```

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

This returns the basic information for all the actions that are active right now ordered by `end_date` ascending.
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
partner_id | true | - | If set, results are constrained to this partner id.
meta | true | false | If set, the body will not contain the results but just the meta data for this call.
monthly | true | false | If set, the results are constrained to just the "monthly" actions.
recommended | true | false | If set, constrain the result to actions recommended to the logged in user. 


### Filter

<aside class="warning">
Filters are still a work in progress and open for discussion.
</aside>

Example — Filters are based on an Enum e.g. "Eten & Drinken" = 1, "Zonder kinderen" = 2 and are sent as a comma seperated string.
These enums will translate to the "Pijler", Boolean flags of the "Acties", location regions and "Aanbiedingen" values.


`GET /api/{version}/actions?filter=1,3,6`

### Parameters

Parameter | Type | Optional | Default | Description
--------- | ---- | -------- | ------- | -----------
pass_owner_code | string | false | - | The code of the 'Organization'.
api_version | float | false | - | The version number of the API.
pass_type_number | integer | false | - | The number of the 'PasSoort'.
account_password | string | false | -	| The password of the Pashouder.
account_login | string | false | - | The inlog name of the Pashouder.
page | integer | false | - | The result page number.
per_page | integer | false | - | The count of the results per page.
meta | boolean | true | false | If set, the body will not contain the results but just the meta data for this call.
pillar | string | true | - | Specifies the type of actions.
latitude | float | true | - | If set, the actions are returned based on distance from the latitude & longitude. Only applies if both are set.
longitude | float | true | - | If set, the actions are returned based on distance from the latitude & longitude. Only applies if both are set.
partner_id | integer | true | - | If set, results are constrained to this partner id.
action_type | string | true | - | If set, results are constrained to this action type.


### Status code

Code | Description
---- | -----------
400 | One or more mandatory parameters are empty.
400 | Can't find the 'PasSoort'.
401 | Can't find the 'organization' of the pass.
401 | Wrong values in the basic authentication
404 | Can't find any action with these parameters.
500 | No values in the basic authentication.
200 | Everything is ok.


## Get Action

Returns a specific action.

```
Request endpoint Acceptatie:
GET https://rotterdampas-acc.passcloud.nl/rest/getaction/
Request endpoint Productie:
GET https://rotterdampas.passcloud.nl/rest/getaction/
```

```
Authentication in Acceptatie: Basic authentication with a rest user.
username: wsrest2
password: Intermediad!2
```

```
Authentication in Productie will follow after testing.
```

> Response

```json
{
  "type_": "Standaard",
  "reservation_phone_number": "0172-444705",
  "action_locations": [ 	{
	"id_": 545,
	"title": "Amsterdam",
	"street": "straat",
	"zipcode": "4444 ZZ",
	"street_number": 44,
	"region": "Zuid-Holland",
	"latitude": 999999,
	"longitude": 999999,
  }],
  "has_user_shared_experience": false,
  "action_partners": [{
    "region": "Amsterdam",
    "phone_number": "020 763 0599",
    "zipcode": "1082 ME",
    "street": "G. Mahlerlaan",
    "name": "Amsterdam Expo BV",
    "street_number": "106",
    "id_": 10234,
    "url": "http://www.amsterdamexpo.nl",
    "email_address": "arnold.vandewater@terminal1.nl"
  }],
  "action_tags": [	{
	"title": "Titel Tag"
  }],
  "action_flags": [{
    "is_same_day_consumable": false,
    "is_indoors": false,
    "is_child_friendly": false,
    "is_fun_without_children": false,
    "is_bad_weather": false,
    "is_nice_weather": false
  }],
  "more_information_phone_number": "0172-444705",
  "action_offers": [ 	{
	"title": "Titel"
	"percentage": 4.75,
	"amount": 7.95,
	"get_action_tariffs": [{
		"description": "omschrijving tarief",
		"tariff_original": 50.50,
		"tariff_dicount": 10.50,
		"minimum_age": 18,
		"maximum_age": 65,			
  }]}]
  "title": "Stadspas Aanbieding 100% Korting",
  "end_date": 1441058399000,
  "short_description": "Stadspasje Aanb. A",
  "availability_type": 2,
  "reservation_url": "http://www.example.com/",
  "total_reviews": 3,
  "action_images": [	{
	"cdn_url": "http://www.example.com/example.jpg"
  }],
  "more_information_email": ,
  "thumbnail": "http://example.com/example.jpg",
  "average_review": 5.433333333333334,
  "pillar": "Cultuur",
  "id_": 30274,
  "reservation_email": "test@testen.nl",
  "more_information_url": "http://www.example.com/",
  "is_user_wishlist_item": false,
  "has_user_consumed_action": false,
  "start_date": 1422745200000
}
```

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

### Parameters

Parameter | Type | Optional | Default | Description
--------- | ---- | -------- | ------- | -----------
pass_owner_code | string | false | - | The code of the 'Organization'.
api_version | float | false | - | The version number of the API.
pass_type_number | integer | false | - | The number of the 'PasSoort'.
account_password | string | false | - | The password of the Pashouder.
account_login | string | false | - | The inlog name of the Pashouder.
user_review | boolean | true | false | If set, only returns the review belonging to this user.
id_ | integer | false | - | ID number of the specific action.


### Status code

Code | Description
---- | -----------
400 | One or more mandatory parameters are empty.
400 | Can't find the 'PasSoort'.
401 | Can't find the 'organization' of the pass.
401 |	Wrong values in the basic authentication
404 | Can't find any action with the 'id_'.
500 | No values in the basic authentication.
200 | Everything is ok.


## Get Related Actions

Returns all actions related to this actions based on tags and usage.

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
### Request

`GET /api/{version}/actions/{id}/related`

### Query Parameters

Parameter | Optional | Default | Description
--------- | -------- | ------- | -----------
meta | true | false | If set, the body will not contain the results but just the meta data for this call.

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


## Add review

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


## Add review image

Creates an image belonging to the review.

### Request

A multipart request containing an image under fieldname "image".

`POST /api/{version}/actions/{id}/reviews/{id}/images`

## Delete review image

Deletes an image belonging to the review.

### Request

`DELETE /api/{version}/actions/{id}/reviews/{id}/images`

## Update Review

Updates the users review.

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

### Request

`PUT /api/{version}/actions/{id}/reviews/{id}`

## Delete Review

### Request

`DELETE /api/{version}/actions/{id}/reviews/{id}`

# User

## Get actions

Returns the actions consumed by the current user ordered by consumption date.

> Reponse

```json
{
  "meta": {
    "page": 0,
    "page_size": 30,
    "total_records": 100
  },
  "actions": [
    {
      "id": 1,
      "title": "Zin van bewegen",
      "pillar": "EtenDrinken",
      "thumbnail": "http://example.com/example.jpg",
      "short_description": "Letterlijk tussen de zee- en binnenvaartschepen ontdek jij het Rotterdamse havengebied. De indrukwekkende skyline glijdt aan je voorbij. En dan werven, dokken en oneindig veel containers…",
      "is_user_wishlist_item": true,
      "consumption_date": "2015-12-09T19:33:00 +0000",
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
### Request

`GET /api/{version}/users/me/actions`

### Query Parameters

Parameter | Optional | Default | Description
--------- | -------- | ------- | -----------
meta | true | false | If set, the body will not contain the results but just the meta data for this call. 
include_family | true | false | If set, the result will include the actions consumed by the users family. 
year | true | - | If included, the results will constrained to the specific "pass year".

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
`GET /api/{version}/users/me/reviews`

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
`GET /api/{version}/users/me/savings`

### Query parameters
Parameter | Optional | Default | Description
--------- | -------- | ------- | -----------
include_family | true | false | If set, the result will include the savings by the users family.

## Add saving

A user can add a finished "Actie" and its savings manually.

> Request

```json
{
  "action_id": 1,
  "amount": 23.00,
  "date_of_use": "2015-12-09T19:33:00 +0000"
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
`POST /api/{version}/users/me/savings`

## Update saving

A user can update a finished "Actie" and its savings manually.

> Request

```json
{
  "action_id": 1,
  "amount": 23.00,
  "date_of_use": "2015-12-09T19:33:00 +0000"
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
`PUT /api/{version}/users/me/savings`


## Delete saving

A user can delete a finished "Actie" and its savings manually.


### Request
`DELETE /api/{version}/users/me/savings/{id}`

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

`GET /api/{versions}/users/me/wishlists`

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
`GET /api/{versions}/users/me/wishlists/{id}`

## Add Wishlist

> Request

```json
{
  "title": "Zomervakantie"
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
`POST /api/{versions}/users/me/wishlists`

## Update Wishlist

> Request

```json
{
  "title": "Zomervakantie"
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
`PUT /api/{versions}/users/me/wishlists/{id}`

## Add action to wishlist

> Request

```json
{
  "action_id": 1
}
```
Adds an action to a wishlist

### Request

`POST /api/{versions}/users/me/wishlists/{id}`

## Delete Wishlist

Deletes an authenticated users wishlist.

### Request
`DELETE /api/{versions}/users/wishlists/{id}`

## Remove action from wishlist

> Request

```json
{
  "action_id": 1
}
```

Removes an action from a wishlist

### Request

`DELETE /api/{versions}/users/me/wishlists/{id}`

# Passes

## Get Passes

Returns all the passes of the current user.


### Request
 
`GET /api/{version}/users/me/passes`
 
### Query Parameters

Parameter | Optional | Default | Description
--------- | -------- | ------- | -----------
include_family | true | false | If set, includes the passes of the users family.

> Response

```json
{
  "meta": {
    "page": 0,
    "page_size": 20,
    "total_records": 5
  },
  "passes":[
    {
      "id": 1,
      "owner_name": "N. de Vries",
      "number": 086951,
      "image": "wwww.example.com/image.png",
      "active": true,
      "end_date": "2015-12-13T19:33:00 +0000"
    }
  ]
}
```
