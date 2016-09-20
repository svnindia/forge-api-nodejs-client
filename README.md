# Forge Node.js SDK

## Overview
This [Node.js](https://nodejs.org/) SDK (version 0.1.6) enables you to easily integrate the Forge REST APIs
into your application, including [Data Management](https://developer.autodesk.com/en/docs/data/v2/overview/),
[Model Derivative](https://developer.autodesk.com/en/docs/model-derivative/v2/overview/),
[3D Print](https://developer.autodesk.com/en/docs/print/v1/overview/),
and [Design Automation](https://developer.autodesk.com/en/docs/design-automation/v2/overview/).

## Requirements
* Node.js version 4 and above.
* A registered app on the [Forge Developer portal](https://developer.autodesk.com/myapps).
* A Node.js web server (such as Express) for 3-legged authentication.


## Installation
```sh
    npm install forge-apis --save
```

## Tutorial
Follow this tutorial to see a step-by-step authentication guide, and examples of how to use the (Data Management) APIs.

Create an app on the Forge Developer portal, and select the desired API products in the app creation page (e.g., Data Management API). Note the app key and app secret.

### Authentication

This SDK comes with an [OAuth2](https://developer.autodesk.com/en/docs/oauth/v2/overview/) client that allows you to retrieve 2-legged and 3-legged tokens. It also enables you to refresh 3-legged tokens. The tutorial uses 2-legged and 3-legged tokens for calling different Data Management endpoints.

#### 2-Legged Token

This type of token is given directly to the application without the user's consent.
To get a 2-legged token run the following code:

``` JavaScript
var ForgeSDK = require('forge-apis');
var CLIENT_ID = '<your-client-id>' , CLIENT_SECRET = '<your-client-secret>';

// Initialize the 2-legged OAuth2 client, and optionally set specific scopes.
// If you omit scopes, the generated token will have all scope permissions.
var oAuth2TwoLegged = new ForgeSDK.AuthClientTwoLegged(CLIENT_ID, CLIENT_SECRET, [
    'data:read',
    'data:write'
]);

oAuth2TwoLegged.authenticate().then(function(credentials){
    // The `credentials` object contains an access_token that you use to call the endpoints.
    // You can set the credentials globally on the oauth client and retrieve them later on.
    oAuth2TwoLegged.setCredentials(credentials);
}, function(err){
    console.error(err);
});
```

#### 3-Legged Token
##### Generate an Authentication URL

To ask for permissions from a user to retrieve an access token, you
redirect the user to a consent page. Run this code to create a consent page URL:

``` JavaScript
var ForgeSDK = require('forge-apis');
var CLIENT_ID = '' , CLIENT_SECRET = '', REDIRECT_URL = '';

// Initialize the 3-legged OAuth2 client, and optionally set specific scopes.
// If you omit scopes, the generated token will have all scope permissions.
var oAuth2ThreeLegged = new ForgeSDK.AuthClientThreeLegged(CLIENT_ID, CLIENT_SECRET, REDIRECT_URL, [
    'data:read',
    'data:write'
]);

// Generate a URL page that asks for permissions for the specified scopes.
oAuth2ThreeLegged.generateAuthUrl();
```

##### Retrieve an Authorization Code

Once a user has given permissions on the consent page, Forge will redirect
the page to the redirect URL you provided when you created the app. An authorization code is returned in the query string.

GET /callback?code={authorizationCode}

##### Retrieve an Access Token

Request an access token using the authorization code you received, as shown below:

``` JavaScript
oAuth2ThreeLegged.getToken(authorizationCode).then(function (credentials) {
    // The `credentials` object contains an access_token and an optional refresh_token that you can use to call the endpoints.
}, function(err){
    console.error(err);
});
```

To refresh your access token, call the `oAuth2ThreeLegged.refreshToken(credentials);` method.

#### Example API Calls

Use the `credentials` object to call the Forge APIs.

``` JavaScript

// Import the library.
var ForgeSDK = require('forge-apis');

// Initialize the relevant clients; in this example, the Hubs and Buckets clients (part of the Data Management API).
var HubsApi = new ForgeSDK.HubsApi(); //Hubs Client
var BucketsApi = new ForgeSDK.BucketsApi(); //Buckets Client

// Get the buckets owned by an application.
// Use the twoLeggedCredentials that you retrieved above.
BucketsApi.getBuckets({}, twoLeggedCredentials).then(function(buckets){
    console.log('buckets details response:');
    console.log(buckets);
});

// Get the hubs that are accessible for a member.
// Use the threeLeggedCredentials that you retrieved above.
HubsApi.getHubs({}, threeLeggedCredentials).then(function(hubs) {
    console.log('hubs details response:');
    console.log(hubs);
});

```

## Support

forge.help@autodesk.com