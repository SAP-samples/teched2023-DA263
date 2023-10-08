SAP HANA Cloud is enriched with an Automated Machine Learning (AutoML) approach. AutoML can be helpful, for example, to give a data scientist a head-start into quickly finding a first machine learning model.

A machine learning model is a program that can find patterns or make decisions from a previously unseen dataset. This is made possible by first 'training' the model with a large dataset.
During training, the machine learning algorithm is optimized to find certain patterns or outputs from the dataset, depending on the task. The output of this process - often a computer program with specific rules and data structures - is called a *machine learning model*.

AutoML with SAP HANA Cloud is a great starting point to see what is possible with a dataset, and if it is worth to invest more time into a use case.

</br>

### Unique benefits with PAL (Predictive Analytics Library) AutoML Includes:

- Improved PAL models and business impact 
- Composite pipeline models of multiple PAL algorithms
- Automated algorithm comparison and selection as well as parameter search and optimal selection ML predictions of higher accuracy/value

</br>

### Productivity up-lift and expert experience

- Expert Data Scientists derive best models in less time/better utilization of compute time to derive best models
- Comparable AutoML expert experience and address trending/competitive capability gap and improve time to maximum value with PAL

</br>

![](./Images/DBX_AutoML/automl.png)

</br>  

For this section, a data set containing customer transactions as a table has already been loaded into the SAP HANA Cloud Database (**GX_TRANSACTIONS**). 

<!-- In this section, we look at a concrete example to see what is possible through this new AutoML approach.  -->

The challenge is to predict whether a transaction is fraudulent or not. Such use cases are often quite challenging due to imbalanced data and thus require different techniques before implementing a machine learning model.


------
### Try it out!

Start this lesson by downloading the following zipped [JupyterLab NoteBook](./Files/FraudDetection_AutoML.ipynb.zip). Save the file locally and extract the notebook file. This file will be uploaded into the JupyterLab environment later in the exercise.</br>

The data resides in the **{placeholder|userid}** schema, in table **GX_TRANSACTIONS**. Data is accessed from the Python environment directly in SAP HANA Cloud, and will leverage the native Auto Machine Learning capability in SAP HANA Cloud.

All steps in this exercise will be based on the embedded Python code in the Notebook.


### Logging into JupyterLab environment

1. Open the **JupyterLab URL**


    **[JupyterLab](https://vsystem.ingress.dh-0nrw3t6sj.dh-canary.shoot.live.k8s-hana.ondemand.com/app/jupyter)**

	<!-- **[JupyterLab](https://vsystem.ingress.dh-c509514j.dhaas-live.shoot.live.k8s-hana.ondemand.com/app/jupyter)** -->

2. Enter the following:

   Tenant: **xa-01**</br>
   Username: **{placeholder|userid}**</br>
   Password: **Provided by registration e-mail**

  </br>

  ![](./Images/DBX_AutoML/image003.png)

    
  ![](./Images/DBX_AutoML/image004.png)

3. Then click the **Sign In** button.

> The following steps will import the locally saved Notebook into JupyterLab.

4. In the JupyterLab launchpad screen, select the **File Browser** icon, then click on **Upload Files**.

![](./Images/DBX_AutoML/image005new.png)

5. Browse to the location where the file has been saved and select it then click **Open**.

<!---![](./Images/DBX_AutoML/image006.png)--->

6. The file will now appear in the file area. Double-click on the file to open it.

![](./Images/DBX_AutoML/image007new.png)

7. The Notebook is now ready!

![](./Images/DBX_AutoML/image008new.png)

</br>

### Analysis

1. The first step is to import and install the **SAP HANA Cloud ML Library**. Select the first cell and click the execute button.

![](./Images/DBX_AutoML/image009.png)

> **Tip:** Use the keyboard shortcut ***SHIFT+ENTER*** to execute the code cells throughout the rest of the Notebook.

2. The **hana_ml** library enables you to directly connect to your SAP HANA Cloud tenant. Use the connection details in the table below:

![](./Images/DBX_AutoML/image010.png)

<!-- ```
hana_address = '13b7c15d-848f-40b5-9259-c9c36ab85f56.hna1.prod-eu10.hanacloud.ondemand.com'
hana_port = 443
hana_user = 'AC42602U01'
hana_password = 'ObOXRxseos1!'
hana_encrypt = True #for HANA Cloud
``` -->

| **Input Type**    | **Values**                                                                 |
| ----------------- | -------------------------------------------------------------------------- |
| **Host name:**    | {placeholder|hc}}                                                          |
| **Host Port:**    | 443                                                                        |
| **Username:**     | {placeholder|userid}                                                       |
| **Password:**     | Provided during registration                                               |
| **HANA Encrypt:** | True                                                                       |

</br>

3. Create a data frame through SQL or table function and get the row count.


![](./Images/DBX_AutoML/image011.png)

4. Control data and convert the following variables accordingly.


![](./Images/DBX_AutoML/image012.png)

![](./Images/DBX_AutoML/image013.png)


5. Control the conversion and take a look at a short description of the data. 

![](./Images/DBX_AutoML/image014.png)


>**Note:** The target variable is called **Fraud**. In addition, there are eight predictors capturing different information from a transaction.

![](./Images/DBX_AutoML/image015.png)



> **Note:** Data types have been altered accordingly


6. Split the data into a training and testing set.


![](./Images/DBX_AutoML/image016.png)


7. Control the size of the training and testing datasets.

![](./Images/DBX_AutoML/image017.png)


8. Import the following dependencies for the Automatic Classification.

![](./Images/DBX_AutoML/image018.png)


9. Manage the workload in SAP HANA Cloud tenant by creating workload classes. Please execute the following SQL script to set the workload class, which will be used in the Automatic Classification.

![](./Images/DBX_AutoML/image019.png)

>**Note:** Ignore the error if the work class **PAL_AUTOML_WORKLOAD** already exists.

![](./Images/DBX_AutoML/image020.png)

</br>

### Run process

The **AutoML** approach automatically executes data processing, model fitting, comparison and optimization. 

First, create an **AutoML** classifier object **auto_c** in the following cell. It is helpful to review and set respective [AutoML configuration parameters](https://help.sap.com/docs/hana-cloud-database/sap-hana-cloud-sap-hana-database-predictive-analysis-library/automl-d1a54bc3a4664937b77e85a865754b17?locale=en-US)</br>

- The defined scenario will run two iterations of pipeline optimization. The total number of pipelines which will be evaluated is equal to **population_size + generations × offspring_size**. Hence, in this case this amounts to 15 pipelines.
- With **elite_number**, you specify how many of the best pipelines you want to compare.
Setting random_seed =1234 helps to get reproducable **AutoML** runs.


1. Set the maximum runtime for individual pipeline evaluations with the parameter **max_eval_time_mins** or determine if the AutoML shall stop if there are no improvement for the set number of generations with the early_stop parameter. Further, you can set specific performance measures for the optimization with the scoring parameter.

![](./Images/DBX_AutoML/image021.png)

![](./Images/DBX_AutoML/image022.png)

>**Important!** Change **\<YourName\>** to **{placeholder|userid}** in the **.format()** method.


2. Reinitialize and display the AutoML operators and their parameters.

![](./Images/DBX_AutoML/image023.png)

>**Note:** A default set of AutoML classification operators and parameters is provided as the global config-dict, which can be adjusted to the needs of the targeted AutoML scenario. Use methods like **update_config_dict, delete_config_dict, display_config_dic** to update the scenario definition.

3. Adjust some of the settings to narrow the searching space. As the resampling method choose the **SMOTETomek** method, since the data is imbalanced.


![](./Images/DBX_AutoML/image024.png)


4. Exclude the Transformer methods. As machine learning algorithms keep the Hybrid Gradient Boosting Tree and Multi Logistic Regression.

![](./Images/DBX_AutoML/image025.png)


5. Set some parameters for the optimization of the algorithms.

![](./Images/DBX_AutoML/image026.png)


6. Review the complete AutoML configuration for the classification.

![](./Images/DBX_AutoML/image027.png)


7. Fit the Auto ML scenario on the training data. It may take a couple of minutes. If it takes too long, exclude the **SMOTETomek** in the **resampler()** method of the config file.

![](./Images/DBX_AutoML/image028.png)

![](./Images/DBX_AutoML/image029.png)


8. Inspect the pipeline progress through the execution logs.

![](./Images/DBX_AutoML/image030.png)


9. Evaluate the best model on the testing data.

![](./Images/DBX_AutoML/image031.png)

10. Create predictions with your machine learning model.

![](./Images/DBX_AutoML/image032.png)


11. Save the best model in SAP HANA. Therefore, create a Model Storage. Change **'YourSchema'** in the code below to **{placeholder|userid}**. 

![](./Images/DBX_AutoML/image033.png)


12. Save the model through the following command.

![](./Images/DBX_AutoML/image034.png)

</br>

**Congratulations!!** This concludes the lesson on Automated Machine Learning in SAP HANA Cloud. 

For further information on AutoML with HANA Cloud, check out the following links:

[SAP HANA Cloud PAL AutoML Documentation](https://help.sap.com/docs/hana-cloud-database/sap-hana-cloud-sap-hana-database-predictive-analysis-library/automl-d1a54bc3a4664937b77e85a865754b17?locale=en-US)
</br>

[Python ML client for SAP HANA AutoML Reference Document](https://help.sap.com/doc/cd94b08fe2e041c2ba778374572ddba9/2023_2_QRC/en-US/pal/algorithms.html#auto-ml)
</br>

[Github Repository with Example Code](https://github.com/SAP-samples/hana-ml-samples/tree/main/Python-API/usecase-examples/sapcommunity-automl-examples)
















