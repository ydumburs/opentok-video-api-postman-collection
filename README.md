# Overview
Postman collection contains Vonage Video API (a.k.a. TokBox OpenTok) REST API as of 16-Feb-2022 with basic parameters and request body sample. Postman pre-request script is also included in the collection, which automatically generates JSON web token every time you send an API request. 

# Applies To
- Vonage Video API https://tokbox.com/developer/guides/basics/
- Vonage Video REST API https://tokbox.com/developer/rest/
- Vonage Insights API https://tokbox.com/developer/guides/insights/

# Prerequisites
This requires a Vonage API account. If you don’t have one already, you can [sign up today](https://www.vonage.co.uk/communications-apis/video/?adobe_mc=MCMID%3D83313646441230218354214934259658269953%7CMCORGID%3DA8833BC75245AF9E0A490D4D%2540AdobeOrg%7CTS%3D1646137433) and start building with free credit. 

# Procedure
1. Download Postman desktop app or log in to Postman web app. Ref. [Download Postman](https://www.postman.com/downloads/)
2. Import the collection to Postman. Ref. [Importing Postman data](https://learning.postman.com/docs/getting-started/importing-and-exporting-data/#importing-postman-data)
3. Import the environment template. Ref. [Adding environment variables](https://learning.postman.com/docs/sending-requests/managing-environments/#adding-environment-variables)
4. Add your own credential on [CURRENT VALUE] section
projectApiKey = Your Video API project API key  
projectApiSecret = Your Video API project secret  
accountApiKey = Your Video API account API key  
accountApiSecret = Your Video API account secret  
jti = Set jti to a unique identifier for the JWT token. This is optional. See the [JSON web token spec](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1.7) for details.  
5. Make sure you select the environment on the top-right dropdown. It's ready to run, expand the imported collection on the left pane and try running any REST API request.

# How the pre-request script works
--works in progress--  
Those REST API calls must be authenticated using a custom HTTP header `X-OPENTOK-AUTH` along with a JSON web token. Find more about the JWT token claims from [Authentication](https://tokbox.com/developer/rest/#authentication).

Some of those REST API calls require account-level API key, which is only available to Video API account administrators.
The script first check if the API call requires account-level credential or not, the flag `accountLevel` is set on each request headers already.

    var accountLevel = pm.request.headers.get('accountLevel');
    if (accountLevel == "true") {
        // Generate account-level token
        generateAccountLevelToken();
    } else {
        // Generate project-level token
        generateProjectLevelToken();
    }

This function is to get values from Postman environment that is set on step 4 above and set claims.

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
