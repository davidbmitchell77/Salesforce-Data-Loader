## ![salesforce](https://developer.salesforce.com/assets/svg/salesforce-cloud.svg) Salesforce Data Loader Command Line
- **Step 1:** Create the encryption key
- **Step 2:** Create the encrypted password for your login username
- **Step 3:** Create the Field Mapping file
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
| 4.1 | Make a copy of the process-conf.xml file from the \samples\conf directory. ***Be sure to maintain a copy of the original*** because it contains examples of other types of Data Loader processing such as upserts and exports. |
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
                <entry key="sfdc.password" value="e8a68b73992a7a54"/>
                <entry key="process.encryptionKeyFile" value="c:\Users\{user}\.dataloader\dataLoader.key"/>
                <entry key="sfdc.timeoutSecs" value="600"/>
                <entry key="sfdc.loadBatchSize" value="200"/>
                <entry key="sfdc.entity" value="Account"/>
                <entry key="process.operation" value="insert"/>
                <entry key="process.mappingFile" value="C:\DLTest\Command Line\Config\accountInsertMap.sdl"/>
                <entry key="dataAccess.name" value="C:\DLTest\In\insertAccounts.csv"/>
                <entry key="dataAccess.type" value="csvRead"/>
                <entry key="process.outputSuccess" value="c:\DLTest\Log\accountInsert_success.csv"/>
                <entry key="process.outputError" value="c:\DLTest\Log\accountInsert_error.csv"/>
                <entry key="process.initialLastRunDate" value="2005-12-01T00:00:00.000-0800"/>
            </map>
        </property>
    </bean>
</beans>
~~~

| - | Command            |
| - | ------------------ |
| 4.3 | Modify the following parameters in the **process-conf.xml file**: |
- **sfdc.endpoint** - Enter the URL of the Salesforce instance for your organization; for example, https://yourInstance.salesforce.com/.
- **sfdc.username** - Enter the username Data Loader uses to log in.
- **sfdc.password** - Enter the encrypted password value that you created in step 2.
- **process.encryptionKeyFile** - Enter the complete path to the dataloader.key file.
- **process.mappingFile** - Enter the path and file name of the mapping file.
- **dataAccess.Name** - Enter the path and file name of the data file that contains the accounts that you want to import.
- **sfdc.debugMessages** - Currently set to **true** for troubleshooting. Set to **false** after your import is up and running.
- **sfdc.debugMessagesFile** - Enter the path and file name of the command line log file.
- **process.outputSuccess** - Enter the path and file name of the success log file.
- **process.outputError** - Enter the path and file name of the error log file.

#### Step 5: Import the Data
| - | Command            |
| - | ------------------ |
| 5.1 | Copy the account data to a file named **accountInsert.csv** |
| 5.2 | In the command prompt window, enter the following command: **process.bat "\<file path to process-conf.xml>" \<process name>** |  


___  
  
## ![salesforce](https://developer.salesforce.com/assets/svg/salesforce-cloud.svg) Configure Database Access
When you run Data Loader in batch mode from the command line, use **\samples\conf\database-conf.xml** to configure database access objects, which you use to extract data directly from a database.

#### DatabaseConfig Bean
The top-level database configuration object is the DatabaseConfig bean, which has the following properties:

- **dataSource** - The bean that acts as database driver and authenticator. It must refer to an implementation of javax.sql.DataSource such as org.apache.commons.dbcp.BasicDataSource.
- **sqlConfig** - The SQL configuration bean for the data access object that interacts with a database.  

The following code is an example of a DatabaseConfig bean:
~~~xml
<bean id="updateAccountsPostgres" class="com.salesforce.dataloader.dao.database.DatabaseConfig" singleton="true">
    <property name="dataSource" ref="jdbcPostgreSQL"/>
    <property name="sqlConfig" ref="sqlUpdateAccounts"/>
</bean>
~~~
  
#### DataSource bean
The DataSource bean sets the physical information needed for database connections. It contains the following properties:

- **driverClassName** - The fully qualified name of the implementation of a JDBC driver.
- **url** - The string for physically connecting to the database.
- **username** - The username for logging in to the database.
- **password** - The password for logging in to the database.

Depending on your implementation, additional information may be required. For example, use org.apache.commons.dbcp.BasicDataSource when database connections are pooled.

The following code is an example of a DataSource bean:  
~~~xml
<bean id="jdbcPostgreSQL" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
    <property name="driverClassName" value="org.postgresql.Driver"/>
    <property name="url" value="jdbc:postgresql://hard-plum.db.elephantsql.com:5432/azitfggr"/>
    <property name="username" value="azitfggr"/>
    <property name="password" value="FN0-RM4LLseSOqH-jbbDaegW2ab1nnrJ"/>
</bean>
~~~
#### Note
Versions of Data Loader from API version 25.0 onwards do not come with an Oracle JDBC driver.  To add the Oracle JDBC driver to Data Loader:
- Download the latest JDBC driver from http://www.oracle.com/technetwork/database/features/jdbc/index-091264.html.
- Copy the JDBC .jar file to data loader install folder/java/bin.
- Update the process.bat file CLASSPATH (-cp) parameter by appendin a semi-colon and full path to the Oracle JDBC .jar file.

#### Data Access Objects ( \<entry key="dataAccess.type" value="csvRead"/>** )**
When running Data Loader in batch mode from the command line, several data access objects are supported. A data access object allows access to an external data source outside of Salesforce. They can implement a read interface (DataReader), a write interface (DataWriter), or both. See the following list of object names and descriptions:  

**csvRead**  
Allows the reading of a comma or tab-delimited file. There should be a header row at the top of the file that describes each column.  

**csvWrite**  
Allows writing to a comma-delimited file. A header row is added to the top of the file based on the column list provided by the caller.  

**databaseRead**  
Allows the reading of a database. Use database-conf.xml to configure database access.  

**databaseWrite**  
Allows writing to a database. Use database-conf.xml to configure database access.  

Data access objects are specified in the **dataAccess.type** property of the **process-conf.xml** file.

#### SQL Configuration bean
When running Data Loader in batch mode from the command line, the SqlConfig class contains configuration parameters for accessing specific data in the database. As shown in the code samples below, queries and inserts are different but very similar. The bean must be of type com.salesforce.dataloader.dao.database.SqlConfig and have the following properties:
- **sqlString** - The SQL code to be used by the data access object.  The SQL can contain replacement parameters that make the string dependent on configuration or operation variables. Replacement parameters must be delimited on both sides by “@” characters. For example, @process.lastRunDate@.
- **sqlParams** - A property of type map that contains descriptions of the replacement parameters specified in sqlString. Each entry represents one replacement parameter: the key is the replacement parameter's name, the value is the fully qualified Java type to be used when the parameter is set on the SQL statement. Note that “java.sql” types are sometimes required, such as java.sql.Date instead of java.util.Date. For more information, see the official JDBC API documentation.
- **columnNames** - Used when queries (SELECT statements) return a JDBC ResultSet. Contains column names for the data outputted by executing the SQL. The column names are used to access and return the output to the caller of the DataReader interface.
  
The following is an example of an **update** SQL configuration bean:
~~~xml
<bean id="sqlUpdateAccounts" class="com.salesforce.dataloader.dao.database.SqlConfig" singleton="true">
    <property name="sqlString">
        <value>
            UPDATE public."Accounts" SET
              "Name" = @Name@,
              "CreatedDate" = to_timestamp(@CreatedDate@, 'YYYY-MM-DDTHH:MI:SS.MSZ'),
              "CreatedBy" = @CreatedBy@,
              "LastModifiedDate" = to_timestamp(@LastModifiedDate@, 'YYYY-MM-DDTHH:MI:SS.MSZ'),
              "LastModifiedBy" = @LastModifiedBy@,
              "BillingCity" = @BillingCity@,
              "BillingState" = @BillingState@
              WHERE "Id" = @Id@;
        </value>
    </property>
    <property name="sqlParams">
        <map>
            <entry key="Id"               value="java.lang.String"/>
            <entry key="Name"             value="java.lang.String"/>
            <entry key="CreatedDate"      value="java.lang.Date"/>
            <entry key="CreatedBy"        value="java.lang.String"/>
            <entry key="LastModifiedDate" value="java.lang.Date"/>
            <entry key="LastModifiedBy"   value="java.lang.String"/>
            <entry key="BillingCity"      value="java.lang.String"/>
            <entry key="BillingState"     value="java.lang.String"/>
        </map>
    </property>
</bean>
~~~
The following is an exmple of an **export** SQL Configuration bean:
~~~xml
<bean id="queryProductsSQL" class="com.salesforce.dataloader.dao.database.SqlConfig" singleton="true">
    <property name="sqlString">
        <value>
            SELECT "Id" AS "ProductId",
                   "Name",
                   "Category",
                   "Packaging",
                   "UPCProductCode",
                   "UnitPrice",
                   "Active"
              FROM public."Products"
             WHERE "Category" LIKE @process.productCategory@
               AND "LastModifiedDate" >= (DATE(@process.lastRunDate@) - 7)
        </value>
    </property>
    <property name="columnNames">
        <list>
            <value>ProductId</value>
            <value>Name</value>
            <value>Category</value>
            <value>Packaging</value>
            <value>UPCProductCode</value>
            <value>UnitPrice</value>
            <value>Active</value>
        </list>
    </property>
    <property name="sqlParams">
        <map>
            <entry key="process.productCategory" value="java.sql.String"/>
            <entry key="process.lastRunDate" value="java.sql.Timestamp"/>
        </map>
    </property>
</bean>
~~~
___  
## ![salesforce](https://developer.salesforce.com/assets/svg/salesforce-cloud.svg) Mapping Columns

When running Data Loader in batch mode from the command line, you must create a properties file that maps values between Salesforce and data access objects.
Create a new mapping file and give it an extension of .sdl.  Observe the following syntax:  
- On each line, pair a data source with its destination.
- In an import file, put the data source on the left, an equals sign (=) as a separator, and the destination on the right.
- In an export file, put the destination on the left, an equals sign (=) as a separator, and the data source on the right.
- Data sources can be either column names or constants. Surround constants with double quotation marks.
- You may map constants by surrounding them with double quotation marks.
- Specify the full path to the mapping file in the **process.mappingFile** property of the **process-conf.xml** file
  
Column mapping for data update/insert.  Salesforce fields are on the left.  PostgreSQL fields are on the right:
~~~
Id=Id
CreatedBy.Name=CreatedBy
CreatedDate=CreatedDate
LastModifiedBy.Name=LastModifiedBy
LastModifiedDate=LastModifiedDate
Name=Name
BillingCity=BillingCity
BillingState=BillingState
  ~~~
  
Column mapping for data export to .csv file.  Salesforce fields are on the left.  Column headers are on the right:
~~~
Id=account_number
Name=name
Phone=phone
~~~
  
Column mapping for Salesforce upsert.  Column headers are on the left.  Salesforce fields are on the right:
~~~
ProductId=ProductCode
Name=Name
Category=Family
Packaging=Packaging__c
UPCProductCode=Universal_Product_Code__c
UnitPrice=
Active=IsActive
~~~
___
## ![salesforce](https://developer.salesforce.com/assets/svg/salesforce-cloud.svg) Run Individual Batch Processes
To start an individual batch process, use **\bin\process.bat**. The command-line requires the following parameters.

- **Configuration directory** - The default is \conf.  To use an alternate directory, create a directory and add the following files to it:
    copy process-conf.xml from \samples\conf.
    copy database-conf.xml from \samples\conf.
    copy config.properties from \conf.
- **Process name** - The name of the ProcessRunner bean from \configs\process-conf.xml.
  
Sample batch data loader command:
~~~
process.bat ../configs accountInsert
~~~
