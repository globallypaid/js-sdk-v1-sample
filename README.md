# JS SDK

## Installation

> Installation

```shell
npm install @globallypaid/js-sdk --save
```

```shell
yarn install @globallypaid/js-sdk
```

```shell
<script src="https://unpkg.com/@globallypaid/js-sdk@latest"></script>
```
You can install SDK using NPM, Yarn or with a script tag.

Note that if you use pre-defined forms they are injected
as an iframe. So please be sure to check your CSP (Content Security Policy)

## Initialization

Include the Globallypaid.js script on checkout page of your site—it should preferably be loaded directly from [unpkg.com](https://unpkg.com), rather than included in a bundle or hosted yourself.

**Using Globallypaid.js as a module**

We also provide an NPM package that makes it easier to load and use Globallypaid SDK as a module.
Facade class for Globallypaid SDK is `GloballyPaidSDK(publishableKey)`

We also support the first version (GPG). The facade class is `GPGSDK(publishableKey)`.
All the facade methods are the same in both v1 and v2 facades.

> Initialize API (V2)

```javascript
import { GloballypaidGateway } from '@globallypaid/js-sdk';

const gpg = new GloballyPaidSDK('pk_live_...');
```

> Initialize API (V1)

```javascript
import { GPGSDK } from '@globallypaid/js-sdk';

const gpg = new GPGSDK('pk_live_...');
```

## Globallypaid Forms

Globallypaid Forms are customizable UI components used to collect sensitive information in your payment forms.

> Create UI form instance

**`gpg.createForm(type, options?)`**

```javascript
const cardForm = gpg.createForm('card-simple');
```

This method creates an instance of a specific Form. It takes the type of Form to create as well as an options object.

Parameter | Type | Description
--------- | ----------- | -----------
type | string | The UI form type to display
options? | object | A set of options to configure the UI Form

**Form types:**

Type | Description
--------- | -----------
card-simple | Simple card with card number, cvv, expiration date and postal code
card-extended |  Extended card with user personal and card information

> Form with options example

```javascript
const cardSimpleCustomized = gpg.createForm('card-simple', {
    style: {
        base: {
            width: '560px',
            buttonColor: 'white',
            buttonBackground: '#558b2f',
            inputColor: '#558b2f',
            color: '#558b2f'
        }
    }
});
```

**Available options:**

Option | Type | Description
--------- | ----------- | -----------
style | FrameStyle | Add custom user styling

**FrameStyle type:**

Option | Type | Description
--------- | ----------- | -----------
base | object | Base styling
invalid | object | Invalid form styling

**Base type:**

Option | Type | Example | Description
--------- | ----------- | ----------- | -----------
width | string | '500px' | The width of a form (100% by default)
height | string | '500px' | The height of a form (100% by default)
height | string | '500px' | The height of a form (100% by default)
fontFamily | string | 'Roboto, sans-serif' | Font family of a form (Roboto by default)
fontSize | string | '16px' | Font size of a form
inputColor | string | '#000' | Input element color
color | string | '#fff' | Labels color
border | string | '1px solid green' | Sets frame border
background | string | '#fafafa' | Frame background (transparent by default)
buttonBackground | string | '#fefefe' | Submit button background
buttonColor | string | '#ffffff' | Submit button text color

**Invalid type:**

Option | Type | Example | Description
--------- | ----------- | ----------- | -----------
color | string | '#fefefe' | Error messages text and input border color


> Mount form to a DOM node

**`form.mount(nodeId)`**

> page.html

```html
<div id="gpg-form-container"></div>
```
> script.js

```javascript
const cardSimple = gpg.createForm('card-simple');

cardSimple.mount('gpg-form-container');
```

Parameter | Type | Description
--------- | ----------- | -----------
nodeId | string | The id of a container where your Form will be mounted.

The `mount(nodeId)` method attaches your Form to the DOM. Method accepts the id of an element (e.g., 'gpg-form-container);

You need to create a container DOM element to mount a Form.


## Form events

The only way to communicate with your `Form` is by listening to an event. Forms might emit any of the events below. All events have a payload object that has it's own type.

**`form.on(eventType, callback, errorCallback?)`**

> Subscribe to token creation event

```javascript
cardSimple.on('TOKEN_CREATION',
    (token) => {
        cardSimple.showSuccess();
        console.log(token);
        // Do whatever you need with the token.
        // Send a request to yours backend to perform a charge request
    },
    (error) => {
        cardSimple.showFailed();
        console.log(error);
    }
);
```

Parameter | Type | Description
--------- | ----------- | -----------
eventType | string | The name of the event. In this case, 'TOKEN_CREATION'
callback | function | A callback function that you provide that will be called when the event is fired and the result is successful
errorCallback? | function | A callback function that you provide that will be called when the event error is fired.

Event | Callback payload type | Description
--------- | ----------- | -----------
TOKEN_CREATION | TokenResponse or ErrorResponse | Subscribe to a form submission and receive token response in a callback payload.

> TokenResponse example (V2)

```json
{
    "id": "tok_xLS3xebo1kCHMSn-7HO3pg",
    "type": "CreditCard",
    "brand": "Visa",
    "last_four": "1111",
    "expiration": "1234",
    "billing_contact": {
      "first_name": null,
      "last_name": null,
      "address": {
        "line_1": null,
        "line_2": null,
        "city": null,
        "state": null,
        "postal_code": "12345",
        "country": null
      },
      "phone": null,
      "email": null
    },
    "created": "2020-10-16T12:57:49.4026471Z",
    "updated": "2020-10-16T12:57:49.4026471Z"
}
```

> ErrorResponse example (V2)

```json
{
    "id": "LuXHMy6_-UuD-gZTP43PRw",
    "response_code": "400",
    "message": "InvalidRequestException: Illegal Card expiration format. Must be MMYY. MM is a month (01-12), YY is a year.",
    "approved": false,
    "completed": "2020-10-29T12:27:46.2514732Z",
    "success": false
}
```

> TokenResponse example (V1)

```json
{
    "Token": "4777770000000012",
    "EmployeeID": "123",
    "CCLastFour": "1111",
    "CCFirstFour": "4111",
    "CCBrand": "Visa",
    "ExpirationDate": "1234",
    "FirstName": "FirstName",
    "LastName": "LastName",
    "Address1": "Address1",
    "Address2": "Address2",
    "City": "City",
    "State": "ca",
    "PostalCode": "36576",
    "Country": "USA",
    "Phone": null,
    "Email": null
}
```

> ErrorResponse example (V1)

```json
{
    "Result": "ERROR",
    "ErrorCode": "400",
    "ErrorMessage": "Invalid cardholder data"
}
```

## Show success form state

**`cardSimple.showSuccess()`**

Show success state of a form.

> Success state example

```javascript
cardSimple.showSuccess();
```

## Show error form state

**`cardSimple.showError(errorMessage)`**

Show error message under the submit button.

> Failed state example

```javascript
cardSimple.showError('Transaction failed');
```

## Reset form

**`cardSimple.resetForm()`**

Reset a form to its default state.

> Reset form example

```javascript
cardSimple.resetForm();
```

## Unmount Form from the DOM

**`cardSimple.unmount()`**

Removes `Form` from the DOM if it is mounted.

> Unmount example

```javascript
cardSimple.unmount();
```

## Custom payment form

There is a possibility to tokenize card without using pre-defined Globallypaid UI forms.
SDK provides a function to tokenize input card.

> Create token explicitly (V2)

```javascript
gpg.createToken(cardNumber, cvv, expiration, zipCode).then(response => {
    console.log(response);
    // Do whatever you need with the token.
    // Send a request to yours backend to perform a charge request
}).catch(error => {
    console.log(error);
});
```

**`gpg.createToken(cardNumber, cvv, expiration, zipCode)`**

This method returns a `Promise` with the `TokenResponse`

Parameter | Type | Description
--------- | ----------- | -----------
cardNumber | string | Card number (e.g. 4111111111111111)
cvv | string | Card CVV (e.g. 123)
expiration | string | Card expiration date MMYY (e.g. 1224)
zipCode | string | Zip code (e.g. 12345)


> Create token explicitly (V1)

```javascript
const tokenRequestPayload = {
    Payload: "4111111111111111",
    Expiration: "1234",
    CVV: "1234",
    FirstName: "efsf",
    LastName: "wefwe",
    Address1: "fdsdgfg",
    Address2: "123",
    City: "fghfphlko",
    State: "ca",
    PostalCode: "36576",
    Country: "USA",
    Phone: "1234256545",
    Email: "fwefw@dasd.com"
};

gpg.createToken(tokenRequestPayload).then(response => {
    console.log(response);
    // Do whatever you need with the token.
    // Send a request to yours backend to perform a charge request
}).catch(error => {
    console.log(error);
});
```
**`gpg.createToken(tokenRequestPayload)`**

This method returns a `Promise` with the `TokenResponseV1`

Parameter | Type | Description
--------- | ----------- | -----------
tokenRequestPayload | TokenRequestV1 | See the full example in the code sample




