# Registration, Login and Authentication

## Overview

The identity system manages all user credentials, account profile information, and the access to different resources.  This section covers the two fundamental concepts: user profile management and authentication.  For information around how different user relationships are managed please check User Management.

## User profile management
The minimal information required to register/create a new user account within the platform is just user name and password.  Alternatively user can login using third party identities such as Sina ID, Google ID, etc.  Integration with local third party identity systems can be added per request.  Please reach out to SilkCloud for further information.

Once logged in user can fill in supplemental information, such as addresses, privacy settings, security settings, etc. to complete his profile. The standard user profile elements include : 

* ADDRESS
* NAME
* EMAIL
* PHONE
* DOB
* SMS
* QQ
* WHATSAPP
* PASSPORT - e.g., “USA 123456789”
* GOVERNMENT_ID - SSN or equivalent in other countries.
* DRIVERS_LICENSE - e.g., “USA CA 12345”
* GENDER

Customized user profile elements can be added per request.

## Authentication

This is the process for the server to check user access to platform resources.  Depending on where and when the authentication happens there are 3 typical authentication processes:

### Login Authentication

This is the standard process in which server checks user login credentials and grant standard privileges.  The system supports both first party login credentials and third party logins such as Sina ID Login, Google ID login,  etc.  First party authentication returns results immediately.  Third party logins redirect user to external ID system.

### Access rights authentication

After login a user may attempt to access resources that are protected by escalated security measures. For example a user logs in to change his password.  However change password is not within standard privilege granted by the login authentication process. So client has to perform authentication again to require higher level privileges.  

Within the access rights authentication process server may respond with special security measures such as password reentry or two-factor authentications.  User has to pass these additional challenges to be successfully authenticated for higher privileges.  The security measures are pre-defined during client onboarding process.  For more information please check User Identity Protection.

Another scenario that requires access right authentication is when user attempts to perform functions that require special roles.  Similarly since these functions are not within standard privileges an authentication request is needed.  The server checks user's role assignment to determine whether access to the function is allowed.  Please check Authorizations for more details on how to manage user roles and rights on the platform.

### OAuth2

Authentication for third party server to obtain access token to platform APIs.
OAuth2 and its extension OpenId Connect Protocol (http://openid.net/connect/) is implemented.

## End to End Flows

### User registration using a browser (Account creation)

1. User clicks on create new account which triggers an authentication request to be sent to the Auth server.
1. The Auth Server responds with view "register", requiring  browser redirection to the Registration Page.
1. Browser displays the registration page.  If  the server response includes requirement to show reCAPTCHA browser also needs to display the reCAPTCHA together with the registration form.
1. User inputs information required for registration and browser sends another authentication request to server to create new user account.
1. Step 2-4 is repeated, until the Auth Server returns 302.
1. The registration page redirects back to the auth Server.
1. The Auth Server finally redirects back to the application's callback URL

### User login using a browser

1. An user attempts to access a function that requires user to login or gather explicit consent.  Due to the privilege level needed, client sends an authentication request to the Auth Server.
1. The Auth Server responds with view "login", requiring  browser redirection to the login Page.
  * Auth server may also respond with view "Challenge" then browser needs to redirect to the challenge page.  For more details please check User Identity Protection
  * Auth server may respond with view "consent", then browser needs to shows  UI for the user to confirm granting access .
1. Browser displays the required view.
1. Browser collects user inputs and sends another authentication request to the server.
1. Step 2-4 is repeated, until the Auth Server returns 302.
1. The login page redirects back to the Auth Server.
1. The Auth Server finally redirects back to the application's callback URL

## Sample Code

### Authentication request triggers a view to display registration view with reCAPTCHA challenge

#### Sample Request

```http
POST /connect/authorize?fs=oEYLTZnI0N HTTP/1.1
 Content-Type: application/json
 Accept: application/json
 Host: auth.silkcloud.com
 
 {
 }
 ```
 
#### Sample Response
 
```http
 HTTP/1.1 200 Ok
 Content-Type: application/json
 Cache-Control: no-store
 Pragma: no-cache
 {
  "view": "register",
  "model": {
    "clientId": "s6BhdRkqt3",
    "locales": "en_US",
    "theme": "default",
    "recaptchaImageUrl": "http://blablabla"
    ...
  }
 }
```
### User registration information collected and sent to the Auth server

#### Sample Request

```http
POST /connect/authorize?fs=oEYLTZnI0N HTTP/1.1
 Content-Type: application/json
 Accept: application/json
 Host: auth.silkcloud.com
 
 {
 "event": "next"
 "model": {
  "username": "user1@silkcloud.com",
  "password": "password",
  "dob": "…",
  …
 }
 }
```

#### Sample Response (GOOD)

```http
HTTP/1.1 302 Found
 Location: https://auth.silkcloud.com/connect/authorize?fs=oEYLTZnI0N
```

#### Sample Responese (BAD)

```http
HTTP/1.1 400 Bad Request
 Content-Type: application/json
 Cache-Control: no-store
 Pragma: no-cache
 
 {
  "code": "10123",
  "message": "some error happens",
 }
 ```
 
### Authentication request triggers a login view 

#### Sample Request

```http
POST /connect/authorize?fs=oEYLTZnI0N HTTP/1.1
 Content-Type: application/json
 Accept: application/json
 Host: auth.silkcloud.com
  {
 }
 ```
 
#### Sample Response
 
```http
 HTTP/1.1 200 Ok
 Content-Type: application/json
 Cache-Control: no-store
 Pragma: no-cache
  {
  "view": "login",
  "model": {
 "clientId": "s6BhdRkqt3",
 "locales": "en_US",
 "theme": "default",
 ...
  }
 }
```
 
### Browser collects user login credential and request authentication
 
 * GOOD response: Server response with requirement to gather user consent ("consent" view) 
 * BAD response: login fails due to invalid username or password.
 
#### Sample Request
```http
 POST /connect/authorize?fs=oEYLTZnI0N HTTP/1.1
 Content-Type: application/json
 Accept: application/json
 Host: auth.silkcloud.com
 
 {
 "event": "next"
 "model": {
  "username": "user1@silkcloud.com"
  "password": "password"
 }
 }
```
 
#### Sample Response (GOOD)
```http
 HTTP/1.1 200 Ok
 Content-Type: application/json
 Cache-Control: no-store
 Pragma: no-cache
 
 {
  "view": "consent",
  "model": {
 "clientId": "s6BhdRkqt3",
 "locales": "en_US",
 "theme": "default",
 "scopes": "email profile ..."
 ...
  }
 }
```
 
#### Sample Response (BAD)
```http
 HTTP/1.1 400 Bad Request
 Content-Type: application/json
 Cache-Control: no-store
 Pragma: no-cache
 
 {
  "code": "10123",
  "message": "username or password is invalid",
 }
```
 
 