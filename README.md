Cordova LocalNotification-Plugin
==================================

A Local notification plugins for Cordova 3.x.x

by Sebastián Katzer ([github.com/katzer](https://github.com/katzer))

modified by Asi Farran to better support cold-start scenarios ([github.com/asiFarran](https://github.com/asiFarran)) 

## Supported Platforms
- **iOS**<br>
See [Local and Push Notification Programming Guide](http://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/WhatAreRemoteNotif.html) for detailed informations and screenshots.

- **Android** *(SDK >=11)*<br>
See [Notification Guide](http://developer.android.com/guide/topics/ui/notifiers/notifications.html) for detailed informations and screenshots.



## Dependencies
Cordova will check all dependencies and install them if they are missing.
- [org.apache.cordova.device](https://github.com/apache/cordova-plugin-device) *(since v0.6.0)*


## Adding the Plugin to your project
Through the [Command-line Interface](http://cordova.apache.org/docs/en/3.0.0/guide_cli_index.md.html#The%20Command-line%20Interface):
```bash
# from master:
cordova plugin add https://github.com/katzer/cordova-plugin-local-notifications.git

# stable version:
cordova plugin add de.appplant.cordova.plugin.local-notification
```

## Removing the Plugin from your project
Through the [Command-line Interface](http://cordova.apache.org/docs/en/3.0.0/guide_cli_index.md.html#The%20Command-line%20Interface):
```
cordova plugin rm de.appplant.cordova.plugin.local-notification
```

## PhoneGap Build
Add the following xml to your config.xml to always use the latest version of this plugin:
```
<gap:plugin name="de.appplant.cordova.plugin.local-notification" />
```
or to use this exact version:
```
<gap:plugin name="de.appplant.cordova.plugin.local-notification" version="0.7.0" />
```
More informations can be found [here](https://build.phonegap.com/plugins/413).



## Using the plugin
The plugin creates the object ```window.plugin.notification.local``` with the following methods:

### add()
The method allows to add a custom notification. It takes an hash as an argument to specify the notification's properties and returns the ID for the notification.<br>
All properties are optional. If no date object is given, the notification will pop-up immediately.

```javascript
window.plugin.notification.local.add({
    id:         String,  // A unique id of the notifiction
    date:       Date,    // This expects a date object
    message:    String,  // The message that is displayed
    title:      String,  // The title of the message
    repeat:     String,  // Has the options of 'secondly', 'minutely', 'hourly', 'daily', 'weekly', 'monthly', 'yearly'
    badge:      Number,  // Displays number badge to notification
    sound:      String,  // A sound to be played
    json:       String,  // Data to be passed through the notification
    autoCancel: Boolean, // Setting this flag and the notification is automatically canceled when the user clicks it
    ongoing:    Boolean, // Prevent clearing of notification (Android only)
});
```
**Note:** On Android the notification id needs to be a string which can be converted to a number. If the ID has an invalid format, it will be ignored, but canceling the notification will fail.

### cancel()
The method cancels a notification which was previously added. It takes the ID of the notification as an argument.
```javascript
window.plugin.notification.local.cancel(String);
```

### cancelAll()
The method cancels all notifications which were previously added by the application.
```javascript
window.plugin.notification.local.cancelAll();
```

### onadd() | ontrigger() | oncancel()
There are 3 different callback types available. For each of them one listener can be specified. The listener has to be a function and takes the following arguments:
 - event: The Name of the event
 - id: The ID of the notification
 - json:  A custom (JSON) string

```javascript
window.plugin.notification.local.on_callback_ = function (id, state, json) {};
```

### getDefaults()
Gives an overview about all available notification properties for the platform and their default values. The function returns an object.
```javascript
window.plugin.notification.local.getDefaults();
```

### setDefaults ()
Overrides the default properties. The function takes an object as argument.
```javascript
window.plugin.notification.local.setDefaults(Object);
```


## Examples
#### Will fire every week on this day, 60 seconds from now
```javascript
var now                  = new Date().getTime(),
    _60_seconds_from_now = new Date(now + 60*1000);

window.plugin.notification.local.add({
    id:      1, // is converted to a string
    title:   'Reminder',
    message: 'Dont forget to buy some flowers.',
    repeat:  'weekly',
    date:    _60_seconds_from_now
});
```

#### Pop's up immediately
```javascript
window.plugin.notification.local.add({ message: 'Great app!' });
```

#### Plays no sound if the notification pop's up
```javascript
window.plugin.notification.local.add({ sound: null });
```

#### Callback registration
```javascript
window.plugin.notification.local.onadd = function (id, state, json) {};
```

#### Pass data through the notification
```javascript
window.plugin.notification.local.add({
    id:         1,
    message:    'I love BlackBerry!',
    json:       JSON.stringify({ test: 123 })
});

window.plugin.notification.local.ontrigger = function (id, state, json) {
    console.log(id, JSON.parse(json).test);
}
```

### Change the default value for autoCancel
```javascript
window.plugin.notification.local.setDefaults({ autoCancel: true });
```


## Platform specifics
### Small and large icons on Android
By default all notifications will display the app icon. But an specific icon can be defined through the `icon` and `smallIcon` properties.
```javascript
/**
 * Displays the <package.name>.R.drawable.ic_launcher icon
 */
window.plugin.notification.local.add({ icon: 'ic_launcher' });

/**
 * Displays the android.R.drawable.ic_dialog_email icon
 */
window.plugin.notification.local.add({ smallIcon: 'ic_dialog_email' });
```

### Notification sound on Android
The sound must be a absolute or relative Uri pointing to the sound file. The default sound is `RingtoneManager.TYPE_NOTIFICATION`.
```javascript
/**
 * Plays the `beep.mp3` which has to be located in the res folder
 */
window.plugin.notification.local.add({ sound: 'android.resource://' + package_name + '/raw/beep' });

/**
 * Plays a remote sound
 */
window.plugin.notification.local.add({ sound: 'http://remotedomain/beep.mp3' });

/**
 * Plays a sound file which has to be located in the android_assets folder
 */
window.plugin.notification.local.add({ sound: '/www/audio/beep.mp3' });

/**
 * Plays the `RingtoneManager.TYPE_ALARM` sound
 */
window.plugin.notification.local.add({ sound: 'TYPE_ALARM' });
```
**Note:** Local sound files must be placed into the res-folder and not into the assets-folder.

### Notification sound on iOS
You can package the audio data in an *aiff*, *wav*, or *caf* file. Then, in Xcode, add the sound file to your project as a nonlocalized resource of the application bundle. You may use the *afconvert* tool to convert sounds.
```javascript
/**
 * Plays the `beep.mp3` which has to be located in the root folder of the project
 */
window.plugin.notification.local.add({ sound: 'beep.caf' });

/**
 * Plays the `beep.mp3` which has to be located in the www folder
 */
window.plugin.notification.local.add({ sound: 'www/sounds/beep.caf' });
```
**Note:** The right to play notification sounds in the notification center settings has to be granted.<br>
**Note:** Custom sounds must be under 30 seconds when played. If a custom sound is over that limit, the default system sound is played instead.


### Custom repeating interval on Android
To specify a custom interval, the `repeat` property can be assigned with an number in minutes.
```javascript
/**
 * Schedules the notification quarterly every 15 mins
 */
window.plugin.notification.local.add({ repeat: 15 });
```


## Quirks
### No sound is played on iOS 7
The right to play notification sounds in the notification center settings has to be granted.


### TypeError: Cannot read property 'currentVersion' of null
Along with Cordova 3.2 and Windows Phone 8 the `version.bat` script has to be renamed to `version`.

On Mac or Linux
```
mv platforms/wp8/cordova/version.bat platforms/wp8/cordova/version
```
On Windows
```
ren platforms\wp8\cordova\version.bat platforms\wp8\cordova\version
```

### Black screen (or app restarts) on Android after a notification was clicked
The launch mode for the main activity has to be set to `singleInstance`
```xml
<activity ... android:launchMode="singleInstance" ... />
```



## License

This software is released under the [Apache 2.0 License](http://opensource.org/licenses/Apache-2.0).
