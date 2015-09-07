<properties 
	pageTitle="데이터를 만들어서 Blob 저장소의 Hive 테이블에 로드 | Microsoft Azure"
	description="Hive 테이블을 만들어서 blob의 데이터를 Hive 테이블에 로드"
	services="machine-learning,storage"
	documentationCenter=""
	authors="hangzh-msft"
	manager="jacob.spoelstra"
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2015"
	ms.author="hangzh;bradsev"/>

 
#데이터를 만들어서 Azure blob 저장소의 Hive 테이블에 로드
 
## 소개
이 문서에서는 Hive 테이블을 만들고 Azure blob 저장소의 데이터를 로드하는 일반 Hive 쿼리를 보여 줍니다. 또한 Hive 테이블을 분할하고 ORC(Optimized Row Columnar) 형식을 사용하여 쿼리 성능을 개선하는 방법에 대한 지침도 제공됩니다.

## 필수 조건
이 문서에서는 사용자가 다음 작업을 수행한 것으로 가정합니다.
 
* Azure 저장소 계정을 만들었습니다. 지침이 필요한 경우 [Azure 저장소 계정 만들기](../hdinsight-get-started.md#storage)를 참조하세요. 
* 사용자 지정된 Hadoop 클러스터에 HDInsight 서비스를 프로비전했습니다. 지침이 필요한 경우 [고급 분석을 위한 Azure HDInsight Hadoop 클러스터 사용자 지정](machine-learning-data-science-customize-hadoop-cluster.md)을 참조하세요.
* 클러스터에 대한 원격 액세스를 설정하고, 로그인하고, Hadoop 명령줄 콘솔을 열었습니다. 지침이 필요한 경우 [Hadoop 클러스터의 헤드 노드에 액세스](machine-learning-data-science-customize-hadoop-cluster.md#headnode)를 참조하세요. 

## Azure Blob 저장소에 데이터 업로드
[고급 분석을 위한 Azure 가상 컴퓨터 설정](machine-learning-data-science-setup-virtual-machine.md)의 지침에 따라 Azure 가상 컴퓨터를 만드는 경우 이 스크립트 파일을 가상 컴퓨터의 *C:\\Users\<사용자 이름>\\Documents\\Data Science Scripts* 디렉터리에 다운로드해야 합니다. 이러한 Hive 쿼리는 제출이 가능하도록 적절한 필드에서 사용자 데이터 스키마 및 Azure blob 저장소 구성을 연결하기만 하면 됩니다.

Hive 테이블의 데이터가 **압축되지 않은** 테이블 형식이고 Hadoop 클러스터에서 사용하는 저장소 계정의 기본 또는 추가 컨테이너에 데이터가 업로드된 것으로 가정합니다.

_NYC Taxi Trip Data_를 연습하고 싶다면 먼저 24개의 <a href="http://www.andresmh.com/nyctaxitrips/" target="_blank">NYC Taxi Trip Data</a> 파일(Trip 파일 12개, Fare 파일 12개)을 다운로드하고, .csv 파일로 모든 파일의 **압축을 풀고**, [고급 분석 프로세스 및 기술을 위한 Azure HDInsight Hadoop 클러스터 사용자 지정](machine-learning-data-science-customize-hadoop-cluster.md) 항목에 설명된 절차에 따라 작성된 기본 Azure 저장소 계정 또는 Azure 저장소 계정의 적절한 컨테이너에 해당 파일을 업로드해야 합니다. 저장소 계정의 기본 컨테이너에 .csv 파일을 업로드하는 프로세스는 이 [페이지](machine-learning-data-science-process-hive-walkthrough/#upload)에 나와 있습니다.

## Hive 쿼리를 제출하는 방법
Hadoop 클러스터 헤드 노드의 Hadoop 명령줄 콘솔에서 Hive 쿼리를 제출할 수 있습니다. 이렇게 하려면 Hadoop 클러스터의 헤드 노드에 로그인하여 Hadoop 명령줄 콘솔을 열고 여기에서 Hive 쿼리를 제출합니다. 이 작업을 수행하는 방법에 대한 지침은 [고급 분석 프로세스에서 Hive 쿼리를 HDInsight Hadoop 클러스터에 제출](machine-learning-data-science-process-hive-tables.md)을 참조하세요.

사용자는 웹 브라우저에 URL

https://&#60;Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor

입력하여 쿼리 콘솔(Hive 편집기)을 사용할 수도 있습니다. 로그인하려면 Hadoop 클러스터 자격 증명을 입력하라는 메시지가 표시되므로 이 자격 증명이 도움이 됩니다.

또는 [PowerShell을 사용하여 Hive 작업을 제출](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell)할 수 있습니다.


## <a name="create-tables"></a> Hive 데이터베이스 및 테이블 만들기

Hive 쿼리는 [Github 리포지토리](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql)에서 공유되며 여기에서 다운로드할 수 있습니다.

다음은 Hive 테이블을 만드는 Hive 쿼리입니다.

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

- **&#60;database name>**: 사용자가 만들려고 하는 데이터베이스 이름입니다. 사용자가 기본 데이터베이스를 사용하려는 경우 *create database...* 쿼리를 생략할 수 있습니다. 
- **&#60;table name>**: 사용자가 지정된 데이터베이스 내에 만들려는 테이블 이름입니다. 사용자가 기본 데이터베이스를 사용하려는 경우 &#60;database name> 없이 *&#60;table name>*에서 직접 테이블을 참조할 수 있습니다.
- **&#60;field separator>**: 데이터 파일에서 Hive 테이블에 업로드할 필드를 구분하는 구분 기호입니다. 
- **&#60;line separator>**: 데이터 파일의 줄을 구분하는 구분 기호입니다. 
- **&#60;storage location>**: Hive 테이블의 데이터를 저장할 Azure 저장소 위치입니다. 사용자가 *LOCATION &#60;storage location>*을 지정하지 않으면 기본적으로 데이터베이스 및 테이블이 Hive 클러스터의 기본 컨테이너에 있는 *hive/warehouse/* 디렉터리에 저장됩니다. 사용자가 저장소 위치를 지정하려면 저장소 위치가 데이터베이스 및 테이블의 기본 컨테이너 내부에 있어야 합니다. 이 위치는 *'wasb:///&#60;directory 1>/'* 또는 *'wasb:///&#60;directory 1>/&#60;directory 2>/'* 등의 형식으로 클러스터 기본 컨테이너에 대한 상대 위치로 참조해야 합니다. 쿼리가 실행된 후 기본 컨테이너 내에 상대 디렉터리가 만들어집니다. 
- **TBLPROPERTIES("skip.header.line.count"="1")**: 데이터 파일에 헤더 줄이 있으면 사용자는 *create table* 쿼리의 **끝**에 이 속성을 추가해야 합니다. 그렇지 않으면 헤더 줄이 테이블의 레코드로 로드됩니다. 데이터 파일에 헤더 줄이 없으면 쿼리에서 이 구성을 생략해도 됩니다. 

## <a name="load-data"></a>Hive 테이블에 데이터 로드
다음은 Hive 테이블에 데이터를 로드하는 Hive 쿼리입니다.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **&#60;path to blob data>**: Hive 테이블에 업로드할 Blob 파일이 HDInsight Hadoop 클러스터의 기본 컨테이너에 있으면 *&#60;path to blob data>*가 *'wasb:///&#60;directory in this container>/&#60;blob file name>'* 형식이어야 합니다. blob 파일이 HDInsight Hadoop 클러스터의 추가 컨테이너에 있을 수도 있습니다. 이 경우 *&#60;path to blob data>*는 *'wasb://&#60;container name>@&#60;storage account name>.blob.core.windows.net/&#60;blob file name>'* 형식이어야 합니다.

	>[AZURE.NOTE]Hive 테이블에 업로드할 blob 데이터가 Hadoop 클러스터에 대한 저장소 계정의 기본 또는 추가 컨테이너에 있어야 합니다. 그렇지 않으면 데이터에 액세스할 수 없기 때문에 *LOAD DATA* 쿼리가 실패합니다.


## <a name="partition-orc"></a>고급 항목: 분할된 테이블 및 ORC 형식으로 Hive 데이터 저장

데이터가 큰 경우 테이블을 분할하면 테이블의 파티션 몇 개만 검색하면 되는 쿼리의 속도가 향상됩니다. 예를 들어 웹 사이트의 로그 데이터를 날짜별로 분할하는 것이 합리적입니다.

Hive 테이블 분할 외에도 Hive 데이터를 ORC(Optimized Row Columnar) 형식으로 저장하는 방법 또한 도움이 됩니다. ORC 형식에 대한 자세한 내용은 <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">ORC 파일을 사용하면 Hive에서 데이터를 읽고, 쓰고, 처리할 때 성능 향상</a>을 참조하세요.

### 분할된 테이블
다음은 분할된 테이블을 만들고 그 테이블에 데이터를 로드하는 Hive 쿼리입니다.

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

사용자는 blob 저장소의 데이터를 ORC 형식으로 저장된 Hive 테이블에 바로 로드할 수 없습니다. Azure blob의 데이터를 ORC 형식으로 저장된 Hive 테이블에 로드하려면 다음 단계를 수행해야 합니다.

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

2. 1단계의 외부 테이블과 동일한 스키마 및 필드 구분 기호를 사용하여 내부 테이블을 만들고 Hive 데이터를 ORC 형식으로 저장합니다.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. 1단계의 외부 테이블에서 데이터를 선택하여 ORC 테이블에 삽입합니다.

		INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

	>[AZURE.NOTE]TEXTFILE 테이블 *&#60;database name>.&#60;external textfile table name>*에 파티션이 있으면 3단계의 `SELECT * FROM <database name>.<external textfile table name>` 명령에서는 반환된 데이터 집합의 필드로 파티션 변수를 선택합니다. *&#60;database name>.&#60;ORC table name>*에 삽입은 실패합니다. *&#60;database name>.&#60;ORC table name>*에는 테이블 스키마의 필드인 파티션 변수가 없기 때문입니다. 이 경우 사용자는 *&#60;database name>.&#60;ORC table name>*에 삽입할 필드를 다음과 같이 구체적으로 선택해야 합니다.

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		   SELECT field1, field2, ..., fieldN
		   FROM <database name>.<external textfile table name> 
		   WHERE <partition variable>=<partition value>;

4. 모든 데이터가 *&#60;database name>.&#60;ORC table name>*에 삽입된 후에는 다음 쿼리를 사용할 때 *&#60;external textfile table name>*을 삭제하는 것이 안전합니다.

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

이 절차를 모두 수행했다면 이제 ORC 형식의 데이터를 사용할 수 있는 테이블이 준비되었을 것입니다.

<!---HONumber=August15_HO9-->