This page documents the process for including the nudge library in your Android application. For iOS instructions, please see [here](https://github.com/getlarky/nudge-documentation/wiki/nudge-iOS-integration-instructions)! This guide assumes that you're targeting at least Android API 21 and that you have a valid nudge API key. If you don't have an API key, please contact someone on the nudge team.

## Requirements

Before getting started, please make sure you are using the following:

* Gradle: version 4+
* Android Studio: version 3.0+
* [Android plugin for Gradle](https://developer.android.com/studio/releases/gradle-plugin): version 3.0+
* Android [`minSdkVersion`](https://developer.android.com/guide/topics/manifest/uses-sdk-element#min): 21+

## Importing the library

nudge is distributed using [jitpack](https://jitpack.io). You'll need to import jitpack into your repositories which will, in turn, allow you to import the nudge library from jitpack.

1. Add jitpack to your *project-wide* `build.gradle` file. This should be near imports like `jcenter` and `google`. It is recommended to re-sync your project after making `build.gradle` changes to ensure dependencies are working and up to date 👍 

```Jav
allprojects {
    repositories {
        google()
        jcenter()

        // Add the code starting here
        maven {url 'https://jitpack.io'} 
        // And ending here!

    }
}
```

2. Add the nudge library as a dependency to your *app-specific* `build.gradle` file. 

```Java
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:26.1.0'
    testImplementation 'junit:junit:4.12'

     // Add this line
    implementation 'com.github.getlarky:nudge-android:1.5.0'
}
```

## Adding the library to your project

Now that you've got the nudge library imported, we just need to add it into your app's functionality!

1. Find the file you want to add nudge to; we recommend doing this wherever you authenticate your users. This is often your `MainActivity.java` or `MainActivity.kt` page.

2. Import the package alongside your other imports:

```Java
// Java
import com.larky.nudge.Nudge;
```
```Kotlin
// Kotlin
import com.larky.nudge.Nudge
```

3. You can initialize nudge with *one line* of code by using `NudgeBuilder`. You will need to provide your application's context, your api key, and a reference to the current activity . You can also customize the persistent notification text and title -- this is the text that is displayed in a persistent notification to let the user know your app is running. If you initialize in your MainActivity.java file, it should look like this:

```Java
//Java example usage of Nudge.NudgeBuilder()
private Nudge myNudge = new Nudge.NudgeBuilder()
                .setActivity(this)
                .setApiKey("<your nudge api key goes here>")
                .setApplicationContext(getApplicationContext())
                .setPersistentNotificationText("Notification Text")
                .setPersistentNotificationTitle("Notification Title")
                .build();
``` 
```Kotlin
//Kotlin example usage of Nudge.NudgeBuilder()
myNudge = Nudge.NudgeBuilder()
            .setActivity(this)
            .setApiKey("<your nudge api key goes here>")
            .setApplicationContext(applicationContext)
            .setPersistentNotificationText("Persistent Notification Text")
            .setPersistentNotificationTitle("Notification Title")
            .build()
```

4. Since nudge needs the user's permission to use location services, the library will prompt the user upon initialization. **If your app does not already have the `ACCESS_FINE_LOCATION` permission** by the time you initialize the nudge library, you'll need to make sure to pass along the user's response back to us. Make sure to do this in the **same activity** that you initialize the library. Also, make sure to import `NonNull` (if you're using Java) so that it is available:
```Java
// Java
import androidx.annotation.NonNull;
.
.
.
// MainActivity Class
@Override
   public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults)
{
       myNudge.onRequestPermissionsResult(requestCode, permissions, grantResults);
}
```
```Kotlin
// Kotlin
    override fun onRequestPermissionsResult(requestCode: Int, permissions: Array<String>, grantResults: IntArray) {
        myNudge?.onRequestPermissionsResult(requestCode, permissions, grantResults)
    }
```


5. If you want, you can register a callback to get notified whenever the status of the `Nudge` instance changes. To do so, use the `NudgeStatusNotifier` public interface:
 ```Java
// Java
    class myNudgeStatusListener implements Nudge.NudgeStatusNotifier{
        public void onNudgeStatusChange(Boolean active, String status){
            Log.i(TAG, (active ? "Active - " : "Inactive - ") + status);
        }
    }
```
```Kotlin
// Kotlin
   internal inner class myNudgeStatusListener : Nudge.NudgeStatusNotifier {
        override fun onNudgeStatusChange(active: Boolean, status: String) {
            Log.i(TAG, (if (active) "Active - " else "Inactive - ") + status)
        }
    }
```
You can then set the callback using `setStatusUpdateCallback()` in `NudgeBuilder`:
```Java
// Java
private Nudge myNudge = new Nudge.NudgeBuilder()
                .setActivity(this)
                .setApiKey("<your api key>")
                .setApplicationContext(getApplicationContext())
                .setPersistentNotificationTitle("Notification Title")
                .setPersistentNotificationText("Notification Text")
                .setStatusUpdateCallback(new myNudgeStatusListener())
                .build();
```
```Kotlin
// Kotlin
myNudge = Nudge.NudgeBuilder()
            .setActivity(this)
            .setApiKey("<you api key>")
            .setApplicationContext(applicationContext)
            .setPersistentNotificationText("Persistent Notification Text")
            .setStatusUpdateCallback(myNudgeStatusListener())
            .build()
```

6. Additionally, you can pass in the identifier for a resource you'd like to use for the small icon of your nudge notifications. To do so, call `setResourceId()`:
```Java
// Java
private Nudge myNudge = new Nudge.NudgeBuilder()
                .setActivity(this)
                .setApiKey("<your api key>")
                .setApplicationContext(getApplicationContext())
                .setPersistentNotificationTitle("Notification Title")
                .setPersistentNotificationText("Notification Text")
                .setStatusUpdateCallback(new myNudgeStatusListener())
                .setResourceId(R.drawable.my_png_icon) // <- setResourceID()
                .build();
```

7. You can also change the default behavior when a user taps the persistent notification to open the settings for the persistent notification channel. This is useful if you want to allow your users to hide the persistent notification. To do so, use `setPersistentNotificationBehavior()` and set it to `Nudge.PERSISTENT_NOTI_TAP_CHANNEL_SETTINGS`. To revert to default use `Nudge.PERSISTENT_NOTI_TAP_DEFAULT`.
```Java
// Java
private Nudge myNudge = new Nudge.NudgeBuilder()
                ...
                .setPersistentNotificationBehavior(Nudge.PERSISTENT_NOTI_TAP_CHANNEL_SETTINGS)
                ...
                .build();
```


8. If you need you can programmatically enable/disable your notifications. This is useful if you want to allow your users to turn notifications on/off. To do so, use `setEnabled(true|false)`. If you don't set this option the default value is `true`.
```Java
// Java
private Nudge myNudge = new Nudge.NudgeBuilder()
                ...
                .setEnabled(true)
                ...
                .build();
```

That's it! You can now distribute your app to your users and they'll be registered within the nudge ecosystem. You'll be able to send them notifications through your nudge dashboard! 


## *If you already have Firebase Cloud Messenger installed

If you already use FCM, there's no need to worry! You can still use nudge alongside your existing project. You just need one additional line of code in your `FirebaseMessagingService`! Make sure you `import com.larky.nudge.Nudge` at the top of the file and add the following line to your `onMessageReceived` handler:

```Java
// Java
public void onMessageReceived(RemoteMessage remoteMessage) {
  if (remoteMessage.getData() != null) {
   Map < String, String > data = remoteMessage.getData();
   String isNudge = data.get("isNudge");
    if (Boolean.parseBoolean(isNudge)) {
     Nudge.handlePush(remoteMessage);
     Log.d(TAG, "nudge notification received");
    } else {
     //Your code to handle your non-nudge data notifications
     Log.d(TAG, "Non-nudge data notification received");
    }
   }
   //Handle non-data notifications
   Log.d(TAG, "Non-data notification received");
}
```
```Kotlin
// Kotlin
override fun onMessageReceived(remoteMessage: RemoteMessage) {
        if (remoteMessage.data != null) {
            val data = remoteMessage.data
            val isNudge = data["isNudge"]
            if (java.lang.Boolean.parseBoolean(isNudge)) {
                Nudge.handlePush(remoteMessage)
                Log.d(TAG,"nudge notification received")
            }
            else {
                //Your code to handle your non-nudge data notifications
                Log.d(TAG, "Non-nudge data notification received")
            }
        }
        //Handle non-data notifications
        Log.d(TAG,"Non-data notification received")
    }
```

Also, we have found that developers **may have problems getting the correct token** if they implement the deprecated service [`FirebaseInstanceIDService`](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceIdService) or method [`getToken()`](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#getToken()). Please **only use** the [`FirebaseMessagingService`](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessagingService) method [`onNewToken()`](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessagingService.html#onNewToken(java.lang.String)) to check for new tokens. When that token comes in, we recommend using [`getToken(String senderId, String scope)`](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#getToken(java.lang.String,%20java.lang.String)) to actually retrieve your token to ensure there's no confusion.

If you're having trouble retrieving the correct token for other Firebase project(s), this is likely the source of the problem.
