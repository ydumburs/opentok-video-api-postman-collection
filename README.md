Overview
======================
This Postman collection is designed for the OpenTok Video API, allowing you to manage OpenTok. It provides a set of pre-configured authentications and requests to interact with Video API endpoints efficiently.  

REST API references: 
- https://tokbox.com/developer/rest/
- https://tokbox.com/developer/guides/insights/

How It Works
======================
## **Using Postman Variables** 
Refer to https://github.com/ydumburs/vonage-application-api-postman-collection/blob/main/README.md#using-postman-variables

## **Authentication**  
To make an OpenTok REST API request, you need to Authenticate using a HTTP header `X-OPENTOK-AUTH` along with a JWT generated from your API key and secret. 

JWT Generation Options:  
1. Use: https://jwt.io/ with payload exlained here https://tokbox.com/developer/rest/#authentication
2. Use a Python script exlained here https://tokbox.com/developer/rest/#authentication
3. You can also create a JWT using the `jsonwebtoken`. For convenience, configure the endpoint using the SDK and use the `Generate a JWT` request at the top of the Collection. This request includes a Postman post-request script that automatically stores the returned JWT in a Collection variable upon completion.
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
