Overview
======================
This Postman collection is designed for managing the OpenTok Video API. It includes pre-configured authentications and requests to interact with Video API endpoints efficiently.

REST API references: 
- https://tokbox.com/developer/rest/
- https://tokbox.com/developer/guides/insights/

How to Use
======================
1. **Install Postman**  
   Download the Postman desktop app: https://www.postman.com/downloads/. You can also use the Web version: https://blog.postman.com/announcing-postman-for-the-web-now-in-open-beta/.
2. **Import the Collection**  
   Import the `OpenTok Video API.postman_collection.json` into Postman from the `Import` icon. 
3. **Configure Collection variables**  
   Click on the `OpenTok Video API` in the left-hand navigation tree, then select `Variables` tab in the center pane. In the `Current value` section, set your projectApiKey, projectApiSecret, and jti. 
7. **Generate a JWT**  
   Click on the `Generate a JWT` folder in the left-hand navigation tree, then select `Postman script`. In the center pane, click `Send`. This will run the script and generate a JWT. For more information on JWT generation, please see https://github.com/ydumburs/opentok-video-api-postman-collection/blob/main/README.md#authentication. 
8. **Send Requests**  
   Select any request from the left-hand navigation and click the `Send` button. Note that you may need to modify the request body as needed. Once the JWT has expired, you'll receive a `token has expired` error. To resolve this, you'll need to generate a new JWT by running the `Postman script` again.

How It Works
======================
## **Using Postman Variables**  
In this collection, items enclosed in `{{ }}` are variables (e.g., `{{sessionId}}`). You can either replace these placeholders with your own values or set them as variables in Postman. For more information about Postman Collection variable, visit: https://learning.postman.com/docs/sending-requests/variables/variables/#defining-collection-variables. 

## **Authentication**  
To make requests to the OpenTok REST API, you need to authenticate using the `X-OPENTOK-AUTH` HTTP header with a JSON Web Token (JWT). The JWT is generated using your API key and secret.  

Authentication Types:  
1. Project management requests in the `Project` folder in this Collection (e.g., create a project) require a JWT generated using the OpenTok Account API key and secret.
2. Other requests require a JWT generated using the OpenTok Project API key and secret.

If your JWT expires, you will receive a token has expired error. Simply generate a new JWT to continue making requests.  
  
You can generate a JWT using one of the following methods:  
1. **Python Script**  
   Use a Python script detailed here https://tokbox.com/developer/rest/#authentication 
2. **Server-Side**  
   Generate a JWT server-side using the `jsonwebtoken` library. For convenience, you can configure the endpoint as shown in this Node.js script https://github.com/ydumburs/opentok-node-generate-jwt and use the `Server-side` request in the `Generate a JWT` folder at the top of the Collection. This request includes a Postman post-request script that automatically stores the returned JWT in a collection variable.
```
// Postman Post-request script
const response = pm.response.json(); 
if (response.token) {
    pm.collectionVariables.set("jwt", response.token); 
    console.log("JWT has been set to collection variable:", response.token);
} else {
    console.error("JWT token not found in the response");
}
```
3. **Postman Pre-request Script**  
   Set your API credentials in a Collection variable, then use the `Postman script` in the `Generate a JWT` folder at the top of the Collection. This script generates a JWT from your API credentials and encodes using the `CryptoJS` library. Then it sets a JWT as the Collection variable `jwt`, which is valid for 5 minutes. **Important Note**: For generating JWTs in Postman, the `CryptoJS` remains a practical choice within Postman's scripting environment, even though it is no longer actively maintained. For security-critical applications, you may want to consider generating JWTs using other options that are more secure.
```
// Postman Pre-request script
/**
 * OpenTok Video API - Postman Pre-request Script
 * Author: Yukari Dumburs (Created: 19 Jan 2022, Updated: 1 Sep 2024)
 */
var apiSecret = pm.collectionVariables.get('projectApiSecret');
var apiKey = pm.collectionVariables.get('projectApiKey');
var jti = pm.collectionVariables.get('jti');
if (!apiSecret || !apiKey || !jti) {
    throw new Error("API Secret or API Key or jti is missing!");
}
var currentTimestamp = Math.floor(Date.now() / 1000);
var expiryTimestamp = currentTimestamp + 300; // 5 minutes
var header = {
    'typ': 'JWT',
    'alg': 'HS256'
};
var payload = {
    'iss': apiKey,
    'ist': 'project',
    'iat': currentTimestamp,
    'exp': expiryTimestamp,
    'jti': jti
};

function base64url(source) {
    let encodedSource = CryptoJS.enc.Base64.stringify(source);
    encodedSource = encodedSource.replace(/=+$/, '');
    encodedSource = encodedSource.replace(/\+/g, '-');
    encodedSource = encodedSource.replace(/\//g, '_');
    return encodedSource;
}

// Encode header
var stringifiedHeader = CryptoJS.enc.Utf8.parse(JSON.stringify(header));
var encodedHeader = base64url(stringifiedHeader);

// Encode payload
var stringifiedData = CryptoJS.enc.Utf8.parse(JSON.stringify(payload));
var encodedData = base64url(stringifiedData);

// Build token
var token = `${encodedHeader}.${encodedData}`;

// Sign token
var signature = CryptoJS.HmacSHA256(token, apiSecret);
signature = base64url(signature);
var signedToken = `${token}.${signature}`;

pm.collectionVariables.set('jwt', signedToken);
```

