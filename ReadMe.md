## ![salesforce](https://developer.salesforce.com/assets/svg/salesforce-cloud.svg) Salesforce Data Loader Command Line
- **Step 1:** Create the encryption key
- **Step 2:** Create the encrypted password for your login username
- **Step 3:** Create the Field Mapping File
- **Step 4:** Create a process-conf.xml file that contains the import configuration settings
- **Step 5:** Run the process and import the data

#### Step 1: Create the Encryption Key File
| - | Command            |
| - | ------------------ |
| 1.1 | Click Start **>** Run, enter **cmd** in the Open field, and click **OK**. |
| 1.2 | Enter **cd \\** to navigate to the root directory of the drive where Data Loader is installed. |
| 1.3 | Navigate to folder **C:\Users\{userName}\dataloader\version\bin** |
| 1.4 | Create an encryption key file by entering the following command: **encrypt.bat -k** |

#### Step 2: Create the Encrypted Password
| - | Command            |
| - | ------------------ |
| 2.1 | Enter the command: **encrypt.bat –e \<password> \<key file path>** |

#### Step 3: Create the Field Mapping File
| - | Command            |
| - | ------------------ |
| 3.1 | Copy the text file and save it with a name of **accountInsertMap.sdl**\. |
~~~
#Mapping values
#Thu May 26 16:19:33 GMT 2011
Name=Name
NumberOfEmployees=NumberOfEmployees
Industry=Industry
~~~
 
#### Step 4: Create the Configuration File
| - | Command            |
| - | ------------------ |
| 4.1 | Make a copy of the process-conf.xml file from the \samples\conf directory. Be sure to maintain a copy of the original because it contains examples of other types of Data Loader processing such as upserts and exports. |
| 4.2 | Open the file in a text editor, and replace the contents with the following XML: |

~~~xml
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN//EN" "http://www.springframework.org/dtd/spring-beans.dtd">
<beans>
    <bean id="accountInsert" class="com.salesforce.dataloader.process.ProcessRunner" scope="prototype">
        <description>accountInsert job gets the account record from the CSV file and inserts it into Salesforce.</description>
        <property name="name" value="accountInsert"/>
        <property name="configOverrideMap">
            <map>
                <entry key="sfdc.debugMessages" value="true"/>
                <entry key="sfdc.debugMessagesFile" value="C:\DLTest\Log\accountInsertSoapTrace.log"/>
                <entry key="sfdc.endpoint" value="https://servername.salesforce.com"/>
                <entry key="sfdc.username" value="admin@Org.org"/>
                <!--Password below has been encrypted using key file, 
                    therefore, it will not work without the key setting: 
                    process.encryptionKeyFile.
                    The password is not a valid encrypted value, 
                    please generate the real value using the encrypt.bat utility -->
                <entry key="sfdc.password" value="e8a68b73992a7a54"/>
                <entry key="process.encryptionKeyFile" value="c:\Users\{user}\.dataloader\dataLoader.key"/>
                <entry key="sfdc.timeoutSecs" value="600"/>
                <entry key="sfdc.loadBatchSize" value="200"/>
                <entry key="sfdc.entity" value="Account"/>
                <entry key="process.operation" value="insert"/>
                <entry key="process.mappingFile" value="C:\DLTest\Command Line\Config\accountInsertMap.sdl"/>
                <entry key="dataAccess.name" value="C:\DLTest\In\insertAccounts.csv"/>
                <entry key="process.outputSuccess" value="c:\DLTest\Log\accountInsert_success.csv"/>
                <entry key="process.outputError" value="c:\DLTest\Log\accountInsert_error.csv"/>
                <entry key="dataAccess.type" value="csvRead"/>
                <entry key="process.initialLastRunDate" value="2005-12-01T00:00:00.000-0800"/>
            </map>
        </property>
    </bean>
</beans>
~~~

| - | Command            |
| - | ------------------ |
| 4.3 | Modify the following parameters in the process-conf.xml file: |
- sfdc.endpoint—Enter the URL of the Salesforce instance for your organization; for example, https://yourInstance.salesforce.com/.
- sfdc.username—Enter the username Data Loader uses to log in.
- sfdc.password—Enter the encrypted password value that you created in step 2.
- process.mappingFile—Enter the path and file name of the mapping file.
- dataAccess.Name—Enter the path and file name of the data file that contains the accounts that you want to import.
- sfdc.debugMessages—Currently set to true for troubleshooting. Set to false after your import is up and running.
- sfdc.debugMessagesFile—Enter the path and file name of the command line log file.
- process.outputSuccess—Enter the path and file name of the success log file.
- process.outputError—Enter the path and file name of the error log file.

#### Step 5: Import the Data
| - | Command            |
| - | ------------------ |
| 5.1 | Copy the account data to a file name **accountInsert.csv** |
| 5.2 | In the command prompt window, enter the following command: **process.bat "\<file path to process-conf.xml>" \<process name>**
