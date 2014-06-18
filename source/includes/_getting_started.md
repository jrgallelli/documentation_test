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

`
curl -X POST --max-redirs 0 \
     "$baseUri/oauth2/authorize?client_id=test_first_party_client&redirect_uri=http://localhost/some_sample_page&response_type=code"
`

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
`
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache
 
{
    "code": "someBase64AuthCode"
}
`
