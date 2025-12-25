## Screenomics: How to Prepare and Run Studies

The Screenomics framework enables researchers to capture and organize smartphone screen content, user interactions, and other behavioral data (e.g., step counts, location) over extended periods in participants’ natural environments. No programming expertise is required, but careful planning is essential for a successful study.

This guide is designed to help researchers who may not have a programming background understand what is required, how to set up the tools, and how to run a study using the framework.

In the following sections, we will cover:
1. **Backend infrastructure and compliance considerations** – How to ensure your data storage meets IRB and institutional requirements.
2. **Device selection and study design** – Choosing between participant-owned or study-issued phones, and selecting appropriate devices and operating systems.
3. **Setting up tools and requirements** – Installing Android Studio and preparing your computer for app compilation.
4. App compilation and configuration – Building the study and dashboard apps, configuring modules, sampling rates, and screenshot quality.
5. **Participants, data volume, and cost estimation** – Understanding storage needs, operational costs, and how to estimate them.
6. **App distribution** – Options for distributing the app safely to participants.
7. **Study timeline and planning** – Suggested workflow for preparing the study, obtaining approvals, and starting data collection.

---

### 1. Backend Infrastructure and Compliance Considerations
Before beginning a Screenomics study, the research team must carefully evaluate backend infrastructure and ensure compliance with institutional and regulatory standards. This step is critical to avoid delays in IRB approval and to guarantee secure handling of participant data. Below we break down the main considerations.

#### a. Institutional Backend Support and Storage Capabilities
- **Confirm storage capacity:** Screenomics studies can generate large amounts of data (e.g., screenshots, audio, and sensor streams). For example, one participant may generate up to 10 GB of data per day, which scales quickly with study size and duration. Your institution must have the capacity to securely store this data for the entire study and any retention period required by your IRB.  
- **Evaluate support resources:** Some universities provide centralized IT support for research studies, including managed cloud services or on-premise storage. If your institution offers this, reach out early to ensure their systems meet your study’s scale and security needs.  
- **Plan for long-term access:** Consider not only storage for active studies but also long-term archival of raw and processed data. IRBs often require data retention policies of several years.

#### b. Default Backend: Firebase
The Screenomics framework is built on **Google Firebase**, which provides a turnkey backend solution with three main components:  

> **Backend**: The backend refers to the server-side systems and services that manage authentication, databases, and data storage. 


- **Firebase Authentication**: Handles secure user login and identity management.  
- **Firestore Database**: Stores structured data, such as logs, metadata, or survey responses.  
- **Cloud Storage**: Stores large unstructured data files, such as screenshots or audio.  

All three services are **HIPAA-compliant** when configured properly and are automatically set up during app compilation. This means researchers without extensive technical expertise can quickly get a secure backend running with minimal configuration.

#### c. Alternative Backends (AWS, Azure, Local Servers)
Some institutions may prefer or require different backend providers:
- **AWS (Amazon Web Services)** or **Microsoft Azure** are common alternatives, often with existing enterprise agreements.  
- **Local institutional servers** may be mandated by certain IRBs for sensitive studies.  

**Important:** The Screenomics framework does not currently provide automatic setup for these alternatives. If researchers choose to use them, a software developer may be needed to:  
- Configure authentication and data storage manually.  
- Ensure secure transmission of data from the app to the chosen backend.  
- Replicate any analytics or workflows that Firebase handles automatically.  

#### d. IRB and Security Compliance
- **Confirm with your IRB:** Before beginning, explicitly verify that the chosen backend meets your IRB’s data security requirements (e.g., HIPAA, GDPR, institutional policies).  
- **Checklist for IRB approval:**  
  - Type of data collected (screenshots, text, GPS, audio).  
  - Where the data will be stored (Firebase, AWS, Azure, local servers).  
  - How the data will be encrypted (in transit and at rest).  
  - Who will have access to the data and how access is logged.  
  - Data retention and deletion policies.  
- **Legal and regulatory requirements:** Depending on your participant population and institution, you may need to meet HIPAA, GDPR, or local equivalents. Clarify this early to avoid delays.

#### e. Summary
- Screenomics relies on Firebase by default, which is HIPAA-compliant and automatically configured during app compilation.  
- Institutions may allow or require alternative backends (AWS, Azure, local servers), but these need manual setup by a developer.  
- Researchers must confirm with their IRB that the chosen backend complies with security and regulatory requirements, covering encryption, access control, and data retention.  
- Early planning of backend capacity, compliance, and institutional support is essential for smooth study execution.  

---

### 2. Device Selection

When preparing for a Screenomics study, careful device selection is critical to ensure data quality, participant compliance, and overall study feasibility. This section outlines considerations related to device compatibility, performance, participant-owned vs. study-issued devices, and associated trade-offs.

#### a. Device Compatibility
The Screenomics data collection app is designed to be broadly compatible with Android devices. For best performance, we recommend using devices running **Android 11 or higher**, as these versions provide better background task management, security patches, and developer support. While older versions may work, they can introduce unexpected performance issues or compatibility problems with certain APIs.

#### b. Recommended Devices
Although the app can technically run on any Android device, our experience shows that **Google Pixel** and **Samsung Galaxy** phones tend to be the most reliable for Screenomics research. These devices benefit from consistent hardware quality, faster software updates, and strong long-term support from the manufacturer. Choosing well-supported devices minimizes device-related variability and reduces the likelihood of encountering unexpected issues during a study.

#### c. Participant-Owned Devices
Allowing participants to use their **own smartphones** offers clear advantages. It reduces overall study costs, avoids the need to purchase and distribute hardware, and provides a more naturalistic measure of participants’ daily behavior since they continue using their personal apps and accounts. However, this approach introduces several challenges:
- **Hardware variability:** Different models may capture data differently (e.g., performance differences across devices).  
- **Operating system fragmentation:** Some participants may not have the latest Android version, leading to inconsistent app behavior.  
- **Personal app usage conflicts:** Other apps running in the background may interfere with data capture.  
- **Troubleshooting difficulties:** Technical support becomes harder when researchers do not control the device environment.  

Despite these challenges, participant-owned devices remain a viable option, especially for larger-scale or lower-budget studies.

#### d. Study-Issued Devices
An alternative approach is to provide **study-issued phones** to participants. This ensures:
- **Standardized data collection:** Every participant uses the same hardware and software environment, leading to consistent performance.  
- **Easier troubleshooting:** Researchers have complete control over the devices and can quickly replicate and resolve issues.  
- **Simplified onboarding:** Participants receive pre-configured devices with the app installed, reducing setup burden.  

However, study-issued devices also come with trade-offs:
- **Increased cost:** Institutions must purchase devices, potentially provide data plans, and handle device logistics.  
- **Participant adherence challenges:** Some participants may be reluctant to carry a second phone consistently.  
- **Logistical overhead:** Collecting, managing, and re-issuing devices across studies adds administrative complexity.  

#### e. Balancing Trade-Offs
Ultimately, the choice between participant-owned and study-issued devices depends on the **research priorities**:
- Studies prioritizing **ecological validity and cost-efficiency** may prefer participant-owned devices, accepting variability as part of the design.  
- Studies requiring **high data quality and controlled conditions** may lean toward study-issued devices despite higher costs.  

Researchers are encouraged to consider their **study budget, participant population, data quality requirements, and logistical capacity** before deciding on a device strategy.

---

### 3. Setting Up Tools and Requirements
Before building your study app with the Screenomics framework, you will need to install the necessary tools and prepare a suitable working environment. This section explains what you need, why it matters, and how to set up your system.


#### a. Accessing the Source Code
The Screenomics framework is openly available on: [https://github.com/iansulin/Stanford_Screenomics/blob/main/README.md  ](https://github.com/iansulin/Stanford_Screenomics/releases)
- **No GitHub account required:** You can download the code directly without creating or signing in to an account.  

Once downloaded, the framework provides the full source code required to compile a functioning study app.


#### b. Required Development Tool: Android Studio
The Screenomics app must be compiled using **[Android Studio](https://developer.android.com/studio)**, Google’s official development environment for Android apps.  

- **Why Android Studio?**  
  Android Studio includes all the necessary components (SDKs, build tools, emulators) and is the only officially supported way to generate an installable `.apk` file for the study app.  
- **Cross-platform compatibility:** Android Studio works on **Windows**, **macOS**, and **Linux**. This means researchers can use nearly any laptop or desktop computer without restrictions on operating system.  


#### c. Computer Specifications
Although the framework does not require specialized hardware, compiling Android apps and handling large study datasets can place significant demands on your system. For efficient performance, we recommend:  

- **Processor:** Modern quad-core CPU (Intel i5/Ryzen 5 or better)  
- **Memory:** Minimum 16 GB RAM (more helps with simultaneous app compilation and data analysis tasks)  
- **Storage:** SSD with at least 512 GB of available space for build files, datasets, and caching  
- **Graphics:** Dedicated GPU is not necessary but can speed up Android emulators and other visual tools  

---

### 4. App Compilation and Configuration


App compilation is the process of preparing the study’s data collection app and dashboard app, integrating backend services and finalizing the setup for participant use. The Screenomics framework is designed to simplify this process so that researchers can configure and compile an app without needing programming expertise.


#### a. Step-by-Step Guidance
- **Documentation & Tutorials**  
  Comprehensive step-by-step instructions are available on [Ch2_App-Compilation](https://github.com/iansulin/Stanford_Screenomics/tree/main/Ch2_App-Compilation).  
  - Written guides walk through the compilation process.  
  - Video tutorials are also provided for visual learners.  

- **No Programming Required**  
  All backend connections (e.g., Firebase Authentication, Firestore, Cloud Storage) are integrated automatically during compilation.  
  - Researchers select options through configuration files and build settings.  
  - Custom backend integrations (e.g., AWS, Azure) require additional development work, but the default Firebase setup is automatic.  

#### b. Researcher Configurations During Compilation
Compilation is not just about building the app; it is also the point at which key study parameters are finalized.  

1. **Data Collection Module Activations**  
   - Decide which modules (e.g., screenshots, step counts, GPS, accelerometer, audio samples) will be included.  
   - Each module can be toggled on or off depending on study design and IRB approval.  

2. **Sampling Rates**  
   - Define how often data is collected (e.g., screenshot every 5 seconds vs. every 30 seconds).  
   - Higher sampling frequency increases data richness but also raises storage costs and battery usage.  

3. **Screenshot Quality**  
   - Resolution and compression can be adjusted.  
   - Higher quality improves readability but requires more storage and bandwidth.  
   - Lower quality reduces costs and storage but may impact analytic utility.  


#### c. Impact on Study Operations
The compilation settings directly affect storage, performance, and cost. Researchers should carefully consider:  

- **Storage Requirements**  
  - Higher sampling + higher screenshot resolution = significantly larger storage needs.  
  - Cloud storage costs can scale rapidly; rough estimates should be calculated before finalizing settings.  

- **Battery Life & Participant Burden**  
  - Frequent data collection increases battery drain, which may frustrate participants.  
  - Balance between scientific value and participant usability is critical.  

- **Operational Costs**  
  - Firebase storage and Firestore document writes are billed per usage.  
  - Optimizing settings during compilation helps avoid unnecessary expenses.  


#### d. Summary
App compilation and configuration is the bridge between framework code and a functional study app.  
- No programming is needed, but careful study design decisions must be made at this stage.  
- Researchers finalize which modules to collect, how often to collect them, and at what quality.  
- These choices directly determine participant experience, backend costs, and the quality of research data.  


---

### 5. Participants, Data Volume, and Cost Estimation

App compilation prepares both the **study data collection app** and the **dashboard app**, integrating backend connections without requiring programming expertise.  
Step-by-step documentation (with videos) is available on GitHub to guide researchers through the process.  

#### a. Key Steps in Compilation
- **Finalize Data Modules**  
  Decide which data streams will be collected (e.g., screenshots, interactions, locations, step counts).  

- **Set Sampling Rates**  
  Configure how often each type of data is captured. Higher frequency provides finer resolution but increases storage and cost.  

- **Select Screenshot Quality**  
  Screenshots can be captured at full, medium, or low resolution. Lower resolution reduces costs but may limit analytic detail.  

- **Integrate Backend Connections**  
  Confirm cloud storage, authentication, and database configurations are properly linked before building the app.  

- **Export Compiled App**  
  Once configured, the app is compiled and ready for deployment to participants’ devices.  


#### b. Cost Considerations

Study costs are primarily driven by:
- **Screenshot Storage**: Image resolution and frequency have the largest effect.  
- **Firestore Writes**: Each logged event (e.g., screen state, metadata, sensor reading) generates a document write.  
- **Cloud Storage Pricing**: Based on average GB-months stored.  
- **Network Egress Costs**: Downloading/exporting the dataset incurs additional fees.

#### c. Example Cost Breakdown Scenarios

The following table illustrates approximate costs under different study designs, assuming Google Cloud **pay-as-you-go pricing** (as of August 2025).  

| Scenario | Participants | Duration | Data Collected (per participant per day) | Total Data (GB) | Average Stored Volume (GB-months) | Storage Cost ($) | Firestore Cost (Text + Ops) | Download Cost ($0.12/GB) | Total Est. Cost ($) |
|----------|--------------|----------|-------------------------------------------|-----------------|----------------------------------|------------------|-----------------------------|---------------------------|---------------------|
| **Baseline (default settings)** | 20 | 4 weeks (28 days) | ~10 GB screenshots + 2M docs | ~5,600 GB (5.6 TB) | ~2,800 GB (~2.73 TB-months) | $56–$73 | ~$2,200–$2,250 | ~$670 | **~$3,000** |
| **Reduced Quality & Sampling** | 20 | 4 weeks (28 days) | ~2.5 GB screenshots + 1M docs | ~1,400 GB (1.4 TB) | ~700 GB (~0.68 TB-months) | $14–$18 | ~$500–$550 | ~$170 | **~$750–$800** |
| **Larger Cohort** | 50 | 2 weeks (14 days) | ~10 GB screenshots + 2M docs | ~7,000 GB (7 TB) | ~3,500 GB (~3.41 TB-months) | $70–$90 | ~$2,800–$3,000 | ~$840 | **~$4,000** |
| **Single Participant Pilot** | 1 | 2 weeks (14 days) | ~10 GB screenshots + 2M docs | ~140 GB | ~70 GB-months | $1.40–$1.80 | ~$20 | ~$17 | **~$40** |

**Notes:**  
- Costs are approximate and depend on resolution, sampling rates, and Google Cloud’s current pricing.  
- Firestore costs are dominated by write operations, not storage.
- Using compressed image formats or longer sampling intervals significantly reduces costs.
- The “Total Est. Cost” column reflects data collection costs only and does not include download/transfer charges or long-term storage fees.
- Additional charges would apply if data are exported, transferred, or stored beyond the study period.
- Download costs apply if the full dataset is exported out of the cloud.


**Tips:**
- Reduce screenshot quality or extend sampling intervals to lower costs
- Use the [Google Cloud Pricing Calculator](https://cloud.google.com/products/calculator) for custom estimates
- Some institutions provide **free or subsidized cloud storage** for research studies, often with defined monthly limits (e.g., 1 TB/month).
  - Researchers can work with institutional IT or cloud administrators to maximize these resources.  

---

### 6. App Distribution

After the study app is compiled, researchers must decide how to distribute it to participants. There are two primary approaches: direct APK distribution and Google Play Store release. Each method has advantages and considerations.


#### a. Direct APK Distribution
- Allows **immediate installation** of the app on participants’ devices.  
- Requires participants to **enable installation from unknown sources**, which may raise **security concerns**.  
- Useful for **small pilot studies** or internal testing where immediate access is needed.  


#### b. Google Play Store Release
- Provides a **familiar installation process** for participants.  
- Supports **automatic updates** and is suitable for **larger or regulated studies**.  
- **Requirements for distribution:**  
  - A **Google Play Developer Account** ([Sign up here](https://play.google.com/console/about/))  
  - A detailed app listing including **title, description, icon, and screenshots** ([App content & store listing guide](https://developer.android.com/distribute/best-practices/launch/launch-checklist))  
  - A **public privacy policy** describing data collection, storage, and security practices ([Privacy policy guidance](https://support.google.com/googleplay/android-developer/answer/113469#privacy))  
  - Justification for any **sensitive permissions** (e.g., screenshots, location tracking) ([Permissions guide](https://developer.android.com/training/permissions/overview))  
  - Specification of the **target audience**, particularly if minors are involved ([Target audience guidance](https://support.google.com/googleplay/android-developer/answer/9157803))  
  - IRB approval documentation may be requested during review.  


- **Study-specific security:**  
  - Researchers can define a **study-specific password** to ensure only authorized participants access the app.  
  - Reviewers may request login credentials as part of the Play Store approval process.  

- **Timeline:**  
  - The Play Store review process can take **several weeks**, so plan accordingly.  
  - Once approved, participant recruitment and app deployment can begin.  


#### c. Summary
- **Direct APK distribution** is fast but less secure.  
- **Google Play distribution** is secure, scalable, and user-friendly, but requires preparation and review.  
- Researchers should select the method that balances **study size, security, and regulatory compliance**.


**Google Play Store Submission Checklist**

| Item | Description | Resources / Notes |
|------|-------------|-----------------|
| Developer Account | Register a Google Play Developer Account | [Sign up](https://play.google.com/console/about/) |
| App Listing | Title, description, icon, and screenshots | [App content guide](https://developer.android.com/distribute/best-practices/launch/launch-checklist) |
| Privacy Policy | Public document explaining data collection, storage, and security | [Privacy policy guidance](https://support.google.com/googleplay/android-developer/answer/113469#privacy) |
| Permissions Justification | Explain why sensitive permissions are needed (screenshots, location, etc.) | [Permissions guide](https://developer.android.com/training/permissions/overview) |
| Target Audience | Define app users, specify if minors are included | [Target audience guide](https://support.google.com/googleplay/android-developer/answer/9157803) |
| IRB Documentation | Provide approval documents if required | Institution-specific |
| Study-specific Password | Restrict access to authorized participants only | Configure in app settings |
| Testing & QA | Verify app functionality, backend connections, and security | Internal testing recommended |
| Timeline Planning | Account for review process (typically several weeks) | Schedule recruitment and deployment accordingly |


**Additional Resources:**  
- [Google Play Console Overview](https://play.google.com/console/about/)  
- [Launching Checklist for Android Apps](https://developer.android.com/distribute/best-practices/launch/launch-checklist)  
- [Google Play App Privacy & Permissions](https://support.google.com/googleplay/android-developer/answer/113469)  

---

### 7. Study Timeline and Planning

Careful planning is essential to ensure that a Screenomics study proceeds smoothly, from backend setup to participant recruitment. The study timeline typically involves several key phases:

#### a. Backend and Compliance Confirmation
- Verify that the institution can provide necessary backend support and storage.  
- Ensure that all chosen systems comply with **IRB and security standards**.  
- Confirm cloud service configurations (Firebase or alternative) are properly integrated.


#### b. Study Design Planning
- Define **participant inclusion criteria** (e.g., age range, device type).  
- Decide on **data collection modules** and sampling rates.  
- Determine **data quality standards** and **cost estimates** for storage and operations.  

#### c. IRB Preparation
- Prepare **IRB-ready materials**, including:  
  - Informed consent forms  
  - Privacy policies  
  - Withdrawal procedures  
  - Any relevant legal or regulatory documents  
- In the U.S., **IRB approval typically takes 2–3 months**.  

#### d. App Customization and Compilation
- During IRB review, the app can be:  
  - Configured with selected modules and sampling rates  
  - Compiled and tested for functionality and stability  
- Ensure that backend connections, permissions, and security measures are properly set.

#### e. Google Play Store Review
- After IRB approval, submit the app and supporting documentation to the Play Store.  
- Account for **several weeks** of review time.  
- Ensure all **submission requirements** (developer account, privacy policy, permissions, etc.) are met.  

#### f. Participant Recruitment and Data Collection
- Following Play Store approval, begin recruiting participants.  
- Data collection can continue for the desired **study duration**.  
- Monitor data quality and system performance throughout the study.

#### g. Summary
- Early preparation for IRB and Play Store approvals is crucial to avoid delays.  
- Parallel app customization during the approval period allows for efficient timeline management.  
- A structured timeline ensures smooth deployment, data collection, and compliance.




[Back to Top](#top)

