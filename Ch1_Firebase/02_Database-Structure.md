
## 1.02. Database Structure


### 1.02.1. Firestore Database
This section mirrors the hierarchical structure of the actual Firestore database's organization where text-based data is stored. The numbers in parentheses indicate the section number in this document that explain the corresponding database entry in more detail.

```
Project
    - install
        - [timestamp]
        - [timestamp]
        - [...]
    - settings_profiles
        - _default_
            * [setting]
            * [...]
        - [XXX]
            * [setting]
            * [...]
    - ticker
        - [username]
            * event: [timestamp]
            * event: [...]
    - users
        - [username]
            - events
                * [event]
                * [...]
            - settings
                * [setting]
                * [...]
            - specs
                * [spec]
                * [...]
        - [...]
    - user_directory
        - [username]
            * email: [email]
        - [...]
```
---

### 1.02.2. Google Cloud Storage
Google Cloud Storage where non-text-based data is stored in user-specific folders named by username. Each non-text-based file is saved with a file name that includes the username and timestamp.

```
Project
    - [username]
        * file: [username_timestamp]
        * file: [...]
    - [...]
```


[Back to Top](#top)





