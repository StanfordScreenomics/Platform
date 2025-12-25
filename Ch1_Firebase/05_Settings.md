## 1.05. Settings

The Stanford Screenomics offers researchers the flexibility to customize features and adapt to changing study needs without involving participants. Using the Firestore `settings_profiles` collection, researchers can define and update key parameters such as sampling intervals, data collection preferences, and network usage directly via the Firebase console on a per-user, per-group, or global basis. The app periodically retrieves these updates at intervals specified by the `settings-refresh-interval` setting, enabling efficient adjustments while balancing device considerations like battery life, storage, and privacy.

---

### 1.05.1. Dynamic Parameters (Configurable Settings)

Below is **a list of all dynamic parameters** (total sixteen, as of Feb 2025) that are configurable per-group or per-user through the settings framework.

| Parameter Name | Unit | Range [Suggested Minimum/Default] | Description |
|---|---|---|---|
| `settings-refresh-interval` | Millisecond (ms) | 1 - infinite [300000] | The interval at which dynamic parametert values in the `settings_profile` will be reloaded from Firestore and used by the app. If the parameter values are changed for a participant or a study group, this is the longest amount of time you can expect it to take for the new settings to take effect. |
| `settings-group-override` | True(Yes)/False(No) | 1/0 [1] | Whether or not a participant's study group profile takes priority over individual participant profile settings. **Always 1 in the _\_default_\_ profile setting**|
| `data-text-upload-wifi-only` | True(Yes)/False(No) | 1/0 [0] | Whether or not text-based data uploads should only be done over Wi-Fi. |
| `data-nontext-upload-wifi-only` | True(Yes)/False(No) | 1/0 [0] | Whether or not non-text-based data uploads should only be done over Wi-Fi. |
| `data-text-upload-interval` | Millisecond (ms) | 1 - infinite [5000] | The interval at which the app starts uploading a new batch of text-based data, assuming the previous upload round finished. |
| `data-nontext-upload-interval` | Millisecond (ms) | 1 - infinite [5000] | The interval at which the app starts uploading a new batch of non-text-based data, assuming the previous upload round finished. |

- It’s important to note that decreasing the values of `data-text-upload-interval` and `data-nontext-upload-interval` won’t really make the batch upload “faster,” because whether or not screenshots accumulate in storage is strictly dependent on connection speed. For instance, if the number of bytes/sec of screenshot images being captured is greater than the bytes/sec of the netwofk connection speed, we can expect screenshots to accumulate on the user’s phone regardless of the value of this setting.

| Parameter Name | Unit | Range [Suggested Minimum/Default] | Description |
|---|---|---|---|
| `screenshot-interval` | Millisecond (ms) | 1 - infinite [100000] | The interval at which screenshots are taken. |
| `screenshot-absolute-timing` | True(Yes)/False(No) | 1/0 [0] | Whether or not the app should use an absolute interval for the timing of screenshots. |

- If this setting is enabled, the app uses absolute timing, and screenshots will always be taken on the interval they were on when the app first started. For example, suppose screenshots are taken every 5 seconds, and when the app started screenshots were being taken at times xx:xx:04 and xx:xx:09. Then, the app will continue screenshotting at timestamps with seconds values ending in 4 and 9, throughout the lifetime of the app.
- If this setting is disabled, screen-onset relative timing is used. This means a new interval is started every time the user turns on the screen. The first screenshot in the interval will be taken at the moment of screen onset. Assuming a 5-second capture interval, the next screenshot will be 5 seconds after onset, the following one 10 seconds after onset, etc. Regardless of the value of this setting, a screenshot is always taken immediately when the screen is turned on. In the case that this setting is enabled, said screenshot will occur in addition to the regularly scheduled interval. This setting is enabled by default.
- In reality, these aren't entirely true, as there is some imprecision in Android’s timing system, but that’s the idea.

| Parameter Name | Unit | Range [Suggested Minimum/Default] | Description |
|---|---|---|---|
| `screenshot-check-interval` | Millisecond (ms) | 1 - infinite [60000] | The interval at which the app checks itself to make sure screenshotting is still occurring regularly. This should never really need to be changed. For developers, this is the interval at which `CaptureUploadService.checkScreengrabbing()` is run.
| `force-image-quality` | % | 10 to 100 [50] | The image quality level (compression efficiency) to use for all screenshots. If this is set to a value of 10-100, then all screenshots will be saved with an image quality of 10-100%, respecitvely. Higher quality levels will take more storage space, but will be better images. In essence, the image size in pixels stays the same, but the image file size changes based on the quality level. |

- It is important to note that the value (10-100) is not a direct percentage of the file size but rather a measure of compression quality. While a lower value may result in a smaller file, the reduction is not always linear or predictable because Android's compression algorithms analyze image content, and the compression efficiency depends on the image's format, color patterns and details. Generally, JPEG images and smoother visuals tend to compress better than PNG images or complex, detailed graphics.

| Parameter Name | Unit | Range [Suggested Minimum/Default] | Description |
|---|---|---|---|
| `gps-enabled` | True(Yes)/False(No) | 1/0 [1] | Whether or not GPS data is gathered at `gps-location-interval` for this participant through location module. If 0, data collection for `GPSLocationEvents` will stop at the user's next settings refresh. |
| `gps-location-interval` | Millisecond (ms) | 1 - infinite [300000] | The interval at which new GPS positions are recorded. This is the interval at which we can expect to see `GPSLocationEvents` being recorded. |

- Regardless of the value of `gps-enabled`, participants will be asked for location permissions during the initial app setup if the location module was activated during app compilation.
- During permission granting, participants can choose between: 1. Precise location (participant's exact location; ideal for studies requiring detailed positioning) or 2. Approximate location (a general, coarse location within an area, with accuracy between 1 to 3 kilometers; suitable for studies that only need a broad understanding of participant locations or have high privacy concerns).
- Regardless of the value of `gps-location-interval`, Android will skip reporting events if it determines that the participant hasn’t moved since the last event.
- A smaller value of `gps-location-interval` will provide richier (more frequent) data, but at the cost of increased battery usage.

| Parameter Name | Unit | Range [Suggested Minimum/Default] | Description |
|---|---|---|---|
| `pa-enabled` | True(Yes)/False(No) | 1/0 [1] | Whether or not Physical Activity (PA) data is gathered at `pa-stepcounts-interval` for this participant through activity module. If 0, data collection for `StepCountEvent` will stop at the user's next settings refresh. |
| `pa-stepcounts-interval` | Millisecond (ms) | 1 - infinite [300000] | The interval at which `StepCountEvent` (the total steps taken) are recorded through the activity module. |
- A smaller value of `pa-stepcounts-interval` will provide richier (more frequent) data, but at the cost of increased battery usage.

| Parameter Name | Unit | Range [Suggested Minimum/Default] | Description |
|---|---|---|---|
| `foreground-app-check-interval` | Millisecond (ms) | 1 - infinite [1000] | The interval at which Screenomics checks to see if the foreground app has changed. This effectively determines the temporal resolution of NewForegroundAppEvents. |
| `specs-check-interval` | Millisecond (ms) | 1 - infinite [86400000] | The interval at which Screenomics reports a user's device specs. This information is valuable for identifying operating system updates and new device usage. |
| `kill-switch` | True(On)/False(No) | 1/0 [0] | This value is used to immediately force close the app on participants' phones. If the kill-switch is off, the app behaves normally. If the kill-switch is turned on, the app will terminate itself as soon as it synchronizes profile settings with the database. The affected participant(s) will be unable to start up the app manually until the kill-switch is turned off again. |
- The `kill-switch` could be useful if a participant wants to stop data collection but cannot figure out how to uninstall the app, or if a malicious user is found for whatever reason.

---

### 1.05.2. Settings-Profiles

Settings_profiles enable the creation of groups of settings values to apply to large numbers of users. Settings profiles are recorded in the `settings_profiles` collection in Firestore (one of the top level collections). 

Profiles are named according to study groups (the three-letter codes that users sign up with). When a user signs up with a specific study group, the app copies all of the settings profile values from the profile with that study group name. For example, if there is a `settings_profile` named *CON* users who sign up with this code will receive the values of the settings in `/settings_profiles/CON/` in Firestore. 

The *\_default\_* settings profile contain default values for all of the settings. If a user signs up with a group code for which a settings profile does not exist, they will receive exactly the settings listed in the *\_default\_* profile. The *\_default\_* profile also means it's NOT necessary to specify values for ALL settings in named settings profiles. This is because, for a new user, settings not explicitly listed in their settings profile will be drawn from the defaults instead.

Below are some example scenario to explain what settings new users receive. Suppose the `settings_profiles` section of the database contains the following:

```
_default_
  gps-enabled = 0
  data-nontext-upload-wifi-only = 1
  // other settings excluded for simplicity

CON
  gps-enabled = 1
```

Suppose a new user signs up with the group code CON. This user will have GPS enabled, and will upload non-text-based data over Wi-Fi only. This is because they took the `gps-enabled` value from the CON settings profile, but received the default value for `data-nontext-upload-wifi-only` since this setting isn't mentioned in the CON profile. 

Alternatively, say a new user signs up with a group code *INT*. This code doesn't have an associated settings profile, so the user receives entirely the default settings. This means they will have GPS disabled, but will upload non-text-based data over Wi-Fi only. 

---

### 1.05.3. Configuring settings on a per-user basis

The settings profile system makes it easy to configure settings for large groups of users, but it's also possible to dynamically control settings for individual users. This can be done in Firestore by going to the collection `/users/[username]/settings/user-settings` and changing settings there manually.

For any changes to take effect for this user, it is also necessary to set the value of the `settings-group-override` setting to 0 for this user. If this is not done, the changes made will be overwritten the settings from the settings profile the user loads from (i.e., the settings for their study group), and nothing will change. This is explained in the next section. 

The parameter values listed in a user's own settings directory reflect exactly what settings that user is using. All settings that exist are listed here, so to check the current settings values of a specific user, you can look in their settings directory in Firestore. 

---

### 1.05.4. `settings-group-override`

The `settings-group-override` parameter, if set to 1 (enabled), will cause a user to draw their settings from their settings profile at all times. This is the default for app users. 

The utility of `settings-group-override` being **enabled** is that it allows for quick changes to the settings values for entire study groups, or for all Stanford Screenomics users. If a setting is changed for a specific study group profile, all members of that study group that have `settings-group-override` enabled will receive that change. This new value will be reflected in those users' individual settings collections [[See Section **Configuring settings on a per-user basis**](#Configuring-settings-on-a-per-user-basis)]. If a setting is changed in the *\_default\_* profile, then it will be changed for all users in the system that have `settings-group-override` enabled, provided that said settings isn't superseded by a different value in a study-group named profile. 

The use of `settings-group-override` being disabled for a specific user is that it enables manual configuration of settings for that user. For example, if there is one user who is okay with uploading non-text-based data over their data plan (e.g. 5G, LTE), you can change `data-nontext-upload-wifi-only` to 0 and `settings-group-override` to 0 in that user’s own settings collection. Changing `data-nontext-upload-wifi-only` will allow their Screenomics app to upload over data plan; changing `settings-group-override` will prevent the Wi-Fi change from being overwritten by the value in the `_default_` profile. If `settings-group-override` is re-enabled for this user later on, then all manual changes for this user will be overwritten with
the profile values (in this case, `data-nontext-upload-wifi-only` will be set back to 1).

A possible point of confusion is that `settings-group-override` itself is present both in the
*\_default\_* directory of the settings_profiles section, as well as in individual users’ settings. It is worth noting that the value in the individual users’ settings is the one that has an effect. So, if you disable `settings-group-override` for a specific user, this user will NOT be loading their settings from their study group’s profile. The value in the *\_default\_* section is simply the value being given to new users.

Funnily enough, changing `settings-group-override` to 0 in the `_default_` profile will result in this value propagating to all users! Then, all users will no longer be syncing their settings with the settings profiles. So, **never change `settings-group-override` to 0 in the *\_default\_* profile**, because then re-enabling it for all users will require manually going through every user in the database and changing the values back to 1.


[Back to Top](#top)
