## 1.06. Install Codes

All users, upon opening the app for the first time, generate a unique install code that is stored on their device. These codes are also immediately uploaded to Firestore under the top-level `install` collection. Each entry is an association from a timestamp to an install code, and can be read as “the user with this install code first ran the app at this time.”

In general, this isn’t that helpful, and the `install` collection as well as the concept of install codes can mostly be ignored. 

However, its main use is that it provides a way to tell if users are installing the app but not completing the setup process. For such a user, an install code will still appear in the `install` collection. If they never created an account, the install code will not have a corresponding account. 

Users that DID complete the setup process report their install code in `CaptureStartupEvents`. So, in theory it’s possible to detect dropouts by cross-referencing the full list of install codes with `CaptureStartupEvents` that contain them. Install codes without a corresponding `CaptureStartupEvent` represent users who never fully got the app running. At this time, no such tool exists to complete this detective work, but who knows, maybe it will someday.
