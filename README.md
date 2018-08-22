Welcome to your guide to integrating your apps with the nudge library! This page will serve as your high-level, start-to-finish overview of the integration process. You'll be linked to several other in-depth guides for specific steps, but this page will steer you through the big pieces. Let's get started!

### 1. Obtain your api key

If you don't already have one, you'll need to get an api key from the nudge team. This key will allow your nudge-connected apps to talk to our servers and let them know how to handle your data. 

### 2. Integrate the nudge library into your Android app

Once you have an api key, use it to [add the nudge library](https://github.com/getlarky/nudge/wiki/nudge-Android-integration-instructions) to your app. This step should be done by someone who has access to your **Java** codebase and can build and distribute your app. Once they've completed this step, you should be able to see a token with `"platform": "android"` [here](http://nudge-push-1928712587.us-east-2.elb.amazonaws.com/v1/tokens).

### 3. Create an APNs auth key

Apple requires you to create an authentication key to send push notifications to iOS devices. If you already have one for your team, you can just send it to the nudge team. If you need to generate one, follow the guide [here](https://github.com/getlarky/nudge/wiki/nudge-APNs-authentication-key-instructions)

### 4. Integrate the nudge library into your iOS app

Similar to step 2, use your api key and auth key to [add the nudge library](https://github.com/getlarky/nudge/wiki/nudge-iOS-integration-instructions) to your app. This step should be done by someone who has access to your **Swift** codebase and can build and distribute your app*. Once they've completed this step, you should be able to see a token with `"platform": "ios"` [here](http://nudge-push-1928712587.us-east-2.elb.amazonaws.com/v1/tokens).

*Note that nudge requires your app to be distributed via adhoc, enterprise, or distribution channels. Please contact us if you're interested in support for sandbox or development apps.

### 5. Distribute your apps to any test users you'd like

If you'd like a wider audience of test users, you can distribute your nudge-powered apps to them by simply distributing your apps like normal.

### 6. Access the dashboard and send your first nudge!

Now that your apps are integrated and distributed, follow [this guide](https://github.com/getlarky/nudge/wiki/nudge-Dashboard-instructions) to get started sending nudges! All notifications sent will go to any users that have downloaded your nudge-powered apps!

### Questions?

We're always here to help! Please feel free to contact us at for any support you need along the way. Happy nudging!

* Gregg Hammerman, CEO: [gregg@larky.com](mailto:gregg@larky.com)
* David Groom, CTO: [david@larky.com](mailto:david@larky.com)
* Evan Snyder, Product Manger and Lead Developer: [evan@larky.com](mailto:evan@larky.com)