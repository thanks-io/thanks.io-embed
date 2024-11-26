# thanks.io - Embed Widget

[thanks.io](https://www.thanks.io) makes it easy to send direct mail programmatically.  This embed widget makes it easy to integrate your CRM with thanks.io and give your clients that ability to send direct mail with minimal effort.  For more custom integrations please visit [https://api-docs.thanks.io/](https://api-docs.thanks.io/) for more info.

`ThanksIOClient` provides OAuth-based authentication, recipient management, and a modal interface for sending postcards.  

---

## Example

[https://dashboard.thanks.io/embed_example](https://dashboard.thanks.io/embed_example) provides an example of how to use this widget.

## API Mode

Before testing your integreation make sure you put your account into `API Mode` so no actual cards are sent.  This setting can be turned on at [https://dashboard.thanks.io/profile/billing](https://dashboard.thanks.io/profile/billing)

## Installation

Import thanksio javascript using script tag.
```html
<script src="https://cdn.thanks.io/thanks.io-embed/1.0.1/widget.js"></script>
```

To get started, initialize the `ThanksIOClient` in your JavaScript code:
```javascript
const thanksIOClient = new ThanksIOClient('{clientId}', '{clientSecret}', '{clientRedirectUrl}');
```

---

## Methods

The `ThanksIOClient` provides various methods to set options, manage recipients, handle OAuth, and initialize the modal interface for sending postcards.

### 1\. setOptions

#### Description

The `setOptions` method customizes client behavior. Currently, it supports the `logs` option, which enables or disables error logging.

#### Usage


```javascript
thanksIOClient.setOptions({
  logs: false
});
```

#### Parameters

- **options** (Object): Object containing configuration options.
  - `logs` (boolean): Enables (`true`) or disables (`false`) error tracking and logging. By default, this is set to `true` to facilitate debugging by tracking unexpected errors.

### 2\. oAuth

#### Description

The `oAuth` method redirects the user to the Thanks.io's OAuth authorization page. Upon successful authentication, Thanks.io will redirect the user back to the URL specified as `redirectUrl` during the client initialization.

#### Usage

```javascript
thanksIOClient.oAuth();
```

### 3\. verifyOAuthCode

#### Description

The `verifyOAuthCode` method is used to retrieve an access token from Thanks.io based on an authorization code. This method should be called after the redirect from the OAuth process, where the authorization code is present in the URL.

Upon successful retrieval, the access and refresh tokens are saved to local storage.

#### Usage

```javascript
const urlParams = new URLSearchParams(window.location.search);
const authorizationCode = urlParams.get('code');
const isVerified = await thanksIOClient.verifyOAuthCode(authorizationCode);
if (isVerified) {
  console.log("OAuth verification successful");
} else {
  console.error("OAuth verification failed");
}
```

#### Returns

- **Boolean**: `true` if access token retrieval was successful, `false` otherwise.

### 4\. setRecipients

#### Description

The `setRecipients` method allows you to set a list of recipients who will receive the postcards. Each recipient in the list must include specific fields: `name`, `address`, `city`, `province`, `postal_code`, and `country`.

#### Usage


```javascript
thanksIOClient.setRecipients([
  {
    name: 'John Doe',
    address: '123 Main St',
    city: 'Anytown',
    province: 'State',
    postal_code: '12345',
    country: 'USA'
  },
  // Additional recipients
]);
```

#### Parameters

* **recipients** (Array): An array of recipient objects. Each object should contain the following properties:
  *   `name` (string): Recipient's name.
  *   `address` (string): Street address of the recipient.
  *   `city` (string): City name.
  *   `province` (string): State or province.
  *   `postal_code` (string): Postal or ZIP code.
  *   `country` (string): Country name (e.g., 'USA').

#### Throws

* An error if the recipients list is empty or any required field is missing.

### 5\. initModal

#### Description

The `initModal` method initializes the modal widget to start sending postcards. Before calling this method, ensure successful OAuth authentication (via `oAuth` and `verifyOAuthCode`) and that recipients are set using `setRecipients`.

#### Usage

```javascript
thanksIOClient.initModal();
```

#### Requirements

1.  **OAuth Authentication**: Make sure to authenticate using the `oAuth` and `verifyOAuthCode` methods.
2.  **Recipient List**: Use the `setRecipients` method to specify the recipients before opening the modal.

#### Throws

*   An error if the access token is missing, asking the user to connect their Thanks.io account.
*   An error if the recipients list is empty, asking the user to set recipients using `setRecipients` method.


### 6\. onSendCallback

The `onSendCallback` method allows CRM users to receive a callback when a card is successfully sent.

### Usage

```javascript
thanksIOClient.onSendCallback((response) => {
  console.log('Card sent successfully:', response);
});
```

### 7\. isValidToken

The `isValidToken` method allows CRM users to validate the access token. This method will return Promise with a boolean value.

### Usage

```javascript
thanksIOClient.isValidToken().then((response) => {
  if (response) {
    console.log('Token is valid');
  } else {
    console.log('Token is invalid');
  }
});
```

---

## Example Usage

Here’s a full example showing how to initialize and use the `ThanksIOClient`:


### 1. Initiating OAuth Authentication
To start the OAuth flow, use the `oAuth` method from the `ThanksIOClient` class. This method initiates the OAuth process by redirecting the user to Thanks.io's authorization page. The example below demonstrates setting up the client and attaching the OAuth function to a button click event.

```javascript
const thanksIOClient = new ThanksIOClient('id', 'secret', 'redirectUrl');
thanksIOClient.oAuth();
```

#### Description
- **Setup:** Initialize `ThanksIOClient` with your OAuth `clientId`, `clientSecret`, and `redirectUri`.
- **Trigger OAuth:** Attach the `oAuth()` method to a button element in the CRM to start the authentication process.

This assumes that CRM developers will implement a button (e.g., an “Authorize with Thanks.io” button) that calls `thanksIOClient.oAuth()` on click to initiate the OAuth code request.

### 2. Retrieving Access Token on Redirect URL
After the OAuth authorization, Thanks.io redirects the user back to the specified `redirectUri` with an authorization code as a URL parameter. Use the `verifyOAuthCode` method to exchange this authorization code for an access token.

```javascript
const thanksIOClient = new ThanksIOClient('id', 'secret', 'redirectUrl');
const urlParams = new URLSearchParams(window.location.search);
const authorizationCode = urlParams.get('code');
if (authorizationCode) {
    thanksIOClient.verifyOAuthCode(authorizationCode);
}
```

#### Description
- **Extract Authorization Code:** Retrieve the authorization code from the URL parameters on the redirect page.
- **Get Access Token:** Use `verifyOAuthCode` with the retrieved authorization code to obtain and store the access token in local storage.

This step completes the OAuth process, storing tokens securely for authenticated requests with Thanks.io’s API.

### 3. Setting Recipients and Open the Widget Modal
Once authentication is complete, CRM users can open the Thanks.io widget modal to initiate interactions like sending postcards or gift cards. Before calling the modal, set the recipient details with `setRecipients`, then initialize the modal with `initModal`.

```javascript
const thanksIOClient = new ThanksIOClient('id', 'secret', 'redirectUrl');
thanksIOClient.setRecipients([{
    "name": "Current Resident",
    "address": "123 Main Street",
    "city": "Fake City",
    "province": "NY",
    "postal_code": "55555",
    "country": "US"
}]);
thanksIOClient.initModal();
```

#### Description
- **Set Recipients:** Use `setRecipients()` to provide recipient details, ensuring fields like name, address, city, etc., are filled.
- **Initialize Modal:** Attach `initModal()` to a button (e.g., “Open Thanks.io Modal”) that, when clicked, opens the widget modal for interactions.

With these three steps, the widget can fully integrate with the CRM to handle user authentication, recipient data, and interactive modal features.


Notes
-----

*   **Error Handling**: By default, `logs` are enabled to capture unexpected errors for easier debugging.
*   **Local Storage**: The client stores `thanksIOAccessToken` and `thanksIORefreshToken` in the local storage for managing access tokens.
