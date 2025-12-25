## 1.01. Overview: Firebase Integration in the Stanford Screenomics

The **Stanford Screenomics system leverages Firebase’s suite of services** to enable **secure, scalable, and efficient data collection** for research. The system is designed to handle user authentication, event logging, real-time synchronization, and cloud storage, ensuring compliance with HIPAA regulations while providing a seamless experience for researchers and participants.

The _Chapter 1. Firebase_ provides an overview of how Firebase integrates with the Screenomics app, detailing key components and their roles in the research workflow by introducing: 
- **Database structure and storage** – How Firestore and Google Cloud Storage are used to manage text-based and non-text-based data.
- **User authentication and onboarding** – How Firebase Authentication secures user accounts and manages logins.
- **Event-based data collection** – How the app records user interactions and activities in Firestore.
- **Real-time monitoring and system health** – How `ticker` and Crashlytics provide real-time system status updates and error tracking.
- **Dynamic settings control** – How Firestore enables remote configuration of study parameters without requiring app updates.

---

### 1.01.1. How the Screenomics System Uses Firebase
The Screenomics system follows a **cloud-centric design**, where all core functionalities are managed through Firebase services. Below is an overview of key integrations:

**1. Firestore Database (Text-Based Data Storage)**
- **Stores event data** collected from various app modules.
- Organized into structured collections such as `users-events`, `settings_profiles`, and `ticker`.
- Allows real-time data retrieval and analytics.

**2. Google Cloud Storage (Non-Text Data Storage)**
- Handles storage of large binary files (e.g., screenshots).
- Files are stored in a user-specific directory, ensuring privacy and organization.

**3. Firebase Authentication (User Sign-Up & Login)**
- Manages secure user registration and sign-in.
- Enforces password authentication to restrict access to authorized participants only.

**4. Crashlytics (App Stability & Debugging)**
- Automatically captures and reports app crashes.
- Provides developers with logs and stack traces to debug issues efficiently.

**5. Ticker System (Real-Time Monitoring)**
- Tracks app activity in near real-time.
- Allows researchers to monitor which users are actively generating event data.

**6. Dynamic Settings Control (Firestore-Managed Parameters)**
- Researchers can modify data collection parameters remotely using Firestore.
- Settings such as sampling intervals, GPS tracking, and network usage can be adjusted without app updates.

---

### 1.01.2. Developer & Researcher Workflow

Researchers and developers working with the **Stanford Screenomics system** can leverage Firebase’s capabilities to:
- **Monitor app activity** and ensure all participants are actively contributing data.
- **Customize data collection parameters** based on study requirements.
- **Retrieve user-generated events** from Firestore for analysis.
- **Diagnose and fix errors** using Crashlytics.

---

### 1.01.3. Next Steps

The following sections will provide detailed guidance on:
- The **Firestore database structure** and how different collections store user data.
- How **events** are captured, processed, and stored in Firestore.
- How **user authentication** works within the app.
- How **settings profiles** can be used to dynamically control study configurations.
- How **Crashlytics and the ticker system** help maintain app stability and track active participants.

By following this guide, researchers and developers will gain a comprehensive understanding of how the **Screenomics system** integrates with Firebase to enable real-time, scalable, and research-driven data collection.



[Back to Top](#top)
