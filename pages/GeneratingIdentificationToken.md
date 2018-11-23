# Generating a token

If you have ***API key*** and ***API secret*** you can create an identification token.
### Sending request
Send a *HTTP Post* request to: [https://ivs.idenfy.com/api/v2/token](https://ivs.idenfy.com/api/v2/token)<br>
The request must contain JSON with optional and mandatory parameters:

|Key|Required|Explanation|Type|Constraints<img width=/>|Default value|
|-|-|-|-|-|-|
|`clientId`|Yes|A unique string identifying a client.|String|- Not null<br>- Max length 100<img width=750/>|-|
|`firstName`|No|A name(s) of a client to be identified.|String|- Min length 1<br>- Max length 100|-|
|`lastName`|No|A surname(s) of a client to be identified.|String|- Min length 1<br>- Max length 100|-|
|`successUrl`|No|An url where a client will be redirected after a successful identification.|String|- Min length 5<br>- Max length 2048|`https://`<br>`ui.idenfy.com/`<br>`result?status=success`|
|`errorUrl`|No|An url where a client will be redirected after a failed identification.|String|- Min length 5<br>- Max length 2048|`https://`<br>`ui.idenfy.com/`<br>`result?status=fail`|
|`locale`|No|A country code in alpha-2 format. Determines what default language a client will see in identification UI.|String|- Values:<br>&nbsp;&nbsp;&nbsp;&nbsp;-`lt`<br>&nbsp;&nbsp;&nbsp;&nbsp;-`en`<br>&nbsp;&nbsp;&nbsp;&nbsp;-`ru`|`en`|
|`expiryTime`|No|Length of time in seconds after which a newly generated token will become invalid.|Integer|- More than 0|`3600`|
|`sessionLength`|No|Length of time in seconds where a client is given to identify himself in indentification UI.|Integer|- More than 60<br>- Less than 3600|`600`|
|`country`|No|A default document country in alpha-2 code for a client. A client will not be able to select a different country.|String|- Any country in alpha-2 code|`null`|
### Receiving response
|Key|Explanation|Constraints|Example value|
|-|-|-|-|
|`message`|A message for a developer about the status of generated token.|- Max length 100<img width=375/>|`"Token created successfully"`
|`authToken`|A unique string for identification process (will be passed as an url parameter when redirecting a client to identification platform).|- Length equals 26|`"3FA5TFPA2ZE3LMPGGS1EGOJNJE"`
|`scanRef`|A unique string identifying a client identification in iDenfy’s side.|- Length equals 36|`"d2714c8a-ec05-11e8-834f-067891e3383a"`
|`clientId`|A unique string identifying a client in your companies side. (The same value when requesting to generate a token)|- Not null<br>- Max length 100|`"5F7E4FR14"`

### Graphical representation of token generation (UML activity)

<img src="https://raw.githubusercontent.com/idenfy/Documentation/master/resources/TokenGenerationUmlActivityDiagram.jpg" alt="Token generation UML activity diagram" width="450">

### Examples
#### Example requests

You can choose not to send any data regarding your client that needs to be identified. The only mandatory parameter is **clientId**.
```json 
{  
   "clientId":"100000"
}
```
As our only mandatory parameter is **clientId**, howerver we strongly recommend that you would append at least client’s name and surname if possible. It increases the success rate of identification verification.
```json
{  
   "clientId":"100000",
   "firstName":"John Tom",
   "lastName":"Smith "
}
```
Specify all of the parameters for full control.
```json
{  
   "clientId":"100000",
   "firstName":"John Tom",
   "lastName":"Smith ",
   "successUrl":"https://www.my-company.com/idenfy/success",
   "errorUrl":"https://www.my-company.com/idenfy/fail",
   "locale":"en",
   "expiryTime":600,
   "sessionLength":600,
   "country":"en"
}
```

#### Example response
If supplied data in JSON and ***API key*** with ***API secret*** are valid, you should receive a successful response providing you ***scanRef*** which is the main identifier of an identification in *iDefny* platform.

```json
{
  "message": "Token created successfully",
  "authToken": "3FA5TFPA2ZE3LMPGGS1EGOJNJE",
  "scanRef": "d2714c8a-ec05-11e8-834f-067891e3383a",
  "clientId": 100000
}
```

Note that in case of a malformed JSON body or API key/secret mismatch you will receive a standart *iDenfy* API error response. For more on *iDenfy* API responses visit [iDenfy error messages](https://github.com/idenfy/Documentation/blob/master/pages/StandardErrorMessages.md).

### FAQ:

#### What is the difference between expiryTime and sessionLength parameters?

**expiryTime** parameter defines how long the generated token is going to live. For example, if token was generated with expiry time of 3600 (1 hour) a client can start his identification process any time withing that 1 hour. After an hour is passed, a token will no longer be valid and and a client will see an internal “session expired” page. However **sessionLength** determines how long a client can stay in identification platform. **sessionLength** takes effect only when a client visits the identification platform. For example, if token was generated with session length of 600 (10 minutes) a client has 10 minutes to identify himself (take photos of documents and his face) in the platform. After 10 minutes a client will see an internal “session expired” page.

#### What if my client’s name/surname contains non-Latin characters?

We accept any type of characters (Latin and non-Latin).