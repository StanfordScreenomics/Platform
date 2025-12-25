## 3.03. Processing Event

Once an event is **captured**, **extracted**, and **stored** in a `HashMap`, it is **passed to** `EventOperationManager.addEvent()`. This method **automates event processing**, including **formatting**, **storing** data in local SQLite, and **uploading** it to Firestore.

Developers **do NOT need to manually format events, write to databases, or handle network uploads**, as these processes run automatically in the background. This is achieved through the `moduleManater` and `databaseManager` modules, both of which provide **reusable components** that enable any data collection module to process events efficiently and consistently.

--- 

### 3.03.1. Receiving Event Data

When an event occurs, `EventOperationManager.addEvent()` **receives event data** in the form of two parameters:

1. `moduleInfo` (General Event Metadata)

- This is a `Map<String, String>` that provides general event metadata (consistant across all captured momentary events), including:
    - Event Type (e.g., "interaction", "battery-state", "battery-charging")
    - Class Name (e.g., "InteractionEvent", "BatteryStateEvent", "BatteryChargingEvent")
    - Update Ticker (Determines if the event should update Firestore ticker)
- This metadata is provided by `ModuleCharacteristics`, which ensures **all events follow a standardized format**.

2. `eventDetails` (Momentary Event Metadata)

- This is the `HashMap<String, String>` we just passed, containing momentary event details (e.g., "status", "activity", "time", "time-local").
- The `eventDetails` `HashMap` is created inside the data collection module where the momentary event data was collected (e.g., `interactionModule`, `batteryModule`) right before being passed to addEvent().

**Example** of what `addEvent()` receives.
```java
EventOperationManager.getInstance().addEvent(
    ModuleCharacteristics.getInstance().getInteractionEventCharacteristics(), // moduleInfo
    eventMap // eventDetails
);
```

| Parameter | Example Data | Description |
|---|---|---|
| `moduleInfo` | `{ "className": "InteractionEvent", "type": "interaction", "updateTicker": "1" }` | **General event metadata**, retrieved from `ModuleCharacteristics`. |
| `eventDetails` | `{ "activity": "scroll-up", "time": "20250216123045678", "time-local": "2025-02-16 16:30:45" }` | **Momentary event metadata**, including activity and timestamps. |


**NOTE:** Before retrieving general event metadata, **developers must first define the event inside `ModuleCharacteristics` class within `moduleManager` module**.

**Code.** Adding a new event general metadata (`getNewEventCharacteristics()`)
```java
public class ModuleCharacteristics {
    public Map<String, String> getCustomEventCharacteristics() {
        return new ModuleCharacteristicsData("newEvent", "newType", "1").toMap();
    }
}
```
- `"newEvent"`: **className**  (identifies source)
- `"newType"`: **type** (categorizes event type)
- `"1"`: **updateTicker** (whether to trigger Firestore ticker update [1 = trigger, 0 = do not trigger]).
- `toMap()` → Converts metadata into a `Map<String, String>` that `addEvent()` can use.

---

### 3.03.2 Formatting Event Data

After receiving event data, `addEvent()` calls `EventData.Builder` to structure the event before storing it.

- **`EventData.Builder`**
    - **Creates a structured event object** that follows a standard schema.
    - **Ensures all events have required fields** (activity, timestamps, other relavant metadata).
    - **Adds extra fields dynamically** (e.g., "interactionType", "batteryLevel").

**Code.** Overview of how `EventData.Builder` is used in `addEvent()`
```java
public void addEvent(Map<String, String> moduleInfo, HashMap<String, String> eventDetails) {
    synchronized (LOCK) {  // Ensures thread safety

        // Step 1: Format the event using EventData.Builder
        EventData event = new EventData.Builder(moduleInfo.get("type"), moduleInfo.get("className"))
            .addFields(eventDetails)  // Add extracted event details
            .build(); // Finalize the event object (this converts the builder into a final EventData object)
        }
    }
}
```

**Example 1.** `EventData.Builder` receiving twp inputs: general event metadata (`moduleInfo`) and momentary event metadata (`eventDetails`).
```java
moduleInfo = { "className": "InteractionEvent", "type": "interaction", "updateTicker": "1" };

eventDetails = { 
    "activity": "scroll-up", 
    "time": "20250216123045678", 
    "time-local": "2025-02-16 16:30:45"
};
```

**Example 2.** Dynamically adding all extracted details inside `EventData.Builder`.
```java
EventData event = new EventData.Builder("interaction", "InteractionEvent") 
    .addField("activity", "scroll-up") 
    .addField("time", "20250216123045678") 
    .addField("time-local", "2025-02-16 16:30:45") 
    .build();
```

**Example 3.** Final structure of an event in the standardized format (`EventData` object).
```
{
  "className": "InteractionEvent",
  "type": "interaction",
  "activity": "scroll-up",
  "time": "20250216123045678",
  "time-local": "2025-02-16 16:30:45"
}
```

---

### 3.03.3 Buffering the Event in Memory

Once `EventOperationManager.addEvent()` receives and formats the event using `EventData.Builder`, it **does not immediately store or upload the event**. Instead, it **buffers the event in memory** to improve efficiency.

> **What Does "Buffering the Event in Memory" Mean?**
> * Instead of writing each event directly to local storage (SQLite) or Firestore, events are **temporarily stored in memory (RAM) inside a buffer** (an `ArrayList<EventData>`).
> * **When the buffer reaches a predefined batch size**, all buffered events are **written to local storage (SQLite) at once** to optimize performance.
> * **This prevents frequent database write**s, which are slow and inefficient if done for every single event.

| Action | Code Snippet | Purpose |
|---|---|---|
| **1. Add event to memory buffer**	| `eventBuffer.add(event);` | Temporarily stores event in RAM instead of writing immediately. |
| **2. Check buffer size** | `if (eventBuffer.size() >= BATCH_SIZE) ...` | Ensures we only write to storage when enough events are collected. |
| **3. If buffer is full, write events to SQLite** | `flushEventsToDB();` | Moves all buffered events to local storage in one efficient batch. |

**Code.** Buffering events in memory with `addEvent()` 
```java
public void addEvent(Map<String, String> moduleInfo, HashMap<String, String> eventDetails) {
    synchronized (LOCK) {  // Ensures thread safety

        // Step 1: Format the event using EventData.Builder
        EventData event = new EventData.Builder(moduleInfo.get("type"), moduleInfo.get("className"))
            .addFields(eventDetails)  // Add extracted event details
            .build();

        // Step 2: Add event to the memory buffer
        eventBuffer.add(event);

        // Step 3: If buffer size reaches the limit, write to local storage
        if (eventBuffer.size() >= BATCH_SIZE) {
            flushEventsToDB();  // Writes all buffered events to SQLite
        }
    }
}
```

| Without Buffering | With Buffering |
|---|---|
| Writes to SQLite every single event | Writes multiple events in one batch |
| Slow performance due to frequent database access | Optimized performance by reducing I/O operations |
| Increased power usage (more CPU and disk writes) | Lower power usage, fewer disk writes |

---

### 3.03.4. Triggers Batch Writing

Once an event is buffered in memory inside `eventBuffer`, it is not immediately written to SQLite. Instead, **the system waits until enough events accumulate before writing them in bulk**. This improves efficiency and reduces I/O operations.

> Batch writing to SQLite occurs in the following scenarios:
> * When the number of buffered events reaches a predefined batch size (`BATCH_SIZE`).
> * When the app is backgrounded or closed, triggering a forced flush.
> * When a periodic background task checks for pending events and commits them to SQLite.

**Code .** Writing all buffered events to SQLite in one batch with `flushEventsToDB()`
```java
private void flushEventsToDB() {
    database.beginTransaction();
    try {
        for (EventData event : eventBuffer) {
            ContentValues values = new ContentValues();
            for (Map.Entry<String, String> entry : event.getFields().entrySet()) {
                values.put(entry.getKey(), entry.getValue());
            }
            database.insert("events_table", null, values);  // Batch write to SQLite
        }
        database.setTransactionSuccessful();
    } finally {
        database.endTransaction();
        eventBuffer.clear();  // Clear buffer after writing to storage
    }
}
```

> After writing to SQLite, events are stored locally on the user’s device in SQLite. **If the device is offline, events remain in SQLite until network connectivity is restored.** The next step is uploading events to Firestore, either immediately or later.

| Without Batch Writing | With Batch Writing |
|---|---|
| Writes each event **one at a time** to SQLite | Writes **multiple events in a single transaction** |
| **Slow performance** due to frequent disk I/O | **Optimized performance** by reducing database writes |
| **High power consumption** | **Lower power usage** |

---

### 3.03.5. Uploading Events to Firestore

After events are stored in SQLite, they are **eventually uploaded to Firestore**. 

This can happen in **two ways**:
1. **Automatic Batch Upload (Default Behavior)**
- Most events are stored in SQLite first and later uploaded in bulk to Firestore when:
    - The device is **online**.
    - A **scheduled background job** runs to check for pending events.
    - The user **opens the app**, triggering an upload check.
2. **Immediate Upload (For Critical Events)**
- Some time-sensitive events **skip SQLite** and are **uploaded immediately**, when:
    - **Logout events** must be uploaded immediately to ensure proper session tracking.
    - **Crash reports** must be sent instantly before the app closes.
    - , using:
```java
EventUploader.getInstance().uploadImmediately(event);
```
When a batch upload is triggered, `uploadEventsToFirestore()` retrieves **unsent events from SQLite** and pushes them to Firestore.

**Code.** Uploading Events to Firestore
```java
private void uploadEventsToFirestore() {
    List<EventData> pendingEvents = DataStorage.getEvents(); // Step 1: Retrieve pending events from SQLite
    for (EventData event : pendingEvents) {
        FirebaseFirestore.getInstance()
            .collection("events")
            .document(event.getId()) // Step 2: Assign unique Firestore document ID
            .set(event.getFields(), SetOptions.merge()) // Step 3: Upload event data
            .addOnSuccessListener(aVoid -> DataStorage.markEventAsUploaded(event)); // Step 4: Mark event as uploaded in SQLite
    }
}
```

| Action | Code Snippet | Purpose |
|---|---|---|
| **1. Retrieve pending events** | `DataStorage.getEvents();` | Gets unsent events from SQLite. |
| **2. Assign Firestore document ID** | `.document(event.getId())` | Ensures unique event tracking. |
| **3. Upload event fields** | `.set(event.getFields(), SetOptions.merge())` | Sends event data to Firestore. |
| **4. Mark event as uploaded** | `DataStorage.markEventAsUploaded(event);` | Prevents duplicate uploads. |

---

### 3.03.6. End-to-End Event Processing 

**Example.** Integrating `EventOperationManager.addEvent()` with the `HashMap` code shown in [02.3. Event Timestamp Assignment](../)
```java
public void collectData(Event event) {
    HashMap<String, String> interactionData = HashMapPool.getMap(); // Step 1: Retrieve a HashMap from the pool

    // Step 2: Extract relevant details
    String actionType = "scroll-up"; // Example action type
    String utcTimestamp = new EventTimestamp().getTimestring(); // Get UTC timestamp
    String localTimestamp = new EventTimestamp().getSystemClockTimestring(); // Get local timestamp

    // Step 3: Store extracted data in the HashMap
    interactionData.put("activity", actionType); // Add event details
    interactionData.put("time", utcTimestamp); // Add UTC timestamp
    interactionData.put("time-local", localTimestamp); // Add local timestamp

    // Step 4: Pass event data to EventOperationManager for processing, storage, and upload
    EventOperationManager.getInstance().addEvent(
        ModuleCharacteristics.getInstance().getInteractionEventCharacteristics(), // Metadata about the event type
        interactionData // The fully constructed HashMap containing event data
    );

    // Step 5: Release the HashMap back to the pool after processing
    HashMapPool.releaseMap(interactionData);
}
```

| Action | Code Snippet | Description |
|---|---|---|
| **1. Receives event data** | `addEvent(Map<String, String> moduleInfo, HashMap<String, String> eventDetails`) | The method takes two parameter: `moduleInfo` (general event metadata) and `eventDetails` (momentary event metadata). |
| **2. Formats event data using `EventData.Builder`** | `EventData event = new EventData.Builder(...)` | Ensures the event follows a standardized structure for efficient multimodal data fusion. |
| **3. Buffers the event in memory** | `eventBuffer.add(event)` | Instead of writing immediately, events are **stored temporarily in memory** to optimize performance. |
| **4. Batch local storage writing** | `if (eventBuffer.size() >= BATCH_SIZE) flushEventsToDB();` | When enough events are buffered, they are written to the user device's **hidden local storage** (SQLite). |
| **5. Batch uploads to Firestore** | `EventUploader.uploadImmediately(event)` | Events stored in local storage are transferred in bulk to Firestore |

---

### 3.03.7. Developer Guide

| Step | To Do | Background Task |
|---|---|---|
| **1. Capture Event** | Listen for an event (e.g., battery change) and create a `HashMap` | No manual formatting required. `HashMapPool` optimizes memory usage. |
| **2. Assign Timestamp** | Use `EventTimestamp.getTimestring()` | Generates **UTC and local timestamps** automatically. |
| **3. Define Event Metadata** | Ensure the event is added to `ModuleCharacteristics`. | Standardized metadata (`className`, `type`, `updateTicker`) is retrieved automatically. |
| **4. Format Event** | Call `EventOperationManager.addEvent()` | `ModuleCharacteristics` and `EventData.Builder` standardize the event format to ensure consistency. |
| **5. Store Locally** | No action required. | Events are automatically **buffered** in memory first, then **written** to SQLite in batches for efficiency. |
| **6. Upload to Firestore** | No action required (unless an immediate upload is needed). | Events are automatically **uploaded** in batches, or immediately if needed. |

[Back to Top](#top)




