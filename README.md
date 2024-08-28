# DataBricks_X_DataFactory ( Mainly Databricks ) 

## _Overview / Context:_
This is a documentation of my journey implementing a medallion architecture where it can logically and neatly arranges data within a data lakehouse. At the time of writing the documentation, I've completed the implementation within DataBricks. However, Hans' student credit has expired and currently has no access to Databricks to run any sort of code. Luckily, all python code and markdown text are saved within Hans' computer. Technically, implementing a medallion structure within ADF is possible _(through the use of Azure Functions)_ but is less flexible compared to Databricks. Also, databricks are scalable too , making it easier when there's a slight change within the pipeline. 

## _Stages of the Medallion Architecture explained:_  
### **Outline:**
This architecture is organised into a series of data layers, each serving a specific purpose in handling the transformation and cleansing of the data. Essentially, at each stage, data is made more organised and keep tracks of when each record is updated and how it's being updated. Implemented for modern data lakes and data platforms to facilitate data management & analytics.  

### _Landing Zone (optional):_  
###### 
- Data first lands in the landing zone, which often is a temporary storage area ingested from different sources.
- It is essentially where raw data from various sources first arrives and is initially stored.
- Is stored in it's original, unmodified form
- Landing Zone may not be necessary but it can be very helpful when the pipeline goes through 2-3 layers compared to only 1 layer ( e.g. bronze layer )

**Examples of Landing Zone:**
_Cloud Storage Bucket:_ An Amazon S3 bucket or Azure Blob Storage where raw data files ( e.g. CSVs, JSONs) from different sources are uploaded. 
_File System Directory:_ Collect log files or data dumps from various applications. 

### _Bronze Layer:_
![image](https://github.com/user-attachments/assets/63ca2655-9715-4c22-afd0-f6394878a68f)
###### The bronze layer serves as: 
- The foundational layer for raw unprocessed data ingested from different source system or from the landing zone.
- Here, data is in its raw form but organised in a structured manner, most likely in directory format.  
- Commonly used formats include CSV, JSON, Parquet, Avro.

Since I was starting from scratch, I artifically created a number of bronze csv files , seperated them into different directories based on distinct topics ( E.G. sales & product )
![image](https://github.com/user-attachments/assets/8613b92a-689d-4572-b12f-5ae707406a61)



_[ insert image of formatting ] and explain what I need to look out for? ]_

### _Config Folder:_

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

