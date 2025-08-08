---
title: API Reference

language_tabs: 
  - shell
  - javascript

# includes:
#   - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Suitcase settlement API
---

# Introduction

This document serves as an overview for the API enpoints of: </br>
- **Suitcase Settlement App** </br>
- **Suitcase Admin Dashboard**.

We have examples in Shell and JavaScript. You can view code examples to the right, and you can switch the programming language of the examples with the tabs in the top right.
<aside class="warning">
Note that all routes are only available via https
</aside>


# Settlement Api V2
The API definitions for **V2** are up-to-date. <br>
These endpoints are actively maintained and used by the client side code in <a href="https://suitcase.legal">**Settlement App**</a>.

Potential error message are of the following schema: <br>
{ <br>
  &emsp;&emsp;"status_code": status_code, <br>
  &emsp;&emsp;"error": {  <br>
    &emsp;&emsp;&emsp;&emsp;"message": "an error message" <br>
    &emsp;&emsp;&emsp;&emsp;"code": "string that represents the error code" (eg. "unauthorized", "user_not_found"...) <br>
  &emsp;&emsp;} <br>
}


## Register User
This endpoint registers a new user in the system.
### Https Request
`POST https://api.suitcase.legal/v2/user`

```shell
curl -X POST "https://api.suitcase.legal/v2/user" \
  -d "user_type=private" \
  -d "email=test@example.com" \
  -d "password=a_strong_password" \
  -d "name=John Doe" \
  -d "title=Dr." \
  -d "salutation=Mr." \
  -d "street=123 Main St" \
  -d "zip=12345" \
  -d "city=Anytown" \
  -d "country=USA" \
  -d "care_of=abc-def" \
  -d "account_association=Meinrecht" \
  -d "phone=555-123-4567"
```
```javascript
const url = 'https://api.suitcase.legal/v2/user';
const payload = new URLSearchParams({
  'user_type': 'private',
  'email': 'test@example.com',
  'password': 'a_strong_password',
  'name': 'John Doe',
  'title': 'Dr.',
  'salutation': 'Mr.',
  'street': '123 Main St',
  'zip': '12345',
  'city': 'Anytown',
  'country': 'USA',
  'care_of': 'abc-def',
  'account_association': 'Meinrecht',
  'phone': '555-123-4567'
});
const options = {
	method: 'POST',
	headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
	body: payload
});
```

### Body Parameters
Parameter | Required | Description
--------- | -------- | -----------
user_type | Yes | (String) The type of user account (e.g., "private", "company", "lawyer").
email | Yes | (String) The user's email address. Must be unique.
password | Yes | (String) The user's password.
name | Yes | (String) The user's name. If it is a company, it is the company name.
salutation | Yes | (String) The user's salutation (e.g., "Mr.", "Ms.").
street | Yes | (String) The user's street and house number.
zip | Yes | (String) The user's postal code.
city | Yes | (String) The user's city.
country | Yes | (String) The user's country.
phone | Yes | (String) The user's phone number.
title | No | (String) The user's academic or professional title.
care_of | No | (String) "Care of" address line.
account_association | No | (String) Any associated account identifier.
user_id | No | (Number) The user id in case a temporary user already exists and the email has to be update aswell. (Used in casehub)

### Response Status 
Code        | Description | Error_code
----------- | ----------- | -----------
201 | The user was successfully created. | 
400 | The request was malformed | malformed_data
409 | The user already exists. | user_conflicted
500 | An unexpected error occurred on the server. | internal_error

## Password reset request
This endpoint initializes a password reset process by sending the user an email including a short lived token.
The token can be used to set a new password for the user.
### Https Request
`POST https://api.suitcase.legal/v2/password/reset`

```shell
curl -X POST "https://api.suitcase.legal/v2/password/reset" \
  -d "email=test@example.com" \
```
```javascript
await fetch('https://api.suitcase.legal/v2/password/reset', {
	method: 'POST',
	headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
	body: new URLSearchParams({'email': 'test@example.com'}),
});
```
### Body Parameters
Parameter | Required | Description
--------- | -------- | -----------
email | Yes | (String) Note that this value is required as plaintext.

### Response Status 
Code        | Description | Error_code
----------- | ----------- | ----------
200 | The password request was successfully initialized. |
400 | User not found. | user_not_found
400 | The request was malformed. | malformed_data
403 | User not confirmed. | user_unconfirmed
500 | An unexpected error occurred on the server. | internal_error

## Change user password
This endpoint finishes the password reset process by changing the password of a user based on a short lived token.
### Https Request
`PUT https://api.suitcase.legal/v2/password/reset`

```shell
curl -X PUT "https://api.suitcase.legal/v2/password/reset" \
  -d "token=123456789" \
  -d "password=new_password" \
```
```javascript
await fetch('https://api.suitcase.legal/v2/password/reset', {
	method: 'PUT',
	headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
	body: new URLSearchParams({'token': '123456789', 'password': 'new_password'})
});
```

### Body Parameters
Parameter | Required | Description
--------- | -------- | -----------
password | Yes | (String) The new password.
token | Yes | (String) The token that is tied to the user account.

### Response Status 
Code        | Description | Error_code
----------- | ----------- | ----------
204 | The password request was successfully finished. No new content is coming from the server. | 
403 | No access to this resource | user_forbidden
500 | An unexpected error occurred on the server. | internal_error

## Contract preview
This endpoint returns a preview of the contracts for a given use case
### Https Request
`GET https://api.suitcase.legal/v2/contract/preview`

```shell
curl -X GET "https://api.suitcase.legal/v2/contract/preview?usecase=MieteKaution&factors=0"
```
```javascript
await fetch('https://api.suitcase.legal/v2/contract/preview?usecase=MieteKaution&factors=0', {
	method: 'GET',
});

```
> The above request returns JSON (shortened) as follows:

```json
[
   {
      "header_de":"VERGLEICHSVERTRAG",
      "header_en":"SETTLEMENT AGREEMENT",
      "ref_id":"SET-XXXX-XX-XXXXXX"
   },
   {
      "de_text":"Die nachfolgend bezeichneten Parteien schließen zur Erledigung des Streits aus dem Mietvertrag vom XXX über die Rückzahlung der Mietkaution folgenden Vergleich:",
      "de_title":"Rückzahlung der Mietkaution",
      "en_text":" The parties named below conclude the following settlement to settle the dispute arising from the rental agreement dated XXX regarding the repayment of the rental deposit:",
      "en_title":"Repayment rental deposit"
   },
   {
      "de_text":[
         {
            "text":"Der Vermieter verpflichtet sich, binnen zehn (10) Bankarbeitstagen nach Abschluss dieser Vereinbarung an den Mieter €XXX zu zahlen. Die bereits geleistete Zahlung i.H.v. €XXX wird darauf angerechnet."
         },
         {
            "text":"Im Falle eines Mietkautionssparbuchs mit Sperrvermerk tritt an die Stelle der Zahlungspflicht nach Ziff. 1 die Auflösung desselben. Der Vermieter hat einen Anspruch i.H.v. €XXX; der Restbetrag einschließlich etwaiger Erträge wird an den Mieter ausgekehrt. Beide Seiten verpflichten sich, ihre Zustimmung zur Auflösung des Mietkautionssparbuchs im Verhältnis der vorbezeichneten Geldbeträge zu erteilen."
         },
         {
            "text":"Im Falle eines Treuhandkontos des Vermieters tritt an die Stelle der Zahlungspflicht nach Ziff. 1 die Auflösung desselben. Der Vermieter hat einen Anspruch i.H.v. €XXX aus dem Treuhandkonto; der Restbetrag einschließlich etwaiger Erträge wird an den Mieter ausgekehrt."
         },
         {
            "text":"Im Falle einer Mietkautionsbürgschaft einigen sich die Parteien an Stelle der Zahlungspflicht nach Ziff. 1 im Wege eines Änderungsvertrags, dass der Vermieter die Mietsicherheit i.H.v. €XXX verwerten darf. Der Vermieter verpflichtet sich, Zug-um-Zug gegen Zahlung i.H.v. €XXX die Bürgschaftsurkunde an den Bürgen herauszugeben. Das Innenverhältnis zwischen Mieter und Bürge bleibt hiervon unberührt."
         },
         {
            "text":"Im Falle eines Kautionskontos eines Dritten gilt Ziff. 2 entsprechend."
         }
      ],
      "de_title":"Rückzahlung",
      "en_text":[
        "...": ""
      ],
      "en_title":"Repayment"
   },
   {
      "de_text": "...",
      "de_title":"Rücktrittsrecht",
      "en_text": "...",
      "en_title":"Right of withdrawal"
   },
   {
      "texts": "..."
   }
]
```

### Query Parameters
Parameter | Required | Description
--------- | -------- | -----------
usecase | Yes | (String) The use case for the contract
factors | Yes | (u16) A number whose binary representation gives information about what additional contract factors to include (bitflags)

### The bitflags are defined as follows. 
_Termination date: 0b000000000000001_ <br>
_Exemption instant: 0b000000000000010_ <br>
_Exemption date: 0b000000000000100_ <br>
_Sprinter clause: 0b000000000001000_ <br>
_Overtime: 0b000000000010000_ <br>
_Vacation: 0b000000000100000_ <br>
_Christmas pay: 0b000000001000000_ <br>
_Vacation pay: 0b000000010000000_ <br>
_Commission: 0b000000100000000_ <br>
_Annual bonus: 0b000001000000000_ <br>
_Other compensation: 0b000010000000000_ <br>
_Company car: 0b000100000000000_ <br>
_Provided items: 0b001000000000000_ <br>
_Arbeitszeugnis: 0b010000000000000_ <br>
_Gerichtsvergleich: 0b100000000000000_
<aside class="notice">Note that factors are only relevant for TerminationCase and CancellationCase </aside>

### Response Status 
Code        | Description | Error_code 
----------- | ----------- | ----------
200 | The response is a json array containing the clauses of the contract. |
400 | The request was malformed. | malformed_data

## Fetch case via casehub
This endpoint fetches a case via a token to be visible for users that don't have a user account.
### Https Request
`GET https://api.suitcase.legal/v2/casehub`

```shell
curl -X GET "https://api.suitcase.legal/v2/casehub?case_uuid=ABCDE12345"
```
```javascript
await fetch('https://api.suitcase.legal/v2/casehub?case_uuid=ABCDE12345', {
	method: 'GET',
});
```

### Query Parameters
Parameter | Required | Description
--------- | -------- | -----------
case_uuid | Yes | (String) uuid that is tied to a case.

### Response Status 
Code        | Description | Error_code
----------- | ----------- | ----------
200 | The response has no body if the case is ongoing or finished. If the case status is 'started' the response has a json body containing relevant case information. |
409 | The case status is not 'started' | 
400 | The request was malformed | malformed_data
400 | The case does not exist | case_not_found
500 | An unexpected error occurred on the server. | internal_error

# Authenticated Settlement Api V2 

```shell
curl "api_endpoint_here" \
  -H "Authorization: Bearer key"
```

```javascript
let headers = new Headers();
headers.set('Authorization', `Bearer ${key}`);
await fetch("api_endpoint_here", {
	method: 'GET',
	headers,
});
```

> Make sure to replace `key` with your API key.

The following API's are only accessible if an authorization header is present. <br>
Additionally the backend extracts necessary user information (email) from the jwt on every request.

<aside class="notice">
Ask an admin for a test key if required.
</aside>

## Fetch cases
This endpoint fetches cases with optional filters.
### Https Request
`GET https://api.suitcase.legal/v2/case`

```shell
curl -X GET "https://api.suitcase.legal/v2/case?filter_status=Failed"
```
```javascript
await fetch('https://api.suitcase.legal/v2/case?filter_status=Failed', {
	method: 'GET',
});
```

> The above request returns JSON (shortened) as follows:

```json
{
   "cases":[
      {
         "id":1,
         "offer_1":null,
         "offer_2":null,
         "offer_3":null,
         "request_1":70000,
         "request_2":140000,
         "request_3":120000,
         "start_date":"2025-07-07T09:05:30.162034Z",
         "claim":{
            "type":"DepositCase",
            "deposit":100000,
            "date_start":"2015-11-11T00:00:00Z",
            "date_end":"2025-07-07T00:00:00Z",
            "received_value":100,
            "property":"ABC Straße, 33102, Paderborn"
         },
         "status":"Failed",
         .
         .
         .
      }
   ],
   "num_cases":1
}
```


### Query Parameters
Parameter | Required | Description
--------- | -------- | -----------
offset            | No | (u32) The cases are paged by 50 cases per batch. Offset of 1 fetches the first 50 cases while offset of 2 fetches the cases 50 - 100.
search            | No | (String) Filter the case by a search string. Fields that apply the search string are claimant + respondent information and ref_id.
limit_days_past_start | No | (i32) The cases are filtered by the number of days since the cases start_date passed. 3 indicates that the cases start_date cannot be older than 3 days.
limit_days_past_end | No | (i32) The cases are filtered by the number of days since the cases end_date passed. 3 indicates that the cases end_date cannot be older than 3 days.
filter_status     | No | (String) The cases are filtered by their status. Values can be 'Laufend', 'Erfolgreich' and 'Erfolglos'.

### Response Status 
Code        | Description | Error_code
----------- | ----------- | ----------
200 | The response returns a JSON structure that contains an array with all the cases of the user after the filter is applied. Additonally the JSON structure includes the total number of cases for the user
400 | The request was malformed | malformed_data
401 | Invalid jwt token | unauthorized
500 | An unexpected error occurred on the server. | internal_error

## Fetch case file
This endpoint fetches a case file.
### Https Request
`GET https://api.suitcase.legal/v2/case/file`

```shell
curl -X GET "https://api.suitcase.legal/v2/case/file?id=1&name=Testfile.pdf"
```
```javascript
await fetch('https://api.suitcase.legal/v2/case/file?id=1&name=Testfile.pdf', {
	method: 'GET',
});
```

### Query Parameters
Parameter | Required | Description
--------- | -------- | -----------
id | Yes | (i32) The id of the case, the file belongs to.
name | Yes | (String) The name of the file to be downloaded.

### Response Status 
Code        | Description | Error_code
----------- | ----------- | ---------
200 | The response is a stream with the contents of the file.
400 | Case does not exist | case_not_found
401 | Invalid jwt token | unauthorized
500 | An unexpected error occurred on the server. | internal_error

## Bid on case 
This endpoint places a bid for an open case.
### Https Request
`PATCH https://api.suitcase.legal/v2/case/bid`

```shell
curl -X PATCH "https://api.suitcase.legal/v2/case/bid" \
  -d "{ "id": 5, "bids": [100000, 50000, 20000] }" 

```
```javascript
const url = 'https://api.suitcase.legal/v2/case/bid';
const payload = {
  id: 5,
  bids: [100000, 50000, 20000],
};
const options = {
  method: 'PATCH',
  headers: {
    'Content-Type': 'application/json',
  },
  body: payload,
};
const response = await fetch(url, options);
```

### Body Parameters
Parameter | Required | Description
--------- | -------- | -----------
id | Yes | (Integer) The id of the case.
bids | Yes | (Integer array) The bid values. (1 or 3)

### Response Status 
Code        | Description | Error_code
----------- | ----------- | ----------
200 | The response contains the updated case information as a JSON object.
400 | The request was malformed | malformed_data
400 | Case does not exist | case_not_found
401 | Invalid jwt token | unauthorized
500 | An unexpected error occurred on the server. | internal_error

## Fetch user
This endpoint fetches a user.
### Https Request
`GET https://api.suitcase.legal/v2/user`

```shell
curl -X GET "https://api.suitcase.legal/v2/user"
```
```javascript
await fetch('https://api.suitcase.legal/v2/user', {
	method: 'GET',
});
```

> The above request returns a JSON structure as follows:

```json
{
   "user_id": "10",
   "user_type":"Lawyer",
   "email":"test@suitcase.legal",
   "name":"Max Mustermann",
   "salutation":"Herr",
   "title":null,
   "care_of":null,
   "address":{
      "street":"Am Kartoffelgarten 14",
      "zip":"81671",
      "city":"München",
      "country":"Germany"
   },
   "phone":"+49123456789",
   "account_association":{
      "Custom":"Suitcase"
   },
}
```

### Response Status 
Code        | Description | Error_code
----------- | ----------- | ----------
200 | The response returns the user information in JSON format.
400 | User does not exist | user_not_found
400 | The user data is malformed | malformed_data
401 | Invalid jwt token | unauthorized
403 | Unconfirmed user | user_unconfirmed
500 | An unexpected error occurred on the server. | internal_error

## Resend email confirmation
This endpoint resend's a confirmation email for not confirmed accounts.
### Https Request
`POST https://api.suitcase.legal/v2/user/confirmation`

```shell
curl -X POST "https://api.suitcase.legal/v2/user/confirmation"
```
```javascript
await fetch('https://api.suitcase.legal/v2/user/confirmation', {
	method: 'POST',
});
```

### Response Status 
Code        | Description | Error_code
----------- | ----------- | -----------
202 | The request was successful and a new email has been sent.
400 | The user does not exist | user_not_found
401 | Invalid jwt token | unauthorized
500 | An unexpected error occurred on the server. | internal_error

## Case creation
This endpoint creates a new case.
### Https Request
`POST https://api.suitcase.legal/v2/case`

```shell
curl -X POST "https://api.suitcase.legal/v2/case" \
  -d '{ "opposition": { "email": "test@suitcase.legal", "name": "Test GmbH", "street": "Street", "zip": "12345", "city": "City", "country": "Country", "user_type": "temporary_company" },
        "bids": vec![10000, 5000, 500],
        "claim": { "type": "ArbeitKuendigung", "salary": 450000, "date_start": "2022-02-01T00:00:00.000Z", "date_end": "2023-02-01T00:00:00.000Z", "date_notice": "2023-02-01T00:00:00.000Z", "reason":"betriebsbedingt" },
        "is_respondent": false,
        "description": "testdescription"
      }'


```
```javascript
const url = 'https://api.suitcase.legal/v2/case';
const payload = {
  opposition: {
    email: "test@suitcase.legal",
    name: "Test GmbH",
    street: "Street",
    zip: "12345",
    city: "City",
    country: "Country",
    user_type: "temporary_company"
  },
  bids: [10000, 5000, 500],
  claim: {
    type: "ArbeitKuendigung",
    salary: 450000,
    date_start: "2022-02-01T00:00:00.000Z",
    date_end: "2023-02-01T00:00:00.000Z",
    date_notice: "2023-02-01T00:00:00.000Z",
    reason:"betriebsbedingt"
  },
  is_respondent: false
};
const options = {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: payload,
};
const response = await fetch(url, options);
```

### Body Parameters
Parameter | Required | Description
--------- | -------- | -----------
opposition | Yes | (JSON) Nested json object(UserData) for the opposition
bids | Yes | (Integer array) The bid values. (1 or 3 bids)
claim | Yes | (JSON) Nested json object(claim) for the claim
mandate | No | (JSON) Nested json object(UserData) for a mandate (relevant if case opener is lawyer)
opposition_mandate | No | (JSON) Nested json(UserData) object for an opposition_mandate (relevant if opposition has lawyer)
is_respondent | No | (boolean) Flag if case is opened by respondent. (Defaults to false)
insurance | No | (String) Insurance of the case opener.
description | No | (String) Case description.

### UserData Object
Parameter | Required | Description
--------- | -------- | -----------
email | No | (String) Email of the participant. (Defaults to random generated email).
name | Yes | (String)
street | Yes | (String)
zip | Yes | (String)
city | Yes | (String) 
country | Yes | (String)
phone | No | (String)
care_of | No | (String) Alternative address for post.

### Claim Object
The claim object can take many shaped based on the use case. Viable constructs are shown below.

### HaftRegress Object
Parameter | Required | Description
--------- | -------- | -----------
type | Yes | (String) "HaftRegress"
reclaim_value | Yes | (Integer) The value to be reclaimed.
reclaim_number | Yes | (String) The unique identifier for the reclaim.
invoice_number | Yes | (String) The associated invoice number.

### BankPhishing Object
Parameter | Required | Description
--------- | -------- | -----------
type | Yes | (String) "BankPhishing"
charges | Yes | (Array of Date/Time UTC and Integer tuples) A list of charges, each with a timestamp and an amount.
identifier | No | (String) An optional identifier for the phishing attempt.
customer_identifier | No | (String) An optional identifier for the customer involved.
lawsuit | Yes | (Boolean) Indicates if a lawsuit has been filed.
cost_covered | Yes | (Boolean) Indicates if the costs related to the phishing are covered.

### ArbeitFreiwilligenprogramm Object
Parameter | Required | Description
--------- | -------- | -----------
type | Yes | (String) "ArbeitFreiwilligenprogramm"
id | Yes | (Integer) Unique identifier for the volunteer termination case.
date_start | Yes | (Date/Time UTC) The start date of the volunteer's engagement.
salary | Yes | (Integer) The monthly salary of the volunteer.
kids | Yes | (Integer) Number of children.
in_need_of_care_relatives | Yes | (Integer) Number of relatives in need of care.
disability | Yes | (Boolean) Indicates if the volunteer has a disability.
factors | No | (JSON) Additional factors influencing the volunteer termination.

### HaftDirekt Object
Parameter | Required | Description
--------- | -------- | -----------
type | Yes | (String) "HaftDirekt"
damage_subject | Yes | (String) The subject or nature of the damage claim.
damage_number | Yes | (String) The unique reference number for the damage claim.

### ReisenFlugrechte Object
Parameter | Required | Description
--------- | -------- | -----------
type | Yes | (String) "ReisenFlugrechte"
departure | Yes | (String) The departure airport code or name.
destination | Yes | (String) The destination airport code or name.
date_time | Yes | (Date/Time UTC) The scheduled date and time of the flight.
flight_number | Yes | (String) The flight identification number.

### ArbeitAufhebung Object
Parameter | Required | Description
--------- | -------- | -----------
type | Yes | (String) "ArbeitAufhebung"
salary | Yes | (Integer) The salary relevant to the cancellation.
date_start | Yes | (Date/Time UTC) The original start date of the cancelled agreement.
date_end | Yes | (Date/Time UTC) The original end date of the cancelled agreement.
date_signature | Yes | (Date/Time UTC) The date the cancellation agreement was signed.
factors | No | (JSON) Additional factors for the cancellation use case.

### MieteKuendigung Object
Parameter | Required | Description
--------- | -------- | -----------
type | Yes | (String) "MieteKuendigung"
rent | Yes | (Integer) The monthly rent amount.
date_end | Yes | (Date/Time UTC) The desired termination date for the rental agreement.
property | Yes | (String) The address of the rented property.

### MieteKaution Object
Parameter | Required | Description
--------- | -------- | -----------
type | Yes | (String) "MieteKaution"
deposit | Yes | (Integer) The original deposit amount.
date_end | Yes | (Date/Time UTC) The end date of the deposit period.
received_value | Yes | (Integer) The value received from the deposit.
property | Yes | (String) The address of the property associated with the deposit.

### ArbeitKuendigung Object
Parameter | Required | Description
--------- | -------- | -----------
type | Yes | (String) "ArbeitKuendigung"
salary | Yes | (Integer) The employee's salary at the time of termination.
date_start | Yes | (Date/Time UTC) The employment start date.
date_end | Yes | (Date/Time UTC) The employment end date.
date_notice | Yes | (Date/Time UTC) The date the termination notice was given.
reason | Yes | (String) The reason for the termination.
factors | No | (JSON) Additional factors influencing the termination case.

### Factors Object
Parameter | Required | Description
--------- | -------- | -----------
termination_date | No | (Date/Time UTC) An alternative or adjusted termination date.
exemption_instant | No | (Boolean) Indicates if an instant exemption applies.
exemption_date | No | (Date/Time UTC) An alternative date for exemption.
sprinter_clause | No | (Integer) Value related to a sprinter clause.
overtime | No | (Integer) Accumulated overtime hours.
vacation | No | (Integer) Remaining vacation days.
christmas_pay | No | (Integer) Amount of Christmas bonus.
vacation_pay | No | (Integer) Amount of vacation pay.
commission | No | (Integer) Commission amount.
annual_bonus | No | (Integer) Annual bonus percentage.
other_compensation | No | (JSON) Details about other compensation components.
company_car | No | (JSON) Details about the company car.
provided_items | No | (String) Description of items provided by the company.
arbeitszeugnis | No | (Integer) Value related to the employment reference (Arbeitszeugnis).
gerichtsvergleich | No | (String) Details about a court settlement (Gerichtsvergleich).

### Compensation Object
Parameter | Required | Description
--------- | -------- | -----------
reason | Yes | (String) The reason for the additional compensation.
amount | Yes | (Integer) The amount of the additional compensation.

### Car Object
Parameter | Required | Description
--------- | -------- | -----------
car_date | Yes | (Date/Time UTC) The date related to the company car.
car_pay | No | (Integer) The monetary value or payment associated with the car.

### Response Status 
Code        | Description | Error_code
----------- | ----------- | ----------
201 | The response contains a json object with the id, ref_id and stripe payment id (in case of 0,00 the stripe payment id is 'Whitelisted')
400 | The request or internal data was malformed | malformed_data
400 | The user does not exist | user_not_found
401 | The jwt is invalid | unauthorized
500 | An unexpected error occurred on the server. | internal_error

## Upload case file
This endpoint uploads a file for a given case.
### Https Request
`POST https://api.suitcase.legal/v2/case/file`

```shell
curl -X POST "https://api.suitcase.legal/v2/case/file" \
  --form id='1' ...
```
```javascript
const url = 'https://api.suitcase.legal/v2/case/file';
const formData = new FormData();

formData.append('id', "1");
files.forEach((file) => { // files is an array of files
		formData.append('file', file);
});

const options = {
  method: 'POST',
  body: formData,
};
const response = await fetch(url, options);
```

### FormData Parameters
Parameter | Required | Description
--------- | -------- | -----------
id | Yes | (i32) Id of the case
files | Yes | (Array[Files]) An array of files

### Response Status 
Code        | Description | Error_code
----------- | ----------- | ----------
201 | The files were uploaded.
400 | The request or internal data was malformed | malformed_data
400 | The user does not exist | user_not_found
401 | Invalid jwt | unauthorized
500 | An unexpected error occurred on the server. | internal_error

## Delete a case file
This endpoint deletes a file for a given case.
### Https Request
`DELETE https://api.suitcase.legal/v2/case/file`

```shell
curl -X DELETE "https://api.suitcase.legal/v2/case/file?id=1&name=Test.pdf"
```
```javascript
await fetch('https://api.suitcase.legal/v2/case/file?id=1&name=Test.pdf', {
	method: 'DELETE',
});
```

### Query Parameters
Parameter | Required | Description
--------- | -------- | -----------
id | Yes | (i32) Id of the case
name | Yes | (String) Name of the file

### Response Status 
Code        | Description | Error_code
----------- | ----------- | ----------
204 | The file was deleted and no content new content is returned from the server.
400 | Case does not exist | case_not_found
401 | Invalid jwt | unauthorized
500 | An unexpected error occurred on the server. | internal_error

## Generate limitation contract
This endpoint generates a limitation contract for a Failed/Expired case.
### Https Request
`POST https://api.suitcase.legal/v2/contract/limitation`

```shell
curl -X POST "https://api.suitcase.legal/v2/contract/limitation"  \
  d "id=1" \
```
```javascript
const url = 'https://api.suitcase.legal/v2/contract/limitation';
const payload = new URLSearchParams({
  'id': 'id',
});
const options = {
  method: 'POST',
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded',
  },
  body: payload,
};
const response = await fetch(url, options);
```

### Form Parameters
Parameter | Required | Description
--------- | -------- | -----------
id | Yes | (i32) Id of the case

### Response Status 
Code        | Description | Error_code
----------- | ----------- | -----------
201 | The contract was generated.
400 | The internal data was malformed | malformed_data
400 | Case does not exist | case_not_found
401 | JWT invalid | unauthorized
500 | An unexpected error occurred on the server. | internal_error

## User feedback
This endpoint creates user feedback.
### Https Request
`POST https://api.suitcase.legal/v2/feedback`

```shell
curl -X POST "https://api.suitcase.legal/v2/feedback"  \
  d "user_id=5" \
  d "rating=10" \
  d "feedback=big bug" \
  d "feedback_type=bug" \
```
```javascript
const url = 'https://api.suitcase.legal/v2/feedback';
const payload = new URLSearchParams({
  'user_id': 5,
  'rating': 10,
  'feedback': 'big bug',
  'feedback_type': 'bug',
});
const options = {
  method: 'POST',
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded',
  },
  body: payload,
};
const response = await fetch(url, options);
```

### Form Parameters
Parameter | Required | Description
--------- | -------- | -----------
user_id | Yes | (Integer) id of a user
rating | Yes | (i32) rating points (1-10)
feedback | Yes | (String) Feedback text
feedback_type | Yes | (String) feedback type ("feature request", "bug" etc...)

### Response Status 
Code        | Description | Error_code
----------- | ----------- | ----------
201 | The feedback was created.
401 | JWT invalid | unauthorized
500 | An unexpected error occurred on the server. | internal_error

## Case statistics
This endpoint creates case statistics.
### Https Request
`POST https://api.suitcase.legal/v2/statistics`

```shell
curl -X POST "https://api.suitcase.legal/v2/statistics" \
  -H "Content-Type: application/json" \
  -d '{
    "id": 123,
    "offer_1_probability": 0.1,
    "offer_2_probability": 0.2,
    "offer_3_probability": 0.3,
    "request_1_probability": 0.4,
    "request_2_probability": 0.5,
    "request_3_probability": 0.6,
    "registration": {
      "recommendation": 0.9,
      "dispute_value": 100000,
      "duration_registration": "duration"
    }
  }'
```
```javascript
const url = 'https://api.suitcase.legal/v2/statistics';
const payload = {
  id: 123,
  offer_1_probability: 0.1,
  offer_2_probability: 0.2,
  offer_3_probability: 0.3,
  request_1_probability: 0.4,
  request_2_probability: 0.5,
  request_3_probability: 0.6,
  registration: {
    recommendation: 0.9,
    dispute_value: 100000,
    duration_registration: "PT1H30M"
  }
};
const options = {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: payload,
};
const response = await fetch(url, options);
```

### Body Parameters
Parameter | Required | Description
--------- | -------- | -----------
offer_1_probability | No | (f32) Probability for offer 1
offer_2_probability | No | (f32) Probability for offer 2
offer_3_probability | No | (f32) Probability for offer 3
request_1_probability | No | (f32) Probability for request 1
request_2_probability | No | (f32) Probability for request 2
request_3_probability | No | (f32) Probability for request 3
registration | No | (JSON) Nested json object(CaseCreationStatFields) 

### CaseCreationStatFields Object
Parameter | Required | Description
--------- | -------- | -----------
recommendation | Yes | (f32) Recommendation value
dispute_value | Yes | (i64) Dispute value in cents
duration_registration | Yes | (String) Duration of registration in ISO 8601 format

### Response Status 
Code        | Description | Error_code
----------- | ----------- | -----------
201 | The statistic was created.
400 | The case does not exist | case_not_found
401 | JWT invalid | unauthorized
500 | An unexpected error occurred on the server. | internal_error

## Case statistics modification
This endpoint modifies case statistics based on user nps for a case.
### Https Request
`PATCH https://api.suitcase.legal/v2/statistics`

```shell
curl -X PATCH "https://api.suitcase.legal/v2/statistics" \
  -d "id=123" \
  -d "nps=7" \
  -d "nps_text=hallo" \
  -d "is_from=respondent" \
```
```javascript
const url = 'https://api.suitcase.legal/v2/statistics';
const payload = new URLSearchParams({
  "id": "123",
  "nps": "7",
  "nps_text": "hallo",
  "is_from": "respondent",
});
const options = {
  method: 'PATCH',
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded',
  },
  body: payload,
};
const response = await fetch(url, options);
```

### Body Parameters
Parameter | Required | Description
--------- | -------- | -----------
id | Yes | (i32) Id of the case
nps | Yes | (i32) Nps score (1-10)
nps_text | Yes | (String) feedback text
is_from | Yes | (String) role of the user in a given case(claimant/respondent)

### Response Status 
Code        | Description | Error_code
----------- | ----------- | ----------
200 | The statistic were modified.
400 | The case does not exist | case_not_found
401 | JWT invalid | unauthorized
500 | An unexpected error occurred on the server. | internal_error

## Case statistics submitted
This endpoint checks if a user has already submitted nps for a case.
### Https Request
`GET https://api.suitcase.legal/v2/statistics/submitted`

```shell
curl -X GET "https://api.suitcase.legal/v2/statistics/submitted?id=1&is_claimant=false"
```
```javascript
await fetch('https://api.suitcase.legal/v2/statistics/submitted?id=1&is_claimant=false', {
	method: 'GET',
});
```

### Query Parameters
Parameter | Required | Description
--------- | -------- | -----------
id | Yes | (i32) Id of the case
is_claimant | Yes | (boolean) Flag if the request is coming from the claimant

### Response Status 
Code        | Description | Error_code
----------- | ----------- | ----------
200 | The response contains a json object with a single field 'nps_submitted: bool'.
400 | The case does not exist | case_not_found
401 | JWT invalid | unauthorized
500 | An unexpected error occurred on the server. | internal_error
