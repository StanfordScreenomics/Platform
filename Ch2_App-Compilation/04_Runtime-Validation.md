
## 2.04. Runtime Validation

The **Runtime Validation Process** ensures that the app functions correctly when executed in a simulated environment. This process involves **running the app on an emulator** and **verifying data flow** to ensure proper interaction with the database. Once the app is launched in the emulator, various user actions are simulated to test core functionalities. During this process, the database is actively monitored to confirm that data is being stored, retrieved, and updated correctly. By performing runtime validation, developers can catch potential issues related to app behavior and data flow before deploying the app to real devices.

**Before you begin:**
* A video instruction file is available here: [04_Runtime-Validation_Video.mp4](../Ch2_App-Compilation/04_Runtime-Validation_Video.mp4)  
  → Click the link above, then press the **Download** button (top-right) to save the video.

---

### 2.04.1. Simulating the App in an Emulator

a. **Set Up an Emulator**
  - Click on the **Device Manager** icon in the toolbar (📱 it looks like a phone).
    - If the icon is not visible, go to **Tools > Device Manager**.
  - Click **Create Virtual Device**.
  - Choose a device from the list and click **Next**.
  - Select a system image (download it if prompted) and click **Next**.
  - Configure additional settings if needed, then click **Finish**.

b. **Run the App on the Emulator**
  - Start the **Emulator**
    - In Device Manager, find your created virtual device and click the green "Run ▶" button (green triangle) or press `Shift + F10` to launch it.
  - Wait for the Emulator to Boot (it may take a few minutes to boot up for the first time).
  - Once the emulator is running, you can interact with your app as you would on a physical device.
    - Use your mouse to click, drag, and type.

---

### 2.04.2. Test Registration

a. **Enter Registration Details**
  - On the first launch of your app in the emulator, fill in the required fields:
    - Study Group Code
    - Participant ID
    - Email
    - Password

b. **Submit the Form**
  - Tap the Register button to submit the form.
  - Observe the app’s behavior to ensure successful registration.
  
c. **Check Firebase Console for User Registration**
  - Go to **Firebase Console**
  - In your Firebase Console, click on **Build > Authentication** in the left sidebar
  - Click on the "**Users**" tab at the top of the Authentication section.
  - **Verify User Registration**
    - **Look for the newly registered user in the list**.
    - Ensure the email and other details match the registration input.
    - If the user does not appear, check for any errors in the app or Firebase setup.
  - **Repeat Testing**
    - If you need to test multiple registrations, you can either delete the user from the Firebase Console or use a different email each time.

---

### 2.04.3. Test Data Collection

a. **Grant Required Permissions**
  - After successful registration, the app will request permissions. Grant all necessary permissions for data collection (e.g., screen recording, accessibility services).
    - If permissions are denied, the app will not start data collection.

b. **Confirm Data Collection Is Running**
  - Check for the Media Projection icon
    - Swipe down from the top of the emulator/device screen.
    - Look for the **screen recording/media projection icon**, which indicates that data collection is ongoing.
    - If the icon is missing, check if permissions were denied or if the app crashed.

c. **Verify Firestore Database Setup**
  - In the Firebase Console, navigate to **Firestore Database**.
  - Check that the following four collections have been created automatically:
    - `install`
    - `settings_profiles`
    - `ticker`
    - `users`
   
d. **Check User-Specific Data in Firestore Database**
  - In the `users` collection, find the newly registered user.
    - The **username** should be `Study Group Code + Participant ID`.
  - Click on the username document and ensure it contains three subcollections:
    - `events` (captures interactions, timestamps, etc.)
    - `settings` (stores user-specific settings profile)
    - `specs` (device specifications and configurations)

e. **Verify Dynamic Parameters** 
  - Locate the `settings_profiles` collection (Study Group Settings)
  - Look for a document corresponding to the **study group code** used during registration.
  - Open the document and check the parameters (e.g., data collection interval, data quality settings).
  - Now locate the `users` > `username` > `settings` > `user_settings` document.
  - **Verify that all parameters are configured correctly based on per-user, per-group, and default settings profiles.**
  - In either `settings_profiles` (per-group) or users > settings (per-user), change parameter values, such as data collection interval (e.g., from 60000 to 30000 milliseconds).
  - **Verify again that all parameters are updated correctly based on per-user, per-group, and default settings profiles.**
  - Verify that newly collected data reflects the updated settings.
    - Alternatively, you can monitor real-time updates in **Android Studio Logcat** (View > Tool Windows > Logcat).

f. **Verify Cloud Storage Setup**
  - In the Firebase Console, navigate to **Storage**.
  - Open the **Files** tab and check if a new folder has been created for the user.
    - The folder should be named using the **username** (study group code + participant ID).

g. **Check User-Specific Data in Cloud Storage**
  - Inside the user's folder, verify if non-text-based data (i.e., screenshot image files) are being uploaded.



[Back to Top](#top)

