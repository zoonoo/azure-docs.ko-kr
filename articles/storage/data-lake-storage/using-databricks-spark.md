---
title: Azure Databricks에서 Spark를 사용하여 Azure Data Lake Storage Gen2 미리 보기 데이터에 액세스 | Microsoft Docs
description: Azure Databricks 클러스터에서 Spark 쿼리를 실행하여 Azure Data Lake Storage Gen2 저장소 계정의 데이터에 액세스하는 방법을 알아봅니다.
services: hdinsight,storage
tags: azure-portal
author: dineshm
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 6/27/2018
ms.author: dineshm
ms.openlocfilehash: 4f01352f13b7a831791c7e74520cc08b98afa661
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521458"
---
# <a name="tutorial-access-azure-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>자습서: Azure Databricks에서 Spark를 사용하여 Azure Data Lake Storage Gen2 미리 보기 데이터에 액세스

이 자습서에서는 Azure Databricks 클러스터에서 Spark 쿼리를 실행하여 Azure Data Lake Storage Gen2 미리 보기 지원 계정의 데이터를 쿼리하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Databricks 클러스터 만들기
> * 저장소 계정으로 비구조적 데이터 수집
> * Azure Function을 트리거하여 데이터 처리
> * Blob 저장소의 데이터에 대한 분석 실행

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 [미국 운수부](https://transtats.bts.gov/Tables.asp?DB_ID=120&DB_Name=Airline%20On-Time%20Performance%20Data&DB_Short_Name=On-Time)에서 사용할 수 있는 항공사 비행 데이터를 사용하고 쿼리하는 방법을 보여 줍니다. 2년 이상의 항공사 데이터를 다운로드하고(모든 필드 선택) 컴퓨터에 결과를 저장합니다. 다운로드한 파일의 이름과 경로를 적어 둡니다. 이 정보는 이후의 단계에서 필요합니다.

> [!NOTE]
> **미리 압축된 파일** 확인란을 클릭하여 모든 데이터 필드를 선택합니다. 다운로드는 상당한 크기의 기가바이트 분량이지만, 분석에는 이 정도의 데이터가 필요합니다.

## <a name="create-an-azure-data-lake-storage-gen2-account"></a>Azure Data Lake Storage Gen2 계정 만들기

시작하려면 새 [Azure Data Lake Storage Gen2 계정](quickstart-create-account.md)을 만들고 고유한 이름을 지정합니다. 그런 다음, 저장소 계정으로 이동하여 구성 설정을 검색합니다.

> [!IMPORTANT]
> 미리 보기로 있는 동안 Azure Functions는 단일 구조 네임스페이스로 만든 Azure Data Lake Storage Gen2 계정에서만 작동합니다.

1. **설정** 아래에서 **액세스 키**를 클릭합니다.
3. **key1** 옆의 **복사** 단추를 클릭하여 키 값을 복사합니다.

계정 이름과 키는 모두 이 자습서의 이후 단계에서 필요합니다. 텍스트 편집기를 열고 나중에 참조할 수 있도록 계정 이름과 키를 별도로 보관합니다.

## <a name="create-a-databricks-cluster"></a>Databricks 클러스터 만들기

다음 단계는 데이터 작업 영역을 만들기 위해 [Databricks 클러스터](https://docs.azuredatabricks.net/)를 만듭니다.

1. [Databricks 서비스](https://ms.portal.azure.com/#create/Microsoft.Databricks)를 만들고 이름을 **myFlightDataService**로 지정합니다(서비스를 만들 때 *대시보드에 고정* 확인란을 선택함).
2. **작업 영역 시작**을 클릭하여 새 브라우저 창에서 작업 영역을 엽니다.
3. 왼쪽 탐색 모음에서 **클러스터**를 클릭합니다.
4. **클러스터 만들기**를 클릭합니다.
5. *클러스터 이름* 필드에서 **myFlightDataCluster**를 입력합니다.
6. *작업자 유형* 필드에서 **Standard_D8s_v3**을 선택합니다.
7. **최소 작업자 수** 값을 *4*로 변경합니다.
8. 페이지 위쪽에서 **클러스터 만들기**을 클릭합니다(이 프로세스를 완료하는 데 최대 5분이 걸릴 수 있음).
9. 프로세스가 완료되면 탐색 모음의 왼쪽 위에서 **Azure Databricks**를 선택합니다.
10. 페이지 아래쪽의 중간에 있는 **새로 만들기** 섹션 아래에서 **Notebook**을 선택합니다.
11. **이름** 필드에 원하는 이름을 입력하고 **Python**을 언어로 선택합니다.
12. 다른 모든 필드는 기본값으로 그대로 유지할 수 있습니다.
13. **만들기**를 선택합니다.
14. **Cmd 1** 셀에 다음 코드를 붙여넣고 저장소 계정에서 보관한 값으로 바꿉니다.

    ```bash
    spark.conf.set("fs.azure.account.key.<account_name>.dfs.core.windows.net", "<account_key>") 
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
    dbutils.fs.ls("abfs://<file_system>@<account_name>.dfs.core.windows.net/")
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
    ```

## <a name="ingest-data"></a>데이터 수집

### <a name="copy-source-data-into-the-storage-account"></a>저장소 계정에 원본 데이터 복사

다음 작업은 AzCopy를 사용하여 *.csv* 파일의 데이터를 Azure 저장소에 복사하는 것입니다. 명령 프롬프트 창을 열고 다음 명령을 입력합니다. `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>` 및 `<ACCOUNT_KEY>` 자리 표시자는 이전 단계에서 별도로 설정한 해당 값으로 바꿔야 합니다.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Databricks Notebook을 사용하여 CSV를 Parquet로 변환

브라우저에서 Databricks를 다시 열고 다음 단계를 실행합니다.

1. 탐색 모음의 왼쪽 위에서 **Azure Databricks**를 선택합니다.
2. 페이지 아래쪽의 중간에 있는 **새로 만들기** 섹션 아래에서 **Notebook**을 선택합니다.
3. **이름** 필드에서 **CSV2Parquet**를 입력합니다.
4. 다른 모든 필드는 기본값으로 그대로 유지할 수 있습니다.
5. **만들기**를 선택합니다.
6. 다음 코드를 **Cmd 1** 셀에 붙여넣습니다(이 코드는 편집기에서 자동 저장됨).

    ```python
    #mount Azure Blob Storage as an HDFS file system to your databricks cluster
    #you need to specify a storage account and container to connect to. 
    #use a SAS token or an account key to connect to Blob Storage.  
    accountname = "<insert account name>' 
    accountkey = " <insert account key>'
    fullname = "fs.azure.account.key." +accountname+ ".blob.core.windows.net"
    accountsource = "abfs://dbricks@" +accountname+ ".blob.core.windows.net/folder1"
    #create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/On_Time_On_Time*.csv")
    #read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet(accountsource + '/parquet/flights')

    #read the flight details parquet file 
    #flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")
    print("Done")
    ```

## <a name="explore-data-using-hadoop-distributed-file-system"></a>Hadoop 분산 파일 시스템을 사용하여 데이터 탐색

Databricks 작업 영역으로 돌아가서 왼쪽 탐색 모음에서 **최근** 아이콘을 클릭합니다.

1. **비행 데이터 분석** 노트북을 클릭합니다.
2. **Ctrl+Alt+N**을 눌러 새 셀을 만듭니다.

다음 코드 블록을 각각 **Cmd 1**에 입력하고, **Cmd+Enter**를 눌러 Python 스크립트를 실행합니다.

AzCopy를 통해 업로드된 CSV 파일의 목록을 가져오려면 다음 스크립트를 실행합니다.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"
dbutils.fs.ls(source + "/temp")
display(dbutils.fs.ls(source + "/temp/"))
```

새 파일을 만들고 *parquet/flights* 폴더에 파일을 나열하려면 다음 스크립트를 실행합니다.

```python
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"

dbutils.fs.help()

dbutils.fs.put(source + "/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls(source + "/temp/parquet/flights")
```
이러한 코드 샘플을 통해 Azure Data Lake Storage Gen2 지원 계정에 저장된 데이터를 사용하여 HDFS의 계층적 특성을 살펴보았습니다.

## <a name="query-the-data"></a>데이터 쿼리

다음으로, Azure Data Lake Storage에 업로드한 데이터를 쿼리할 수 있습니다. 다음 코드 블록을 각각 **Cmd 1**에 입력하고, **Cmd+Enter**를 눌러 Python 스크립트를 실행합니다.

### <a name="simple-queries"></a>단순 쿼리

데이터 원본에 대한 데이터 프레임을 만들려면 다음 스크립트를 실행합니다.

> [!IMPORTANT]
> **<YOUR_CSV_FILE_NAME>** 자리 표시자는 이 자습서의 시작 부분에서 다운로드한 파일 이름으로 바꿔야 합니다.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet(accountsource + '/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created via the Azure Function
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferrably run this in a separate cmd cell
display(flightDF)
```

데이터에 대한 분석 쿼리를 실행하려면 다음 스크립트를 실행합니다.

```python
#Run each of these queries, preferrably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet(accountsource + '/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet(accountsource + '/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Carrier) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```
### <a name="complex-queries"></a>복합 쿼리

더 복잡한 다음 쿼리를 실행하려면 노트북에서 각 세그먼트를 한 번에 실행하고 결과를 검사합니다.

```python
#find the airline with the most flights

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")

#show the top row without truncation
output.show(1, False)

#show the top 10 airlines
output.show(10, False)

#Determine which is the least on time airline

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE DepDelay>60 or ArrDelay>60 group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("select * from v")
#output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")
#show the top row without truncation
output.show(1, False)

#which airline improved its performance
#find the airline with the most improvement in delays
#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v1")
spark.sql("DROP VIEW IF EXISTS v2")
spark.sql("CREATE TEMPORARY VIEW v1 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2016 group by Carrier order by NumFlights desc LIMIT 10")
spark.sql("CREATE TEMPORARY VIEW v2 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2017 group by Carrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT distinct ac.AirlineName, v1.Carrier, v1.NumFlights, v2.NumFlights from v1 INNER JOIN v2 ON v1.Carrier = v2.Carrier INNER JOIN AirlineCodes ac ON v2.Carrier = ac.AirlineCode WHERE v1.NumFlights > v2.NumFlights")
#show the top row without truncation
output.show(10, False)

#display for visual analysis
display(output)
```

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight에서 Apache Hive를 사용하여 데이터 추출, 변환 및 로드](tutorial-extract-transform-load-hive.md)