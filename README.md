Overview
======================
This Postman collection is designed for managing the OpenTok Video API. It includes pre-configured authentications and requests to interact with Video API endpoints efficiently.

REST API references: 
- https://tokbox.com/developer/rest/
- https://tokbox.com/developer/guides/insights/

How It Works
======================
## **Using Postman Variables**  
In this collection, items enclosed in `{{ }}` are variables (e.g., `{{sessionId}}`). You can either replace these placeholders with your own values or set them as variables in Postman. For more information about Postman Collection variable, visit: https://learning.postman.com/docs/sending-requests/variables/variables/#defining-collection-variables. 

## **Authentication**  
To make requests to the OpenTok REST API, you need to authenticate using the `X-OPENTOK-AUTH` HTTP header along with a JWT generated from your API key and secret. 

JWT Generation Options:  
1. Use https://jwt.io/ to generate your JWT. The payload details are explained https://tokbox.com/developer/rest/#authentication
2. Use the Python script detailed here https://tokbox.com/developer/rest/#authentication
3. You can also create a JWT using the `jsonwebtoken` library. For convenience, configure the endpoint like this sample Node.js script https://github.com/ydumburs/opentok-generate-jwt and use the `Generate a JWT` request at the bottom of the Collection. This request includes a Postman post-request script that automatically stores the returned JWT in a collection variable.  
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
