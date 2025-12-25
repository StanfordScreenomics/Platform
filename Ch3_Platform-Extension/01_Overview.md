## 3.01. Overview: Extending Screenomics with New Data Collection Modules

The Screenomics app is built on a **modular architecture**, making it easy to extend its functionality by adding new data collection modules. This document serves as an introduction to the development process, outlining how the app is structured and how developers can take advantage of core base modules to simplify implementation.

This guide walks through the essential components of the Screenomics system and how they work together to support modular development. It covers:
- **The app’s modular architecture**, explaining how independent data collection modules interact with the system.
- **Core base modules (`moduleManager` and `databaseManager`)**, which provide reusable components for event handling, metadata management, and data storage.
- **The streamlined development process**, demonstrating how developers can focus solely on capturing events without worrying about event formatting, storage, or uploading.
- **Dynamic controls through Firestore**, enabling real-time adjustments to data collection without requiring app updates.

---

### 3.01.1. How the Modular System Works
The Screenomics app is structured so that each data collection module is responsible for listening to system events, extracting relevant details, and passing that data through a standardized processing pipeline. 

Two key base modules support this system:
- `moduleManager`: Standardizing Event Metadata and Timestamps
  - This module ensures consistency across all event data by:
    - Assigning standardized metadata to each event type.
    - Providing accurate UTC and local timestamps.
- `databaseManager`: Automating Processing and Storage
  - This module handles:
    - **Event Processing:** Formatting event data for uniformity.
    - **Local Storage:** Buffering and writing data efficiently to SQLite.
    - **Uploading to Firestore:** Managing bulk and real-time data uploads.

By leveraging these core modules, developers avoid redundant work and can rely on built-in automation to handle much of the event lifecycle.

---

### 3.01.2. Developer Workflow for Adding a New Module

The process for developing a new data collection module follows a structured flow:

1. **Capture an Event** – A module listens for system events and extracts relevant data.
2. **Assign Metadata** – Event data is classified using standardized metadata.
3. **Send Event for Processing** – The event is passed to `EventOperationManager.addEvent()`, which automates formatting, storage, and uploads.
4. **Control Collection Dynamically** – Modules can be activated or deactivated through `ModuleController`, while Firestore `settings_profiles` allows researchers to dynamically modify data collection parameters remotely.

---

### 3.01.3. Next Steps

The following sections will provide detailed guidance on each step of the development process. You will learn how to:
- Capture and structure event data effectively.
- Use `moduleManager` to ensure consistency in event metadata.
- Utilize `databaseManager` to automate processing and storage.
- Implement module activation switches through `ModuleController`.
- Add dynamic parameters to adjust event logging in real-time.

By following this guide, developers can seamlessly integrate new data collection modules into the Screenomics system while benefiting from an efficient and scalable architecture.


[Back to Top](#top)

