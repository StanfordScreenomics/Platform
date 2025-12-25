
## 1.03. Users

### 1.03.1. Username

Every user of the app, when signing up, registers with:
* **Code**: A sequence of letters and/or numbers identifying which study group the user is in
* **Number**: A number identifying this specific user, to help record who this actually is
* **StudyID**: A study name
* **Password**: A password users must know in order to register successfully (so that only
onboarded participants can participate). It needs to be configured during the app compilation process.

In the database, special characters (such as the @ sign if there is any in the StudyID or group Code). So,
all usernames are condensed into **[code][number][studyid]**, with spaces and special characters
omitted, and converted to lowercase. An example is "**int101studyone**" if **Code = INT, Number = 101, and StudyID = StudyOne**.

---

### 1.03.2. User Specs

When a user first creates their account, the Screenomics app's "Specs" module records some basic specs about their smartphone device. 
These are as follows:
* **`android-version`**: The specific version of the Android operating system that is running on the device at the time the specs were reported
* **`brand`**: The company that manufactures the device
* **`display-id`**: A random internal ID
* **`fingerprint`**: A different random internal ID
* **`manufacturer`**: Also the company that manufactures the device
* **`model`**: The precise model number of the device
* **`product`**: The name of the type of device
* **`time`**: The time the specs were reported, in GMT time `YYYYMMDDHHMMSSsss`
* **`time-local`**: The time the specs were reported, in the user device’s system time `YYYYMMDDHHMMSSsss`

The specs are recorded as entries in the Firestore database under `users` - `specs` collection. 

---

### 1.03.3. User Directory

To enable researchers to communicate with users, the Screenomics app stores each user's email address in a dedicated directory named `user_directory` in the Firestore database when they first create an account. This allows the Screenomics Dashboard app to access the email address.

