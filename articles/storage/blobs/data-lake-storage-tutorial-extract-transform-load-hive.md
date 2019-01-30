---
title: '자습서: Azure HDInsight에서 Apache Hive를 사용하여 ETL(추출, 변환, 로드) 작업 수행'
description: 이 자습서에서는 원시 CSV 데이터 세트에서 데이터를 추출하고, Azure HDInsight에서 Apache Hive를 사용하여 데이터를 변환한 다음, Sqoop을 사용하여 변환된 데이터를 Azure SQL Database로 로드하는 방법을 알아봅니다.
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jamesbak
ms.openlocfilehash: 65d2d69c788a54371664d1a443a79bd121332470
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105154"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>자습서: Azure HDInsight에서 Apache Hive를 사용하여 데이터 추출, 변환 및 로드

이 자습서에서는 데이터 ETL(추출, 변환 및 로드) 작업을 수행합니다. 원시 CSV 데이터 파일을 추출하여 Azure HDInsight 클러스터로 가져오고, Apache Hive를 사용하여 변환하고, Apache Sqoop을 사용하여 Azure SQL 데이터베이스에 로드합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 데이터를 추출하여 HDInsight 클러스터에 로드합니다.
> * Apache Hive를 사용하여 데이터를 변환합니다.
> * Sqoop을 사용하여 Azure SQL 데이터베이스를 로드합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* **HDInsight의 Linux 기반 Hadoop 클러스터**: 새 Linux 기반 HDInsight 클러스터를 만들려면 [Hadoop, Spark, Kafka 등으로 HDInsight에서 클러스터 설정](./data-lake-storage-quickstart-create-connect-hdi-cluster.md)을 참조하세요.

* **Azure SQL Database**: Azure SQL Database를 대상 데이터 저장소로 사용합니다. SQL Database가 없는 경우 [Azure Portal에서 Azure SQL Database 만들기](../../sql-database/sql-database-get-started.md)를 참조하세요.

* **Azure CLI**: 아직 Azure CLI를 설치하지 않은 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

* **SSH 클라이언트**: 자세한 내용은 [SSH를 사용하여 HDInsight(Hadoop)에 연결](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

> [!IMPORTANT]
> 이 문서의 단계를 수행하려면 Linux를 사용하는 HDInsight 클러스터가 필요합니다. Linux는 Azure HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

### <a name="download-the-flight-data"></a>비행 데이터 다운로드

이 자습서에서는 Bureau of Transportation Statistics의 비행 데이터를 사용하여 ETL 작업을 수행하는 방법을 보여줍니다. 자습서를 완료하려면 이 데이터를 다운로드해야 합니다.

1. [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)로 이동합니다.

1. 페이지에서 다음 값을 선택합니다.

   | 이름 | 값 |
   | --- | --- |
   | **Filter Year** |2013 |
   | **Filter Period** |January |
   | **필드** |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |

1. 기타 모든 필드를 지웁니다.

1. **다운로드**를 선택합니다. 선택한 데이터 필드가 포함된 .zip 파일을 얻게 됩니다.

## <a name="extract-and-upload-the-data"></a>데이터 추출 및 업로드

이 섹션에서는 `scp`를 사용하여 데이터를 HDInsight 클러스터에 업로드합니다.

명령 프롬프트를 열고 다음 명령을 사용하여 HDInsight 클러스터 헤드 노드에 .zip 파일을 업로드합니다.

```bash
scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
```

* \<FILE_NAME>을 .zip 파일의 이름으로 바꿉니다.
* \<SSH_USER_NAME>을 HDInsight 클러스터에 대한 SSH 로그인으로 바꿉니다.
* \<CLUSTER_NAME>을 HDInsight 클러스터의 이름으로 바꿉니다.

SSH 로그인을 인증하는 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. 

공용 키를 사용하는 경우 `-i` 매개 변수를 사용하고 개인 키와 일치하는 경로를 지정합니다. 예: `scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:`

업로드를 완료한 후에 SSH를 사용하여 클러스터에 연결합니다. 명령 프롬프트에서 다음 명령을 입력합니다.

```bash
ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
```

다음 명령을 사용하여 .zip 파일의 압축을 풉니다.

```bash
unzip <FILE_NAME>.zip
```

이 명령은 크기가 약 60MB인 **.csv** 파일의 압축을 풉니다.

다음 명령을 사용하여 디렉터리를 만들고, *.csv* 파일을 이 디렉터리에 복사합니다.

```bash
hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
```

다음 명령을 사용하여 Data Lake Storage Gen2 파일 시스템을 만듭니다.

```bash
hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
```

## <a name="transform-the-data"></a>데이터 변환

이 섹션에서는 Beeline을 사용하여 Apache Hive 작업을 실행합니다.

Apache Hive 작업의 일부로 .csv 파일의 데이터를 **delays**라는 Apache Hive 테이블로 가져옵니다.

HDInsight 클러스터에 대해 이미 있는 SSH 프롬프트에서 다음 명령을 사용하여 **flightdelays.hql**이라는 새 파일을 만들고 편집합니다.

```bash
nano flightdelays.hql
```

이 파일의 내용으로 다음 텍스트를 사용합니다.

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

파일을 저장하려면 Esc 키를 선택하고 `:x`를 입력합니다.

Hive를 시작하고 **flightdelays.hql** 파일을 실행하려면 다음 명령을 사용합니다.

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
```

__flightdelays.hql__ 스크립트 실행이 완료된 후에 다음 명령을 사용하여 대화형 Beeline 세션을 엽니다.

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
```

`jdbc:hive2://localhost:10001/>` 프롬프트를 수신하면, 다음 쿼리를 사용하여 가져온 비행 지연 데이터에서 데이터를 검색합니다.

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

Beeline을 종료하려면 프롬프트에 `!quit`을 입력합니다.

## <a name="create-a-sql-database-table"></a>SQL Database 테이블 만들기

이 작업에 사용되는 SQL 데이터베이스의 서버 이름이 필요합니다. 다음 단계를 완료하여 서버 이름을 찾습니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.

1. **SQL Databases**를 선택합니다.

1. 사용하려는 데이터베이스의 이름을 필터링합니다. 서버 이름은 **서버 이름** 열에 나열됩니다.

1. 사용할 데이터베이스의 이름을 필터링합니다. 서버 이름은 **서버 이름** 열에 나열됩니다.

    ![Azure SQL 서버 세부 정보 가져오기](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Azure SQL 서버 세부 정보 가져오기")

    여러 가지 방법으로 SQL Database에 연결하고 테이블을 생성할 수 있습니다. 다음 단계는 HDInsight 클러스터의 [FreeTDS](http://www.freetds.org/)를 사용합니다.

FreeTDS를 설치하려면 클러스터에 대한 SSH 연결에서 다음 명령을 사용합니다.

```bash
sudo apt-get --assume-yes install freetds-dev freetds-bin
```

설치가 완료되면 다음 명령을 사용하여 SQL Database 서버에 연결합니다.

* \<SERVER_NAME>을 SQL Database 서버 이름으로 바꿉니다.
* \<ADMIN_LOGIN>을 SQL Database의 관리 로그인으로 바꿉니다.
* \<DATABASE_NAME>을 데이터베이스 이름으로 바꿉니다.

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -p 1433 -D <DATABASE_NAME>
```

메시지가 표시되면 SQL Database 관리자 로그인의 암호를 입력합니다.

다음 텍스트와 비슷한 출력이 표시됩니다.

```
locale is "en_US.UTF-8"
locale charset is "UTF-8"
using default charset "UTF-8"
Default database being set to sqooptest
1>
```

`1>` 프롬프트에서 다음 명령문을 입력합니다.

```hiveql
CREATE TABLE [dbo].[delays](
[origin_city_name] [nvarchar](50) NOT NULL,
[weather_delay] float,
CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
([origin_city_name] ASC))
GO
```

`GO` 문을 입력하면 이전 문이 평가됩니다.
이 쿼리는 클러스터형 인덱스가 있는 **delays**라는 테이블을 만듭니다.

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

`exit` at the `1>`를 입력하여 tsql 유틸리티를 종료합니다.

## <a name="export-and-load-the-data"></a>데이터 내보내기 및 로드

이전 섹션에서는 `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` 위치에서 변환된 데이터를 복사했습니다. 이 섹션에서는 Sqoop을 사용하여 `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`에서 Azure SQL 데이터베이스에 만든 테이블로 데이터를 내보냅니다.

다음 명령을 사용하여 Sqoop이 SQL Database를 볼 수 있는지 확인합니다.

```bash
sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
```

이 명령은 **delays** 테이블을 만든 데이터베이스를 포함한 데이터베이스 목록을 반환합니다.

다음 명령을 사용하여 **hivesampletable** 테이블에서 **delays** 테이블로 데이터를 내보냅니다.

```bash
sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
```

Sqoop은 **delays** 테이블을 포함하는 데이터베이스에 연결한 다음, `/tutorials/flightdelays/output` 디렉터리에서 **delays** 테이블로 데이터를 내보냅니다.

`sqoop` 명령이 완료되면 tsql 유틸리티를 사용하여 데이터베이스에 연결합니다.

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
```

다음 명령문을 사용하여 데이터가 **delays** 테이블로 내보내졌는지 확인합니다.

```sql
SELECT * FROM delays
GO
```

테이블에 데이터 목록이 표시됩니다. 테이블은 도시 이름 및 해당 도시에 대한 평균 비행 지연 시간을 포함합니다.

`exit`를 입력하여 tsql 유틸리티를 종료합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에 사용된 모든 리소스는 이미 있는 것들입니다. 따라서 정리가 필요 없습니다.

## <a name="next-steps"></a>다음 단계

HDInsight에서 데이터를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Databricks를 사용하여 데이터 추출, 변환 및 로드](./data-lake-storage-use-hdi-cluster.md)
