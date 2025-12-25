## 1.07. Ticker

The `ticker` collection allows for easy monitoring of which app users are actively using the Stanford Screenomics app and transferring data. Once the app has a significant number of users, it can be time consuming to check each user’s Cloud Storage folder or events directory, and make
sure each one shows recent activity. The `ticker` is designed to streamline this process by distilling the most recent activities of all users into one central location in Firestore. The `ticker` data is sent to the Screenomics Dashboard app for real-time tracking.

The `ticker` filters `users-events` data every 5 seconds to update a user's ticker entries. Currently, ticker entries are populated for 15 events, meaning the `ticker` can be used to monitor when the app last reported an event.  

| Event | Metadata |
|---|---|
| `GPSLocationEvent` | Most recent `timestamp` |
| `ScreenOnOffEvent` | Most recent `timestamp`; `On`/`Off` |
| `AccessibilityEvent` | Most recent `timestamp` |
| `InternetEvent` | Most recent `timestamp`; `Connected-to-WiFi`/`Disconnected-from-WiFi`/`Connected-to-DataPlan`/`Disconnected-from-DataPlan` |
| `StepCountEvent` | Most recent `timestamp` |
| `BatteryStateEvent` | Most recent `timestamp` |
| `BatteryChargingEvent` | Most recent `timestamp` |
| `ScreenshotPauseEvent` | Most recent `timestamp`; `Paused`/`Resumed` |
| `ScreenshotFailureEvent` | Most recent `timestamp` |
| `ScreenshotEvent` | Most recent `timestamp` |
| `ScreenshotUploadEvent` | Most recent `timestamp` |
| `NewForegroundAppEvent` | Most recent `timestamp` |
| `CaptureStartupEvent` | Most recent `timestamp` |
| `LowMemoryEvent` | Most recent `timestamp` |
| `SystemPowerEvent` | Most recent `timestamp` |
| `Alarm-Manager-Notification-Event` | Most recent `timestamp` |
| `LogInOutEvent` | Most recent `timestamp`; `Login`/`Logout` |

For developers, each event reported to the ticker can be toggled on or off by updating the `updateTicker` value to 0 (off) or 1 (on) in `EventUploader.java` (see the example code below). For example, if the `updateTicker` for the `GPSLocationEvent` class in `getLocationEventCharacteristics` is set to 0, the ticker will not report GPS sensor activities.

```java
public Map<String, String> getLocationEventCharacteristics() {
        return new ModuleCharacteristicsData(
                        className: "GPSLocationEvent",
                        type: "location",
                        updateTicker: "0"
                   ).toMap();
}
```

Ticker data resides in the top-level `ticker` collection in Firestore. This collection is organized as such:

```
Project
    - ticker
        * [username]
            * [event]: [timestamp], [metadata]
            * [...]
        * [...]
```

Each `username` document contains `event` collection for users reporting a specific type of event and the most recent timestamp and its associated metadata.

In the case of a `username` not listed under the `ticker` collection, this may be a cause for concern, either suggesting the user has either never fully completed the app installation, or the app has never been running on their phone for the entire duration of use. If an `event` is not listed under a `username`, that likely means either the user has never generated this type of event or the user has not granted the permission to collect the event data. 

If a user reports up-to-date timestamps for all events, that’s good, and suggests all is running well. If a user has an up-to-date `ScreenshotEvent` timestamp but an outdated
`ScreenshotUploadEvent` timestamp, then the app is capturing screenshots but the user hasn’t
uploaded screenshots recently, probably because of a lack of network connectivity (e.g., a lack of Wi-Fi). If both `ScreenshotEvent` and `ScreenshotUploadEvent` timestamps are outdated, despite the `ScreenshotPauseEvent` reporting `Resumed` and `LogInOutEvent` reporting `Login`, then it's very likely that the app had stopped running at some point.

To peruse the `ticker` more easily, it is best to click the filter icon on the left top corner of the `username` document in Firestore, so that the document is showing only that event. This will generally prompt Firestore to show all timestamps directly beside each `username`.

The `ticker` timestamps are written to Firestore in a human-readable format so that, well, you can read them, and are expressed in Pacific time, opposed to the GMT timestamps used in most of the rest of the app, so that researchers at **Stanford Human Screenome Project** (https://cyber.fsi.stanford.edu/content/cyber-project-human-screenome) can easily understand them without needing to do a timezone conversion.



[Back to Top](#top)
