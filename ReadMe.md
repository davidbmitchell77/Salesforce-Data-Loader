## ![salesforce](https://developer.salesforce.com/assets/svg/salesforce-cloud.svg) Data Loader Command Line Quick Start
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
