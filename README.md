# Overview
Postman collection contains Vonage Video API (a.k.a. TokBox OpenTok) REST API as of 16-Feb-2022 with basic parameters and request body sample. This collection contains Postman pre-request script which automatically generates JWT every time you send API request. 

# Applies To
- Vonage Video REST API https://tokbox.com/developer/rest/
- Vonage Insights API https://tokbox.com/developer/guides/insights/

# Procedure
1. Download Postman desktop app or login to Postman web app (ref. https://www.postman.com/downloads/)
2. Import the collection to Postman (ref. https://learning.postman.com/docs/getting-started/importing-and-exporting-data/#importing-postman-data)
3. Import the environment template (ref. https://learning.postman.com/docs/sending-requests/managing-environments/#adding-environment-variables)
4. Add your own credential on [CURRENT VALUE] section
projectApiKey = Your Video API project API key  
projectApiSecret = Your Video API project secret  
accountApiKey = Your Video API account API key  
accountApiSecret = Your Video API account secret  
jti = Set jti to a unique identifier for the JWT. This is optional. See the [JSON web token spec](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1.7) for details.  
5. Make sure you select the environment on the top-right dropdown. It's ready to run, expand the imported collection on the left pane and try running any REST API request.

# Pre-request script 
--works in progress--

