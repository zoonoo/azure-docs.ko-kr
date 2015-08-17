<properties 
	pageTitle="HDInsight의 Hadoop을 사용하여 비행 지연 데이터 분석 | Microsoft Azure" 
	description="하나의 Windows PowerShell 스크립트를 사용하여 HDInsight 클러스터를 프로비전하고, Hive 작업을 실행하고, Sqoop 작업을 실행하고, 클러스터를 삭제하는 방법을 알아봅니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="larryfr"/>

#HDInsight의 Hive를 사용하여 비행 지연 데이터 분석

Linux 기반 HDInsight(미리 보기)에서 Hive를 사용하여 비행 지연 데이터를 분석한 다음 Sqoop을 사용하여 Azure SQL 데이터베이스에 데이터를 내보내는 방법에 대해 알아봅니다

> [AZURE.NOTE]Windows 기반 HDInsight 클러스터(예: Python 및 Hive)와 함께 이 문서의 개별 항목을 사용할 수 있지만, 이 문서의 여러 단계는 Linux 기반 클러스터와 관련이 있습니다. Windows 기반 클러스터를 사용하는 단계는 [HDInsight에서 Hive를 사용하여 비행 지연 데이터 분석](hdinsight-analyze-flight-delay-data.md)을 참조하세요.

###필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- __HDInsight 클러스터__. 새 Linux 기반 HDInsight 클러스터를 만드는 단계는 [Linux의 HDInsight에서 Hive와 Hadoop 사용 시작](hdinsight-hadoop-linux-tutorial-get-started.md)을 참조하세요.

- __Azure SQL 데이터베이스__. Azure SQL 데이터베이스를 대상 데이터 저장소로 사용합니다. SQL 데이터베이스가 없는 경우, [Azure SQL 데이터베이스를 만들도록 구성하는 방법](../sql-database/sql-database-create-configure.md)을 참조하세요.

- __Azure CLI__. Azure CLI를 설치하지 않은 경우 자세한 단계는 [Azure CLI 설치 및 구성](../xplat-cli.md)을 참조하세요.


##비행 데이터 다운로드

1. [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website](영문)로 이동합니다.
2. 페이지에서 다음 값을 선택합니다.

	| Name | Value | | Filter Year | 2013 | | Filter Period | January | | Fields | Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Clear all other fields |

3. **다운로드**를 클릭합니다.

##데이터 업로드

1. 다음 명령을 사용하여 HDInsight 클러스터 헤드 노드에 zip 파일을 업로드합니다.

		scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

	__FILENAME__을 zip 파일의 이름으로 바꿉니다. __USERNAME__을 HDInsight 클러스터에 대한 SSH 로그인으로 바꿉니다. CLUSTERNAME을 HDInsight 클러스터의 이름으로 바꿉니다.
	
	> [AZURE.NOTE]SSH 로그인을 인증하는 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. 공용 키를 사용하는 경우, `-i` 매개 변수를 사용하고 개인 키와 일치하는 경로를 지정합니다. 예: `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. 업로드가 완료되면 SSH를 사용하여 클러스터에 연결합니다.

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Linux 기반 HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
	
	* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH를 사용합니다.](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows)
	
3. 연결되면 다음을 사용하여.zip 파일의 압축을 풉니다.

		unzip FILENAME.zip
	
	약 60MB인 .csv 파일의 압축을 풉니다.
	
4. 다음을 사용하여 WASB(HDInsight에서 사용된 분산 데이터 저장소)에 새 디렉터리를 만들고 파일을 복사합니다.

	hadoop fs -mkdir -p /tutorials/flightdelays/data hadoop fs -copyFromLocal FILENAME.csv /tutorials/flightdelays/data/FILENAME.csv
	
##HiveQL 만들기 및 실행

다음 단계를 사용하여 CSV 파일에서 __지연__라는 Hive 테이블로 데이터를 가져옵니다.

1. 다음을 사용하여 __flightdelays.hql__이라는 새 파일을 만들고 편집합니다.

		nano flightdelays.hql
		
	이 파일의 내용으로 다음을 사용합니다.
	
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
		LOCATION '/tutorials/flightdelays/data';
		
		-- Drop the delays table if it exists
		DROP TABLE delays;
		-- Create the delays table and populate it with data
		-- pulled in from the CSV file (via the external table defined previously)
		CREATE TABLE delays AS
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
		
2. __Ctrl + X__, __Y__를 차례로 사용하여 파일을 저장합니다.

3. 다음을 사용하여 Hive를 시작하고 __flightdelays.hql__ 파일을 실행합니다.

		hive -i flightdelays.hql
		
	파일을 실행한 후 `hive>` 프롬프트를 반환합니다.

4. `hive>` 프롬프트를 수신하면, 다음을 사용하여 가져온 비행 지연 데이터에서 데이터를 검색합니다.

		INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
		SELECT regexp_replace(origin_city_name, '''', ''),
			avg(weather_delay)
		FROM delays
		WHERE weather_delay IS NOT NULL
		GROUP BY origin_city_name;

	평균 지연 시간과 함께 날씨 지연이 발생 된 도시 목록을 검색하고 `/tutorials/flightdelays/output`에 저장합니다. 나중에 Sqoop는 이 위치에서 데이터를 읽어 Azure SQL 데이터베이스로 내보냅니다.

##SQL 데이터베이스 만들기

다음 단계에 따라 Azure SQL 데이터베이스를 만듭니다. Sqoop 통해 HDInsight에서 내보낸 데이터를 보관하는데 사용됩니다.

1. Azure CLI가 설치된 개발 환경에서 새 Azure SQL 데이터베이스를 만들려면 다음 명령을 사용합니다.

		azure sql server create <adminLogin> <adminPassword> <region>

	예: `azure sql server create admin password "West US"`

	명령이 완료되면 다음과 유사한 응답이 표시됩니다.

		info:    Executing command sql server create
		+ Creating SQL Server
		data:    Server Name i1qwc540ts
		info:    sql server create command OK

> [AZURE.IMPORTANT]이 명령에 의해 반환된 서버 이름에 유의합니다. 이것은 생성된 SQL 데이터베이스 서버의 짧은 이름입니다. FQDN(정규화된 도메인 이름)은 `<shortname>.database.windows.net`입니다.

2. 다음 명령을 사용하여 SQL 데이터베이스 서버에서 **sqooptest**라는 이름의 데이터베이스를 생성합니다.

        azure sql db create [options] <serverName> sqooptest <adminLogin> <adminPassword>

    완료되면 “OK” 메시지가 반환됩니다.

	> [AZURE.NOTE]액세스가 없다는 오류가 나타나면 다음 명령을 사용하여 클라이언트 워크스테이션의 IP 주소를 SQL 데이터베이스에 추가해야 할 수도 있습니다.
	>
	> `sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

##SQL 데이터베이스 테이블 만들기

> [AZURE.NOTE]여러 가지 방법으로 SQL 데이터베이스에 연결하여 테이블을 생성할 수 있습니다. 다음 단계는 HDInsight 클러스터의 [FreeTDS](http://www.freetds.org/)를 사용합니다.

1. SSH를 사용하여 Linux 기반 HDInsight 클러스터에 연결하고 SSH 세션에서 다음 단계를 실행합니다.

3. 다음 명령을 사용하여 FreeTDS:를 설치합니다.

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. FreeTDS가 설치되면 다음 명령을 사용하여 이전에 생성한 SQL 데이터베이스 서버에 연결합니다.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    다음과 유사한 출력이 표시됩니다.

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. `1>` 프롬프트에 다음 행을 입력합니다.

        CREATE TABLE [dbo].[delays](
		[origin_city_name] [nvarchar](50) NOT NULL,
		[weather_delay] float,
		CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
		([origin_city_name] ASC))
		GO

    `GO` 명령문을 입력하면 이전 명령문이 평가됩니다. 클러스터된 인덱스(SQL 데이터베이스에서 필요한)가 있는 __지연__이라는 새 테이블을 만듭니다.

    다음을 사용하여 테이블이 생성되었는지 확인합니다.

        SELECT * FROM information_schema.tables
        GO

    다음과 비슷한 결과가 나타나야 합니다.

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     delays      BASE TABLE

8. `1>` 프롬프트에서 `exit`를 입력하여 tsql 유틸리티를 종료합니다.
	
##Sqoop으로 데이터 내보내기

1. 다음 명령을 사용하여 Sqoop lib 디렉터리에서 SQL Server JDBC 드라이버에 링크를 만듭니다. 이를 통해 Sqoop이 다음 드라이버를 사용하여 SQL 데이터베이스와 통신할 수 있습니다.

		sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /usr/hdp/current/sqoop-client/lib/sqljdbc4.jar

2. 다음 명령을 사용하여 Sqoop이 SQL 데이터베이스를 볼 수 있는지 확인합니다.

		sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

	그러면 앞에서 만든 sqooptest 데이터베이스를 포함한 데이터베이스 목록이 반환됩니다.

3. 다음 명령을 사용하여 hivesampletable에서 mobiledata 테이블로 데이터를 내보냅니다.

		sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasb:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

	Sqoop가 SQL 데이터베이스, sqooptest 데이터베이스에 연결하고 wasb:///tutorials/flightdelays/output(이전에 hive 쿼리의 출력을 저장한 위치)에서 데이터를 지연 테이블로 내보내도록 합니다.

4. 명령이 완료되면 다음을 통해 TSQL을 사용하여 데이터베이스에 연결합니다.

		TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

	연결되면 다음 명령문을 사용하여 데이터가 mobiledata 테이블로 내보내기되었는지 확인합니다.
	
		SELECT * FROM delays
		GO

	테이블에 데이터 목록이 표시됩니다. `exit`를 입력하여 tsql 유틸리티를 종료합니다.

##<a id="nextsteps"></a> 다음 단계
이제 파일을 Azure Blob 저장소로 업로드하는 방법, Azure Blob 저장소의 데이터를 사용하여 Hive 테이블을 채우는 방법, Hive 쿼리를 실행하는 방법, Sqoop을 사용하여 HDFS의 데이터를 Azure SQL 데이터베이스로 내보내는 방법을 배웠습니다. 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight 시작][hdinsight-get-started]
* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [HDInsight에서 Oozie 사용][hdinsight-use-oozie]
* [HDInsight에서 Sqoop 사용][hdinsight-use-sqoop]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]
* [HDInsight용 Java MapReduce 프로그램 개발][hdinsight-develop-mapreduce]
* [HDInsight용 Python Hadoop 스트리밍 프로그램 개발][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 

<!---HONumber=August15_HO6-->