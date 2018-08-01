# Africa's Talking Node.js SDK

[![NPM](https://nodei.co/npm/africastalking.png?downloads=true&downloadRank=true&stars=true)](https://www.npmjs.org/package/africastalking)

> The wrapper provides convenient access to the Africa's Talking API from applications written for Node.js.

## Documentation

Take a look at the [API docs here](http://docs.africastalking.com).


## Install

You can install the package from [npm](npmjs.com/package/africastalking) by running: 

```bash
$ npm install --save africastalking
```

## Usage

The package needs to be configured with your app username and API key, which you can get from the [dashboard](https://account/africastalking.com).

> You can use this SDK for either production or sandbox apps. For sandbox, the app username is **ALWAYS** `sandbox`

```javascript
const options = {
    apiKey: 'YOUR_API_KEY',         // use your sandbox app API key for development in the test environment
    username: 'YOUR_USERNAME',      // use 'sandbox' for development in the test environment
};
const AfricasTalking = require('africastalking')(options);

// Initialize a service e.g. SMS
sms = africastalking.SMS

// Use the service
const options = {
    to: ['+254711XXXYYY', '+254733YYYZZZ'],
    message: "I'm a lumberjack and its ok, I work all night and sleep all day"
}

// Send message and capture the response or error
sms.send(options)
    .then( response => {
        console.log(response);
    })
    .catch( error => {
        console.log(error);
    });
```

See [example](example/) for more usage examples.

## Initialization

Initialize the SDK as a requirement by doing `require('africastalking')(options)`. After initialization, you can get instances of offered services as follows:

- [Application Service](#applicationservice) : `AfricasTalking.APPLICATION`

- [Airtime Service](#airtimeservice) : `AfricasTalking.AIRTIME`

- [SMS Service](#smsservice) : `AfricasTalking.SMS`

- [Payments Service](#paymentservice) : `AfricasTalking.PAYMENTS`

- [Voice Service](#voiceservice) : `AfricasTalking.VOICE`

- [USSD Service](#ussdservice) : `AfricasTalking.USSD`

- [Token Service](#tokenservice) : `AfricasTalking.TOKEN`

## Services

All methoods are asynchronous

All phone numbers use the international format. e.g. `+234xxxxxxxx`.



### `ApplicationService`

- `fetchApplicationData()`: Get app information. e.g. balance

For more information, please read [http://docs.africastalking.com/userdata/balance](http://docs.africastalking.com/userdata/balance)



### `AirtimeService`

- `airtime.send({ recipients })`: Send airtime to a bunch of phone numbers. `recipients` is an array of objects containing the following keys:
    - `phoneNumber`: Recipient of airtime
    - `amount`: Amount sent `>= 10 && <= 10K` with currency e.g `KES 100`

For more information, please read [http://docs.africastalking.com/airtime/sending](http://docs.africastalking.com/airtime/sending)



### `SmsService`

- `send({ to, from, message, enqueue })`: Send a message

  - `to`: Recipients phone number.
  - `from`: Shortcode or alphanumeric ID that is registered with Africa's Talking account.
  - `message`: SMS content
  - `enqueue`: Set to true if you would like to deliver as many messages to the API without waiting for an acknowledgement from telcos.


- `sendPremium({ to, from, message, enqueue, keyword, linkId, retryDurationInHours })`: Send premium SMS

  - `send()` parameters plus:
  - `keyword`: You premium product keyword
  - `linkId`: We forward the `linkId` to your application when the user send a message to your service
  - `retryDurationInHours`: It specifies the number of hours your subscription message should be retried in case it's not delivered to the subscriber


- `fetchMessages({ lastReceivedId })`: Manually retrieve your messages

  - `lastReceivedId`: "This is the id of the message that you last processed". Defaults to `0`


- `fetchSubscription({ shortCode, keyword, lastReceivedId })`: Fetch your premium subscription data

  - `shortCode`: This is the premium short code mapped to your account.
  - `keyword`: A premium keyword under the above short code and mapped to your account.
  - `lastReceivedId`: "This is the id of the message that you last processed". Defaults to `0`


- `createSubscription({ shortCode, keyword, phoneNumber, checkoutToken })`: Create a premium subscription

  - `shortCode`: This is the premium short code mapped to your account.
  - `keyword`: A premium keyword under the above short code and mapped to your account.
  - `phoneNumber`:  The phone number to be subscribed
  - `checkoutToken`: This is a token used to validate the subscription request.


For more information on:
- SMS service: [http://docs.africastalking.com/sms](http://docs.africastalking.com/sms)
- How to fetch subscriptions: [http://docs.africastalking.com/subscriptions/fetchsubscriptions](http://docs.africastalking.com/subscriptions/fetchsubscriptions)
- How to listen for subscription notifications: [http://docs.africastalking.com/subscriptions/callback](http://docs.africastalking.com/subscriptions/callback)



### `PaymentService`

- `cardCheckout({ productName, paymentCard/checkoutToken, currencyCode, amount, narration, metadata })` Initiate a card checkout.

  - `productName`: Your payment product
  - `paymentCard`: Card to charge.
  - `checkoutToken`: Token from a previous successful transaction. Replaces `paymentCard`.
  - `currencyCode`: 3-digit ISO format currency code
  - `amount`: Amount to charge
  - `narration`: Checkout description
  - `metadata`: Additional info to go with the checkout


- `validateCardCheckout({ transactionId, otp })` Validate a card checkout.

  - `transactionId`: Transaction ID returned on charge request
  - `otp`: A user-provided OTP


- `bankCheckout({ productName, bankAccount, currencyCode, amount, narration, metadata })` Initiate a bank checkout.

  - `productName`: Your payment product
  - `bankAccount`: Bank account to charge.
  - `currencyCode`: 3-digit ISO format currency code
  - `amount`: Amount to charge
  - `narration`: Checkout description
  - `metadata`: Additional info to go with the checkout


- `validateBankCheckout({ transactionId, otp })` Validate a bank checkout.

  - `transactionId`: Transaction ID returned on charge request
  - `otp`: A user-provided OTP


- `bankTransfer({ productName, recipients })` Initiate a bank transfer.

  - `productName`: Your payment product
  - `recipients`: A list of banks to transfer to.


- `mobileCheckout({ productName, phoneNumber, currencyCode, amount, metadata })`: Initiate mobile checkout.

  - `productName`: Your payment product
  - `phoneNumber`: Mobile wallet to charge
  - `currencyCode`: 3-digit ISO format currency code
  - `amount`: Amount to charge
  - `metadata`: Additional info to go with the checkout


- `mobileB2C({ productName, recipients })`:  Send mobile money to consumer.

  - `productName`: Your payment product
  - `recipients`: A list of consumers that will receive the money.


- `mobileB2B({ productName, provider, transferType, currencyCode, destinationChannel, destinationAccount, amount, metadata })`:   Send mobile money to busness.

  - `productName`: Your payment product
  - `provider`: Provider used to process request. Checkout  `payments.PROVIDER.*`
  - `transferType`: Checkout  `payments.TRANSFER_TYPE.*`
  - `currencyCode`: 3-digit ISO format currency code
  - `destinationChannel`: Name or number of channel to receive payment
  - `destinationAccount`: Account name used to receive money
  - `amount`: Amount to transfer
  - `metadata`: Additional info to go with the transfer


- `walletTransfer({ productName, targetProductCode, currencyCode, amount, metadata })` Move money form one payment product to another.

  - `productName`: Your payment product
  - `targetProductCode`: ID of recipient payment product on Africa's Talking
  - `currencyCode`: 3-digit ISO format currency code
  - `amount`: Amount to transfer
  - `metadata`: Additional info to go with the transfer


- `topupStash({ productName, currencyCode, amount, metadata })` Move money from a Payment Product to an app's stash.

  - `productName`: Your payment product
  - `currencyCode`: 3-digit ISO format currency code
  - `amount`: Amount to transfer
  - `metadata`: Additional info to go with the transfer


- `fetchProductTransactions({ productName, ...filters })`: Fetch payment product transactions.

  - `productName`: Your payment product
  - `filters`: Query filters. Includes:

    - `pageNumber`: Page number to fetch results from. Starts from `1`. `REQUIRED`
    - `count`:  Number of results to fetch. `REQUIRED`
    - `startDate`: Start Date to consider when fetching.
    - `endDate`: End Date to consider when fetching.
    - `category`: Category to consider when fetching.
    - `prodiver`: Provider to consider when fetching.
    - `status`: Status to consider when fetching.
    - `source`: Source to consider when fetching.
    - `destination`: Destination to consider when fetching.
    - `providerChannel`: Provider channel to consider when fetching.


- `findTransaction({ transactionId })`: Find a particular transaction.

- `fetchWalletTransactions({ ...filters })`: Fetch wallet transactions.

  - `filters`: Query filter. Includes:
    - `pageNumber`: Page number to fetch results from. Starts from `1`. `REQUIRED`
    - `count`: Number of results to fetch. `REQUIRED`
    - `startDate`: Start Date to consider when fetching.
    - `endDate`: End Date to consider when fetching.
    - `categories`: Comma delimited list of categories to consider when fetching.


- `fetchWalletBalance()`: Fetch your wallet's balance

For more information, please read [http://docs.africastalking.com/payments](http://docs.africastalking.com/payments)


### `VoiceService`

- `voice.call({ callFrom, callTo })`: Initiate a phone call

    - `callFrom`: Your Africa's Talking issued virtual phone number.
    - `callTo`: Phone number to dial


- `voice.fetchQuedCalls({ phoneNumber })`: Get queued calls

    - `phoneNumber`: Your Africa's Talking issued virtual phone number


- `voice.uploadMediaFile({ phoneNumber, url })`: Upload voice media file

    - `phoneNumber`: Your Africa's Talking issued virtual phone number
    - `url`: URL to your media file.


> Helpers that will construct proper `xml` to send back to Africa's Taking API when it comes `POST`ing.
- `Say`, `Play`, `GetDigits`, `Dial`, `Record`, `Enqueue`, `Dequeue`, `Conference`, `Redirect`, `Reject`
> Remember to send back an HTTP 200.

For more information, please read [http://docs.africastalking.com/voice](http://docs.africastalking.com/voice) and [issue #15](https://github.com/AfricasTalkingLtd/africastalking-node.js/issues/15)



### `UssdService`

If you are using connect-like frameworks (*express*), you could use the middleware `AfricasTalking.USSD(handler)`:

`handler(params, next)`: Process USSD request and call `next()` when done.

- `params`: contains the following user data sent by Africa's Talking servers: `sessionId`, `serviceCode`, `phoneNumber` and `text`.
- `next(args)`: `args` must contain the following:
    - `response`: Text to display on user's device. `REQUIRED`
    - `endSession`: Boolean to decide whether to **END** session or to **CON**tinue it. `REQUIRED`

For more information, please read [http://docs.africastalking.com/ussd](http://docs.africastalking.com/ussd)



### `TokenService`

- `createCheckoutToken(phoneNumber)`: Create a new checkout token.

- `generateAuthToken()`: Generate an auth token to use for authentication instead of an API key.



## Development

Run all tests:

```bash
$ npm install
$ npm test
```

or on Windows...

```bash
$ npm install
$ npm run test-windows
```


## Issues

If you find a bug, please file an issue on [our issue tracker on GitHub](https://github.com/AfricasTalkingLtd/africastalking-node.js/issues).
