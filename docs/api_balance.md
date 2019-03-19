---
title: Get Balance
hide_title: true
---
## Methods of Getting Wallet Balance
### Getting an Account’s Balance ###

A user’s balance is the number of Kin units in his or her account (can also contain a fraction). You may want to retrieve the balance in response to a user request or to check whether a user has enough funding to perform a Spend request. When you request a user’s balance, you receive a `Balance` object in response, which contains the amount as a `Decimal` on iOS or `BigDecimal` on Android.

There are 3 ways you can retrieve the user’s balance:

* Get the cached balance (the last balance that was received on the client side). The cached balance is updated upon SDK initialization and for every transaction. Usually, this will be the same balance as the one stored in the Kin blockchain. But in some situations it might not be up to date, for instance due to network connection issues.
* Get the balance from the Kin Server (the balance stored in the Kin blockchain). This is the definitive balance value. This is an asynchronous call that requires you to implement callback functions.
* Create an `Observer` object that receives notifications when the user’s balance changes.

*To get the cached balance:*
<!--DOCUSAURUS_CODE_TABS-->
<!--iOS-->
> **NOTE:**  
> This value may be nil if no known balance is present (for example, no account is associated yet)

```swift
Kin.shared.lastKnownBalance
```
<!--Android-->
> **NOTE:**  
> If no account was found for the user, you will receive a balance of 0 for that user.

```java
try {
        Balance cachedBalance = Kin.getCachedBalance();
    } catch (ClientException e) {
        e.printStackTrace();
}
```
<!--END_DOCUSAURUS_CODE_TABS-->

*To get the balance from the Kin Server (from the blockchain)*

Call get balance and implement the 2 response callback functions.
<!--DOCUSAURUS_CODE_TABS-->
<!--iOS-->
```swift
Kin.shared.balance() { balance, error in
    if let b = balance {
        print("balance is \(b.amount)")
    } else if let e = error {
        print("error getting balance: \(e.localizedDescription)")
    }
}
```
<!--Android-->
```java
Kin.getBalance(new KinCallback<Balance>() {
    @Override
    public void onResponse(Balance balance) {
        // Got the balance from the network
    }

    @Override
    public void onFailure(KinEcosystemException exception) {
        // Got an error from the blockchain network
    }
});
```
<!--END_DOCUSAURUS_CODE_TABS-->
(See [BlockchainException](api_common_errors.md#blockchainException--Represents-an-error-originated-with-kin-blockchain-error-code-might-be) and [ServiceException](api_common_errors.md#serviceexception---represents-an-error-communicating-with-kin-server-error-code-might-be) for possible errors.)

*To listen continuously for balance updates:*

<!--DOCUSAURUS_CODE_TABS-->
<!--iOS-->
```swift
var balanceObserverId: String? = nil
do {
    balanceObserverId = try Kin.shared.addBalanceObserver { balance in
        print("balance: \(balance.amount)")
    }
} catch {
    print("Error setting balance observer: \(error)")
}
```
<!--Android-->
Create an `Observer` object and implements its `onChanged()` function.
```java
// Add balance observer
balanceObserver = new Observer<Balance>() {
    @Override
    public void onChanged(Balance value) {
        showToast("Balance - " + value.getAmount().intValue());
    }
};

try {
    Kin.addBalanceObserver(balanceObserver);
}
catch (TaskFailedException e) {
    e.printStackTrace();
}
```
<!--END_DOCUSAURUS_CODE_TABS-->

> **NOTES:**  
> * The `Observer` object sends a first update with the last known balance, and then opens a connection to the blockchain network to receive subsequent live updates.
> * Make sure to add balance observer only when required (for example when app UI need to show updated balance) and remove the observer as soon as possible to avoid keeping open network connection.

When you're done listening to balance changes, remove the observer:

<!--DOCUSAURUS_CODE_TABS-->
<!--iOS-->
```swift
if let observerId = balanceObserverId {
    Kin.shared.removeBalanceObserver(observerId)
}
```
<!--Android-->
```java
// Remove the balance observer
try {
    Kin.removeBalanceObserver(balanceObserver);
} catch (TaskFailedException e) {
    e.printStackTrace();
}
```
<!--END_DOCUSAURUS_CODE_TABS-->
