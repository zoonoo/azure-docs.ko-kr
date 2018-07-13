---
title: '자습서: HDInsight에서 Hive를 사용하여 ETL(추출, 변환, 로드) 작업 수행 - Azure'
description: HDInsight에서 Hive를 사용하여 원시 CSV 데이터 집합에서 데이터를 추출하고, 변환한 다음, Sqoop을 사용하여 변환된 데이터를 Azure SQL 데이터베이스로 로드하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: jamesbak
manager: jahogg
tags: azure-portal
ms.component: data-lake-storage-gen2
ms.service: hdinsight
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: jamesbak
ms.custom: H1Hack27Feb2017,hdinsightactive,mvc
ms.openlocfilehash: 8f5771ac860d40eab979bf9be92b18da8f5d850d
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342371"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-on-azure-hdinsight"></a>자습서: Azure HDInsight에서 Apache Hive를 사용하여 데이터 추출, 변환 및 로드

이 자습서에서는 원시 CSV 데이터 파일을 사용하고, HDInsight 클러스터로 가져온 다음, Azure HDInsight에서 Apache Hive를 사용하여 데이터를 변환합니다. 데이터가 변환된 후 Apache Sqoop을 사용하여 Azure SQL 데이터베이스로 해당 데이터를 로드합니다. 이 문서에서는 공개적으로 사용할 수 있는 비행 데이터를 사용합니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 샘플 비행 데이터 다운로드
> * HDInsight 클러스터에 데이터 업로드
> * Hive를 사용하여 데이터 변환
> * Azure SQL 데이터베이스에 테이블 만들기
> * Sqoop을 사용하여 Azure SQL 데이터베이스에 데이터 내보내기


다음 그림에서는 일반적인 ETL 응용 프로그램 흐름을 보여줍니다.

![Azure HDInsight에서 Apache Hive를 사용하여 ETL 작업](./media/tutorial-extract-transform-load-hive/hdinsight-etl-architecture.png "Azure HDInsight에서 Apache Hive를 사용하여 ETL 작업")

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* **HDInsight의 Linux 기반 Hadoop 클러스터** 새 Linux 기반 HDInsight 클러스터를 만드는 방법에 대한 단계는 [Hadoop, Spark, Kafka 등으로 HDInsight에서 클러스터 설정](./quickstart-create-connect-hdi-cluster.md)을 참조하세요.

* **Azure SQL Database**. Azure SQL Database를 대상 데이터 저장소로 사용합니다. SQL Database가 없는 경우 [Azure Portal에서 Azure SQL Database 만들기](../../sql-database/sql-database-get-started.md)를 참조하세요.

* **Azure CLI 2.0**. Azure CLI를 설치하지 않은 경우 자세한 단계는 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

* **SSH 클라이언트** 자세한 내용은 [SSH를 사용하여 HDInsight(Hadoop)에 연결](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

> [!IMPORTANT]
> 이 문서의 단계에는 Linux를 사용하는 HDInsight 클러스터가 필요합니다. Linux는 Azure HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

## <a name="download-the-flight-data"></a>비행 데이터 다운로드

1. [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website](영문)로 이동합니다.

2. 페이지에서 다음 값을 선택합니다.

   | 이름 | 값 |
   | --- | --- |
   | 필터 연도 |2013 |
   | 필터 기간 |1월 |
   | 필드 |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   기타 모든 필드 지우기

3. **다운로드**를 선택합니다. 선택한 데이터 필드와 함께 .zip 파일을 가져옵니다.

## <a name="upload-data-to-an-hdinsight-cluster"></a>HDInsight 클러스터에 데이터 업로드

HDInsight 클러스터와 연결된 저장소로 데이터를 업로드하는 여러 가지 방법이 있습니다. 이 섹션에서는 `scp`를 사용하여 데이터를 업로드합니다. 데이터를 업로드하는 다른 방법을 알아보려면 [Distcp를 사용하여 Azure Storage Blob과 Data Lake Storage Gen2 미리 보기 간에 데이터 복사](use-distcp.md)를 참조하세요.

1. 명령 프롬프트를 열고 다음 명령을 사용하여 HDInsight 클러스터 헤드 노드에 .zip 파일을 업로드합니다.

    ```bash
    scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
    ```

    *FILE_NAME*을 .zip 파일의 이름으로 바꿉니다. *SSH_USER_NAME*을 HDInsight 클러스터에 대한 SSH 로그인으로 바꿉니다. *CLUSTER_NAME*을 HDInsight 클러스터의 이름으로 바꿉니다.

   > [!NOTE]
   > SSH 로그인을 인증하는 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. 공용 키를 사용하는 경우 `-i` 매개 변수를 사용하고 개인 키와 일치하는 경로를 지정합니다. 예: `scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:`

2. 업로드를 완료한 후에 SSH를 사용하여 클러스터에 연결합니다. 명령 프롬프트에서 다음 명령을 입력합니다.

    ```bash
    ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
    ```

3. 다음 명령을 사용하여 .zip 파일의 압축을 풉니다.

    ```bash
    unzip <FILE_NAME>.zip
    ```

    이 명령은 크기가 약 60MB인 *.csv* 파일의 압축을 풉니다.

4. 다음 명령을 사용하여 디렉터리를 만들고, *.csv* 파일을 이 디렉터리에 복사합니다.

    ```bash
    hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
    hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
    ```

5. Data Lake Storage Gen2 파일 시스템을 만듭니다.

    ```bash
    hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
    ```

## <a name="transform-data-using-a-hive-query"></a>Hive 쿼리를 사용하여 데이터 변환

HDInsight 클러스터에서 Hive 작업을 실행하는 여러 가지 방법이 있습니다. 이 섹션에서는 Beeline을 사용하여 Hive 작업을 실행합니다. Hive 작업 실행의 다른 메서드에 대한 정보는 [HDInsight의 Hive 사용](../../hdinsight/hadoop/hdinsight-use-hive.md)을 참조하세요.

Hive 작업의 일부로 .csv 파일에서 **지연**이라는 Hive 테이블로 데이터를 가져옵니다.

1. HDInsight 클러스터에 대해 이미 있는 SSH 프롬프트에서 다음 명령을 사용하여 **flightdelays.hql**이라는 새 파일을 만들고 편집합니다.

    ```bash
    nano flightdelays.hql
    ```

2. 이 파일의 내용으로 다음 텍스트를 사용합니다.

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/processed
    AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

2. 파일을 저장하려면 **Esc** 키를 누른 다음, `:x`를 입력합니다.

3. Hive를 시작하고 **flightdelays.hql** 파일을 실행하려면 다음 명령을 사용합니다.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. __flightdelays.hql__ 스크립트 실행이 완료된 후에 다음 명령을 사용하여 대화형 Beeline 세션을 엽니다.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. `jdbc:hive2://localhost:10001/>` 프롬프트를 수신하면, 다음 쿼리를 사용하여 가져온 비행 지연 데이터에서 데이터를 검색합니다.

    ```hiveql
    INSERT OVERWRITE DIRECTORY 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    이 쿼리는 평균 지연 시간과 함께 날씨 지연이 발생된 도시 목록이 검색된 후 `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`에 저장됩니다. 나중에 Sqoop는 이 위치에서 데이터를 읽어 Azure SQL Database로 내보냅니다.

6. Beeline을 종료하려면 프롬프트에 `!quit`을 입력합니다.

## <a name="create-a-sql-database-table"></a>SQL Database 테이블 만들기

이 섹션에서는 Azure SQL 데이터베이스를 이미 만들었다고 가정합니다. 아직 SQL Databases가 없는 경우 [Azure Portal에서 Azure SQL Databases 만들기](../../sql-database/sql-database-get-started.md)의 정보를 사용하여 만듭니다.

이미 SQL Database가 있는 경우 서버 이름을 가져와야 합니다. 서버를 찾으려면 [Azure Portal](https://portal.azure.com)에서 이름을 지정하고, **SQL Databases**를 선택한 다음, 사용하려는 데이터베이스의 이름을 필터링합니다. 서버 이름은 **서버 이름** 열에 나열됩니다.

![Azure SQL 서버 세부 정보 가져오기](./media/tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Azure SQL 서버 세부 정보 가져오기")

> [!NOTE]
> 여러 가지 방법으로 SQL Database에 연결하고 테이블을 생성할 수 있습니다. 다음 단계는 HDInsight 클러스터의 [FreeTDS](http://www.freetds.org/)를 사용합니다.


1. FreeTDS를 설치하려면 클러스터에 대한 SSH 연결에서 다음 명령을 사용합니다.

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. 설치가 완료된 후에 다음 명령을 사용하여 SQL Database 서버에 연결합니다. **serverName**을 SQL Database 서버 이름으로 바꿉니다. **adminLogin** 및 **adminPassword**를 SQL Database의 로그인으로 바꿉니다. **databaseName**을 데이터베이스 이름으로 바꿉니다.

    ```bash
    TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -p 1433 -D <DATABASE_NAME>
    ```

    메시지가 표시되면 SQL Database 관리자 로그인에 대한 암호를 입력합니다.

    다음 텍스트와 비슷한 출력이 표시됩니다.

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. `1>` 프롬프트에 다음 행을 입력합니다.

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    `GO` 문을 입력하면 이전 문이 평가됩니다. 이 쿼리는 클러스터형 인덱스가 있는 **지연**이라는 테이블을 만듭니다.

    다음 쿼리를 사용하여 테이블이 생성되었는지 확인합니다.

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    다음 텍스트와 유사하게 출력됩니다.

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

5. `1>` 프롬프트에 `exit`를 입력하여 tsql 유틸리티를 종료합니다.


## <a name="export-data-to-sql-database-using-sqoop"></a>Sqoop을 사용하여 SQL 데이터베이스로 데이터 내보내기

이전 섹션에서는 `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`에서 변환된 데이터를 복사했습니다. 이 섹션에서는 Sqoop을 사용하여 `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`에서 Azure SQL 데이터베이스에서 만든 테이블로 데이터를 내보냅니다. 

1. 다음 명령을 사용하여 Sqoop이 SQL Database를 볼 수 있는지 확인합니다.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
    ```

    이 명령은 지연 테이블을 만든 데이터베이스를 포함한 데이터베이스 목록을 반환합니다.

2. 다음 명령을 사용하여 hivesampletable에서 delays 테이블로 데이터를 내보냅니다.

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' 
    --fields-terminated-by '\t' -m 1
    ```

    Sqoop은 지연 테이블을 포함하는 데이터베이스에 연결되고 `/tutorials/flightdelays/output` 디렉터리에서 지연 테이블로 데이터를 내보냅니다.

3. sqoop 명령이 완료되면 tsql 유틸리티를 사용하여 데이터베이스에 연결합니다.

    ```bash
    TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
    ```

    다음 문을 사용하여 데이터가 delays 테이블로 내보내졌는지 확인합니다.

    ```sql
    SELECT * FROM delays
    GO
    ```

    테이블에 데이터 목록이 표시됩니다. 테이블은 도시 이름 및 해당 도시에 대한 평균 비행 지연 시간을 포함합니다. 

    `exit` 를 입력하여 tsql 유틸리티를 종료합니다.

## <a name="next-steps"></a>다음 단계

HDInsight에서 데이터 사용에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]
* [HDInsight의 Hadoop용 Java MapReduce 프로그램 개발][hdinsight-develop-mapreduce]
* [HDInsight용 Python 스트리밍 MapReduce 프로그램 개발][hdinsight-develop-streaming]
* [HDInsight에서 Oozie 사용][hdinsight-use-oozie]
* [HDInsight에서 Sqoop 사용][hdinsight-use-sqoop]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: ../../hdinsight/hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:../../hdinsight/hadoop/hdinsight-use-hive.md
[hdinsight-provision]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: ../../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: ../../hdinsight/hdinsight-upload-data.md
[hdinsight-get-started]: ../../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:../../hdinsight/hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:../../hdinsight/hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:../../hdinsight/hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:../../hdinsight/hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
