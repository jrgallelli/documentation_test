---
title: API Reference
language_tabs: 
  - shell
  - ruby
  - python
search: true
published: true
---

# Locale

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
{
  "self": {
    "href": "/v1/countries/US",
     "id": "US" 
  },
  "countryCode": "US",
  "defaultLocale": "en_US",
  "defaultCurrency": "USD",
  "subCountries": {
    "NY": {
      "shortNameKey": "US_NY_SHORT",
      "longNameKey": "US_NY_LONG"
    },
    "CA": {
      "shortNameKey": "US_CA_SHORT",
      "longNameKey": "US_CA_LONG"
    }
  },
  "supportedLocaes": [ 
    "en_US",
    "fr_US"
  ],
  "localeKeys": { 
     "shortNameKey": "US_SHORT",
     "longNameKey": "US_LONG"
  },
  "futureExpansion": {},
  "rev": "k930dkd03kdk3k3k5",
  "createdTime": "2013-01-01T01:32:53Z",
  "updatedTime": "2013-01-01T01:32:53Z"
}  
```

This endpoint gets a specific country.

### HTTP Request

`GET http://localhost:8080/v1/countries/{id}`

### URL Parameters

Parameter | Type | Description
--------- | ------- | -----------
id | string | The id of country

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