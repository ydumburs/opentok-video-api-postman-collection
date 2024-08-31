Overview
======================
This Postman collection is designed for managing the OpenTok Video API. It includes pre-configured authentications and requests to interact with Video API endpoints efficiently.

REST API references: 
- https://tokbox.com/developer/rest/
- https://tokbox.com/developer/guides/insights/

How It Works
======================
## **Using Postman Variables** 
Refer to https://github.com/ydumburs/vonage-application-api-postman-collection/blob/main/README.md#using-postman-variables

## **Authentication**  
To make requests to the OpenTok REST API, you need to authenticate using the `X-OPENTOK-AUTH` HTTP header along with a JWT generated from your API key and secret. 

JWT Generation Options:  
1. Use https://jwt.io/ to generate your JWT. The payload details are explained https://tokbox.com/developer/rest/#authentication
2. Use the Python script detailed here https://tokbox.com/developer/rest/#authentication
3. You can also create a JWT using the `jsonwebtoken` library. For convenience, configure the endpoint using https://github.com/ydumburs/opentok-generate-jwt. Use the `Generate a JWT` request at the bottomn of the Collection. This request includes a Postman post-request script that automatically stores the returned JWT in a collection variable.  
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
