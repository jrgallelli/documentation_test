---
title: API Reference
language_tabs: 
  - shell
  - ruby
  - python
toc_footers: 
  - "<a href='#'>Sign Up for a Developer Key</a>"
  - "<a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>"
includes: 
  - errors
search: true
published: true
---

# Country

The country resource provides metadata for each country.

Field | Type | Description
---------- | ---------------- | --------------------------------
self | Country | The link of the country resource
countryCode | string | The country code of the country resource. It equals to self.id
defaultLocale | string | The locale that the country should use by default
defaultCurrency | string | The currency that the country should use by default
subCountries | map string,LocalizeKey | JSON object that maps from a code for the sub country (state, province, etc.) to a JSON object that contains the localization keys for the sub country's short and long names,
supportedLocales | array string | Array for supported locales to the locales appropriate for this country
localeKeys | LocalizeKey | Localizable properties and the corresponding key value to lookup in translation service.
futureExpansion | map string, any | Optional. Used to add properties between major API revisions. In next major API version, all these properties will be refactored into the main body and futureExpansion will again be empty.
rev | string | An opaque string that represents the state of the resource. Used for optimistic concurrency control.
createdTime | string | The created datetime of the resource - must be ISO 8601.
updatedTime | string | he updated datetime of the resource - must be ISO 8601.

# Sample URI
http://localhost:8080/v1/countries

# Supported Methods

## GET /countries/{id}

> This command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Isis",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint gets a specific country.

### HTTP Request

`GET http://localhost:8080/v1/countries/{id}`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

## GET /countries

This endpoint lists all of the countries.

### HTTP Request

`GET http://localhost:8080/v1/countries`

### Query Parameters

Parameter | Type | Description
--------- | ------- | -----------
defaultCurrencyId | string | Filter country by defaultCurrencyId
cursor | string | The paging cursor of the search result. Used in the "next" field in results to fetch next page.
count | integer | ￼￼The page size of the search result.

## PUT /countries/{id}

Updates the country.

<aside class="warning">Internal use only.</aside>

## POST /countries

Creates a new country.

<aside class="warning">Internal use only.</aside>

## DELETE /countries/{id}

Deletes a country.

<aside class="warning">Internal use only.</aside>

# Authentication

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import 'kittn'

api = Kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace `meowmeowmeow` with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import 'kittn'

api = Kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Isis",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import 'kittn'

api = Kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/3"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Isis",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">If you're not using an administrator API key, note that some kittens will return 403 Forbidden if they are hidden for admins only.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the cat to retrieve