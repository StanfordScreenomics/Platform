## 2.05. App Publication

Understanding app publication is essential for ensuring that your Android app reaches users efficiently and securely. The process involves generating either a **Signed APK** or a **Signed App Bundle (AAB)**, both of which verify the authenticity of your app and enable distribution through the Google Play Store or other platforms. Choosing the right format impacts security, performance optimization, and update management. While a Signed APK is directly installable and can be shared manually, a Signed App Bundle is required for Google Play distribution, allowing Play Store to optimize delivery for different devices. Below is a comparison of the two formats:

| Feature | Signed APK (Android Package Kit) | Signed App Bundle (AAB) |
|---|---|----|
| **Format** | Installable APK file | Publishing format, not directly installable |
| **Size Optimization** | Contains all resources, making it larger | Google Play optimizes and delivers only necessary resources |
| **Distribution** | Can be shared manually or via third-party stores | Must be uploaded to Google Play for processing |
| **Usage** | Used for direct installs and testing | Required for Google Play Store since August 2021 |
| **Automatic Updates** | Developer handles multi-device optimization | Google Play optimizes delivery for different devices |
| **Signing Key Management** | Developer manages signing keys manually | Supports Google Play App Signing for security |

**Which One Should You Use?**
* For manual distribution or testing → Use Signed APK
* For Google Play Store → Use Signed App Bundle (AAB)

**Before you begin:**
* A video instruction file is available here: [05_App-Publication_Video.mp4](../Ch2_App-Compilation/05_App-Publication_Video.mp4)  
  → Click the link above, then press the **Download** button (top-right) to save the video.

---

### 2.05.1. Creating a Keystore 

a. Create a Keystore
  - In Android Studio, go to Build in the top menu > select "**Generate Signed Bundle / APK**."
  - In the dialog that appears, select **APK** and click **Next**.
  - Click on the "**Create new....**" button.
  - Fill in the required fields
    - **Key store path**: Choose a location to save your keystore file.
    - **Password**: Set a password for the keystore.
    - **Key alias**: Enter a name (alias) for your key.
    - **Key password**: Set a password for the key (can be the same as the keystore password).
    - **Validity (years)**: Set for how many years the key will be valid (e.g., 25).
    - **Certificate information**: Fill in your name, organization, and other details.
  - Click **OK** to create and save the keystore.

> A **keystore** is a file that contains cryptographic keys used to sign your Android app, which is essential for distribution on the Google Play Store. It includes a private key for signing and a public certificate for identification. The keystore ensures app integrity, verifies the developer's identity, and facilitates updates.
>
> **If you lose your keystore, you cannot update your app on the Play Store, as all updates must be signed with the same key. Keeping the private key secure is crucial to prevent unauthorized access**. While you can generate a new keystore anytime, doing so creates a new identity for your app. Users would need to uninstall the old version and install the new one, risking data loss. **Once an app is published with a particular key, you cannot change it without consequences.**

---

### 2.05.2. Building a Signed APK

a. **Generate the Signed APK**
  - Select Your **Keystore**
    - In the `Generate Signed APK` dialog, select the keystore file you just created.
    - Enter the keystore password and the key alias/password you set earlier.
  - Select Build Variants
    - Choose the build variant (e.g., **release**) from the dropdown.
    - Click **Next**, review the settings, and then click **Finish**.
  - Locate the APK
    - Once the build is complete, a dialog box will appear in the bottom-right corner of Android Studio.
    - Click **Locate** to open the folder where the APK is saved.
      - Alternatively, manually navigate to the directory: `app/build/outputs/apk/release/`

---

### 2.05.3. Building a Signed App Bundle

a. **Generate the Signed App Bundle**
  - In Android Studio, go to Build in the top menu > select "**Generate Signed Bundle / APK**."
  - In the dialog that appears, select **Android App Bundle** and click **Next**.
  - Select Your **Keystore**
    - In the `Generate Signed APK` dialog, select the keystore file you just created.
    - Enter the keystore password and the key alias/password you set earlier.
  - Select Build Variants
    - Choose the build variant (e.g., **release**) from the dropdown.
    - Click **Next**, review the settings, and then click **Finish**.
  - Locate the App Bundle
    - Once the build is complete, a dialog box will appear in the bottom-right corner of Android Studio.
    - Click **Locate** to open the folder where the app bundle is saved.
      - Alternatively, manually navigate to the directory: `app/build/outputs/bundle/release/`




[Back to Top](#top)
