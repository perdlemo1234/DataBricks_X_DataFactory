# DataBricks_X_DataFactory ( Mainly Databricks ) 

## _Overview / Context:_
This is a documentation of my journey implementing a medallion architecture where it can logically and neatly arranges data within a data lakehouse. At the time of writing the documentation, I've completed the implementation within DataBricks. However, Hans' student credit has expired and currently has no access to Databricks to run any sort of code. Luckily, all python code and markdown text are saved within Hans' computer. Technically, implementing a medallion structure within ADF is possible _(through the use of Azure Functions)_ but is less flexible compared to Databricks. Also, databricks are scalable too , making it easier when there's a slight change within the pipeline. 

## _Stages of the Medallion Architecture explained:_  
### **Outline:**
###### This architecture is organised into a series of data layers, each serving a specific purpose in handling the transformation and cleansing of the data. Essentially, at each stage, data is made more organised and keep tracks of when each record is updated and how it's being updated. Implemented for modern data lakes and data platforms to facilitate data management & analytics. Link explaning the medallion architecture -> (https://www.databricks.com/glossary/medallion-architecture#:~:text=The%20Gold%20layer%20is%20for,quality%20rules%20are%20applied%20here )
![image](https://github.com/user-attachments/assets/214dee16-58bc-4ecb-b35b-85e0425c0c81) ( Without the inclusion of the Landing Zone )
---
### _Landing Zone (optional):_  
###### 
- Data first lands in the landing zone, which often is a temporary storage area ingested from different sources.
- It is essentially where raw data from various sources first arrives and is initially stored.
- Is stored in it's original, unmodified form
- Landing Zone may not be necessary but it can be very helpful when the pipeline goes through 2-3 layers compared to only 1 layer ( e.g. bronze layer )

**Examples of Landing Zone:**
- **_Cloud Storage Bucket:_** An Amazon S3 bucket or Azure Blob Storage where raw data files ( e.g. CSVs, JSONs) from different sources are uploaded. 
- **_File System Directory:_** Collect log files or data dumps from various applications. 
---
### _Bronze Layer:_
![image](https://github.com/user-attachments/assets/63ca2655-9715-4c22-afd0-f6394878a68f)
### The Bronze Layer here has these following notable points: 
- The foundational layer for raw unprocessed data ingested from different source system or from the landing zone.
- Here, data is in its raw form but organised in a structured manner, most likely in directory format.  
- Commonly used formats include CSV, JSON, Parquet, Avro.

Since I was starting from scratch, I artifically created a number of bronze csv files , seperated them into different directories based on distinct topics ( E.G. sales & product )  

![image](https://github.com/user-attachments/assets/fc32731d-dfd9-4bf4-8c5a-f2942d244b32)
###### From the picture, we can point out a few important things from my experimentation:
- The Bronze directory can be split into different categories tackling different business challenges/problems ( e.g. sales , product , health_care ) 
- Continuing the breadcrumb trail, directories are split into "ingestion_daes" like _( 20240710 , 20240715 , 20240717 )_ . Note that the naming convention is now ONLY dates and not date_time. 
- Within each "ingestion_date" directory, multiple .csv files are created based on certain ingestion time intervals The CSV files . By logic, all .csv files should have their "date_time" greater than the title of the directory they belong to. However, sometimes, the system or manual input might make an input mistake and may want to include a file which originates from an earlier date. So, technially, files from any "date_time" can be included ; however, if majority of the files are from an "earlier date" , it might be worth investigating further.   
---
### _Config Folder:_
###### The config folder serves as: 
- Centralising various settings required for data pipelines , processing jobs or applications
- Different environment may require different settings. The config allows us to manage these settings in a controlled manner.
- Change record of data when date is later than the "offset_date". "offset_date" is then updated accordingly. 

However, this was created from scratch and Rnd ; so , the tables _"offset_date"_ and _"table"_ are included for simplicity:  

![image](https://github.com/user-attachments/assets/248ecfa0-899e-472c-abe9-0cd756f3a5a1)
---
### _Silver Layer:_
![image](https://github.com/user-attachments/assets/cff9a073-6427-4527-9541-8fc16f7ce9cc)
### The Silver Layer here has these following notable points: 
- Intial basic cleaning/transformation occurs
- Involves deduplication, correcting errors , and handling missing values.
- Transformed into a more structured layer. Might involve converting data-types, standardising formats, and applying basic transformations to make data more usable.
- Data should be stored under a Parquet format -> _Website that explains the advantages of Parquet format_ ( https://www.databricks.com/glossary/what-is-parquet#:~:text=Parquet%20is%20optimized%20to%20work,therefore%20greatly%20minimizing%20the%20IO. )

In the silver layer, the files are stored within directories based on subject categories. The files must be read through code because they are stored in a Parquet format. 

#### _How would the silver file look like?_
Sadly, there's not a picture that I can put because parquet format can be viewed under a .csv format when code is executed. _(no credits left)_ Through the Rnd, the expected silver parquet file output should be consolidating all available records and also updating records which has been modified after the offset date. 

###### Further details to be discussed within the medallion procedure:
----
### _Gold Layer:_
![image](https://github.com/user-attachments/assets/1eda5d0c-c477-4495-b8aa-3e94ad740772)  
### The Gold Layer here has these following notable points: 
- Has high qualty and purposeful data which has undergone extensive cleaning, transformation, and aggregation.
- Typically very structured and is ready for consumption by analysts using software like Power BI to complete Predictive Modelling , Machine Learning, and Data Mining.

###### Typically, this involves combining columns from different datasets or using a simple arithemtic calculation to obtain a brand new column. Further details will be discussed within  
---
## _Different Types of Load:_
_Full Load / Destructive Load:_  
![image](https://github.com/user-attachments/assets/b0dd11ec-ecf8-4519-8f24-05f8ef06aa90)
Either called full or destructive load. The full load in ETL involves truncating the target table before loading ALL data from source to target table. Hence, it being called a destructive load. Truncating involves removing _**ALL records**_ from the table in a database, but it does not affect the schema ; essentially, leaving a blank skeletion / structure. This type of load is very straightforward and can be easily implemented. 

_Differential/Incremental/Fractional Load:_  
![image](https://github.com/user-attachments/assets/8c32e280-4b79-4f98-b1be-86a012abde7a)
As the name suggests, only a portion of the data from the target table is updated. Why do we do this? In my experimentation, I compared the offset date from config file with modified date from ALL bronze source file. If "modified_date" is greater than "offset_date" ; then, that specific record will be replaced within 

_Imagine this Scenario:_
- We have millions of records in the entire table, but we only need a select few of records to be updated 
- Also, the window of opportunity to upload might be limited . File is very huge so it is not possible to reload everything at once.
- Hence, we need to figure out which records
    _#1)_ Needs an Update_
    **&**
    _#2) Can be inserted from the source table as a fresh/new record_
- This is when Incremental Load comes into place. 
---
**How do we know when to use which load?**   
_Full Load:_
![image](https://github.com/user-attachments/assets/4cacda52-ff53-4d22-b469-5f37f432a8dd) <-- ( Databricks function created to carry out full load )
- When setting up a new data system or data warehouse, full load can be more straightforward & efficient as it doesn't require tracking
- If small volumes of data, then it can be more straightforward & effiicent because it doesn't require tracking changes.
- When consistency is a very important aspect of the transformation.

_Incremental Load:_
![image](https://github.com/user-attachments/assets/bd64acd6-a86f-45ae-b61b-bec57089a400) <-- ( Databricks function created to carry out incremental load )
- Optimal for datasets with vast amount of data - full loads wold be impractial... **_( Too much Time and Effort! )_**
- Ideal for environments with frequent data changes or updates.
- Lower impact on system performance during loading time.
---
## _Medallion Procedure ( in Databricks ) - Basically, what's the logic behind the code?:_  
Upon explaining the unique layers and loading methods, we can finally employ , test , and verify data transformations at each major data layer. 

#### _Getting directories by date (excluding time):_
![image](https://github.com/user-attachments/assets/63a2de86-5d40-4e0c-a1a2-eaaffdeb6130) <- "get_directories_by_date" function 
This function seperates the directory paths into 2 arrays. One where the dates are before the _offset date,_ and the other is after the _offset date._ 

So, the function returns arrays of directory paths called "later_directories" , "not_later_directories" and "offset_date_path"

Thought it might seem quite pointless now, the seperation of directories allows us to effectively pinpoint which directories to focus on / save resources finding the latest file. 

#### _Bronze -> Silver:_
##### Below consists a rough outline of how my experimented transformation would look like if there's a **full load** : 
1) Load Bronze .csv files  ( from landing zone )
2) Use the ( "get_directories_by_date" ) function to obtain the array ( "later_directories" ) which contains file paths of the directories past the "offset_date".
3) From "later_directories" array, obtain the **latest** "date" directory. From **latest** "date" directory, choose **latest** "date_time" file for full loading.
4) Perform "full_load" using the "full_load" function. _( Additional details to be discussed later on )_



5) Read offset date from offset file from a specific category.
6) Compare offsetdate with modified date from source file.
7) If modified date is greater than offset date, we update that record into silver dataset. If a specific record is new; then, insert that as a new record into the silver table.
8) Perform deduplication.
9) To verify the accuracy of transformation, load silver parquet format into dataframe and view it as a .csv file. 

[ Reunderstand the code ]
[Check code and comment approriately]
[Give my specific example and give appropriate and elaborate analysis of the code 
[Comment why there's no bronze->silver function]

#### _Silver -> Gold:_  


## _Small technical things to take note of:_


