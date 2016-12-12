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

### Send Payment
An outbound request that will issue a payment from your merchant account to the recipient.
You can push to a Neteller account or any valid email and NETELLER will notify the recipient via email. If the reciepient is a NETELLER member the funds will deposit immediately. If not they will be required to register in order to access the funds. A URL to the signup flow is returned in the payment request for this purpose. If additional profile info is supplied with the request this will be used to auto-fill the signup page the user will be directed to.


|Item                                | Value              |
|------------------------------------|--------------------|
| Scope required to initiate request | none (default)     |
| Scope(s) impacting response        | `account_enhanced_profile account_contact`|
| Expandable Resources	             | customer           |
| Related Resources (if applicable)	 | none               |

#### Send Payment To Email - Request

| Param                                 | Type    | Required |
|---------------------------------------|---------|----------|
| [payeeProfile](#payeeProfile)         | `{}`    | Yes      |
| [transaction](#transaction-request)   | `{}`    | Yes      |
| message                               | String  | No       |


#### Send Payment To Email - Response
> Http Code : 201

| Param                                 | Type    | Required |
|---------------------------------------|---------|----------|
| customer                              | `{}`    | Yes      |
| [transaction](#transaction-response)  | `{}`    | Yes      |
| [links](#links)                       | `[]`    | No       |

___customer___ `Expandable`
> The associated customer information for the payment.
> Note the actual object exists as `customer : { link: { url: "" ... }}`

| Param       | Type    | Validations / Notes |
|-------------|---------|---------------------|
| url         | String  | String, is url      |
| rel         | String  | String, `customer`  |
| method      | String  | String, `GET`       |


#### Send payment to non-registered email with profile data to provide a signup link - Request

| Param                               | Type    | Required |
|-------------------------------------|---------|----------|
| [payeeProfile](#payeeProfile)       | `{}`    | Yes      |
| [transaction](#transaction-request) | `{}`    | Yes      |
| message                             | String  | No       |



#### Send payment to non-registered email with profile data to provide a signup link - Response
> Http Code 201

| Param                                 | Type    | Required |
|---------------------------------------|---------|----------|
| customer                              | `{}`    | Yes      |
| [transaction](#transaction-response)  | `{}`    | Yes      |
| [links](#links)                       | `[{}]`  | No       |


___customer___
> The associated customer information for the payment.

| Param             | Type    | Validations / Notes                         |
|-------------------|---------|---------------------------------------------|
| customerId        | String  | `CUS_0d676b4b-0eb8-4d78-af25-e41ab431e325`  |
| accountProfile    | `{}`    | See [object](#accountProfile)               |
| verificationLevel | String  | See notes on verificationLevel below        |
| availableBalance  | `{}`    | See [object](#availableBalance)             |








### Request Payment
An inbound payment request which facilitates collecting payment from a NETELLER member to your merchant account.

You are required to render a secure browser form. This allows the user to enter the amount to transfer and confirm the transaction with their verification code (Secure Id or Google Authenticator OTP). If approved funds will transfer immediately. NETELLER will notify the sender via email.

|Item                                | Value              |
|------------------------------------|--------------------|
| Scope required to initiate request | none (default)     |
| Scope(s) impacting response        | `account_enhanced_profile account_contact`|
| Expandable Resources	             | customer           |
| Related Resources (if applicable)	 | none               |


#### Request Payment - Request

| Param                                | Type | Validations / Notes           |
|--------------------------------------|------|-------------------------------|
| [paymentMethod](#paymentMethod)      | `{}` | Unique to merchants system    |
| [transaction](#transaction-response) | `{}` | Amount in cents               |
| verificationCode  | String  | 6 digit Secure Id, or Authentication Code OTP |

The member verification code for the transaction. This is the members 6 digit Secure Id, or Authentication Code OTP (one time password) if the member has two step authentication enabled.

#### Request Payment - Response
> Http Code 201

| Param                                 | Type    | Required |
|---------------------------------------|---------|----------|
| customer                              | `{}`    | Yes      |
| [transaction](#transaction-response)  | `{}`    | Yes      |
| [links](#links)                       | `[{}]`  | No       |



### Lookup Payment
Can be used to inquire about any API initiated transactions. Additional detail will be provided depending on the transaction type and the current state of the transaction. This API request returns a payment object. Response values vary dependent upon the type of payment being queried. If using your merchant reference id pass the parameter of ?refType=merchantRefId to denote that you are querying with your merchant reference ID and not the NETELLER Transaction ID.

|Item                                | Value              |
|------------------------------------|--------------------|
| Scope required to initiate request | none (default)     |
| Scope(s) impacting response        | `account_enhanced_profile account_contact`|
| Expandable Resources	             | customer           |
| Related Resources (if applicable)	 | none               |

#### Lookup Payment - Request
> This is a `GET` request with all params in the query string

| Params    | Type    | Validations / Notes   | Default/Possible Values      |
|-----------|---------|-----------------------|------------------------------|
| id        | String  | id of the transaction | `required param`             |
| refType   | String  | Optional ID type      | `NETELLER` / `merchantRefId` |
| expand    | String  | Resource to expand    |  `null` / `customer`         |


#### Lookup Payment - Response

| Param                                  | Type    | Required |
|----------------------------------------|---------|----------|
| customer                               | `{}`    | Yes      |
| [billingDetails](#billingDetail)       | `{}`    | Yes      |
| [transaction](#transaction-response)   | `{}`    | Yes      |
| [links](#links)                        | `[{}]`  | No       |


### List Payment
The payments lookup function can be used to retrieve a listing of transactions associated with your account. This API request returns a list of payment objects.

|Item                                | Value              |
|------------------------------------|--------------------|
| Scope required to initiate request | none (default)     |
| Scope(s) impacting response        | none               |
| Expandable Resources	             | customer           |
| Related Resources (if applicable)	 | none               |

#### List Payment - Request
> This is a `GET` request with all params in the query string

| Params    | Type    | Validations / Notes   | Default/Possible Values      |
|-----------|---------|-----------------------|------------------------------|
| startDate | String  | Transaction report start date | past 7 days / ISO 8601 format (UTC) YYYY-MM-DDThh:mm:ssZ |
| endDate   | String  | Transaction report end date. If you supply an endDate you must supply a start date | `null`/ ISO 8601 format (UTC) YYYY-MM-DDThh:mm:ssZ |
| sortOrder | String  | Sort order (optional) | `desc` on transaction date/ `desc asc` |
| limit     | Number  | Number of records to return. Max is 100 | 10 / `null`|
| offset    | Number  | Allows you to fetch the next set of resources        | `null` / `null`|

#### List Payment - Response
> The first 3 attributes returned in the response are contained within a list `[]`.

| Param                                  | Type    | Required | Index       |
|----------------------------------------|---------|----------|-------------|
| customer                               | `{}`    | Yes      | 0           |
| [billingDetails](#billingDetail)       | `{}`    | Yes      | 1           |
| [transaction](#transaction-response)   | `{}`    | Yes      | 2           |
| [links](#links)                        | `[{}]`  | No       | Not in list |


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

#### links
> This is an array of self-referencing links. Takes the form of `[{}]`

| Param | Type    | Validations / Notes |
|-------|---------|---------------------|
| rel   | String  | `"self"`            |
| href  | String  | Is String           |


#### accountProfile
#### payeeProfile
> `Request`  : The recipient of the payment. At minimum, the payee's email address or NETELLER Account ID must be supplied.

| Param            | Type    | Validations / Notes  |
|------------------|---------|----------------------|
| email/account_id | String  | Is a String          |

> `Response`  :  The associated account profile. This is normally nested within the customer object.

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


#### paymentMethod
> The source of funds for the transfer into the merchant account.

| Param     | Type    | Validations / Notes                                    |
|-----------|---------|--------------------------------------------------------|
| type      | String  | Type of payment, `Neteller`                            |
| value     | String  | payment type and source of funds `jsmith@neteller.com` |


#### billingDetail
Element                 |Type                       |Description
---                     |---                        |---
email                   |string `length<=100`   |The account email address.
firstName               |string `length<=25`    |The clients first name, or given name.
lastName                |string `length<=25`    |The clients last name, or family name.
address1                |string `length<=35`    |The clients residential, or street address.
address2                |string `length<=35`    |Continuation of the clients residential, or street address.
address3                |string `length<=35`    |Continuation of the clients residential, or street address.
city                    |string `length<=50`    |The clients city of residence.
countrySubdivisionCode  |string `length=2`      |The ISO 3166-2 code indicating the state/province/district or other value denoting the clients country subdivision \(e.g. BE=Berlin. 13=Tôkyô \[Tokyo\], AB=Alberta\)
country                 |string `length=2`      |The ISO 3166-1 Alpha 2-code for the clients country of residence \(e.g. Germany = DE, JP=Japan. CA=Canada\)
postCode                |string `length<=10`    |The zip code, or postal code, of the clients residence.
lang                    |string `length=5`      |The preferred language of communication. See [Languages](#languages) for complete list.
