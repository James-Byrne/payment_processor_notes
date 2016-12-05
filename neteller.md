# Neteller Notes
### General Information
---
- Uses Auth as a token based auth framework
- URL based
- Works with JSON
  - Has a test vs prod mode, Access them by changing
	- url ( https://test.api. vs https://api. )
	- credentials

### Neteller Pagnation
---
If user asks for all subscriptions with pagination set at 15
- List of 15 subscriptions will be returned
- List will have links to the next 15 records
	- 1 - 15 & 31 - 45

### Neteller Resource Expansion
---
- Similar to db eager loading
* Load a specified resource & its related resources
* For resources with many expandable children you can specify which resources to expand with a comma seperated list
	* `?expand=resource1,resource2,resource3`

### Neteller Auth Authentication
---
- Based on a Oauth standard (RFC6749)
* Utilizes a bearer token
* To get a token
	* Make a request to Neteller token endpoint with the `grant_type` field
	* Your first have to register your application to integrate
	* You get a client id and a client secret

### Supported Oauth flows
---
#### client_credentials grant flow
* Get access token with only client credentials
* Access functions that do not require member authorisation

#### authorisation_code grant flow
- Used for functions that require member auth
- Redirects user to Neteller auth server to auth as a Neteller member and approve requested permissions
- That auth token can be used to get an access token with access to Member api functions

### Neteller Oauth Errors
Params :
- error : The error that occurred
- error_description : Reason for the error
- state : Provided if state param was present in auth request

#### Possible errors :
- invalid_request :
- unauthorised_client
- access_denied
- unsupported_response_type
* invalid_scope
* server_error
* temporarily_unavailable

#### Sample Response
- `https://your_url/?error=access_denied&error_description=The+resource+owner+or+authorization+server+denied+your+request`

### Neteller token request error
Params :
- error : The error that occurred
- error_description : Reason for the error
- state : Provided if state param was present in auth request

#### Possible error:
- invalid_request
- invalid_client
- invalid_grant
- unauthorized_client
- server_error
- unsupported_grant_type
- invalid_scope


## Netellergo Hosted Payment
---
Creating an order
- May include a list of items/fees/taxes to collect payment for
- Includes redirect urls :
```js
  "redirects": [{
    "rel": "on_success",
    "uri": "your-success-url"
  }, {
    "rel": "on_cancel",
    "uri": "your-success-url"
  }]
```

Neteller will respond with
- details of the order request
- unique `order_id`
- HATEOS link for the _hosted__payment flow_ you will redirect your customer to
  - Once Neteller has completed the request the customer will be re-directed to the
  appropriate redirection url supplied in the create order request
