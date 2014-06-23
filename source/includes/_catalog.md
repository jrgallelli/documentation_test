# Catalog Management and Promotion

## Overview

Selling any product (physical goods, downloadable software, video, virtual goods, etc.) in a store involves two fundamental concepts: Item and Offer.

### Item: what to sell

Item describes the contents and technical aspects of the product to be sold, such as app description, screen shoots, system requirement, platform, version number, etc.

### Offer: how to sell

After an item is created, an offer can be created to describe the marketing aspects of the product, such as prices, bundles, promotions, markets, etc.

### Catalog Reviews

Before a product can be released to the public, rigorous reviews have to be performed by store administrators to ensure quality of the product.  Both items and offers need to go through the reviews.

Following is the life cycle diagram of an item or offer object.  A developer or publisher creates a "Draft" and submits it to the store admin for  "PendingReview".   Admin decides whether to approve or reject the item/offer.  Once the state becomes "Approved", the object is ready to be sold. 

![Offer State Diagram](offer_state.png)
