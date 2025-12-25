## 3.06. Takeaway

The Stanford Screenomics platform is designed with a **modular architecture** that allows developers to seamlessly extend its functionality by creating new data collection modules. This document provides an overview of how the app's architecture supports modular development and explains how two core base modules—**`moduleManager`** and **`databaseManager`**—enable developers to easily capture, process, store, and upload event data without needing to build these systems from scratch.

---

### 3.06.1. Modular Architecture

The Stanford Screenomics platform follows a **modular design**, meaning that different types of data collection (e.g., interaction events, network activity, location tracking) are handled by independent **data collection modules**. Each module is responsible for listening to specific system events, extracting relevant data, and passing it through a standardized event processing pipeline.

**Core Components of the Modular System**
1. **Data Collection Modules**: Responsible for listening for and capturing specific events.
    - Example: Interaction module captures user interactions, network module captures network connectivity changes.
2. **`moduleManager`**: Provides reusable components for event metadata and timestamping.
    - Standardizes event metadata across all modules.
    - Ensures consistency in timestamps.
3. **`databaseManager`**: Handles event processing, storage, and upload automation.
    - Formats event data before storage.
    - Buffers events in memory to optimize performance.
    - Writes events to local SQLite storage and later uploads them to Firestore.

---

### 3.06.2. Base Modules

The **`moduleManager`** and **`databaseManager`** modules provide a robust framework that automates key processes such as timestamping, metadata management, data formatting, local storage, and Firestore uploads. By using these modules, developers can **focus on capturing new types of data** rather than implementing redundant processing logic.

**Key Functionalities of `moduleManager`**
- **Standardized Event Metadata** (`ModuleCharacteristics`):
    - Ensures each event type has a `className`, `type`, and `updateTicker`.
    - Provides a consistent format for different event types.
- **Timestamp Assignment** (`EventTimestamp`):
    - Generates both UTC and local timestamps for each event.
    - Ensures all event logs are consistent across different time zones.
- **Module Activation/Deactivation** (`ModuleController`):
    - Developers can turn on or turn off entire module functionalities before compiling the app.

**Key Functionalities of `databaseManager`**
- **Event Processing** (`EventOperationManager.addEvent()`):
    - Handles formatting using `EventData.Builder`.
    - Buffers events in memory before writing them to SQLite.
- **Local Storage & Firestore Upload**:
    - Events are stored in SQLite and later uploaded to Firestore.
    - Certain critical events can be uploaded immediately.
- **Dynamic Parameter** (`SettingsManager`):
    - Firestore `settings_profiles` allow researchers to dynamically adjust data collection behavior without app updates.

---

### 3.06.3. Developer Responsibilities
| **Step** | **To Do** | **Background Task** |
|---|---|---|
| **Capture Event** | Listen for a system event and extract details | No manual formatting required. |
| **Assign Timestamp** | Use `EventTimestamp.getTimestring()` | UTC and local timestamps generated automatically. |
| **Define Metadata** | Add event type to `ModuleCharacteristics` | Standardized metadata is retrieved automatically. |
| **Pass to `addEvent()`** | Call `EventOperationManager.addEvent()` | Event is formatted, buffered, stored, and uploaded. |
| **Control Collection** | Use `ModuleController` or `SettingsManager` | Enables dynamic ON/OFF switching and Firestore control. |

By leveraging the **modular architecture** of the Screenomics app, developers can extend its functionality without needing to manually handle event processing, storage, or uploads. The **`moduleManager`** and **`databaseManager`** modules provide essential reusable components that standardize metadata, automate timestamps, format event data, and optimize storage and uploads. This ensures that all collected data is processed efficiently, consistently, and with minimal manual intervention.


[Back to Top](#top)

