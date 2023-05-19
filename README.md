# Overview
Postman collection for Vonage Video API (a.k.a. TokBox OpenTok) REST API with basic parameters and request body sample. The collection includes pre-request script which automatically generates JSON web token based on your Video API credentials.  
<img width="222" alt="Screenshot 2023-05-19 at 10 51 48" src="https://github.com/ydumburs/vonage-video-api-postman-collection/assets/45432538/55b28857-9b24-4bad-9c8a-2216ffe90ed6">

# Applies To
- [Vonage Video REST API](https://tokbox.com/developer/rest/)
- [Vonage Insights API](https://tokbox.com/developer/guides/insights/)

# Prerequisites
This requires a Vonage API account. If you don’t have one already, you can [sign up today](https://www.vonage.co.uk/communications-apis/video/) and start building with free credit. 

# Set up
1. Download [Postman](https://www.postman.com/downloads/) desktop app if you haven't installed it yet. You can also use [the Web version](https://blog.postman.com/announcing-postman-for-the-web-now-in-open-beta/).
2. Import the Environment template `Vonage Video API.postman_environment.json`. (Ref. [Adding environment variables](https://learning.postman.com/docs/sending-requests/managing-environments/#adding-environment-variables))
3. Add your credentials to the imported Environment. Make sure you add them on `CURRENT VALUE`. The minimum required to generate a JWT is `jti`, `projectApiKey`, `projectApiSecret`. If you use REST methods that are restricted to admin (those are in `Account management` folder), fill the `accountApiKey` and `accountApiSecret` as well.  
- `projectApiKey` = Your Video API project API key  
- `projectApiSecret` = Your Video API project secret  
- `accountApiKey` = Your Video API account API key  
- `accountApiSecret` = Your Video API account secret  
- `jti` = Set jti to a unique identifier for the JWT token. This is optional. (Ref. [JSON web token spec](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1.7)) 
4. Import the collection `Vonage Video API.postman_collection_x.json` into Postman. (Ref. [Importing Postman data](https://learning.postman.com/docs/getting-started/importing-and-exporting-data/#importing-postman-data))

# How to make REST requests
1. Make sure the correct Environment is selected.
2. Select a request from the imported Collection and click on the `Send` icon.
3. Find a response.

# How the pre-request script works
Video API REST methods must be authenticated using a custom HTTP header `X-OPENTOK-AUTH` along with a JWT token.
The JWT token claims will be like this for most of the methods:

    {
        "iss": "YOUR_PROJECT_API_KEY",
        "ist": "project",
        "iat": current_timestamp_in_seconds,
        "exp": expire_timestamp_in_seconds,
        "jti": "jwt_nonce"
    }
    
Some REST methods are restricted to registered administrators for the Vonage Video API account. 
To use these methods, you must set `iss` to the account-level API key as below, which is only available to account administrators.

    {
        "iss": "YOUR_ACCOUNT_API_KEY",
        "ist": "account",
        "iat": current_timestamp_in_seconds,
        "exp": expire_timestamp_in_seconds,
        "jti": "jwt_nonce"
    }

You would normally generate a JWT token manually using the above claims on [JWT.IO](https://jwt.io/).
The pre-request script automates the process, generating JWT token and it's substituted in the `X-OPENTOK-AUTH` header.
The script also detects if the account-level API key is required or not depending on the REST method by checking a header `accountLevel` on each method.

    var accountLevel = pm.request.headers.get('accountLevel');
    if (accountLevel == "true") {
        // Generate account-level token
        generateAccountLevelToken();
    } else {
        // Generate project-level token
        generateProjectLevelToken();
    }

The next part is to set claims:

    function generateAccountLevelToken() {
        // Set JWT token claims
        var apiSecret = pm.environment.get('accountApiSecret');
        var currentTimestamp = Math.floor(Date.now() / 1000);
        var expiryTimestamp = currentTimestamp + 5;
        var header = {
            'typ': 'JWT',
            'alg': 'HS256'
        }
        var payload = {
            'iss': pm.environment.get('accountApiKey'),
            'ist': 'account',
            'iat': currentTimestamp,
            'exp': expiryTimestamp,
            'jti': pm.environment.get('jti')
        }

Then encode the JWT token with Base64url which is URL-safe.

    function base64url(source) {
        // Encode in classical base64
        encodedSource = CryptoJS.enc.Base64.stringify(source);

        // Remove padding equal characters
        encodedSource = encodedSource.replace(/=+$/, '');

        // Replace characters according to base64url specifications
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

The last part is to assign the generated token into Postman environment `jwt` and this is authenticated using the `X-OPENTOK-AUTH` header on each method.

    pm.environment.set('jwt', signedToken);

# Reference
- My script was adapted from https://gist.github.com/corbanb/db03150abbe899285d6a86cc480f674d.  
- Cryptojs https://cryptojs.gitbook.io/docs/

# Disclaimer
This is provided AS IS and not supported by Vonage team. Use it at your own risk.
