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

#### Neteller will respond with
- details of the order request
- unique `order_id`
- HATEOS link for the _hosted__payment flow_ you will redirect your customer to
  - Once Neteller has completed the request the customer will be re-directed to the
  appropriate redirection url supplied in the create order request

#### Sample Request
```bash
POST /v1/orders HTTP/1.1
Host: test.api.neteller.com
Authorization: Bearer 0.AQAAAUpkOdfxAAAAAAA27oCvz0-Y9rEevMOnlll8yDtB.3ukRggt_VV1zXjFNd1h8-TGrMcI
Content-Type: application/json
Cache-Control: no-cache
```
```json
{
  "order":{
    "merchantRefId":"20141231120800",
    "totalAmount":3599,
    "currency":"EUR",
    "lang":"en_US",
    "items":[
      {
        "amount":2500,
        "quantity":1,
        "sku":"XYZPART1",
        "name":"Item A",
        "description":"Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc eget venenatis diam. Integer euismod magna dui, a accumsan elit interdum."
      },
      {
        "amount":200,
        "quantity":2,
        "sku":"",
        "name":"Item B",
        "description":"Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed sit amet congue orci, ac euismod."
      }
    ],
    "fees":[
      {
        "feeAmount":500,
        "feeName":"Setup Fee"
      }
    ],
    "taxes":[
      {
        "taxAmount":199,
        "taxName":"VAT"
      }
    ],
    "redirects":[
      {
        "rel":"on_success",
        "uri":"http://requestb.in/16bf0vr1?result=success"
      },
      {
        "rel":"on_cancel",
        "uri":"http://requestb.in/16bf0vr1?result=cancel"
      }
    ]
  }
}
```

#### Sample Response
```json
{
  "orderId": "ORD_2d8ab7f0-dc97-472f-96c7-16b4488fab35",
  "merchantRefId": "20141219132605",
  "totalAmount": 3599,
  "currency": "EUR",
  "lang": "en_US",
  "items":
  [
    {
      "amount": 2500,
      "quantity": 1,
      "sku": "XYZPART1",
      "name": "Item A",
      "description": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc eget venenatis diam. Integer euismod magna dui, a accumsan elit interdum."
    },
    {
      "amount": 200,
      "quantity": 2,
      "sku": "",
      "name": "Item B",
      "description": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed sit amet congue orci, ac euismod."
    }
  ],
  "fees":
  [
    {
      "feeAmount": 500,
      "feeName": "Setup Fee"
    }
  ],
  "taxes":
  [
    {
      "taxAmount": 199,
      "taxName": "VAT"
    }
  ],
  "redirects":
  [
    {
      "rel": "on_success",
      "uri": "https://example.com/success.html"
    },
    {
      "rel": "on_cancel",
      "uri": "https://example.com/cancel.html"
    }
  ],
  "links":
  [
    {
      "url": "https://api.neteller.com/v1/checkout/ORD_2d8ab7f0-dc97-472f-96c7-16b4488fab35",
      "rel": "hosted_payment",
      "method": "GET"
    },
    {
      "url": "https://api.neteller.com/v1/orders/ORD_2d8ab7f0-dc97-472f-96c7-16b4488fab35",
      "rel": "self",
      "method": "GET"
    }
  ]
}
```

## Neteller Endpoints
---

#### /Authorization
- Obtain an access token with client credentials
- Obtain an access token using auth code
- Exchange your authorization code for an access token
- Exchange refresh token for an access token

#### /Payments
- Send payment
- Request payment
- Lookup a payment
- List payments

#### /Orders
- Create an order
- Lookup an order
- Lookup an invoice for an order

#### /Customers
- Create a new customer
- Lookup customer
- Verify customer details
- Lookup subscription for a customer

#### /Plans
- Create a plan
- Lookup a plan
- Cancel a plan
- Delete a plan
- List all your plans

#### /Subscriptions
- Create a subscription
- Lookup subscription
- Cancel subscription
- List subscription
- Lookup an invoice for a subscription
- List all invoices for a subscription


## Authorization

#### Obtain an access token with client credentials

| Param         | Type    | Default Value |
|---------------|---------|---------------|
| Http Code     | Number  | 201           |
| tokenType     | String  | `"Bearer"`    |
| expiresIn     | Number  | 10000         |
| accessToken   | String  | 0.AQAAAVF-mqsiAAAAAAAbd0A71bIG8IUwcgHV7mAYiG7J.EAAQsWDnpqRj7WwyFVLTsdo0yXWh9L4|

#### Obtain an access token using auth code.
This endpoint is used in conjunction with the 3rd endpoint. This is the first
step and returns an auth code. The 3rd step trades this auth code for an access token.

| Param         | Type    | Default Value |
|---------------|---------|---------------|
| Http Code     | Number  | 200           |
| data          | String  | Header        |

#### Exchange your auth code for an access token

| Param        | Type   | Default Value |
|--------------|--------|---------------|
| Http Code    | Number | 201           |
| tokenType    | String | `"Bearer"`    |
| expiresIn    | Number | 10000         |
| accessToken  | String | 0.AQAAAVF-mqsiAAAAAAAbd0A71bIG8IUwcgHV7mAYiG7J.EAAQsWDnpqRj7WwyFVLTsdo0yXWh9L4|
| refreshToken | String | 0.AgAAAUgUGIkyAAAAB1jwsODI5PCkVNOZul5AJ01mYtdh.ezGCYhN5YD22-BCOPX6U-muc72o|
| scope        | String | "subscription_payment" |

#### Exchange a refresh token for an access token

| Param         | Type    | Default Value |
|---------------|---------|---------------|
| Http Code     | Number  | 201           |
| tokenType     | String  | `"Bearer"`    |
| expiresIn     | Number  | 10000         |
| accessToken   | String  | 0.AQAAAVF-mqsiAAAAAAAbd0A71bIG8IUwcgHV7mAYiG7J.EAAQsWDnpqRj7WwyFVLTsdo0yXWh9L4|
| refreshToken  | String  | 0.AgAAAUgUGIkyAAAAB1jwsODI5PCkVNOZul5AJ01mYtdh.ezGCYhN5YD22-BCOPX6U-muc72o|
| scope         | String  | "subscription_payment"|


## Payments

#### Send Payment To Email - Request

| Param                                 | Type    | Required |
|---------------------------------------|---------|----------|
| payeeProfile                          | `{}`    | Yes      |
| [transaction](#transaction-request)   | `{}`    | Yes      |
| message                               | String  | No       |

___payeeProfile___
> The recipient of the payment. At minimum, the payee's email address or NETELLER Account ID must be supplied.

| Param            | Type    | Validations / Notes  |
|------------------|---------|----------------------|
| email/account_id | String  | Is a String          |


#### Send Payment To Email - Response
> Http Code : 201

| Param                                 | Type    | Required |
|---------------------------------------|---------|----------|
| customer                              | `{}`    | Yes      |
| [transaction](#transaction-response)  | `{}`    | Yes      |
| links                                 | `[]`    | No       |

___customer___ `Expandable`
> The associated customer information for the payment.
> Note the actual object exists as `customer : { link: { url: "" ... }}`

| Param       | Type    | Validations / Notes |
|-------------|---------|---------------------|
| url         | String  | String, is url      |
| rel         | String  | String, `customer`  |
| method      | String  | String, `GET`       |

___links___
> This is an array of self-referencing links. Takes the form of `[{}]`

| Param | Type    | Validations / Notes |
|-------|---------|---------------------|
| rel   | String  | `"self"`            |
| href  | String  | Is String           |


#### Send payment to non-registered email with profile data to provide a signup link - Request

| Param                               | Type    | Required |
|-------------------------------------|---------|----------|
| [payeeProfile](#payeeProfile)       | `{}`    | Yes      |
| [transaction](#transaction-request) | `{}`    | Yes      |
| message                             | String  | No       |



#### Send payment to non-registered email with profile data to provide a signup link - Response
> Http Code 201

| Param         | Type    | Required |
|---------------|---------|----------|
| customer      | `{}`    | Yes      |
| transaction   | `{}`    | Yes      |
| links         | `[{}]`  | No       |


___customer___
> The associated customer information for the payment.

| Param             | Type    | Validations / Notes                         |
|-------------------|---------|---------------------------------------------|
| customerId        | String  | `CUS_0d676b4b-0eb8-4d78-af25-e41ab431e325`  |
| accountProfile    | `{}`    | See [object](#accountProfile)               |
| verificationLevel | String  | See notes on verificationLevel below        |
| availableBalance  | `{}`    | See [object](#availableBalance)             |








# Neteller Objects

#### transaction-request
> Details about the payment.

| Param         | Type    | Validations / Notes         |
|---------------|---------|-----------------------------|
| merchanRefId  | String  | Unique to merchants system  |
| amount        | Number  | Amount in cents             |
| currency      | String  | 2-3 letter identifier       |

#### transaction-response
> Details about the payment.

| Param           | Type    | Validations / Notes                             |
|-----------------|---------|-------------------------------------------------|
| merchanRefId    | String  | Unique to merchants system                      |
| amount          | Number  | Amount in cents                                 |
| currency        | String  | 2-3 letter identifier                           |
| id              | Number  | Neteller transaction id                         |
| transactionType | String  | Type/Specific product of transaction            |
| createDate      | String  | ISO 8601 format (UTC) YYYY-MM-DDThh:mm:ssZ      |
| updateDate      | String  | ISO 8601 format (UTC) YYYY-MM-DDThh:mm:ssZ      |
| fees            | `[{}]`  | See [object](#fees)                             |
| status          | String  | `accepted pending declined cancelled failed`    |



#### fees
> List of fees associated with this transaction.

| Param       | Type    | Validations / Notes                     |
|-------------|---------|-----------------------------------------|
| feeType     | String  | Description of fees e.g. `service_fee`  |
| feeAmount   | Number  | Cost of fees, in cents                  |
| feeCurrency | String  | Currency, 2-3 length string             |


#### accountProfile
#### payeeProfile
> `Response`  :  The associated account profile. This is normally nested within the customer object.
> `Request`  : The recipient of the payment. At minimum, the payee's email address or NETELLER Account ID must be supplied.

| Param                   | Type    | Validations / Notes               |
|-------------------------|---------|-----------------------------------|
| email/account_id        | String  | Is a String                       |
| firstName               | String  | Is an email address               |
| lastName                | String  | Is a String                       |
| address1                | String  | Is a String                       |
| address2                | String  | Is a String                       |
| address3                | String  | Is a String                       |
| city                    | String  | Is a String                       |
| countrySubdivisionCode  | String  | ISO 3166-2 code                   |
| country                 | String  | ISO 3166-1 Alpha 2-code           |
| postcode                | String  | Is a String                       |
| gender                  | String  | Is a string                       |
| contactDetail           | `{}`    | See[ object](#contactDetail)      |
| dateOfBirth             | `{}`    | See[ object](#dateOfBirth)        |
| accountPreferences      | `{}`    | See[ object](#accountPreferences) |


#### contactDetail
> An array of contact numbers. Can accept up to 2 phone numbers, the first number will be considered primary. Phone numbers should contain no special characters. This is normally embedded within an accountProfile/payeeProfile object.

| Param   | Type    | Validations / Notes                                   |
|---------|---------|-------------------------------------------------------|
| type    | String  | `landLine mobile`                                     |
| value   | String  | The full phone number including country dialing code. |

#### dateOfBirth
> Registered birthdate for the account holder.

| Param   | Type    | Validations / Notes |
|---------|---------|---------------------|
| year    | Number  | YYYY                |
| month   | Number  | MM                  |
| day     | Number  | DD                  |

#### accountPreferences
> The Payees preferred currency and language

| Param     | Type    | Validations / Notes |
|-----------|---------|---------------------|
| currency  | String  | The preferred wallet currency of the member. See [Currencies](https://jsapi.apiary.io/apis/netellerrestapiv1/reference/0/payments/send-payment.html#currencies) for complete list. |
| language  | String  | The preferred language of the member. See [Languages](http://paysafegroup.github.io/neteller_rest_api_v1/#/introduction/technical-introduction/languages) for complete list. |


#### accountBalance
> The members currently available balance.

| Param     | Type    | Validations / Notes     |
|-----------|---------|-------------------------|
| amount    | Number  | Amount in cents         |
| currency  | Number  | The associated currency |
