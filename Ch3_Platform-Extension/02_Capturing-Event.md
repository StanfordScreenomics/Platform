## 3.02. Capturing Event

Each event is captured by its respective data collection module, which **listens for specific system events** and prepares the data accordingly, and **constructs a `HashMap` containing event data**, before passing it to `EventOperationManager.addEvent`.

---

### 3.02.1. Listening for Specific Events

- Listening for events is a dynamic, ongoing process that involves setting up listeners or receivers to capture changes in the system. This step is not a single action but rather a continuous state of readiness to respond to events as they happen.
- Therefore, the specific implementation of this step can vary widely based on the type of events you want to capture (e.g., network changes, user interactions); **Each type of event might require different classes, methods, and configurations.**

**Example 1. Listening for Network Changes**: You might set up a `BroadcastReceiver` to listen for network connectivity changes.
```java
public class NetworkStatusCapture extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        // Handle the network status change event
    }
}
```

**Example 2. Listening for User Interactions**: You could set up an `AccessibilityService` to listen for user interactions like clicks and scrolling.
```java
public class UserInteractionCapture extends AccessibilityService {
    @Override
    public void onAccessibilityEvent(AccessibilityEvent event) {
        if (event.getEventType() == AccessibilityEvent.TYPE_VIEW_CLICKED) {
            // Handle click event
            Log.d("UserInteraction", "User clicked: " + event.getContentDescription());
        } else if (event.getEventType() == AccessibilityEvent.TYPE_VIEW_SCROLLED) {
            // Handle scroll event
            Log.d("UserInteraction", "User scrolled: " + event.getScrollX());
        }
    }
}
```

---

### 3.02.2. Constructing a HashMap with Event Metadata

As described in the [02.1. Listening for Specific Events](#3021-listening-for-specific-events), your data collection module listens for specific system events and prepares to capture the relevant details. Once an event occurs, you will **extract the relevant event metadata (e.g., status, activity type) and add timestamps and storing them into a `HashMap`.**. This section covers the process of populating the `HashMap` with event metadata.

- When you create a `HashMap<String, String>`, you have **key-value pairs** where:
    - **Key**: A descriptive `String` that identifies the type of data (e.g., "activity", "status", "time").
    - **Value**: The actual `String` data associated with that key (e.g., "scroll-up", "Connected-to-WiFi", "20250216123045678").
    - **Multiple key-value pairs are added to a single `HashMap`**, each serving a specific purpose (e.g., capturing activity type, timestamps, and metadata)

**Example Representation**. If you are capturing an interaction event with an `activityType`, the `HashMap` might look like this:
```java
HashMap<String, String> interactionData = new HashMap<>(); // Step 1: Create empty HashMap
interactionData.put("activity", actionType); // Step 2: Add event details
interactionData.put("time", new EventTimestamp().getTimestring()); // Step 3: Add UTC timestamp
interactionData.put("time-local", new EventTimestamp().getSystemClockTimestring()); // Step 4: Add local timestamp
```

**Example Look of the HashMap**. In terms of its content, after adding the values, it would look something like this:
```java
{
  "activity": "scroll-up",                // Key: "activity", Value: actionType (e.g., "scroll-up")
  "time": "20250216123045678",        // Key: "time", Value: UTC timestamp
  "time-local": "2025-02-16 16:30:45 (PT)" // Key: "time-local", Value: local timestamp
}
```

**Actual usage** within data collection modules is extend beyond a simple `HashMap<String, String>` by utilizing **`HashMapPool.getMap()`** and **`HashMapPool.releaseMap(eventMap)`** to optimize memory management. A "pool" refers to a collection of reusable resources—in this case, `HashMap` instances.

- **`HashMapPool.getMap()`: Retrieving a Reusable `HashMap`**
    - This method retrieves an available HashMap from the pool. If no pre-existing HashMap is available, a new instance is created. This minimizes memory allocation overhead and enhances performance by reusing objects.
    - **Potential scenarios where the pool may be depleted**:
        - **High concurrent requests**: Multiple data collection modules simultaneously request HashMap instances.
        - **Delayed release of instances**: If HashMap instances aren’t returned quickly (e.g., due to long event processing times), new requests may temporarily fail.
        - **System memory constraints**: Under memory pressure, the system may limit object allocation.
        - **Spikes in activity**: Sudden bursts of user interactions or system events may exceed the pool’s available supply.
    - **Java `HashMap` default behavior**:
        - A HashMap in Java has a **default capacity of 16 buckets** and a **load factor of 0.75**.
        - This means that when the number of entries exceeds 75% of its capacity (i.e., 12 entries), it will resize (e.g., when adding the 13th entry).

- **`HashMapPool.releaseMap(eventMap)`: Returning a `HashMap` to the Pool**
    - Once the event data has been processed, this method returns the `HashMap` to the pool for reuse.
    - **Why releasing `HashMap` matters?**
        - Prevents memory leaks by ensuring unused HashMap instances don’t remain allocated.
        - Enables efficient reuse of memory, reducing unnecessary object creation.
        - Improves performance in continuous data collection scenarios.

**Example** of populating the `HashMapPool.getMap()` and `HashMapPool.releaseMap()` with event metadata. 
```java
public void collectData(Event event) {
    HashMap<String, String> interactionData = HashMapPool.getMap(); // Retrive a HashMap from the pool

    // Extract relevant details
    String actionType = "scroll-up"; // Example action type

    // Store data in the HashMap
    interactionData.put("activity", actionType); // Add event details

    // Release the HashMap back to the pool
    HashMapPool.releaseMap(interactionData);
}
```

> - **Benefits of Using `HashMapPool`**
>     - **Memory Efficiency**: Frequent creation and destruction of `HashMap` instances can lead to increased garbage collection and memory fragmentation. Using a pool minimizes these costs by reusing existing objects.
>     - **Performance Improvement**: Reusing `HashMap` instances speeds up operations, especially in high-frequency data collection scenarios, as it avoids the overhead associated with object creation.
>     - **Simplified Resource Management**: The pooling mechanism abstracts away the complexity of managing object lifecycles, allowing developers to focus on data handling rather than memory management.



---

### 3.02.3. Event Timestamp Assignment

Each event is then assigned two timestamps using `EventTimestamp` at the time of capture. These timestamps are stored within the `HashMap` along with its metadata, to maintain a precise chronological record of events.

- **`EventTimestamp`** (from `moduleManager/EventTimestamp.java`) generates **two timestamps**:
    - `getTimestring()`: Provides a Coordinated Universal Time timestamp (UTC; in `yyyyMMddHHmmssSSS` format) for **event logging and Firestore events collection**.
        - This UTC timestamp ensures all event logs are consistent across different time zones.
    - `getSystemClockTimestring()`: Provides a human-readable local timestamp (user device's system time; in `yyyy-MM-dd HH:mm:ss` format) for **Firestore ticker updates**.

- **Note**: This `EventTimestamp` class is coming from `moduleManager.EventTimestamp`, NOT `databaseManager.EventTimestamp`.
    - The `moduleManager.EventTimestamp` is responsible for **generating real-time event timestamps** as they occur.
    - The `databaseManager.EventTimestamp` is primarily used processing and standardizing timestamps before storing them in the database in a later step.

- When logging events, **developers do NOT need to handle timestamps manually** and instead, should **ALWAYS use `EventTimestamp.getTimestring()` and `timestamp.getSystemClockTimestring()`** to automatically generate two timestamps, to maintain consistency in the system.

**Example** of populating the `HashMap` with event metadata:
```java
public void collectData(Event event) {
    HashMap<String, String> interactionData = HashMapPool.getMap(); // Retrive a HashMap from the pool

    // Extract relevant details
    String actionType = "scroll-up"; // Example action type
    String utcTimestamp = new EventTimestamp().getTimestring(); // Get UTC timestamp
    String localTimestamp = new EventTimestamp().getSystemClockTimestring(); // Get local timestamp

    // Store data in the HashMap
    interactionData.put("activity", actionType); // Add event details
    interactionData.put("time", utcTimestamp); // Add UTC timestamp
    interactionData.put("time-local", localTimestamp); // Add local timestamp
    // At this point, the `HashMap` is fully constructed and ready to be passed to
    // `EventOperationManager.addEvent()` for processing, storage, and upload.

    // Release the HashMap back to the pool
    HashMapPool.releaseMap(interactionData);
}
```

After populating the `HashMap` with event metadata and timestamps, the typical next step before releasing the `HashMap` is 

---

### 3.02.4. From Listening To Logging

**Example.** Listening and logging scroll-up events
```java
public class UserInteractionCapture extends AccessibilityService {
    public void onAccessibilityEvent(AccessibilityEvent event) {
        // Step 1: Listen for scroll event
        if (event.getEventType() == AccessibilityEvent.TYPE_VIEW_SCROLLED) {
            // Step 2: Retrieve a HashMap from the pool
            HashMap<String, String> interactionData = HashMapPool.getMap();

            // Step 3: Extract relevant details
            String actionType = "scroll-up"; // Assuming scroll-up for this example
            String utcTimestamp = new EventTimestamp().getTimestring(); // Get UTC timestamp
            String localTimestamp = new EventTimestamp().getSystemClockTimestring(); // Get local timestamp

            // Step 4: Store extracted data in the HashMap
            interactionData.put("activity", actionType);
            interactionData.put("time", utcTimestamp);
            interactionData.put("time-local", localTimestamp);

            // Step 5: Pass event data to EventOperationManager for processing, storage, and upload
            EventOperationManager.getInstance().addEvent(
                ModuleCharacteristics.getInstance().getInteractionEventCharacteristics(), // Metadata about the event type
                interactionData
            );

            // Step 6: Release the HashMap back to the pool after processing
            HashMapPool.releaseMap(interactionData);
        }
    }
}
```







[Back To Top](#top)
