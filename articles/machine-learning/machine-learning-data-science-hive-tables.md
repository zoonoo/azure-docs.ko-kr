<properties 
	pageTitle="데이터를 만들어서 Blob 저장소의 Hive 테이블에 로드 | Microsoft Azure" 
	description="Hive 테이블을 만들어서 blob의 데이터를 Hive 테이블에 로드" 
	services="machine-learning" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="hangzh;bradsev" />

 
#데이터를 만들어서 Azure blob 저장소의 Hive 테이블에 로드
 
이 문서에서는 Hive 테이블을 만들고 Azure blob 저장소의 데이터를 로드하는 일반 Hive 쿼리를 보여 줍니다. 이러한 Hive 쿼리는 GitHub 리포지토리에 공유되어 있습니다. [Github 리포지토리](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql).


"IPython Notebook 서버를 사용하여 Azure 가상 컴퓨터 설정"의 지침에 따라 Azure 가상 컴퓨터를 만드는 경우 이 스크립트 파일이 가상 컴퓨터의 `C:\Users<user name>\Documents\Data Science Scripts` 디렉터리에 다운로드됩니다. 사용자는 이러한 쿼리의 해당 필드에서 사용자 고유의 데이터 스키마와 Azure blob 저장소 구성을 연결해야 하고 이러한 Hive 쿼리를 제출할 준비가 완료되어야 합니다.

Hive 테이블의 데이터가 **압축되지 않은** 테이블 형식이고 Hadoop 클러스터에서 사용하는 저장소 계정의 기본 또는 추가 컨테이너에 데이터가 업로드된 것으로 가정합니다. _NYC Taxi Trip Data_를 연습하고 싶다면 사용자는 먼저 [24개 파일을 다운로드](http://www.andresmh.com/nyctaxitrips/)(Trip 파일 12개, Fair 파일 12개)하고, .csv 파일로 **압축을 풀고**, [Azure HDInsight Hadoop 클러스터가 사용자 지정](machine-learning-data-science-customize-hadoop-cluster.html)된 경우에 사용되는 Azure 저장소 계정의 기본 또는 추가 컨테이너에 파일을 업로드해야 합니다.

Hadoop 클러스터 헤드 노드의 Hadoop 명령줄에서 Hive 쿼리를 제출할 수 있습니다. 그러려면 다음 작업을 수행해야 합니다.

1. [Hadoop 클러스터의 헤드 노드에 대한 원격 액세스를 설정하고 헤드 노드에 로그인합니다](machine-learning-data-science-customize-hadoop-cluster.md).
2. [헤드 노드의 Hadoop 명령줄에서 Hive 쿼리를 제출합니다](machine-learning-data-science-hive-queries.md).

사용자는 웹 브라우저에 URL `https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor를 입력하여 [쿼리 콘솔(Hive 편집기)]을 사용하거나(로그인하려면 Hadoop 클러스터 자격 증명을 입력해야 함) [PowerShell을 사용하여 Hive 작업을 제출](hdinsight-submit-hadoop-jobs-programmatically.md)할 수도 있습니다.

- [1단계: Hive 데이터베이스 및 테이블 만들기](#create-tables)
- [2단계: Hive 테이블에 데이터 로드](#load-data)
- [고급 항목: 분할된 테이블 및 ORC 형식으로 Hive 데이터 저장](#partition-orc)

## <a name="create-tables"></a>Hive 데이터베이스 및 테이블 만들기

    create database if not exists <database name>;
	CREATE EXTERNAL TABLE if not exists <database name>.<table name>
	(
		field1 string, 
		field2 int, 
		field3 float, 
		field4 double, 
		...,
		fieldN string
	) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>' 
	STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

다음은 사용자가 연결해야 하는 필드와 기타 구성에 대한 설명입니다.

- `<database name>`: 사용자가 만들려고 하는 데이터베이스 이름입니다. 사용자가 기본 데이터베이스를 사용하려는 경우 `create database...` 쿼리를 생략할 수 있습니다. 
- `<table name>`: 사용자가 지정된 데이터베이스 내에 만들려는 테이블 이름입니다. 사용자가 기본 데이터베이스를 사용하려는 경우 `<database name>.` 없이 `<table name>`에서 직접 테이블을 참조할 수 있습니다.
- `<field separator>`: 데이터 파일에서 Hive 테이블에 업로드할 필드를 구분하는 구분 기호입니다. 
- `<line separator>`: 데이터 파일의 줄을 구분하는 구분 기호입니다. 
- `<storage location>`: Hive 테이블의 데이터를 저장할 Azure 저장소 위치입니다. 사용자가 `LOCATION '<storage location>'`을 지정하지 않으면 기본적으로 데이터베이스 및 테이블이 Hive 클러스터 기본 컨테이너의 `hive/warehouse/` 디렉터리에 저장됩니다. 사용자가 저장소 위치를 지정하려면 저장소 위치가 데이터베이스 및 테이블의 기본 컨테이너 내부에 있어야 합니다. 이 위치를 `'wasb:///<directory 1>/'` 또는 `'wasb:///<directory 1>/<directory 2>/'` 형식의 클러스터 기본 컨테이너에 대한 상대 위치로 참조해야 합니다. 쿼리가 실행된 후 기본 컨테이너 내에 상대 디렉터리가 만들어집니다. 
- `TBLPROPERTIES("skip.header.line.count"="1")`: 데이터 파일에 헤더 줄이 있으면 사용자는 `create table` 쿼리의 **끝**에 이 속성을 추가해야 합니다. 그렇지 않으면 헤더 줄이 테이블의 레코드로 로드됩니다. 데이터 파일에 헤더 줄이 없으면 쿼리에서 이 구성을 생략해도 됩니다. 

## <a name="load-data"></a>Hive 테이블에 데이터 로드

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- `<path to blob data>`: Hive 테이블에 업로드할 blob 파일이 HDInsight Hadoop 클러스터의 기본 컨테이너에 있으면 `<path to blob data>`가 `'wasb:///<directory in this container>/<blob file name>'` 형식이어야 합니다. blob 파일이 HDInsight Hadoop 클러스터의 추가 컨테이너에 있을 수도 있습니다. 이 경우 `<path to blob data>`는 `'wasb://<container name>@<storage account name>.blob.windows.core.net/<blob file name>'` 형식이어야 합니다.

> [AZURE.NOTE]Hive 테이블에 업로드할 blob 데이터가 Hadoop 클러스터에 대한 저장소 계정의 기본 또는 추가 컨테이너에 있어야 합니다. 그렇지 않으면 데이터에 액세스할 수 없기 때문에 `LOAD DATA` 쿼리가 실패합니다.


## <a name="partition-orc"></a>고급 항목: 분할된 테이블 및 ORC 형식으로 Hive 데이터 저장

데이터가 큰 경우 테이블을 분할하면 테이블의 파티션 몇 개만 검색하면 되는 쿼리의 속도가 향상됩니다. 예를 들어 웹 사이트의 로그 데이터를 날짜별로 분할하는 것이 합리적입니다.

테이블 분할 외에도 Hive 데이터를 ORC 형식으로 저장하는 방법 또한 도움이 됩니다. ORC는 "Optimized Row Columnar"의 약어입니다. 자세한 내용은 _"[ORC 파일을 사용하면 Hive에서 데이터를 읽고, 쓰고, 처리할 때 성능 향상](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles)"_을 참조하세요.

### 분할된 테이블

분할된 테이블을 만들고 그 테이블에 데이터를 로드하는 쿼리는 다음과 같습니다.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

분할된 테이블에 쿼리할 때 `where` 절의 **시작** 부분에 파티션 조건을 추가하면 검색 효율성이 크게 향상됩니다.

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>ORC 형식으로 Hive 데이터 저장

사용자는 blob의 데이터를 ORC 저장소 형식의 Hive 테이블에 바로 로드할 수 없습니다. Azure blob의 데이터를 ORC 형식으로 저장된 Hive 테이블에 로드하려면 다음 단계를 수행해야 합니다.

1. 외부 테이블 **STORED AS TEXTFILE**을 만들고 blob 저장소의 데이터를 이 테이블에 로드합니다.

		CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
		(
			field1 string,
			field2 int,
			...
			fieldN date
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' 
			lines terminated by '<line separator>' STORED AS TEXTFILE 
			LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

		LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

2. 1단계의 외부 테이블과 동일한 스키마 및 필드 구분 기호를 사용하여 내부 테이블을 만듭니다. Hive 데이터를 ORC 형식으로 저장합니다.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. 1단계의 외부 테이블에서 데이터를 선택하여 ORC 테이블에 삽입합니다.

		INSERT OVERWRITE TABLE <database name>.<ORC table name> SELECT * FROM <database name>.<external textfile table name>;

> [AZURE.NOTE]TEXTFILE 테이블 `<database name>.<external textfile table name>`에 파티션이 있으면 3단계의 `SELECT * FROM <database name>.<external textfile table name>`에서는 반환된 데이터 집합의 필드로 파티션 변수를 선택할 것입니다. 이 필드를 `<database name>.<ORC table name>`에 삽입하면 오류가 발생할 것입니다. `<database name>.<ORC table name>`은 테이블 스키마의 필드로 해당 파티션 변수를 갖고 있지 않기 때문입니다. 이 경우 사용자는 `<database name>.<ORC table name>`에 삽입할 필드를 다음과 같이 구체적으로 선택해야 합니다.

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		      SELECT field1, field2, ..., fieldN
		      FROM <database name>.<external textfile table name> 
		      WHERE <partition variable>=<partition value>;

4. 모든 데이터가 `<database name>.<ORC table name>`에 삽입된 후에는 다음 쿼리를 사용할 때 `<external textfile table name>`을 삭제하는 것이 안전합니다.

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

이제 ORC 형식의 데이터를 사용할 수 있는 테이블이 준비되었습니다.
 

<!---HONumber=July15_HO2-->