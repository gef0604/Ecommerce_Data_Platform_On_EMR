# Data Platform - Spark on EMR
Built a data platform on Amazon EMR. Coding and testing locally, then deply the application on EMR cluster. Completed user sessions analysis, product analysis, convertion rate analysis and advertising traffic analysis. Finished the data visualization by Tableau. Below is display for parts of archivements.

 ![step](https://github.com/gef0604/Ecommerce_Data_Platform_On_EMR/blob/master/images/new%20dash.png)
 
## Technologies
| Name | Usage |
| --- | --- |
| Spark| To build ETL process and archive real-time analysis|
| Hadoop ecosystem | To store the raw data and complete offline calculation|
| Amazon Kineses | Produce real-time data and transit the data to Spark Streaming | 
| Amazon S3 | Storage solution for data platform |
| Amazon Redshift | Data warehouse |
| Tableau | Data visualization |
| Scala | To write spark applications |
 
## Important Configurations
### Amazon EMR
- Launch a spark cluster which consists of three nodes.
- Configure SSH for the master EC2 instance

### Amazon Kinesis
- Launch a Kineses Stream of 2 shards
- Using Kineses Data Generator to generate sessions data

### Configure the pom.xml of Maven 
- Import dependencies of Spark, Kinesis Data Generator and others.

## Requirements
- User Sessions Analysys
    - Stride length and step length analysys
    - Extracting sample sessions randomly
    - Top10 categories calculation
    - Active sessions for popular categories
    
- Convertion Rate Analysis
    - Convertion rate calculation for each page.
    
- Product Analysis
    - Top10 items.
    - Top5 items for each region.
    
- Advertising Traffic Counts
    - Dynamic black-list analysis
    - Advertising traffic for each city
    - Top5 advertisements for each province
    - Traffic count for the past one hour

## Code Explanation 
### Module - Mock
To generate mock data for offline analysis and real-time calculation.
- MockDataGeneragte.scala: To generate user sessions data, structure of data is as below:
        [user_id: Long,
        session_id: String,
        action_time: String,
        search_keyword: String,
        click_category_id: Long,
        click_product_id: Long
        order_category_ids: String
        pay_category_ids: String,
        pay_product_ids: String,
        city_id: Long]
        
- MockRealTimeData.scala
To generate mock data for advertisement traffic, the structure is as below:
        [timestamp: String,
        province: Long,
        city: Long,
        ad_id: Long,
        user_id: Long
        ]
        
### Module - Common
Maintain some consistent variable, util classes, and configuration parameters.
- conf: Maintain the configuration parameters.
- constant: Maintain the constant variables.
- Model: Define the model of data stored in MySQL.
- pool: Provide object pools.
- utils: Util classes, to provide functions to deal with String, timestamp, etc.

## Module - analyze
A module consisting of analyzing funcitons developed by Scala.
- session
    - UserVisitSessionAnalyze.scala: Functions being used to analyze the raw session data.
    - DataModel.scala: To define a temporary data model.
    - SessionAggrAccumulator: To define a customized accumulator, which is used to maintain the proportion of each step length and visit length.
    - CategorySortKey.scala: Being used to decide the priority of categorirs. It'd be used to decide which categories are popular.
    
- page
    - PageOneStepConvertRate.scala: A function being used to calculate the convertion rate for each page.
    - DataModel.scala: Define a tempopary data model(taskid: String, convertionRate: String)
    
- product
    - AreaTop3ProductApp.scala: A function to figure out the top3 products for each region.
    - GroupConcatDistinctUDAF.scala: To define a UDAF function for hive, which figure out the top3 items group by regions.
    - DataModel.scala: To define a temporary data model for RDD.
    
- Advertising
    - AdClickRealTimeStat.scala: A application which consists of multiple functions, including filtering sessions according to the black-list, detecting the unsafe visiting and updating the black-list, calculating the daily top3 advertisements for each privince, and real-time time calculation for the advertising traffic count for the last one hour.
    
    - JdbcHelper: Configure the connection to Mysql, which maintains the black-list.
    - DataModel: Define the case classes for the statment of advertisement, the top3 advertisements for each province, and the trend of clicks on advertisements for the past one hour. 
    
