## 3.04. Module Controller

The `ModuleController` acts as **a central switchboard**, allowing different data collection modules (e.g., networkModule, interactionModule) to be enabled or disabled dynamically.

Instead of manually modifying each module to turn it on or off, developers **control all modules from one place**—the `ModuleController`. This makes the system **scalable**, **efficient**, and **easy to manage**.

`ModuleController` makes this possible by:
* Defining **ON/OFF switches** for each module (via **boolean flags**).
* **Preventing disabled modules from running unnecessarily**, saving CPU & memory.
* Allowing dynamic **control** over which modules are active **without modifying their code**.

> For a research study involving different participant groups, not all participants require the same data collection modules. Some groups may need only interaction data, while others may require network activity, battery usage, or additional behavioral tracking. The module controller feature can therefore serve as a robust solution for researchers seeking to collect specific types of data efficiently. **Using `ModuleController`, it is possible to enable or disable specific modules for different study groups without modifying the core application code**.

> Currently, however, the ability to switch modules on or off must be determined before the app is compiled. This means that any changes to the data being collected require recompilation of the app. A feature for dynamic module activation through the Firebase console for module activation is planned; however, at the time of writing, it is not available. **Any activation or deactivation of data collection modules must be planned ahead of time and compiled into different versions of the app**.

---

### 3.04.1. The Pathway

a. **`ModuleController` Defines Control Flags**

```java
public class ModuleController {
    public static boolean ENABLE_NETWORK = false;  // Default: OFF
    public static boolean ENABLE_INTERACTION = true; // Default: ON
    public static boolean ENABLE_NEW_EVENT = false; // Default: OFF
}
```

The `ModuleController` class contains **static boolean flags** that act as ON/OFF switches for each module:

* These **flags are accessible globally** by all modules.
* Changing them turns modules ON or OFF dynamically.


b. **The Data Collection Module Checks the Flag Before Processing Events**

```java
public class NetworkStatusCapture extends BroadcastReceiver {
    private ModuleCharacteristics moduleCharacteristics;

    @Override
    public void onReceive(Context context, Intent intent) {
        if (!ModuleController.ENABLE_NETWORK) {
            return; // If network monitoring is disabled, exit early
        }

        moduleCharacteristics = ModuleCharacteristics.getInstance();
        ConnectivityManager connMgr = (ConnectivityManager) context.getSystemService(Context.CONNECTIVITY_SERVICE);
        if (connMgr == null) {
            return;
        }

        // Continue extracting network event details and processing...
    }
}
```

**Each data collection module checks the corresponding flag before capturing events**.

* If **`ENABLE_NETWORK == false`**, the method **exits immediately**, skipping all event processing.
* This **prevents unnecessary CPU and memory usage** when the module is disabled.
* If the flag is **`true`**, the module **proceeds with event processing normally**.

---

### 3.04.2. Adding a New Module to `ModuleController`

a. **Define a Flag in `ModuleController`**

Add a boolean flag for the new module.

```java
public class ModuleController {
    public static boolean ENABLE_NEW_EVENT = false; // ✅ Default: OFF
}
```
This flag will be used to enable or disable the module dynamically.


b. **Check the Flag Inside the New Module**

Inside the new event capture module, check if it is enabled **before processing events**.

```java
public class NewEventCapture extends BroadcastReceiver {
    private ModuleCharacteristics moduleCharacteristics;

    @Override
    public void onReceive(Context context, Intent intent) {
        if (!ModuleController.ENABLE_NEW_EVENT) {
            return; // Exit early if module is disabled
        }

        moduleCharacteristics = ModuleCharacteristics.getInstance();
        // Continue processing the new event
    }
}
```
This will **prevent the module from running when disabled**.

---

### 3.04.3. Register a Module with `ModuleController`

**Example.** Integrating `ModuleController` with the event processing code shown in [03.6. End-to-End Event Processing](../)

```java
public class ModuleController {
    // Step 1: Add a boolean flag for the new module
    public static boolean ENABLE_INTERACTIONS = true;
}

public class UserInteractionCapture extends AccessibilityService {
    public void onAccessibilityEvent(AccessibilityEvent event) {
        // Step 2: Check if module is enabled before processing
        if (!ModuleController.ENABLE_INTERACTION) {
            return; // Exit early if interaction tracking is disabled
        }

        // Step 3: Detect scroll event
        if (event.getEventType() == AccessibilityEvent.TYPE_VIEW_SCROLLED) {
            // Step 4: Retrieve a HashMap from the pool
            HashMap<String, String> interactionData = HashMapPool.getMap();

            // Step 5: Extract relevant details
            String actionType = "scroll-up"; // Assuming scroll-up for this example
            String utcTimestamp = new EventTimestamp().getTimestring(); // Get UTC timestamp
            String localTimestamp = new EventTimestamp().getSystemClockTimestring(); // Get local timestamp

            // Step 6: Store extracted data in the HashMap
            interactionData.put("activity", actionType);
            interactionData.put("time", utcTimestamp);
            interactionData.put("time-local", localTimestamp);

            // Step 7: Pass event data to EventOperationManager for processing, storage, and upload
            EventOperationManager.getInstance().addEvent(
                ModuleCharacteristics.getInstance().getInteractionEventCharacteristics(), // Metadata about the event type
                interactionData
            );

            // Step 8: Release the HashMap back to the pool after processing
            HashMapPool.releaseMap(interactionData);
        }
    }
}

```

[Back to Top](#top)

