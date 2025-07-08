---
title: API Reference

language_tabs: 
  - shell
  - javascript

includes:
  - errors

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

# Authentication

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

API's that include the **'/set'** or **'/admin'** path are only availiable if an authorization header is present. 


<aside class="notice">
Ask an admin for a test key if API calls to /set or /admin is required.
</aside>

# Settlement App V2
The API definitions for **V2** are up-to-date. <br>
These endpoints are actively maintained and used by the client side code in <a href="https://suitcase.legal">**Settlement App**</a>.

## Register User
This endpoint registers a new user in the system.
### Https Request
`POST https://api.suitcase.legal/v2/user`

```shell
curl -X POST "https://api.suitcase.legal/v2/user" \
  -d "user_type=private" \
  -d "email=test@example.com" \
  -d "password=a_strong_password" \
  -d "firstname=John" \
  -d "lastname=Doe" \
  -d "salutation=Mr." \
  -d "street=123 Main St" \
  -d "zip=12345" \
  -d "city=Anytown" \
  -d "country=USA" \
  -d "phone=555-123-4567"
```
```javascript
const url = 'https://api.suitcase.legal/v2/user';
const payload = new URLSearchParams({
  'user_type': 'private',
  'email': 'test@example.com',
  'password': 'a_strong_password',
  'firstname': 'John',
  'lastname': 'Doe',
  'salutation': 'Mr.',
  'street': '123 Main St',
  'zip': '12345',
  'city': 'Anytown',
  'country': 'USA',
  'phone': '555-123-4567'
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
### Body Parameters
Parameter | Required | Description
--------- | -------- | -----------
user_type | Yes | (String) The type of user account (e.g., "private", "company", "lawyer").
email | Yes | (String) The user's email address. Must be unique.
password | Yes | (String) The user's password.
firstname | Yes | (String) The user's first name.
lastname | Yes | (String) The user's last name.
salutation | Yes | (String) The user's salutation (e.g., "Mr.", "Ms.").
street | Yes | (String) The user's street and house number.
zip | Yes | (String) The user's postal code.
city | Yes | (String) The user's city.
country | Yes | (String) The user's country.
phone | Yes | (String) The user's phone number.
title | No | (String) The user's academic or professional title.
company | No | (String) The user's company name.
commercial_register | No | (String) The commercial register where the company is listed.
commercial_register_number | No | (String) The company's commercial register number.
legal_form | No | (String) The legal form of the company.
care_of | No | (String) "Care of" address line.
account_association | No | (String) Any associated account identifier.

### Response Status 
Code        | Description
----------- | -----------
201 Created | The user was successfully created. The response body is empty.
400 Bad Request | The request was malformed (e.g., missing required fields).
500 Internal Server Error | An unexpected error occurred on the server.The above command returns an empty response with a 201 Created status code on success.

## Password reset request
This endpoint initializes a password reset process by sending the user an email including a short lived token.
The token can be used to set a new password for the user.
### Https Request
`POST https://api.suitcase.legal/v2/password/reset`

```shell
curl -X POST "https://api.suitcase.legal/v2/password/reset" \
  -d "test@example.com" \
```
```javascript
await fetch('https://api.suitcase.legal/v2/password/reset', {
	method: 'POST',
	headers: { 'Content-Type': 'text/plain' },
	body: test@example.com,
});
```
### Body Parameters
Parameter | Required | Description
--------- | -------- | -----------
email | Yes | (String) Note that this value is required as plaintext.

### Response Status 
Code        | Description
----------- | -----------
200 Success | The password request was successfully initialized. The response body is empty.
400 Bad Request | The request was malformed (e.g., missing required fields).
500 Internal Server Error | An unexpected error occurred on the server.The above command returns an empty response with a 201 Created status code on success.

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
Code        | Description
----------- | -----------
204 NoContent | The password request was successfully finished. No new content is coming from the server.
400 Bad Request | The request was malformed (e.g., missing required fields).
500 Internal Server Error | An unexpected error occurred on the server.The above command returns an empty response with a 201 Created status code on success.

## Contract preview
This endpoint returns a preview of the contracts for a given use case
### Https Request
`GET https://api.suitcase.legal/v2/contract/preview`

```shell
curl -X GET "https://api.suitcase.legal/v2/contract/preview?usecase=DepositCase&factors=0"
```
```javascript
await fetch('https://api.suitcase.legal/v2/contract/preview?usecase=DepositCase&factors=0', {
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
Code        | Description
----------- | -----------
200 Success | The response is a json array containing the clauses of the contract.
400 Bad Request | The request was malformed (e.g., missing required fields).
500 Internal Server Error | An unexpected error occurred on the server.The above command returns an empty response with a 201 Created status code on success.

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
Code        | Description
----------- | -----------
200 Success | The response has no body if the case is ongoing or finished. If the case status is 'started' the response has a json body containing relevant case information.
400 Bad Request | The request was malformed (e.g., missing required fields).
500 Internal Server Error | An unexpected error occurred on the server.The above command returns an empty response with a 201 Created status code on success.

## Fetch case file via casehub
This endpoint fetches a case file via a token to be visible for users that don't have a user account.
### Https Request
`GET https://api.suitcase.legal/v2/casehub/file`

```shell
curl -X GET "https://api.suitcase.legal/v2/casehub/file?uuid=ABCDE12345&email=testmail@suitcase.legal&name=Testfile.pdf"
```
```javascript
await fetch('https://api.suitcase.legal/v2/casehub/file?uuid=ABCDE12345&email=testmail@suitcase.legal&name=Testfile.pdf', {
	method: 'GET',
});
```

### Query Parameters
Parameter | Required | Description
--------- | -------- | -----------
uuid | Yes | (String) uuid that is tied to a case.
email | Yes | (String) email that is tied to a case.
name | Yes | (String) name of the file.

### Response Status 
Code        | Description
----------- | -----------
200 Success | The response is a stream with the contents of the file.
400 Bad Request | The request was malformed (e.g., missing required fields).
500 Internal Server Error | An unexpected error occurred on the server.The above command returns an empty response with a 201 Created status code on success.

## Bid on case via casehub
This endpoint places a bid for an open case and registers a user account for the party that made the bid (registration is legally required at this point).
### Https Request
`PATCH https://api.suitcase.legal/v2/casehub/bid`

```shell
curl -X PATCH "https://api.suitcase.legal/v2/casehub/bid" \
  -d "uuid=ABC1234" \
  -d "email=bidder@example.com" \
  -d "value=100000" \
  -d "value_2=50000" \
  -d "value_3=20000" \
  -d "user_type=private" \
  -d "new_email=newuser@example.com" \
  -d "password=new_strong_password" \
  -d "firstname=Jane" \
  -d "lastname=Doe" \
  -d "salutation=Ms." \
  -d "street=456 Oak Ave" \
  -d "zip=98765" \
  -d "city=Othertown" \
  -d "country=Canada" \
  -d "phone=555-987-6543" \
  -d "title=Dr." \
  -d "company=Example Corp" \
  -d "commercial_register=SomeReg" \
  -d "commercial_register_number=12345" \
  -d "legal_form=LLC" \
  -d "care_of=c/o John Smith" \
  -d "account_association=ACCT123"

```
```javascript
const url = 'https://api.suitcase.legal/v2/casehub/bid';
const payload = new URLSearchParams({
  'uuid': 'ABC1234',
  'email': 'bidder@example.com',
  'value': '100000',
  'value_2': '50000',
  'value_3': '20000',
  'user_type': 'private',
  'new_email': 'newuser@example.com',
  'password': 'new_strong_password',
  'firstname': 'Jane',
  'lastname': 'Doe',
  'salutation': 'Ms.',
  'street': '456 Oak Ave',
  'zip': '98765',
  'city': 'Othertown',
  'country': 'Canada',
  'phone': '555-987-6543',
  'title': 'Dr.',
  'company': 'Example Corp',
  'commercial_register': 'SomeReg',
  'commercial_register_number': '12345',
  'legal_form': 'LLC',
  'care_of': 'c/o John Smith',
  'account_association': 'ACCT123'
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
uuid | Yes | (String) The UUID of the case to bid on.
email | Yes | (String) The email address associated with the existing case.
value | Yes | (Integer) The primary bid value.
value_2 | No | (Integer) An optional secondary bid value.
value_3 | No | (Integer) An optional tertiary bid value.
user_type | Yes | (String) The type of user account for the new user (e.g., "private", "company", "lawyer").
new_email | Yes | (String) The email address for the newly registered user in case the email address in the database is incorrect.
password | Yes | (String) The password for the newly registered user.
firstname | Yes | (String) The first name of the newly registered user.
lastname | Yes | (String) The last name of the newly registered user.
salutation | Yes | (String) The salutation for the newly registered user (e.g., "Mr.", "Ms.").
street | Yes | (String) The street and house number for the newly registered user.
zip | Yes | (String) The postal code for the newly registered user.
city | Yes | (String) The city for the newly registered user.
country | Yes | (String) The country for the newly registered user.
phone | Yes | (String) The phone number for the newly registered user.
title | No | (String) The academic or professional title for the newly registered user.
company | No | (String) The company name for the newly registered user.
commercial_register | No | (String) The commercial register where the company is listed for the new user.
commercial_register_number | No | (String) The company's commercial register number for the new user.
legal_form | No | (String) The legal form of the company for the new user.
care_of | No | (String) "Care of" address line for the new user.
account_association | No | (String) Any associated account identifier for the new user.

### Response Status 
Code        | Description
----------- | -----------
200 Success | The bid was successfully placed.
400 Bad Request | The request was malformed (e.g., missing required fields).
500 Internal Server Error | An unexpected error occurred on the server.The above command returns an empty response with a 201 Created status code on success.
