# Apache Log Parser and Data Normalization Application
ApacheLogs2MySQL consists of two Python Modules & one MySQL Schema designed to automate importing Apache Access & Error Log files into a normalized database schema for reporting & data analysis. 

Application runs on Windows, Linux and MacOS & tested with MySQL versions 8.0.39, 8.4.3, 9.0.0 & 9.1.0.

For hassle-free installation follow `INSTALL.md` step by step. Install information is also in `README.md` for reference but `INSTALL.md` a concise list of installation steps. 

## MySQL view logs totalled by Browsers
MySQL View - apache_logs.access_log_browser_list - data from LogFormat: extended
![view-access_useragent_browser_list](https://github.com/user-attachments/assets/1550daf7-e591-47c4-a70a-cb4fc5fdefd9)
## Application Description
ApacheLogs2MySQL processes the 3 standard Apache Access Logformats - vhost_combined, combined and common

This is a fast, reliable processing application with detailed event-logging and two-staged data conversation. Data manipulation can be fine tuned in second conversion stage if required for customizing LogFormats. Log-levels can be set to capture every process step, info messages and errors of the import process from log file to schema import_log table.

The logging functionality, database design and table relationship contraints produce both physical integrity and logical integrity. This enables a complete audit trail providing the ability to determine when, where and what file each record originated from.

There is no need to move log files either. Log files can be left in the folder they were imported from for later referencing. The application knows what files have been processed. This application will run with no need for user interaction.

All folder pathnames, filename patterns, logging, MySQL connection settings are in .env file for easy installation and maintenance. The folder polling Python module runs great in PM2 daemon process manager for 24/7 online processing.

Python handles polling of log file folders and executing MySQL Database LOAD DATA statements, Stored Procedures & Functions and SQL Statements. Python drives the application but MySQL does all Data Manipulation & Processing.

For Auditability logging of messages, events and errors of processes on client and server is extremely important. This application has both a client and server module. The client module can be run on multiple computers in different locations feeding a single server module.

Application is developed with Python 3.12, MySQL and 4 Python modules. Modules are listed with Python Package Index link, install command for each platform & GitHub Repository link.
## Required Python Modules
Python module links & install command lines for each platform. Single quotes around module name are required on macOS. The simplest option is run the command line under '4. Python Steps'. If that works you are all set. The `requirements.txt` is included in repository.
|Python Package|Windows 10 & 11|Ubuntu 24.04|macOS 15.0.1 Darwin 24.0.0|GitHub Repository|
|--------------|---------------|------------|--------------------------|-----------------|
|[PyMySQL](https://pypi.org/project/PyMySQL/)|python -m pip install PyMySQL[rsa]|sudo apt-get install python3-pymysql|python3 -m pip install 'PyMySQL[rsa]'|[PyMySQL/PyMySQL](https://github.com/PyMySQL/PyMySQL)|
|[user-agents](https://pypi.org/project/user-agents/)|pip install pyyaml ua-parser user-agents|sudo apt-get install python3-user-agents|python3 -m pip install user-agents|[selwin/python-user-agents](https://github.com/selwin/python-user-agents)|
|[watchdog](https://pypi.org/project/watchdog/)|pip install watchdog|sudo apt-get install python3-watchdog|python3 -m pip install watchdog|[gorakhargosh/watchdog](https://github.com/gorakhargosh/watchdog/tree/master)|
|[python-dotenv](https://pypi.org/project/python-dotenv/)|pip install python-dotenv|sudo apt-get install python3-dotenv|python3 -m pip install python-dotenv|[theskumar/python-dotenv](https://github.com/theskumar/python-dotenv)|
## Required MySQL Server Settings
MySQL server must be configured in `my.ini`, `mysqld.cnf` or `my.cnf` depending on platform with following: 
```
[mysqld]
local-infile=1
```
## Supported Log Formats
Apache uses the same Standard Access log formats on all 3 platforms.
```
LogFormat "%v:%p %h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" vhost_combined
```
```
LogFormat "%h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" combined
```
```
LogFormat "%h %l %u %t \"%r\" %>s %O" common
```
Application is designed to use this extended format in Apache configuration to get more information from your servers.
```
LogFormat "\"%h\",%t,%I,%O,%S,%B,%{ms}T,%D,%^FB,%>s,\"%H\",\"%m\",\"%U\",\"%{Referer}i\",\"%{User-Agent}i\",\"%{farmwork.app}C\",%v" extended
```
The application also processes Error Logs with default format for threaded MPMs (Multi-Processing Modules). If you're running Apache 2.4 on any platform and ErrorLogFormat is not defined in config files this is the Error Log format.
```
ErrorLogFormat "[%{u}t] [%-m:%l] [pid %P:tid %T] %7F: %E: [client\ %a] %M% ,\ referer\ %{Referer}i"
```
## Installation Instructions
The steps are very important to make installation painless. Please follow in the order instructions are listed.

### 1. MySQL Steps
Before running `apachLogs2MySQL.sql` open file in your favorite editor and do a ***Find and Replace*** of the following User Account with a User Account with DBA Role on server you are installing on. This will make everything much easier. Copy below:
```
root`@`%`
```
The easiest way to install is use MySQL Command Line Client. Login as User with DBA Role and execute the following:
```
source yourpath/apacheLogs2MySQL.sql
```
MySQL server must be configured in `my.ini`, `mysqld.cnf` or `my.cnf` depending on platform: 
```
[mysqld]
local-infile=1
```
After these 3 steps MySQL server should be good to go.

### 2. Settings.env steps
First rename the settings.env file to .env

By default the load_dotenv() is looking for a file name .env which is standard name for setting files. The file is loaded in both the apacheLogs2MySQL.py and watch4files.py with the following line of code:
```
load_dotenv()  # Loads variables from .env into the environment
```
Windows requires double backslash:
```
C:\\Users\\farmf\\Documents\\apacheLogs\\
```
Lunix & macOS require single frontslash:
```
/home/will/apacheLogs/
```
Below is settings.env with default settings for running on my Windows 11 Pro workstation.
### 3. Settings.env Variables
```
MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_USER=root
MYSQL_PASSWORD=password
MYSQL_SCHEMA=apache_logs
WATCH_PATH=C:\\Users\\farmf\\Documents\\apacheLogs\\
WATCH_RECURSIVE=1
WATCH_INTERVAL=15
ERROR=1
ERROR_LOG=2
ERROR_PATH=C:\\Users\\farmf\\Documents\\apacheLogs\\**/*error*.*
ERROR_RECURSIVE=1
ERROR_PROCESS=1
COMBINED=1
COMBINED_LOG=2
COMBINED_PATH=C:\\Users\\farmf\\Documents\\apacheLogs\\combined\\**/*access*.*
COMBINED_RECURSIVE=1
COMBINED_PROCESS=1
VHOST=1
VHOST_LOG=2
VHOST_PATH=C:\\Users\\farmf\\Documents\\apacheLogs\\vhost\\**/*access*.*
VHOST_RECURSIVE=1
VHOST_PROCESS=1
EXTENDED=1
EXTENDED_LOG=2
EXTENDED_PATH=C:\\Users\\farmf\\Documents\\apacheLogs\\extended\\**/*access*.*
EXTENDED_RECURSIVE=1
EXTENDED_PROCESS=1
USERAGENT=1
USERAGENT_LOG=2
USERAGENT_PROCESS=1
```
### 4. Python Steps
Install all modules:
```
pip install -r requirements.txt
```
macOS platform may require installation of pip.
```
xcode-select --install
python3 -m ensurepip --upgrade 
```
### 5. Run Application
If MySQL steps completed successfully, renamed file `settings.env` to `.env`, updated variables for MySQL server connection and log folders and successfully installed Python modules it is time to run application. If you have log files in the folders already run the apacheLogs2MySQL.py directly. It will process all the logs in all the folders. If you have empty folders and want to drop files into folders run the watch4logs.py. Once you get all logs processed & get a better understanding of application use PM2 to run application 24/7 waiting to process files on arrival.

Run import process directly:
```
python apacheLogs2MySQL.py
```
Run polling module:
```
python watch4logs.py
```
Run polling module from PM2:
```
pm2 start watch4logs.py
```
## Database Normalization
Database normalization is the process of organizing data in a relational database to improve data integrity and reduce redundancy. Normalization ensures that data is organized in a way that makes sense for the data model and attributes, and that the database functions efficiently.

View Data images are from 2 views in the apache_logs schema. Database normalization at work. There are 35 more schema views.
## MySQL view logs totalled by URIs
MySQL View - apache_logs.access_log_requri_list - data from LogFormat: extended
![view-access_requri_list](https://github.com/user-attachments/assets/7cf9ff89-a1d7-4e93-ae93-deeca87175f9)
## Error Log Views
MySQL Error Views - most of the verbiage above is about Access Logs. The application also does the same normalization with error logs. These are many of the views in apache_logs Schema. The error log attribute is the name of the first column. Each attribute has associated table in apache_logs Schema.
![Screenshot 2024-10-26 164911](https://github.com/user-attachments/assets/11094e41-9897-44ab-8c23-e8b75cb5916f)
![Screenshot 2024-10-26 164842](https://github.com/user-attachments/assets/c1fcfb1a-2c45-4525-80ce-11702b0c609a)
![Screenshot 2024-10-26 164449](https://github.com/user-attachments/assets/9bcf7ffe-c72f-43cb-8011-2cdf2978934a)
![Screenshot 2024-10-26 164517](https://github.com/user-attachments/assets/b624d139-3d9f-4184-a63c-b3c70df6d53c)
![Screenshot 2024-10-26 164645](https://github.com/user-attachments/assets/ec15619a-900d-4036-a7b4-fe610777d65d)
![Screenshot 2024-10-26 164714](https://github.com/user-attachments/assets/caaac761-730e-4ccf-8a43-0ef40be7b164)
![Screenshot 2024-10-26 164741](https://github.com/user-attachments/assets/7ab48d24-1d24-4733-ab57-e76654a28e14)
![Screenshot 2024-10-26 164805](https://github.com/user-attachments/assets/d8fae147-69f2-4995-b800-f8c8bf14308e)
![Screenshot 2024-10-26 164828](https://github.com/user-attachments/assets/485d24ea-2c34-4c01-8452-bd43e0993aab)

## Schema Objects - Tables, Views, Store Procedures and Stored Functions
Images of the apache_logs schema objects. Access and Error log attributes have been broken into separate entity tables. Each table populated with the unique values of the entity. Entity Relationship Diagram will be posted soon.

![alt text](<Screenshot 2024-11-04 142923.png>) ![alt text](<Screenshot 2024-11-04 142851.png>) ![alt text](<Screenshot 2024-11-04 142957.png>)
