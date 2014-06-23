# Catalog Management and Promotion

## Overview

Selling any product (physical goods, downloadable software, video, virtual goods, etc.) in a store involves two fundamental concepts: Item and Offer.

### Item: what to sell

Item describes the contents and technical aspects of the product to be sold, such as app description, screen shoots, system requirement, platform, version number, etc.

### Offer: how to sell

After an item is created, an offer can be created to describe the marketing aspects of the product, such as prices, bundles, promotions, markets, etc.

### Catalog Reviews

Before a product can be released to the public, rigorous reviews have to be performed by store administrators to ensure quality of the product.  Both items and offers need to go through the reviews.

Following is the life cycle diagram of an item or offer object.  A developer or publisher creates a **Draft** and submits it to the store admin for  **PendingReview**.   Admin decides whether to approve or reject the item/offer.  Once the state becomes **Approved**, the object is ready to be sold. 

![Offer State Diagram](offer_state.png)

### Offer Bundles

An offer can be associated with 0 to many offers, and 1 to many items. As a result, on the UI, the developers can create a new offer that "bundles" multiple existing offers together. For example, the following "All-in-one-Pack" offer bundled an "Educational Pack offer", an "Fun Pack offer" and one other offer and item in one offer. 

![Offer Bundles Diagram](offer_bundles.png)

## Basic offer creation/publish flow

![Offer Flow Diagram](offer_flow.png)

### Create/Update and Publish an Item

Step 1. Developer creates an item

#### Sample Request

```http
Method POST
Uri http://api.silkcloud.com/items
Body
          {
            "type": "APP",
            "name": "demo item",
            "ownerId": {"href": "https://api.silkcloud.com/v1/users/3234", "id": "3234"},
            "skus":
            [
                {
                    "id": 1,
                    "externalSku": "abc-1",
                    "inventory": 100
                },
                {
                    "id": 2,
                    "externalSku": "abc-2",
                    "inventory": 10
                }
            ],
 
             "properties":
            {
                "mainImage": "the img url"
            }
        }
```
  
#### Sample Response
  
```http
Status Code 200
 
Body
   {
    "self": {
        "href": "http://api.silkcloud.com/v1/items/1393402721479",
        "id": "1393402721479"
    },
    "revision": 1,
    "name": "demo item",
    "status": "DRAFT",
    "type": "APP",
    "ownerId": {
        "href": "http://api.silkcloud.com/v1/items/3234",
        "id": "3234"
    },
    "skus": [
        {
            "id": 1,
            "externalSku": "abc-1",
            "inventory": 100
        },
        {
            "id": 2,
            "externalSku": "abc-2",
            "inventory": 10
        }
    ],
    "properties": {
        "mainImage": "the img url"
    }
}
```

Step 2. Developer may update the item (the status of the item will stay as "Design")

#### Sample Request

```http
Method PUT
 
Uri http://api.silkcloud.com/items/123
 
Body
 
        {
            "name": "demo item 123",
        }
 
Sample response
Status Code 200
 
Body
           {
            "self": {"href": "https://data.silkcloud.com/v1/apps/item-123", "id": "item-123"},
            "type": "APP",
            "name": "demo item 123",
            "revision": 2,
            “status”: “DRAFT”,
            "ownerId": {"href": "https://data.silkcloud.com/v1/apps/3234", "id": "3234"},
            "skus":
            {
                {
                    "id": 1,
                    "externalSku": "abc-1",
                    "inventory": 100
                },
                {
                    "id": 2,
                    "externalSku": "abc-2",
                    "inventory": 10
                }
            }
 
             "properties":
            {
                "mainImage": "the img url"
            }
 
             "createdTime": "2013-01-01T01:32:53Z",
            "createdBy": {"href": "https://data.silkcloud.com/v1/users/3234", "id": "3234"},
            "updatedTime": "2013-01-01T01:35:53Z",
            "updatedBy": {"href": "https://data.silkcloud.com/v1/users/3234", "id": "3234"}
        }
        
  
