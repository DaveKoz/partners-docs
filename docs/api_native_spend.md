---
title: Native Spend
hide_title: true
---

## Creating a Custom Spend Offer

A custom Spend offer allows your users to unlock unique spend opportunities that you define within your app. (Custom offers are created by your app, as opposed to [built-in offers displayed in the Kin Marketplace offer wall](#adding-a-custom-spend-offer-to-the-kin-marketplace-offer-wall).) Your app displays the offer, request user approval, and then [requests payment using the Kin purchase API](#requesting-purchase-payment-for-a-custom-spend-offer).

*To create a custom Spend offer:*

### Requesting purchase Payment for a Custom Spend Offer

*To request payment for a custom Spend offer:*

1. Create a JWT that represents a Spend offer signed by you, using the header and payload templates below. (See [Generating the JWT Token](api_readme.md#generating-the-jwt-token) for more details about JWT structure).

**JWT header:**
```javascript
{
    "alg": "ES256",    // Signature function
    "typ": "JWT",
    "kid": "my key id" // identifier of the keypair that was used to sign the JWT. identifiers and public keys will be provided by signer authority. This enables using multiple private/public key pairs (a list of public keys and their ids need to be provided by signer authority to verifier in advanced)
}
```

**JWT payload:**
```javascript
{
    // common/ standard fields
    "iat": 1549814768,  // issued at - seconds from Epoch
    "iss": "my_app_id", // issuer
    "exp": 1549834768,  // expiration - seconds from Epoch
    "sub": "spend",

   // application fields
    "offer": {
		"id": "my_offer_id", // offer id is decided by you (internal)
		"amount": 12         // amount of kin for this offer - price
    },
    "sender": {
		"user_id": "user id",       // user_id who will perform the order
		"device_id": "user device", // A unique ID of the recipient user device
		"title": "order title",     // order title - appears in order history
		"description": "order history description" // order desc. (in order history)
    }
}
```

2. Call `purchase(…)`, while passing the JWT you built and a callback function that will receive purchase confirmation.

<!--DOCUSAURUS_CODE_TABS-->
<!--Android-->

```java
try {
    Kin.purchase(offerJwt, new KinCallback<OrderConfirmation>() {
        @Override
        public void onResponse(OrderConfirmation orderConfirmation) {
            // OrderConfirmation will be called once Ecosystem received the payment transaction from user.
            // OrderConfirmation can be kept on digital service side as a receipt proving user received his Kin.

            // Send confirmation JWT back to the server in order prove that the user
            // completed the blockchain transaction and purchase can be unlocked for this user.
            System.out.println("Succeed to create native spend.\n jwtConfirmation: " + orderConfirmation.getJwtConfirmation());
        }

        @Override
        public void onFailure(KinEcosystemException exception) {
            System.out.println("Failed - " + error.getMessage());
        }
    });
}
catch (ClientException e) {
    e.printStackTrace();
}
```

<!--iOS-->
```swift
Kin.shared.purchase(offerJWT: encodedNativeOffer) { jwtConfirmation, error in
	if let confirm = jwtConfirmation {
		// success
	} else if let e = error {
		// error
	}
}
```
<!--END_DOCUSAURUS_CODE_TABS-->

> **NOTES:**  
> * The above snippet is taken from the SDK Sample App, in which the JWT is created and signed by the client side for presentation purposes only. Do not use this method in production! In production, the JWT must be signed by the server, with a secure private key.
> * See [BlockchainException](api_common_errors.md#blockchainException--Represents-an-error-originated-with-kin-blockchain-error-code-might-be) and [ServiceException](api_common_errors.md#serviceexception---represents-an-error-communicating-with-kin-server-error-code-might-be) for possible errors.  


3. Complete the purchase after you receive confirmation from the Kin Server that the funds were transferred successfully.
