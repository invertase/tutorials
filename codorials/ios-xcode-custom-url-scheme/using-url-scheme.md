# Using the URL Scheme

Now that you've created a custom `URL Scheme` lets look at how we can use the one we created previously.

> You should now be able to open links in Safari on the device to test this e.g. `myapp://anything-goes-here?someQueryString=12345`

> To save yourself typing these testing links out every time you can always enter a couple examples in the iOS notes app and click through from there.

## With Custom Code

Inbound links get sent to the `UIApplicationDelegate`.

To add a customer handler you can implement a [`handleOpenURL`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1622964-application?language=objc) handler inside your `AppDelegate.m` file:

```objectivec
- (BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url {
  // do something custom with your URL here ...
  // returning true (YES) indicates that you've handled this link
  // returning false (NO) allows other handlers to pick this link up
}
```


## With Firebase Dynamic Links

Firebase Dynamic Links are links that work on multiple platforms, and whether or not your app is already installed.
  
> See [Firebase Dynamic Links](https://firebase.google.com/docs/dynamic-links/) site for more information.

### Add the pod
    
Add the following to your `Podfile`:

```ruby
pod 'Firebase/DynamicLinks'
```

Run `pod update`.

### Configure XCode

1. In your [Firebase console](https://console.firebase.google.com/), open the Dynamic Links section.
    1. Accept the terms of service if you are prompted to do so.
    2. Take note of your project's Dynamic Links domain, which is displayed at the top of the Dynamic Links page. You need your project's Dynamic Links domain to programmatically create Dynamic Links. A Dynamic Links domain looks like app_code.app.goo.gl.
    
        ![console](https://firebase.google.com/docs/dynamic-links/images/dynamic-links-domain.png)

2. Ensure that your app's App Store ID and your Apple Developer Team ID is specified in your app's settings. To view and edit your app's settings, go to your Firebase project's Settings page and select your iOS app.
   You can confirm that your Firebase project is properly configured to use Dynamic Links in your iOS app by opening the following URL:
   
   ```text
   https://app_code.app.goo.gl/apple-app-site-association
   ```
       
     If your app is connected, the apple-app-site-association file contains a reference to your app's App Store ID and bundle ID. For example:
       
   ```javascript
   {"applinks":{"apps":[],"details":[{"appID":"1234567890.com.example.ios","paths":["/*"]}]}}
   ```
       
     If the details field is empty, double-check that you specified your Team ID.
3. In the Info tab of your app's XCode project, create a new URL type to be used for Dynamic Links. Set the `Identifier` field to a unique value and the URL scheme field to either your bundle identifier or a unique value.

4. In the Capabilities tab of your app's Xcode project, enable Associated Domains and add the following to the Associated Domains list: `applinks:app_code.app.goo.gl` where `app_code` is your dynamic links domain application code as in step *1.2* above.

5. Update your `AppDelegate.m` file:
    1. Import Firebase:
    
        ```objectivec
        @import Firebase;
        ```
        
    2. Add the following to the `didFinishLaunchingWithOptions` method before `[FIRApp Configure]`:

        ```objectivec
        [FIROptions defaultOptions].deepLinkURLScheme = @"CUSTOM_URL_SCHEME";
        // ...
        [FIRApp configure];
        ```

        ^-- where `CUSTOM_URL_SCHEME` is one of the custom URL scheme you defined earlier in your Xcode project e.g. `myapp` or `foobar`
    
    3. Setup `AppDelegate.m`'s `continueUserActivity` and `openURL` handlers as normal. See [here](https://github.com/firebase/quickstart-ios/blob/master/dynamiclinks/DynamicLinksExample/AppDelegate.m) for an example.