---
title: Create Account
hide_title: true
---

## Creating a User’s Kin Account

If your app presents Kin Spend and Earn offers to your users, then each user needs a Kin wallet and account in order to take advantage of those offers.

> **NOTE:**  
> Kin Ecosystem SDK must be initialized before any interaction with the SDK, in order to do that you should call `Kin.start(…)` first.

#### Login
*To create or access a user’s Kin account:*

Call `Kin.login(…)`, passing JWT credentials and an optional `KinLoginCallback` to get a response when the user is logged in and has a wallet ready for use.</br>
If that user already has a Kin account, the function only accesses the existing account. Otherwise, the function creates a new wallet and account for the user.

**JWT mode:**

(See [Building the JWT Token](api_readme.md#generating-the-jwt-token) to learn how to build the JWT token.)

<!--DOCUSAURUS_CODE_TABS-->
<!--iOS-->
```swift
try Kin.shared.login(jwt: encodedJWT) { error in
    guard let e = error else {
        print("login success")
        return
    }
    print("login failed. Error: \(error.localizedDescription)")
}
```
> **NOTE:**  
> On iOS, You can immediately call other functions after calling login. Any login or wallet creation/retrieval operations will be performed first and your calls will queue until these are done. The 'KinLoginCallback' is an optional parameter allowing you to know when login is complete, in case you need it.

<!--Android-->
```java
try {
    Kin.login(jwt, new KinCallback<Void>() {
        @Override
        public void onResponse(Void response) {
            Log.d(TAG, "JWT login succeed");
        }

        @Override
        public void onFailure(KinEcosystemException exception) {
            Log.e(TAG, "JWT login failed: " + exception.getMessage());
        }
    });
} catch (BlockchainException e) {
    // Handle exception…
}
```
<!--END_DOCUSAURUS_CODE_TABS-->

#### Logout
*To release access from a user’s Kin account or switch account:*

<!--DOCUSAURUS_CODE_TABS-->
<!--iOS-->
```swift
Kin.shared.logout()
```
<!--Android-->
```java
try {
    Kin.logout();
    // the rest app logout logic
} catch (ClientException e) {
    // Handle exception…
}
```
<!--END_DOCUSAURUS_CODE_TABS-->
`logout()`, is a synchronous call, meaning you can call `login(…)` immediately after that (for switching between users).  After calling `logout`, you should login again in order to interact with the SDK API's, as Kin account is required.
