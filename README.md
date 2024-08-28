# DataBricks_X_DataFactory ( Mainly Databricks ) 

## _Overview / Context:_
This is a documentation of my journey implementing a medallion architecture where it can logically and neatly arranges data within a data lakehouse. At the time of writing the documentation, I've completed the implementation within DataBricks. However, Hans' student credit has expired and currently has no access to Databricks to run any sort of code. Luckily, all python code and markdown text are saved within Hans' computer. Technically, implementing a medallion structure within ADF is possible _(through the use of Azure Functions)_ but is less flexible compared to Databricks. Also, databricks are scalable too , making it easier when there's a slight change within the pipeline. 

## _Stages of the Medallion Architecture explained:_  
### **Outline:**
###### This architecture is organised into a series of data layers, each serving a specific purpose in handling the transformation and cleansing of the data. Essentially, at each stage, data is made more organised and keep tracks of when each record is updated and how it's being updated. Implemented for modern data lakes and data platforms to facilitate data management & analytics.  
---
### _Landing Zone (optional):_  
###### 
- Data first lands in the landing zone, which often is a temporary storage area ingested from different sources.
- It is essentially where raw data from various sources first arrives and is initially stored.
- Is stored in it's original, unmodified form
- Landing Zone may not be necessary but it can be very helpful when the pipeline goes through 2-3 layers compared to only 1 layer ( e.g. bronze layer )

**Examples of Landing Zone:**
_Cloud Storage Bucket:_ An Amazon S3 bucket or Azure Blob Storage where raw data files ( e.g. CSVs, JSONs) from different sources are uploaded. 
_File System Directory:_ Collect log files or data dumps from various applications. 
---
### _Bronze Layer:_
![image](https://github.com/user-attachments/assets/63ca2655-9715-4c22-afd0-f6394878a68f)
###### The bronze layer serves as: 
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

### _Gold Layer:_

## _Different Types of Load:_
_Differential Load_

_Full Load_

## _Medallion Procedure ( in Databricks ) :_  

_Bronze -> Silver_

_Silver -> Gold_  


## _Small technical things to take note of:_

How many?? HAHA

