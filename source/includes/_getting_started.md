# Getting Started

## Determine the Environment

There are two main environments in the commerce system:

- Sandbox Environment. This environment is for integration testing. Testers can try the APIs in the sandbox environment. No real transactions will happen by calling to the sandbox environment. The data in the sandbox environment is fake/mock data.
- Production Environment. This environment is for the production. Any call to this environment is a real transaction.

To get started, you can use Sandbox Environment. The URLs for the environments are listed below:

Environment | URL
------------ | ---------------
Sandbox | TBD https://apis.sandbox.silkcloud.com/
Production | TBD https://apis.silkcloud.com/  

## Obtaining the App Client

The commerce platform uses the OAuth 2.0 as the authorization protocol. In OAuth 2.0, any code calling the APIs are called a client. Clients must be registered first in order to make an API call. After registering, the client gets back their OAuth client ID and client secret. (TBD: Identify client using certificate)
You can start with the following test client ID for sandbox environment while applying for your new client ID. This test client ID has all normal permissions to all APIs. After obtaining the client ID for your application, please switch to validate the permissions you get can still make the calls.

Test Client ID | Client Secret
--------------- | -------------
test_csr | weakClientSecret123
test_first_party_backend | weakClientSecret123
test_first_party_client | weakClientSecret123
test_first_party_server | weakClientSecret123
test_super_client | weakClientSecret123
test_third_party_backend | weakClientSecret123
test_third_party_client | weakClientSecret123
test_third_party_server | weakClientSecret123

The difference of first-party vs third-party is TBD. We’ll first let first-party call almost all APIs to enable implementing the single-page frontend web applications.

To get your own application client ID, refer to the following section: TBD

## Making API Calls

In the getting started section, we will demonstrate how to get the user information. We will use the test_first_party_client as the OAuth client ID.

Note: This section assumes the user login portal is already available. How to write a login portal is covered in TBD.

## Sign-in the User

Let the user sign-in and get the OAuth authorization code.

```bash
curl -X POST --max-redirs 0 \
     "$baseUri/oauth2/authorize?client_id=test_first_party_client&redirect_uri=http://localhost/some_sample_page&response_type=code"
```

**Note**: /authorize is not a typical REST API according to OAuth protocol.

In the curl command:

* $baseUri should be replaced with the URL of your target environment. For sandboxing environment, the value is https://apis.sandbox.silkcloud.com/
* The client_id is specified in the request. You will need to use the client secret in of the specified client_id in order to get the access token.
* The redirect_uri in the request determines how the auth flow will respond to you when then authorization flow has finished. The browser will redirect to the redirect_uri when the flow finishes, with the auth code in the query string. For example:
  * Response Code: 302
  * Location: `http://localhost/some_sample_page?code=someBase64AuthCode`
* A special redirect_uri can be specified to get a json object (maybe after some 302 redirects). For example:
  * Response Code: 200
  * Payload: 

```http
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache
 
{
    "code": "someBase64AuthCode"
}
```
Here is what happens in this step:

* The user is redirected to the identity portal.
* The user can sign-in, sign-up or silent sign-in with cookie in the identity portal.
* Then the auth server will then issue an authorization code.
* The auth server will redirect the client to the specified redirect_uri with the authorization code in the query string.

To perform silent sign-in without redirecting the whole page, the client can always perform POST /authorize with prompt=none first to know whether the user is signed in or not. In the OAuth 2.0 standard, the /authorize endpoint can take a query parameter: prompt=none. When this is specified, the user will not be redirected to the login html. If the silent sign-in is applicable with the cookie, the user is signed in directly. Otherwise the server redirects back with an error in query string.

There are some more details on how to handle the silent sign-in and how to login without redirecting out to another page are covered in TBD section. We will ignore them here. TBD: the client should wrap these steps to a client SDK?

As a successful result of this step, the client can get the authorization code through either query string or json. The authorization code is effective for a short period.

## Get the OAuth Access Token

The client can get the access token base using the authorization code plus the client secret.

```bash
curl -X POST \
     -H "Authorization: Basic dGVzdF9maXJzdF9wYXJ0eV9jbGllbnQ6d2Vha0NsaWVudFNlY3JldDEyMw==" \
     -H "Content-Type: application/x-www-form-urlencoded" \
     "$baseUri/oauth2/token" \
     -d "redirect_uri=http://localhost/some_sample_page&grant_type=authorization_code&code=someBase64AuthCode"
```
In the curl command:

* The Authorization: Basic header contains the base 64 encoded client_id:client_secret pair.
* The redirect_uri must be the same as the one used in the initial /authorize call.
* The grant_type must be authorization_code.
* The code is the authorization code returned in the first step.

The response will be:

```http
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache
 
{
    "access_token":"someBase64AccessToken",
    "token_type":"Bearer",
    "expires_in":3600
}
```
The "expires_in" in the response indicates that the access token expires in 1 hour. This is configurable per client through the server-side policy. When the access token is about to expire, the client can use silent sign-in in javascript to get a new access token.Some notes on the client id type and the client secret. The client secret used by client's single-page applications should be considered insecure. They can be stolen and used to create a different web application. For such client IDs, they cannot have any permission that requires a trusted caller. For example, they cannot call fulfillment APIs directly. They must call place order and the server can make the fulfillment for them. In these scenarios, having the client secret here is just to follow the OAuth protocol. For third-party server, they may really store and use the access token only from their server. In that case, the client secret will never show in their client application.

The access token can be used to call any APIs allowed by the user signed in. For example, the access token can be used to get the user profile of the signed in user. Or it can be used to update user profile of the signed in user. But it cannot be used to update the user profile of an irrelevant user. There can be special rules in the APIs. They are defined in the API docs.

## Call the API

After getting the access token, you can add the header "Authorization: Bearer <accessToken>" to call the APIs. The APIs needs to be in the scope of the access token. Otherwise 401 will be returned. If the token is revoked or expired, 401 will also be returned. The application needs to be prepared to handle 401 error and try sign-in again anywhere.

For example, to get the user, the client can call:

```bash
curl -X GET -H "Authorization: Bearer someBase64AccessToken" "$baseUri/v1/users/6B54FFB83C9F"
```

The response will be:

```http
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache
 
{
  "self": {
    "href": "/v1/users/6B54FFB83C9F",
    "id": "6B54FFB83C9F"
  },
  "username": "liangfuxia",
  "preferredLocale": "en_US",
  "preferredTimeZone": "UTC+08:00",
  "isAnonymous": false,
  "addresses": [
  {
    "href": "/v1/addresses/6C54FEB83D9F ",
    "id": "6C54FEB83D9F"
  }
  ],
  "phones": [
  {
    "href": "/v1/phones/3B54BFE43C9F",
    "id": "3B54BFE43C9F"
  }
  ],
  "emails": [
  {
    "href": "/v1/emails/6B54EEB43D6F",
    "id": "6B54EEB43D6F"
  }
  ],
  "status": "ACTIVE",
  "fullName": "John H Smith",
  "givenName": "John",
  "middleName": "H",
  "familyName": "Smith",
  "nickname":"SmithHoney",
  "birthday": "1993-01-01",
  "gender":"MALE",
  "futureExpansion": {},
  "rev": "1-k930dkd03kdk3k3k5",
  "createdTime": "2013-01-01T01:32:53Z",
  "updatedTime": "2013-01-01T01:32:53Z",
  "groupMemberships": {
    "href": "/v1/groups?userId=6B54FFB83C9F"
  },
  "devices": {
    "href": "/v1/devices?userId=6B54FFB83C9F"
  },
  "authenticators": {
    "href": "/v1/authenticators?userId=6B54FFB83C9F"
  },
  "tosAgreements": {
    "href": "/v1/users/6B54FFB83C9F/tos-agreements"
  },
  "credentials": {
    "href": "/v1/users/6B54FFB83C9F/credentials"
  },
  "credentialAttempts": {
    "href": "/v1/credential-attempts?userId=6B54FFB83C9F"
  },
  "optIns": {
    "href": "/v1/opt-ins?userId=6B54FFB83C9F"
  },
  "securityQuestions": {
    "href": "/v1/users/6B54FFB83C9F/security-questions"
  },
  "securityQuestionAttempts": {
    "href": "/v1/users/6B54FFB83C9F/security-question-attempts"
  },
  "paymentInstruments": {
    "href": "/v1/payment-instruments?userId=6B54FFB83C9F"
  },
  "orders": {
    "href": "/v1/orders?userId=6B54FFB83C9F"
  },
  "carts": {
    "href": "/v1/users/6B54FFB83C9F/carts"
  },
  "entitlements": {
    "href": "/v1/entitlements?userId=6B54FFB83C9F"
  }
}
```

