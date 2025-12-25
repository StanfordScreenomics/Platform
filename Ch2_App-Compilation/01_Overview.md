## 2.01. Overview: Using the Stanford Screenomics App (No Coding Required)

The Stanford Screenomics platform is designed for researchers who want to passively collect smartphone-based digital trace data without needing any coding experience. The app enables data collection through an easy-to-use system that integrates with Firebase, automating many technical processes such as data storage, authentication, and real-time settings control.

The _Chapter 2. App Compilation_ provides an overview of how researchers can **configure, compile, and deploy the app, all without modifying the underlying code**. This chapter serves as a guide for those looking to set up and use the Screenomics platform for their studies.

---

### 2.01.1. Who Is This Guide For?
This guide is intended for:
- **Researchers** who need a ready-to-use digital trace data collection app.
- **Study coordinators** responsible for configuring app settings and distributing it to participants.
- **Non-technical users** who want to deploy the app without writing or modifying code.

**No prior experience with coding or mobile app development is required to follow this guide.** The setup process primarily involves configuring Firebase services and compiling the app using Android Studio.

---

### 2.01.2. How the Screenomics System Works [[See Chapter 1](../Ch1_Firebase/01_Overview.md)]
The **Screenomics app** is built on a cloud-integrated design, meaning that data collection, authentication, and study management are handled through Firebase’s cloud services rather than requiring manual configurations within the app itself. This makes it easy for researchers to manage their studies remotely and ensure that data is securely stored and accessible.

Key features of the Screenomics system include:
- **Automatic data storage**: Text-based data is stored in Firestore, while non-text data (e.g., screenshots) is stored in Google Cloud Storage.
- **User authentication**: Secure participant login and account management through Firebase Authentication.
- **Real-time monitoring**: The ticker system allows researchers to track data collection activity.
- **Dynamic study settings**: Researchers can modify data collection parameters remotely in Firestore, eliminating the need for app updates.
- **Error tracking and diagnostics**: Firebase Crashlytics provides automated crash reporting and debugging support.

---

### 2.01.3. What You Will Learn
This chapter will help you understand:
- **How to set up Firebase** to manage user authentication, data storage, and study parameters.
- **How to install and configure Android Studio** to compile the app for deployment.
- **How to manage study settings dynamically** using Firestore without requiring app updates.
- **How to validate, distribute, and publish the app** to study participants.

---

### 2.01.4. Next Steps
The following sections of this guide will walk you through:
- Setting up Firebase and linking it to the app.
- Installing Android Studio and preparing the app for compilation.
- Validating the app’s functionality before deployment.
- Publishing and distributing the app to study participants.



[Back to Top](#top)
