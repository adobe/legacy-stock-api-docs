# Access API Reference

To establish a secure Adobe Stock session for asset licensing, you create a JWT that encapsulates your identity information and exchange it for an access token. Every call to the Adobe Stock API licensing endpoints must be authorized with this access token in the **Authorization** header, along with the API key you created when you set up your API client in the Developer Portal.



*   The access token is valid for 24 hours after it is created in response to the exchange request.
*   You can request multiple access tokens. Previous tokens are not invalidated when a new one is issued. You can authorize requests with any valid access token.

 


## Access request syntax

Exchange your JWT for an Adobe Stock API access token by making a POST request to the Adobe identity service.


<table>
  <tr>
   <td><strong>Endpoint</strong>
   </td>
   <td>https://ims-na1.adobelogin.com/ims/exchange/jwt
   </td>
  </tr>
</table>




---



## Request parameters

Pass URL-encoded parameters in the body of your POST request:


<table>
  <tr>
   <td><strong>client_id</strong>
   </td>
   <td>The API key assigned to your API client account.
   </td>
  </tr>
  <tr>
   <td><strong>client_secret</strong>
   </td>
   <td>The client secret assigned to your API client account.
   </td>
  </tr>
  <tr>
   <td><strong>jwt_token</strong>
   </td>
   <td>The base-64 encoded JSON token that encapsulates your identity information, signed with the private key for any certificate that you have associated with your API key.
   </td>
  </tr>
</table>




---



## Responses

When a request has been understood and at least partially completed, it returns with HTTP status 200:


<table>
  <tr>
   <td><strong>200 OK</strong>
   </td>
   <td>On success, the response contains a valid access token. Pass this token in the <strong>Authorization</strong> header in all subsequent requests to the Adobe Stock API.
   </td>
  </tr>
</table>


A failed request can result in a response with an HTTP status of 400 or 401 and one of the following error messages in the response body:


<table>
  <tr>
   <td><strong>400</strong>
   </td>
   <td>invalid_client
   </td>
   <td><ul>

<li>Client ID does not exist. This applies both to the client_id parameter and the aud in the JWT.
<li>The aud field in the JWT points to a different IMS environment.
<li>The client_id parameter and the aud field in the JWT do not match.</li></ul>

   </td>
  </tr>
  <tr>
   <td><strong>401</strong>
   </td>
   <td>invalid_client
   </td>
   <td><ul>

<li>Client ID does not have the exchange_jwt scope. This indicates an improper client configuration. Contact support to resolve it.
<li>The client ID and client secret combination is invalid.</li></ul>

   </td>
  </tr>
  <tr>
   <td><strong>400</strong>
   </td>
   <td>invalid_token
   </td>
   <td><ul>

<li>JWT is missing or cannot be decoded.
<li>JWT has expired. In this case, the error_description contains more details.
<li>The exp or jti field of the JWT is not an integer.</li></ul>

   </td>
  </tr>
  <tr>
   <td><strong>400</strong>
   </td>
   <td>invalid_signature
   </td>
   <td><ul>

<li>The JWT signature does not match any certificate on record for given iss / sub combination
<li>The signature does not match the algorithm specified in the JWT header.</li></ul>

   </td>
  </tr>
  <tr>
   <td><strong>400</strong>
   </td>
   <td>invalid_jti
   </td>
   <td><ul>

<li>The binding requires a JTI, but the jti field is missing or was previously used.</li></ul>

   </td>
  </tr>
  <tr>
   <td><strong>400</strong>
   </td>
   <td>invalid_scope
   </td>
   <td>Indicates a problem with the requested scope for the token. The JWT must include "https://ims-na1.adobelogin.com/s/ent_user_sdk": true. Specific scope problems can be:<ul>

<li>Metascopes in the JWT do not match metascopes in the binding.
<li>Metascopes in the JWT do not match target client scopes.
<li>Metascopes in the JWT contain a scope or scopes that do not exist.
<li>The JWT has no metascopes.

<p>
 </li></ul>

   </td>
  </tr>
  <tr>
   <td><strong>400</strong>
   </td>
   <td>bad_request
   </td>
   <td><ul>

<li>JWT payload can be decoded and decrypted but contents are incorrect. Can occur when values for fields such as sub, iss, exp, or jti are not in the proper format.</li></ul>

   </td>
  </tr>
</table>




---



## Example

```
========================= REQUEST ==========================
POST https://ims-na1.adobelogin.com/ims/exchange/jwt
-------------------------- body ----------------------------
client_id=*myClientId*&client_secret=*myClientSecret*&jwt_token=*myJSONWebToken*
------------------------- headers --------------------------
Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
```