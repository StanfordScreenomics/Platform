## 2.02. Firebase Setup

**Before you begin:**
* Create a Gmail account, if you haven't already, as it's required to access Firebase services.
* A video instruction file is available here: [02_Firebase-Setup_Video.mp4](../Ch2_App-Compilation/02_Firebase-Setup_Video.mp4)  
  → Click the link above, then press the **Download** button (top-right) to save the video.

---

### 2.02.1. Create a Firebase Project
a. **Go to Firebase Console**
   - Open [Firebase Console](https://console.firebase.google.com/).

b. **Add a New Project**
   - Click **Create a project**.
   - Enter a project name (e.g., "YourStudyName").
   - Optionally enable Google Analytics.
   - Click **Create Project** and then **Continue**.

> When setting up your Firebase project, think of a clear and descriptive name that reflects the purpose of your app or the project itself. In Firebase, the project name is a human-readable identifier you choose when creating a new Firebase project. Here are some key points about the project name:
> * **Purpose**: It helps you identify your project in the Firebase Console, especially if you have multiple projects. For example, you might name it after your app or its functionality (e.g., "YourStudyName").
> * **Flexibility**: You can choose any name that meets your needs, and it can include letters, numbers, and spaces.
> * **Not Unique**: The project name does not have to be unique across all Firebase projects. However, the project ID, which is generated automatically, must be unique within Firebase.
> * **Visibility**: The project name is visible to you and anyone else who has access to your Firebase Console, making it easier to manage and organize your projects.

---

### 2.02.2. Add Android App to Firebase
a. **Register Your App**
   - In the Firebase project dashboard, click the Android icon.
   - Enter your app's package name (e.g., `edu.stanford.yourstudyname.screenomics`).
   - Optionally, add an app nickname and SHA-1 fingerprint.
   - Click **Register app**.

**App Package Name**
* The original package name of the Screenomics app is `edu.stanford.communication.screenomics`. Typically, the package name follows a 3-level hierarchical format, such as `edu.stanford.yourstudyname`, where each segment represents a different level in Android project: `edu` indicates the top-level domain, `stanford` can represent the second highest, and `mystudyapp` is the lowest. In the case of the Screenomics app, the package is organized into four levels: `edu`, `stanford`, `communication`, and `screenomics`.
* Changing multiple segments or reshaping the levels of the package hierarchy can be quite complex and is not advisable for those with limited experience, as it may lead to various configuration issues throughout the project. **The suggested action is to replace the `communication` part with your chosen study name in lowercase without spacing (e.g., `yourstudyname`), resulting in your new package name format of `edu.stanford.yourstudyname.screenomics`.** This keeps the overall structure intact while minimizing complexity and allowing you to personalize the app for your own study.
> * **Uniqueness**: The app package name is a unique identifier for your Android application, so it is essential to choose a distinctive study name. Therefore, the package name must be unique across all applications on the Google Play Store. It helps distinguish your app from others. 
> * **Not the App Name**: The app package name is not the same as the app's display name (the name users see on their device). The display name is set in your app’s resources and can be different from the package name.
 
b. **Download `google-services.json`**
   - After registering your app, you will be prompted to download the `google-services.json` file.
   - Download the `google-services.json` file and save it to your computer.

> **What is `google-services.json`?** This file contains essential information about your Firebase project, such as API keys, project ID, and service configurations. It allows your Android app to connect to and use various Firebase services.
>    * **Firestore Database**: The google-services.json file allows your app to connect to Firestore, enabling data storage and retrieval.
>    * **Google Cloud Storage**: It facilitates access to Google Cloud Storage for uploading and downloading files.
>    * **Crashlytics**: The file also configures Crashlytics for error reporting, ensuring that crash data is sent to your Firebase project.

---
 
### 2.02.3. Enable Firestore Database
a. **Go to Firestore Database**
   - In your Firebase Console, click on **Build > Firestore Database** in the left sidebar.

b. **Create Database**:
   - Click **Create Database**.
   - Choose **Location** (nam5, if you are in United States).
   - Choose **Start in Production Mode** (this ensures that all third party reads and writes are denied).
   - Click **Next**, then select a location for your database (choose the closest region).
   - Click **Done**.

c. **Review and Edit Rules**
   - At the top of the Firestore Database section, you will see tabs labeled "Data," "Rules," and "Indexes." Click on the "**Rules**" tab.
   - Review and edit Firestore rules
   - Click on the "**Publish**" button to apply the new rules.

**Example Rule 1.** To allow only authenticated users to read and write:
```java
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth != null; // Only authenticated users
    }
  }
}
```

**Example Rule 2.** To allow public access to read and write:
```java
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true; // Anyone can read/write
    }
  }
}
```

> **Important Note:** The Stanford Screenomics team does not provide or share specific security rules, as these must be developed based on study-specific or institutional policies. This approach ensures that security protocols align with each study’s unique requirements, considering factors such as data sensitivity, regulatory compliance, and ethical guidelines. For a detailed overview of security rules, their structure, and implementation principles, refer to the [Firebase Security Rules Primer](https://firebase.google.com/docs/rules).

---

### 2.02.4. Add Settings Profiles
a. Click on the "Start collection" button.
   - Enter **Collection ID**: **`settings_profiles`**
   - Click **Next**.

b. Click on "Add document" button inside the `settings_profiles` collection just added.
   - Enter **Document ID**: `_default_`
      - Each document ID represents a group profile.
   - In the field input area, add **ALL** configurable dynamic parameters one by one, listed in [Chapter 1. Firebase - 05.1. Dynamic Parameters](../Ch1_Firebase/05_Settings.md).
      - For each field:
         - Field Name: Enter the Parameter Name (e.g., `settings-refresh-interval`)
         - Field Type: Select "String" (for all 15 parameters) from the dropdown.
         - Value: Enter the "Suggested Minimum/Default" value for the parameter.

> You  can repeat this process to add multiple group settings profiles, ensuring that each group name (document ID) follows the three capital letters format, except for the `_default_` settings profile. Group profiles do not require all 15 parameters; you can simply add a few essential fields.

> Before compiling the Screenomics app in Android Studio, you must configure both the mandatory default profile and any optional study group profiles. During account registration, the app retrieves all 15 dynamic parameter values from the default profile, a group profile, or a combination of both. Missing values, out-of-range inputs, typos in parameter names, or incorrect field types can cause crashes or malfunctions. **Double-check all entries to ensure accuracy**.

---

### 2.02.5. Enable Authentication
a. **Go to Authentication**
   - In your Firebase Console, click on **Build > Authentication** in the left sidebar.

b. **Get Started**
   - Click on the **Get Started** button.
   - Choose **Sign-in Method**: Sign-in Providers > Native Providers > **Email/Password**
   - Toggle the switch to **enable** "Email/Password."
   - Click on **Save** button at the bottom.

---

### 2.02.6. Setup Cloud Storage
a. **Go to Storage**
   - In your Firebase Console, click on **Build > Storage** in the left sidebar.
   - Click on the "**Get Started**" button.
      - If you don't see the "Get Started" button, you may need to update your plan by linking your billing account. Once linked, the button should appear.

b. **Setup Storage Rules**
   - Choose Storage Location.
      - You will be prompted to select a location for your storage. Choose the location that best serves your needs and click "Continue."
   - Choose **Start in Production Mode** (this ensures that all third party reads and writes are denied).

c. **Review and Edit Rules**
   - Once in the Cloud Storage section, you will see several tabs at the top. Click on the "**Rules**" tab.
   - Review and edit Storage rules
   - Click on the "**Publish**" button to apply the new rules.

**Example Rule 1.** To allow only authenticated users to read and write:
```java
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /{allPaths=**} {
      allow read, write: if request.auth != null; // Only authenticated users
    }
  }
}
```

**Example Rule 2.** To allow public access to read and write:
```java
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /{allPaths=**} {
      allow read, write: if true; // Anyone can read/write
    }
  }
}
```

> **Important Note:** The Stanford Screenomics team does not provide or share specific security rules, as these must be developed based on study-specific or institutional policies. This approach ensures that security protocols align with each study’s unique requirements, considering factors such as data sensitivity, regulatory compliance, and ethical guidelines. For a detailed overview of security rules, their structure, and implementation principles, refer to the [Firebase Security Rules Primer](https://firebase.google.com/docs/rules).

d. **Find and Save `gs:// URI`**
   - Click on the "**Files**" tab.
   - In the left top corner of the Storage bucket, you will find a link icon followed by the **gs://** URI.
      -  The URI should look like this: `gs://yourstudyname-f6198.firebasestorage.app`
   - Store (or remember) this URI for later use.

---

### 2.02.7. Enable Crashlytics
a. **Enable Crashlytics**:
   - In your Firebase Console, click on **Run > Crashlytics** in the left sidebar.
   - Click **Get Started** to enable Crashlytics for your project.
   - Follow any prompts to complete the setup.

> If you don't see the "Get Started" option in the Crashlytics dashboard of the Firebase Console, possible Reasons are:
> * **Already Enabled**: If you have previously set up Crashlytics for your project, it might already be enabled. You should be able to see crash reports and other data without needing to go through the setup process again.
> * **Project Settings**: Check the project settings to confirm that Crashlytics is enabled. You can do this by navigating to the Project Settings in the Firebase Console and looking under the Integrations tab.
> * **No Data Yet (Most Likely)**: If you just set up Crashlytics and haven't triggered any crashes or your app hasn't been used yet, you may not see data immediately.

> **Solution**: Try revisiting Crashlytics during [runtime validation](../02_App-Compilation/03_Runtime-Validation.md) after completing the [Android Studio setup](../02_App-Compilation/02_Android-Studio-Setup.md). Once your app starts generating data, Crashlytics should display relevant information.


[Back to Top](#top)

