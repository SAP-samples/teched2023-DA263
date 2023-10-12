# The SAP HANA Cloud guided experience is an introductory tutorial experience for all users. The following modules can be completed in sequence or individually

- In-Memory Column Tables
- Data Tiering
- Multi-Model - Spatial
- Multi-Model - Document Store
- Multi-Model - Graph
- Multi-Model - AutoML Introduction

<!---**Important!** For all hands-on topics, please refer to the registration email received for details on accounts, passwords, and the date account expires. -->

</br>

 **Please Note:**

- For all hands-on topics, refer to the registration email for details of accounts, passwords, and the account expiration date.

- This exercise is for learning, non-productive use only. All data and objects associated with your account are not available after the account expires.

- After completing the *Getting Started* module, the other modules can be explored in any sequence.

<!--- **Note** the hands-on in this lesson is compulsory in order to explore any of the SAP HANA Cloud features in this workshop. All features are independent and can be explored in any sequence.--->

</br>

## Configure the Environment

<!---Before starting the lessons, the working environment must first be set up and configured. Get started by logging in to SAP Business Application Studio, then clone the project that will be used during the rest of the guided experience.--->

For the workshop we are using so called HANA Deployment Infrastructure Container also known as HDI-Containers. This will allow all participants to work in the same isolated environment (HDI container) without interfering with other participants.
Each of the modules within this guided experience use the same set of tables. These tables contain some generic transactional sales data including details of customers, products, employees as well as review and location data.
The tables and data will be deployed to the database in this step.
There are 2 tools we toggle between

- SAP Business Application Studio (*BAS*) which the browser version of Visual Studio Code (VSC)

- Database Explorer (*DBX*) for SAP HANA Cloud

The SAP Business Application Studio is a web-based tool to allow creating content for HDI and building application. It is a general purpose tool used widely in SAP also for CAP, No-LowCode, Fiori, mobile...
The SAP HANA database explorer is a web-based tool for browsing and working with SAP HANA database objects such as tables, views, functions, stored procedures. In addition, use DBX to import and export data, execute SQL statements, create remote sources, work with multi-model data such as graph, spatial and JSON collections, debug SQLScript, view trace files, and any other SQL activity.

</br>

### Cloning our project from GIT

1. Open the **[SAP Business Application Studio](https://da263-pj0569xc.ap11cf.applicationstudio.cloud.sap/index.html)**

    1. Defining your Workspace for SAP Business Application Studio ![Define BAS](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/GETTING_STARTED/BAS_1_1_0_BAS_START.png)
    2. Starting your Workspace for SAP Business Application Studio ![Start BAS](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/GETTING_STARTED/BAS_1_1_1_BAS_DEFINE.png)

2. Empty Busines Application Studio
![Empty BAS](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/GETTING_STARTED/BAS_1_2_BAS_EMPTY.png)

3. Cloning Git into BAS
![Cloning Git into BAS](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/GETTING_STARTED/BAS_3_CLONE.png)

Cloning name is **<https://github.com/SAP-samples/teched2023-DA263.git>**

4. Read to clone
![Read to clone](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/GETTING_STARTED/BAS_4_CLONE_NAME.png)

5. Arrange the panel
![Arrange the panel](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/GETTING_STARTED/BAS_5_PROJECT.gif)

6. Connect to Cloud Foundry (CF login)
![CF login](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/GETTING_STARTED/BAS_6_CF_LOGIN.gif)

7. Create your HDI container
![HDI](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/GETTING_STARTED/BAS_7_HDI_CREATE.gif)

8. Connect your HDI container
![HDI](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/GETTING_STARTED/BAS_8_OPEN_DBX.png)

9. Connect to your (empty) HANA Database Explorer
![DBX](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/GETTING_STARTED/BAS_9_EMPTY_DBX.png)

10. Deploy Content to your HDI container
![HDI](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/GETTING_STARTED/BAS_90_BAS_DEPLOY.png)

11. Use your DBX to view the tables and data
![HDI](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/GETTING_STARTED/BAS_91_DBX_TABLES.png)

</br>

**Congratulations!** The data and tools for the exercises are now ready for use. Please keep the BAS and the DB Explorer window open as you continue through the exercises.
