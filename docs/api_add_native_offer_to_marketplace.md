---
title: Add Native Offer to Wall
hide_title: true
---
## Add and Remove Native Offers from Offer Wall
### Adding a Custom Earn or Spend Offers to the Kin Marketplace Offer Wall ###

The Kin Marketplace offer wall displays built-in offers, which are served by the Kin Ecosystem Server. Their purpose is to provide users with opportunities to earn initial Kin funding, which they can later spend on spend offers provided by hosting apps.

You can also choose to display a banner for your custom offer in the Kin Marketplace offer wall. This serves as additional "real estate" in which to let the user know about custom offers within your app. When the user clicks on your custom Spend or Earn offer in the Kin Marketplace, your app will be notified, and then you can continues to manage the offer with your own loging and UX/UI flow.

> **NOTE:**  
> You will need to actively launch the Kin Marketplace offer wall so your user can see the offers you added to it. See [Displaying the Kin Marketplace Offer Wall](api_launch_experience.md) for more details.

*To add a custom Spend or Earn offer to the Kin Marketplace:*

1.	Create an object to be notified when the user clicks on your offer in the Kin Marketplace.

<!--DOCUSAURUS_CODE_TABS-->
<!--iOS-->
  ```swift
let offer = NativeOffer(id: "offer id", // OfferId must be a UUID
                        title: "offer title",
                        description: "offer description",
                        amount: 1000,
                        image: "an image URL string",
                        offerType: .spend, // or .earn
                        isModal: true)
```
> **NOTE:**  
> On iOS setting a native offer's `isModal` property to true means that when a user taps on the native offer, the marketplace will first close (dismiss) before invoking the native offer's handler, if set. The default value is false.


<!--Android-->
```java
private void addNativeOfferClickedObserver() {
    try {

        Kin.addNativeOfferClickedObserver(getNativeOfferClickedObserver());
    }
    catch (TaskFailedException e) {
        showToast("Could not add native offer callback");
    }
}

private Observer<NativeOfferClickEvent> getNativeOfferClickedObserver() {
    Observer<NativeOfferClickEvent> nativeOfferClickedObserver = new Observer<NativeOfferClickEvent>() {
        @Override
        public void onChanged(NativeOfferClickEvent nativeOfferClickEvent) {
            NativeOffer nativedOffer = nativeOfferClickEvent.getNativeOffer();
            if (nativeOfferClickEvent.isDismissOnTap()) {
                new AlertDialog.Builder(MainActivity.this).setTitle("Native Offer (" + nativeOffer.getTitle() + ")").setMessage("You tapped a native offer and the observer was notified.").show();
            }
            else {
                Intent nativeOfferIntent = NativeOfferActivity.createIntent(MainActivity.this, nativeOffer.getTitle());
                startActivity(nativeOfferIntent);
            }
        }
    };
    return nativeOfferClickedObserver;
}
```
<!--END_DOCUSAURUS_CODE_TABS-->

2. Create a Native Offer:	
<!--DOCUSAURUS_CODE_TABS-->
<!--iOS-->
  ```swift
/**
 Set the  `nativeOfferHandler` closure on Kin.shared to receive a callback when the native offer has been tapped.

 The callback is of the form `public var nativeOfferHandler: ((NativeOffer) -> ())?`
 */
// example from the sample app:
Kin.shared.nativeOfferHandler = { offer in
    DispatchQueue.main.async {
        let alert = UIAlertController(title: "Native Offer", message: "You tapped a native offer and the handler was invoked.", preferredStyle: .alert)
        alert.addAction(UIAlertAction(title: "Close", style: .cancel, handler: { [weak alert] action in
            alert?.dismiss(animated: true, completion: nil)
        }))

        let presentor = self.presentedViewController ?? self
        presentor.present(alert, animated: true, completion: nil)
    }
}
```
<!--Android-->
```java
// Spend Offer:
NativeOffer nativeOffer =
    new NativeSpendOfferBuilder("The offerID") // OfferId must be a UUID
        .title("Offer Title") // Title to display with offer
        .description("Offer Description") // Desc. to display with offer
        .amount(150) // Purchase amount in Kin
        .image("Image URL")// Image to display with offer
        .build(); 
            
            
// Earn Offer:
NativeOffer nativeOffer =
    new NativeEarnOfferBuilder("The offerID") // OfferId must be a UUID
        .title("Offer Title") // Title to display with offer
        .description("Offer Description") // Desc. to display with offer
        .amount(100) // The Kin amount the user can earn by completing the earn offer 
        .image("Image URL") // Image to display with offer
        .build(); 
```
<!--END_DOCUSAURUS_CODE_TABS-->

3.	Add the native offer you created in the following way:


> **NOTES:**  
> * Each new offer is added as the first offer in Spend/Earn Offers list the Marketplace displays.
> * On Android, Parameter `dismissOnTap` determines if the Marketplace needs to be dismissed on tap.
> * Adding the same offer twice will update the existing one.
<!--DOCUSAURUS_CODE_TABS-->
<!--iOS-->
```swift
do {
    try Kin.shared.add(nativeOffer: offer)
} catch {
    print("failed to add native offer, error: \(error)")
}
```
<!--Android-->
```java
try {
    boolean dismissOnTap = true
    if (Kin.addNativeOffer(nativeOffer, dismissOnTap)) {
        // Native offer added
    } else {
        // Could not add the offer to Kin Marketplace
    }
} catch (ClientException error) {
    ...
}
```
<!--END_DOCUSAURUS_CODE_TABS-->
### Removing a Custom Spend or Earn Offer from Kin Marketplace ###

*To remove a custom Spend or Earn offer from the Kin Marketplace:*
<!--DOCUSAURUS_CODE_TABS-->
<!--iOS-->
```swift
do {
    try Kin.shared.remove(nativeOfferId: offer.id)
} catch {
    print("Failed to remove offer, error: \(error)")
}
```
<!--Android-->

```java
try {
    if (Kin.removeNativeOffer(nativeOffer)) {
         // Native offer removed
    } else {
        // Native offer isn't in Kin Marketplace
    }
} catch (ClientException e) {
    ...
}
```
<!--END_DOCUSAURUS_CODE_TABS-->
