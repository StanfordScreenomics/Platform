## 3.05. Settings Manager

The event data collection process is dynamically controlled through the Firestore console, allowing researchers or administrators to adjust data collection settings without hardcoding or requiring an app update [[See Settings Profiles](../01_Firebase/05_Settings.md)]. This section covers how new dynamic parameters can be added to a new module.


**Note.** `ModuleController` and `SettingsManager` both control event collection, but **they serve different purposes**.

| Feature | `ModuleController` | `SettingsManager` |
|---|---|---|
| **What It Controls** | Enables/disables entire data collection modules (e.g., interaction tracking, network monitoring). | Dynamically adjusts how an event feature behaves (e.g., how often data is collected, whether specific events are logged). |
| **Scope of Control** | Affects entire modules (e.g., turning interaction tracking ON/OFF). | Affects individual features within a module (e.g., enabling/disabling specific interactions). |
| **When It Works** | Must be set before compiling the app. | Can be changed anytime, even after the app is installed, by updating parameter values in Firestore. | 
| **Example Setting** | `ModuleController.ENABLE_INTERACTION = true;` (Enables/disables the interaction module completely). | `"scroll-up-enabled": "1"` (Controls whether only scroll-up events are logged while keeping interaction tracking active). |
| **How Changes Are Applied** | Requires a new app version to change module activation settings. | Settings can be modified remotely via Firestore without an app update. |
| **Where the Settings Are Stored** | Inside the app's source code (ModuleController.java). | In Firestore, accessible and updatable anytime. |
| **Use Case Example** | A study has **two groups**, and one group should NOT track interaction events at all. | A study requires adjusting the step count collection interval or disabling scroll-up tracking mid-study without an app update. |

* Use `ModuleController` for broad ON/OFF control on a module level and `SettingsManager` for fine-tuning event behavior.

---

### 3.05.1. Mechanism

The app controls certain data collection features dynamically by fetching settings from **Firestore documents in `settings_profiles` collection**, instead of hardcoding them. This allows researchers or administrators to remotely enable/disable features and adjust data collection settings during the study period without requiring an app update.

The mechanism works in **four key steps**:

a. **Firestore Stores the Dynamic Parameters**
* Each settings profiles document contains **feature(feature/parameter)-value pairs**, where each pair defines how a specific feature should behave.
  * Example: A parameter like `"forced-image-quality": "100"` ensures the screenshot images are collected at 100% quality.

b. **The Screenomics App Fetches Settings from Firestore via `SettingsManager` (from `databaseManager` Module)**
* At startup, the app retrieves the latest settings from Firestore using:
```java
`SettingsManager.fetchSettingsFromFirestore()`.
```
* The fetched values are stored in `SettingsManager`, making them accessible to different modules.
* If Firestore settings are updated remotely, the app applies the new values based on the `settings-refresh-interval` parameter.

c. **Modules Check Settings Before Processing Events**
* Each data collection module checks its relevant parameter values before collecting or logging data.
  * For example, before recording step counts, the `locationModule` verifies whether `"gps-enabled"` is set to `"1"`.
  * If the setting is `"0"`, the module exits early, preventing unnecessary event collection.

d. **Researchers Can Update Firestore Settings Anytime**
* If researchers decide to disable location tracking on a specific day, they simply update Firestore by `setting "gps-enabled": "0"`.
* The next time the app refreshes settings, it detects the update and automatically stops location tracking, without requiring an app update.

---

### 3.05.2. Add Dynamic Parameters to `Settings_Profiles`

You have already developed a data collection module that captures scroll-up events and sends them to Firestore for storage. Now, imagine you have expanded this module to also capture scroll-down events and now want to allow researchers to dynamically enable or disable **scroll-up** and **scroll-down** logging using Firestore `settings_profiles`.

**Example**. Updated code to handle both scroll-up and scroll-down events.
```java
public class ModuleController {
    // Step 1: Add a boolean flag for the new module
    public static boolean ENABLE_INTERACTIONS = true;
}

public class UserInteractionCapture extends AccessibilityService {
    public void onAccessibilityEvent(AccessibilityEvent event) {
        // Step 2: Check if the interaction module is enabled
        if (!ModuleController.ENABLE_INTERACTION) {
            return; // Exit early if interaction tracking is disabled
        }

        // Step 3: Detect scroll events
        if (event.getEventType() == AccessibilityEvent.TYPE_VIEW_SCROLLED) {
            int scrollDelta = event.getScrollY();

            // Step 4: Determine if it's a scroll-up or scroll-down event
            String actionType = (scrollDelta > 0) ? "scroll-up" : "scroll-down";

            // Step 5: Retrieve a HashMap from the pool
            HashMap<String, String> interactionData = HashMapPool.getMap();

            // Step 6: Extract relevant details
            interactionData.put("activity", actionType);
            interactionData.put("time", new EventTimestamp().getTimestring());
            interactionData.put("time-local", new EventTimestamp().getSystemClockTimestring());

            // Step 7: Pass event data to EventOperationManager for processing, storage, and upload
            EventOperationManager.getInstance().addEvent(
                ModuleCharacteristics.getInstance().getInteractionEventCharacteristics(),
                interactionData
            );

            // Step 8: Release the HashMap back to the pool
            HashMapPool.releaseMap(interactionData);
        }
    }
}
```

To allow remote control over scroll event tracking, **add two new dynamic parameters with their default values** in Firestore - `settings_profiles` > `\_default\_` > `Add Field` [[See Firebase - Settings](../01_Firebase/05_Settings)].

---

### 3.05.3. Fetch Parameters in `databaseManager` Module - `SettingsManager` Class

a. Modify `resetToLocalDefaults()` in `SettingsManager.java` by adding the new dynamic parameters:
```java
public void resetToLocalDefaults() {
    settings.clear();
    settings.put("settings-group-override", 1);
    settings.put("settings-refresh-interval", 250000);
    settings.put("data-nontext-upload-wifi-only", 1);
    settings.put("screenshot-interval", 5000);
    settings.put("screenshot-absolute-timing", 1);
    settings.put("screenshot-check-interval", 70000);
    settings.put("data-nontext-upload-interval", 5 * 60 * 1000);
    settings.put("foreground-app-check-interval", 1000);
    settings.put("gps-location-interval", 60 * 1000);
    settings.put("gps-enabled", 1);
    settings.put("force-image-quality", 10);
    settings.put("kill-switch", 0);
    settings.put("pa-stepcounts-interval", 60 * 1000);
    settings.put("pa-enabled", 1);
    settings.put("data-text-upload-interval", 5 * 60 * 1000);
    settings.put("data-text-upload-wifi-only", 1);
    
    // Add new dynamic parameters for scroll event tracking
    settings.put("scroll-up-enabled", 1);   // 1 = Enabled, 0 = Disabled
    settings.put("scroll-down-enabled", 1); // 1 = Enabled, 0 = Disabled
}

```

b. **Now you can globally retrieve these parameter values in any data collection modules using**: 
```java
SettingsManager.val("scroll-up-enabled");
SettingsManager.val("scroll-down-enabled");
```

---

### 3.05.4. How `SettingsManager.val("parameter-name")` Provides Global Access to Dynamic Parameters

The method `SettingsManager.val("parameter-name")` allows any part of the app to globally access Firestore-controlled settings without needing additional helper functions. This works because:

a. **`SettingsManager` Stores All Settings in a Global Map (`settings`)**

* When the app starts, SettingsManager.loadFromDatabase() retrieves settings from Firestore.
* The fetched settings are stored in an in-memory HashMap<String, Integer> called settings.

```java
public final Map<String, Integer> settings;
```
This settings map acts as a globally accessible storage for all Firestore parameters.

**Note.** All Firestore-controlled parameters are **stored in RAM (random-access memory)** until the next time the app refreshes settings, instead of being read from Firestore every time (which would be slow and inefficient).

b. **`SettingsManager.val("parameter-name")` Reads from the Global settings Map**

* When a module calls `SettingsManager.val("scroll-up-enabled")`, it:
  * Checks if `settings` contains the requested parameter name (`scroll-up-enabled`).
  * If found, returns the stored integer value (`1` for enabled, `0` for disabled).
  * If not found, it returns a default value (`-1`).

**Code**. How `SettingsManager.val()` Works
```java
public static int val(parameter-name) {
    if (mInstance == null) return 0; // If SettingsManager isn’t initialized, return default
    return mInstance.getVal(name);   // Retrieve the value from settings map
}
```
This internally calls:

```java
public int getVal(String name, int defaultValue, boolean errorIfDefault) {
    Integer result = settings.get(name); // Fetch value from global settings map
    if (result != null) return result;
    return -1; // Default if not found
}
```
Since **`settings` is a globally accessible map**, any part of the app can call `SettingsManager.val()` to retrieve Firestore dynamic parameter settings dynamically.

c. **Firestore Updates Are Automatically Applied to settings**

The `SettingsManager.loadFromDataSnapshot()` continuously updates the global `settings` map:

```java
private void loadFromDataSnapshot(DocumentSnapshot dataSnapshot, boolean clear) {
    if (clear) settings.clear(); // Reset settings if needed

    Map<String, Object> data = dataSnapshot.getData();
    if (data != null) {
        for (Map.Entry<String, Object> entry : data.entrySet()) {
            String key = entry.getKey();
            String value = entry.getValue().toString();
            settings.put(key, Integer.parseInt(value)); // Update settings map
        }
    }
}
```

---

### 3.05.5. Add `SettingsManager.val()` for Scroll-Up and Scroll-Down Control 

This updated version of `UserInteractionCapture` dynamically checks values of Firestore parameters `"scroll-up-enabled"` and `"scroll-down-enabled"` before logging scroll events. It ensures:
* Scroll-up and scroll-down event logging can be enabled/disabled remotely.
* No need for additional helper functions—just use SettingsManager.val().
* Automatic real-time updates when Firestore settings change.

**Code**. Interaction Module Updated with Dynamic Parameters 
```java
public class ModuleController {
    // Step 1: Add a boolean flag for the new module
    public static boolean ENABLE_INTERACTIONS = true;
}

public class UserInteractionCapture extends AccessibilityService {
    @Override
    public void onAccessibilityEvent(AccessibilityEvent event) {
        // Step 2: Check if the interaction module is enabled
        if (!ModuleController.ENABLE_INTERACTIONS) {
            return; // Exit early if interaction tracking is disabled
        }

        // Step 3: Detect scroll events
        if (event.getEventType() == AccessibilityEvent.TYPE_VIEW_SCROLLED) {
            int scrollDelta = event.getScrollY();

            // Step 4: Determine if it's a scroll-up or scroll-down event
            String actionType = (scrollDelta > 0) ? "scroll-up" : "scroll-down";

            // Step 5: Check Firestore settings before logging the event
            if (actionType.equals("scroll-up") && SettingsManager.val("scroll-up-enabled") == 0) {
                return; // Skip logging if scroll-up tracking is disabled
            }
            if (actionType.equals("scroll-down") && SettingsManager.val("scroll-down-enabled") == 0) {
                return; // Skip logging if scroll-down tracking is disabled
            }

            // Step 6: Retrieve a HashMap from the pool
            HashMap<String, String> interactionData = HashMapPool.getMap();

            // Step 7: Extract relevant details
            interactionData.put("activity", actionType);
            interactionData.put("time", new EventTimestamp().getTimestring());
            interactionData.put("time-local", new EventTimestamp().getSystemClockTimestring());

            // Step 8: Pass event data to EventOperationManager for processing, storage, and upload
            EventOperationManager.getInstance().addEvent(
                ModuleCharacteristics.getInstance().getInteractionEventCharacteristics(),
                interactionData
            );

            // Step 9: Release the HashMap back to the pool
            HashMapPool.releaseMap(interactionData);
        }
    }

    @Override
    public void onInterrupt() {
        // Required but not used
    }
}
```

Now, researchers can turn scroll-up and scroll-down event tracking ON/OFF anytime.
* `SettingsManager.val("scroll-up-enabled")` and `SettingsManager.val("scroll-down-enabled")` retrieve the Firestore parameter values.
* If Firestore settings change, `SettingsManager.loadFromDatabase()` automatically updates the in-memory settings map (`settings`).
* No need for hardcoded logic—event logging dynamically adjusts based on remote Firestore settings.





[Back to Top](#top)
