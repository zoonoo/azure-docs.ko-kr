<properties 
	pageTitle="고급 분석 프로세스 및 작업의 기술: 1TB Criteo 데이터 집합에 Hadoop 클러스터 사용 | Microsoft Azure" 
	description="HDInsight Hadoop 클러스터를 사용하는 종단 간 시나리오에 ADAPT(고급 분석 처리 및 기술)를 사용하여 공개적으로 사용 가능한 1TB 데이터 집합으로 모델을 빌드 및 배포합니다." 
	metaKeywords="" 
	services="machine-learning,hdinsight" 
	solutions="" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="ginathan;mohabib;bradsev" />

# 고급 분석 처리 및 기술 - 1TB 데이터 집합에서 Azure HDInsight Hadoop 클러스터 사용

이 연습에서는 [Azure HDInsight Hadoop 클러스터](http://azure.microsoft.com/services/hdinsight/)에서 ADAPT(고급 분석 처리 및 기술)를 사용하여 공개적으로 사용 가능한 [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) 데이터 집합 중 하나에서 데이터를 저장, 탐색, 기능 설계 및 다운 샘플링하는 방법을 보여 줍니다. Azure 기계 학습을 사용하여 이 데이터에 대한 이진 분류 및 회귀 모델을 작성합니다. 또한 이러한 모델 중 하나를 웹 서비스로 게시하는 방법을 보여 줍니다.

이 연습에서 iPython notebook을 사용하여 작업을 수행할 수도 있습니다. 이 방법을 사용하려면 [Hive ODBC 연결을 사용하여 Criteo 연습](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) 항목을 참조해야 합니다.


## <a name="dataset"></a>데이터 집합 설명

Criteo 데이터는 클릭 예측 데이터 집합으로, 43억 개가 넘는 레코드로 구성된 약 370GB의 gzip 압축된 TSV 파일(압축 해제 시 약 1.3TB)입니다. [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/)에서 사용할 수 있는 24일간의 클릭 데이터를 가져옵니다. 데이터 과학자의 편의를 위해 실험에 사용할 수 있도록 데이터의 압축을 해제해 두었습니다.

이 데이터 집합의 각 레코드에는 40개의 열이 있습니다.

- 첫 번째 열은 사용자가 광고를 클릭했는지(값 1) 또는 클릭하지 않았는지(값 0)를 나타내는 레이블 열입니다. 
- 다음 13개의 열은 숫자입니다. 
- 마지막 26개는 범주 열입니다. 

열은 익명으로 처리되고 일련의 열거된 이름, 즉 "Col1"(레이블 열)부터 'Col40"(마지막 범주 열)까지를 사용합니다.

다음은 이 데이터 집합에서 발췌한 두 관찰(행)의 처음 20개 열입니다.

	Col1	Col2	Col3	Col4	Col5	Col6	Col7	Col8	Col9	Col10	Col11	Col12	Col13	Col14	Col15			Col16			Col17			Col18			Col19		Col20	

	0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
	0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

이 데이터 집합의 숫자 열과 범주 열 모두에 누락된 값이 있습니다. 아래에서는 누락된 값을 처리하는 간단한 방법을 설명합니다. 데이터에 대한 추가 정보는 Hive 테이블에 저장할 때 살펴봅니다.

**정의:** *CTR(클릭률):* 데이터를 클릭한 백분율입니다. 이 Criteo 데이터 집합의 CTR은 약 3.3% 또는 0.033입니다.

## <a name="mltasks"></a>예측 작업의 예제
이 연습에서는 두 가지 샘플 예측 문제를 처리합니다.

1. **이진 분류**: 사용자가 광고를 클릭했는지 여부를 예측합니다.
	- 클래스 0: 클릭 안 함
	- 클래스 1: 클릭

2. **회귀**: 사용자 기능에서 광고 클릭 확률을 예측합니다.


## <a name="setup"></a>데이터 과학용 HDInsight Hadoop 클러스터 설정

**참고:** 일반적으로 **관리** 작업입니다.

다음 3단계에 따라 HDInsight 클러스터를 사용하여 예측 분석 솔루션을 빌드하기 위한 Azure 데이터 과학 환경을 설정합니다.

1. [저장소 계정 만들기](../storage-whatis-account.md):이 저장소 계정은 Azure Blob 저장소에 데이터를 저장하는 데 사용됩니다. HDInsight 클러스터에 사용되는 데이터는 여기에 저장됩니다.

2. [데이터 과학용 Azure HDInsight Hadoop 클러스터 사용자 지정](machine-learning-data-science-customize-hadoop-cluster.md):이 단계에서는 모든 노드에 설치된 64비트 Anaconda Python 2.7을 사용하여 Azure HDInsight Hadoop 클러스터를 만듭니다. HDInsight 클러스터를 사용자 지정할 때 두 가지 중요한 단계(이 항목에 설명)를 완료해야 합니다.

	* 1단계에서 만든 저장소 계정을 HDInsight 클러스터와 연결해야 합니다. 이 저장소 계정은 클러스터 내에서 처리할 수 있는 데이터에 액세스하는 데 사용됩니다.

	* 클러스터의 헤드 노드에 대한 원격 액세스를 활성화해야 합니다. 여기에서 지정한 원격 액세스 자격 증명(클러스터에 대해 지정한 자격 증명과 다름)을 기억해야 합니다. 아래에서 필요합니다.

3. [Azure ML 작업 영역 만들기](machine-learning-create-workspace.md): 이 Azure 기계 학습 작업 영역은 초기 데이터를 탐색하고 HDInsight 클러스터에 샘플링을 다운로드한 후 기계 학습 모델을 작성하는 데 사용됩니다.

## <a name="getdata"></a>공용 원본에서 데이터 가져오기 및 사용

링크를 클릭하고 사용 약관에 동의한 후 이름을 제공하여 [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) 데이터 집합에 액세스할 수 있습니다. 아래와 같은 스냅숏이 나타납니다.

![Criteo 조건 동의](http://i.imgur.com/hLxfI2E.png)

**Continue to Download**를 클릭하여 데이터 집합 및 사용성을 읽어봅니다.

데이터는 공용 [Azure Blob 저장소](../storage-dotnet-how-to-use-blobs.md) 위치인 wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/에 있습니다. "wasb"는 Azure Blob 저장소 위치를 나타냅니다.

1. 이 공용 Blob 저장소의 데이터는 압축 해제된 데이터의 하위 폴더 3개로 구성됩니다.
		
	1. 하위 폴더 *raw/count/*는 처음 21일간의 데이터(day_00~day_20)를 포함합니다.
	2. 하위 폴더 *raw/train/*은 단일 날짜의 데이터(day_21)를 포함합니다.
	3. 하위 폴더 *raw/test/*는 2일간의 데이터(day_22 및 day_23)를 포함합니다.

2. 원시 gzip 데이터로 시작하려는 경우 기본 폴더 *raw/*에서 day_NN.gz(여기서 NN은 00~23)로 제공됩니다.

로컬 다운로드가 필요 없는 이 데이터를 액세스, 탐색 및 모델링하는 다른 방법은 이 연습의 뒷부분에서 Hive 테이블을 만들 때 설명합니다.

## <a name="login"></a>클러스터 헤드 노드에 로그인

클러스터의 헤드 노드에 로그인하려면 [Azure 관리](manage.windowsazure.com) 포털을 사용하여 클러스터를 찾습니다. 왼쪽에 있는 HDInsight 코끼리 아이콘을 클릭하고 클러스터 이름을 두 번 클릭합니다. **구성** 탭으로 이동하여 페이지 아래쪽에 있는 연결 아이콘을 두 번 클릭하고 메시지가 표시되면 원격 액세스 자격 증명을 입력합니다. 클러스터의 헤드 노드로 이동합니다.

클러스터 헤드 노드에 처음 로그인하면 일반적으로 다음과 같은 화면이 나타납니다.

![클러스터에 로그인](http://i.imgur.com/Yys9Vvm.png)

왼쪽에 데이터 탐색에 자주 사용되는 "Hadoop Command Line"이 있습니다. 두 가지 유용한 URL인 "Hadoop Yarn Status" 및 "Hadoop Name Node"도 있습니다. Yarn Status URL은 작업 진행률을 표시하고, Name Node URL은 클러스터 구성에 대한 세부 정보를 표시합니다.

설정을 완료했으므로 이제 이 연습의 첫 번째 부분인 Hive를 사용하여 데이터 탐색 및 Azure 기계 학습용 데이터 준비를 시작할 수 있습니다.

## <a name="hive-db-tables"></a> Hive 데이터베이스 및 테이블 만들기

Criteo 데이터 집합에 대한 Hive 테이블을 만들려면 헤드 노드의 바탕 화면에서 ***Hadoop Command Line***을 열고 명령을 입력하여 Hive 디렉터리를 입력합니다.

    cd %hive_home%\bin

**중요**: **이 연습의 모든 Hive 명령은 위의 Hive bin/ 디렉터리 프롬프트에서 실행합니다. 경로 문제가 자동으로 해결됩니다. "Hive 디렉터리 프롬프트", "Hive bin/ 디렉터리 프롬프트" 및 "Hadoop Command Line"은 상호 교환적으로 사용되는 용어입니다.**

**중요 2**: **하이브 쿼리를 실행하려면 항상 다음을 수행합니다.** cd %hive_home%\bin hive

"hive >" 기호와 함께 Hive REPL이 표시되면 쿼리를 잘라내고 붙여넣어 실행하면 됩니다.

아래 코드는 "criteo" 데이터베이스를 만들고 4개의 테이블을 생성합니다.


* day_00~day_20일에 작성된 *count 테이블* 1개 
* day_21일에 작성된 *train 테이블* 1개 
* 각각 day_22일과 day_23에 작성된 *test 테이블* 2개 

날짜 중 하루는 휴일이며 모델에서 휴일과 평일 간의 클릭률 차이를 감지할 수 있는지 확인할 것이기 때문에 테스트 데이터 집합을 두 개의 테이블로 분할합니다.

편의를 위해 아래에 [sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) 스크립트가 표시되어 있습니다.

	CREATE DATABASE IF NOT EXISTS criteo;
	DROP TABLE IF EXISTS criteo.criteo_count;
	CREATE TABLE criteo.criteo_count (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

	DROP TABLE IF EXISTS criteo.criteo_train;
	CREATE TABLE criteo.criteo_train (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

	DROP TABLE IF EXISTS criteo.criteo_test_day_22;
	CREATE TABLE criteo.criteo_test_day_22 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

	DROP TABLE IF EXISTS criteo.criteo_test_day_23;
	CREATE TABLE criteo.criteo_test_day_23 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

이러한 테이블은 모두 Azure Blob 저장소(wasb) 위치를 가리키는 외부 테이블입니다.

**지금까지 설명한 모든 Hive 쿼리를 실행할 수 있는 두 가지 방법이 있습니다.**

1. **Hive REPL 명령줄 사용**: 첫 번째는 "hive" 명령을 실행하고 Hive REPL 명령줄에서 위 쿼리를 복사하여 붙여넣는 것입니다. 이렇게 하려면 다음을 수행합니다.

		cd %hive_home%\bin
		hive

 	이제 REPL 명령줄에서 쿼리를 잘라내고 붙여넣어 실행합니다.

2. **쿼리를 파일로 저장하고 명령 실행**: 두 번째는 쿼리를 .hql 파일([sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql))로 저장하고 다음 명령을 실행하여 쿼리를 실행하는 것입니다.

		hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### 데이터베이스 및 테이블 만들기 확인

다음으로, Hive bin/ 디렉터리 프롬프트에서 아래 명령을 실행하여 데이터베이스 생성을 확인합니다.

		hive -e "show databases;"

다음 출력이 표시됩니다.

		criteo
		default
		Time taken: 1.25 seconds, Fetched: 2 row(s)

이는 "criteo"라는 새 데이터베이스의 생성을 확인합니다.

만든 테이블을 확인하려면 Hive bin/ 디렉터리 프롬프트에서 아래 명령을 실행하기만 하면 됩니다.

		hive -e "show tables in criteo;"

그러면 다음 출력이 표시됩니다.

		criteo_count
		criteo_test_day_22
		criteo_test_day_23
		criteo_train
		Time taken: 1.437 seconds, Fetched: 4 row(s)

##<a name="exploration"></a> Hive에서 데이터 탐색

이제 Hive에서 몇 가지 기본 데이터 탐색을 수행할 준비가 완료되었습니다. 먼저 train 및 test 데이터 테이블에서 예제 수를 계산합니다.

### train 예제 수

[sample&#95;hive&#95;count&#95;train&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql)의 내용은 다음과 같습니다.

		SELECT COUNT(*) FROM criteo.criteo_train;

다음과 같은 결과가 산출됩니다.

		192215183
		Time taken: 264.154 seconds, Fetched: 1 row(s)

또는 Hive bin/ 디렉터리 프롬프트에서 아래 명령을 실행할 수도 있습니다.

		hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### 두 가지 테스트 데이터 집합의 test 예제 수

이제 두 가지 테스트 데이터 집합의 예제 수를 계산합니다. [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;22&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql)의 내용은 다음과 같습니다.

		SELECT COUNT(*) FROM criteo.criteo_test_day_22;

다음과 같은 결과가 산출됩니다.
	
		189747893
		Time taken: 267.968 seconds, Fetched: 1 row(s)

일반적으로 아래 명령을 실행하여 Hive bin/ 디렉터리 프롬프트에서 스크립트를 호출할 수도 있습니다.

		hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

마지막으로 day_23을 기준으로 테스트 데이터 집합의 test 예제 수를 알아봅니다.

명령은 위와 유사합니다([sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql) 참조).

		SELECT COUNT(*) FROM criteo.criteo_test_day_23;

다음 출력이 표시됩니다.
	
		178274637
		Time taken: 253.089 seconds, Fetched: 1 row(s)

### 학습 데이터 집합의 레이블 분포

학습 데이터 집합의 레이블 분포를 알아보겠습니다. 여기에 사용되는 [sample&#95;hive&#95;criteo&#95;label&#95;distribution&#95;train&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql)의 내용은 다음과 같습니다.

		SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

다음과 같은 레이블 분포가 산출됩니다.

		1       6292903
		0       185922280
		Time taken: 459.435 seconds, Fetched: 2 row(s)

긍정 레이블의 백분율은 약 3.3%(원래 데이터 집합과 일치)입니다.
		
### 학습 데이터 집합의 일부 숫자 변수에 대한 히스토그램 분포

Hive의 기본 "histogram_numeric" 함수를 사용하여 숫자 변수의 변수를 확인할 수 있습니다. [sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql)의 내용은 다음과 같습니다.

		SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM 
			(SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
			) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

다음과 같은 결과가 산출됩니다.

		26      155878415
		2606    92753
		6755    22086
		11202   6922
		14432   4163
		17815   2488
		21072   1901
		24113   1283
		27429   1225
		30818   906
		34512   723
		38026   387
		41007   290
		43417   312
		45797   571
		49819   428
		53505   328
		56853   527
		61004   160
		65510   3446
		Time taken: 317.851 seconds, Fetched: 20 row(s)

Hive의 LATERAL VIEW - explode 조합은 일반 목록 대신 SQL과 유사한 출력을 생성합니다. 위 테이블에서 첫 번째 열은 bin 중앙값에 해당하고 두 번째 열은 bin 빈도에 해당합니다.

### 학습 데이터 집합의 일부 숫자 변수에 대한 백분위수 근사치

숫자 변수와 함께 백분위수 근사치도 알아보겠습니다. 여기에는 Hive의 기본 "percentile_approx"가 사용됩니다. [sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql)의 내용은 다음과 같습니다.

		SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

다음과 같은 결과가 산출됩니다.

		1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
		Time taken: 564.953 seconds, Fetched: 1 row(s)

일반적으로 백분위수 분포가 숫자 변수의 히스토그램 분포와 밀접한 관련이 있는 것을 알 수 있습니다.

### 학습 데이터 집합의 일부 범주 열에 대한 고유 값 수 확인

데이터 탐색을 계속하면서 이제 일부 범주 열에서 고유한 값의 개수를 확인합니다. 여기에 사용되는 [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql)의 내용은 다음과 같습니다.

		SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

다음과 같은 결과가 산출됩니다.

		19011825
		Time taken: 448.116 seconds, Fetched: 1 row(s)

Col15에 1,900만 개의 고유 값이 있습니다. "one-hot encoding"과 같은 네이티브 기술을 사용하여 이러한 고차원 범주 변수를 인코딩하는 것은 불가능합니다. 따라서 여기에서는 이 문제를 효과적으로 해결하는 [Learning With Counts](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx)라는 강력한 기술을 설명하고 알아봅니다.

이 하위 섹션의 마지막에 일부 다른 범주 열에 대해서도 고유 값 수를 알아봅니다. [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;multiple&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql)의 내용은 다음과 같습니다.

		SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)), 
		COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
		FROM criteo.criteo_train;

다음과 같은 결과가 산출됩니다.

		30935   15200   7349    20067   3
		Time taken: 1933.883 seconds, Fetched: 1 row(s)

Col20을 제외하고 다른 모든 열에도 많은 고유 값이 있습니다.

### train 데이터 집합의 범주 변수 쌍에 대한 동시 발생 수

범주 변수 쌍의 동시 발생 수를 알아보겠습니다. [sample&#95;hive&#95;criteo&#95;paired&#95;categorical&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql)의 코드를 사용하여 확인할 수 있습니다.

		SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

여기에서는 발생별 개수의 역순으로 상위 15개를 확인합니다. 다음 출력이 표시됩니다.

		ad98e872        cea68cd3        8964458
		ad98e872        3dbb483e        8444762
		ad98e872        43ced263        3082503
		ad98e872        420acc05        2694489
		ad98e872        ac4c5591        2559535
		ad98e872        fb1e95da        2227216
		ad98e872        8af1edc8        1794955
		ad98e872        e56937ee        1643550
		ad98e872        d1fade1c        1348719
		ad98e872        977b4431        1115528
		e5f3fd8d        a15d1051        959252
		ad98e872        dd86c04a        872975
		349b3fec        a52ef97d        821062
		e5f3fd8d        a0aaffa6        792250
		265366bf        6f5c7c41        782142
		Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> Azure 기계 학습에 대한 데이터 집합 다운 샘플링

데이터 집합을 탐색하고 모든 변수(조합 포함)에 대해 이 유형의 탐색을 수행하는 방법을 살펴보았으므로 이제 Azure 기계 학습에서 모델을 빌드할 수 있도록 데이터 집합을 다운 샘플링합니다. 중점을 두는 문제는 지정된 예제 특성 집합(Col2~Col40의 기능 값)에 대해 Col1이 0(클릭 안 함)인지 1(클릭)인지 예측하는 것입니다.

학습 및 테스트 데이터 집합을 원래 크기의 1%로 다운 샘플링하려면 Hive의 기본 RAND() 함수를 사용합니다. [sample&#95;hive&#95;criteo&#95;downsample&#95;train&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) 스크립트는 train 데이터 집합에 대해 이 작업을 수행합니다.

		CREATE TABLE criteo.criteo_train_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		---Now downsample and store in this table

		INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

다음과 같은 결과가 산출됩니다.

		Time taken: 12.22 seconds
		Time taken: 298.98 seconds

[sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) 스크립트는 test 데이터 day_22에 대해 이 작업을 수행합니다.

		--- Now for test data (day_22)

		CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

다음과 같은 결과가 산출됩니다.

		Time taken: 1.22 seconds
		Time taken: 317.66 seconds


마지막으로 [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) 스크립트는 test 데이터 day_23에 대해 이 작업을 수행합니다.

		--- Finally test data day_23
		CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

다음과 같은 결과가 산출됩니다.

		Time taken: 1.86 seconds
		Time taken: 300.02 seconds

이제 다운 샘플링한 학습 및 테스트 데이터 집합을 사용하여 Azure 기계 학습에서 모델을 빌드할 준비가 완료되었습니다.

Azure 기계 학습을 계속 진행하기 전에 마지막으로 확인해야 하는 중요한 구성 요소인 count 테이블이 있습니다. 다음 하위 섹션에서는 여기에 대해 자세히 알아봅니다.

##<a name="count"></a> count 테이블에 대한 간략한 설명

몇몇 범주 변수는 차원이 매우 높습니다. 이 연습에서는 이러한 변수를 효율적이고 강력한 방식으로 인코딩하는 [Learning With Counts](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx)라는 강력한 기술을 제공합니다. 이 기술에 대한 자세한 내용은 제공된 링크에서 확인할 수 있습니다.

**참고:** 이 연습에서는 count 테이블을 사용하여 고차원 범주 기능의 압축된 표현을 생성하는 데 중점을 둡니다. 이 기술 외에도 범주 기능을 인코딩하는 여러 기술이 있습니다. 다른 기술에 대한 자세한 내용은 [one-hot-encoding](http://en.wikipedia.org/wiki/One-hot) 및 [feature hashing](http://en.wikipedia.org/wiki/Feature_hashing)을 참조하세요.

개수 데이터에 대한 count 테이블을 작성하려면 raw/count 폴더의 데이터를 사용합니다. 범주 기능에 대해 이러한 count 테이블을 작성하는 방법 또는 미리 작성된 count 테이블을 사용하여 탐색하는 방법은 모델링 섹션에 설명되어 있습니다. "미리 작성된 count 테이블"이란 Microsoft에서 제공하는 count 테이블을 의미합니다. 이러한 테이블에 액세스하는 방법에 대한 자세한 지침은 다음과 같습니다.

## <a name="aml"></a> Azure 기계 학습에서 모델 빌드

Azure 기계 학습의 모델 빌드 프로세스는 다음 단계를 따릅니다.

1. [Hive 테이블에서 Azure 기계 학습으로 데이터 가져오기](#step1)
2. [실험 만들기: 데이터 정리, 학습자 선택 및 count 테이블로 기능화](#step2)
3. [모델 학습](#step3)
4. [테스트 데이터에 대한 모델 점수 매기기](#step4)
5. [모델 평가](#step5)
6. [모델을 사용할 웹 서비스로 게시](#step6)

이제 Azure 기계 학습 스튜디오에서 모델을 빌드할 준비가 완료되었습니다. 다운 샘플링한 데이터는 클러스터에 Hive 테이블로 저장됩니다. Azure 기계 학습 Reader 모듈을 사용하여 이 데이터를 읽습니다. 이 클러스터의 저장소 계정에 액세스하는 데 사용되는 자격 증명은 다음과 같습니다.

### <a name="step1"></a> 1단계: Reader 모듈을 사용하여 Hive 테이블에서 Azure 기계 학습으로 데이터를 가져와 기계 학습 실험용으로 선택

**Reader** 모듈에서 그림에 설정된 매개 변수 값은 예제일 뿐입니다. 다음은 **Reader** 모듈에 대해 설정된 매개 변수를 ‘입력’하는 데 대한 일반적인 지침입니다.

1. Data Source에서 "Hive query"를 선택합니다.
2. "Hive query"에 SELECT * FROM <your_database_name.your_table_name> 정도만 입력합니다.
3. Hcatalog server URI: 클러스터가 "abc"인 경우 https://abc.azurehdinsight.net입니다.
4. Hadoop user account name: 클러스터를 지정할 때 선택한 사용자 이름(원격 액세스 사용자 이름이 아님)입니다.
5. Hadoop user account password: 클러스터를 지정할 때 선택한 위 사용자 이름에 대한 암호(원격 액세스 암호가 아님)입니다.
6. Location of output data: "Azure"를 선택합니다.
7. Azure storage account name: 클러스터와 연결된 저장소 계정입니다.
8. Azure storage account key: 클러스터와 연결된 저장소 계정의 키입니다.
9. Azure container name: 클러스터 이름 "abc" 경우 일반적으로 단순히 "abc"입니다. 

Hive 테이블에서 데이터를 가져오는 동안의 **Reader** 모양은 다음과 같습니다.

![Reader에서 데이터 가져오기](http://i.imgur.com/i3zRaoj.png)

**Reader**에서 데이터 가져오기를 완료하면(모듈에 녹색 틱이 표시됨) 이 데이터를 데이터 집합(선택한 이름 사용)으로 저장합니다. 다음과 같이 표시됩니다.

![Reader에서 데이터 저장](http://i.imgur.com/oxM73Np.png)


**Reader** 모듈의 출력 포트를 마우스 오른쪽 단추로 클릭합니다. **Save as dataset** 옵션 및 **Visualize** 옵션이 표시됩니다. **Visualize** 옵션을 클릭하면 몇 가지 요약 통계에 유용한 오른쪽 패널과 함께 100개의 데이터 행이 표시됩니다. 데이터를 저장하려면 **Save as dataset**을 클릭하고 지침을 따릅니다.

기계 학습 실험에서 사용하기 위해 저장된 데이터 집합을 선택하려면 아래에 표시된 **Search**를 사용하여 데이터 집합을 찾습니다. 그런 다음 데이터 집합의 이름을 부분적으로 입력하여 데이터 집합에 액세스하고 주 패널로 끌어옵니다. 주 패널에 끌어다 놓으면 기계 학습 모델링에 사용하도록 선택됩니다.

![데이터 집합 찾기](http://i.imgur.com/rx4nnUH.png)

학습 및 테스트 데이터 집합 모두에 대해 이를 수행합니다.

### <a name="step2"></a> 2단계: Azure 기계 학습에서 클릭/클릭 안 함을 예측하기 위한 간단한 실험 만들기

먼저 간단한 실험 아키텍처를 보여 준 다음 좀 더 구체적인 내용을 살펴봅니다. 데이터를 정리하고 학습자를 선택한 후 미리 작성되거나 사용자가 처음부터 작성한 count 테이블로 기능화하는 방법을 알아봅니다.

![실험 아키텍처](http://i.imgur.com/R4iTLYi.png)

드릴다운하기 위해 그림과 같이 저장된 데이터 집합으로 시작합니다.

**Clean Missing Data** 모듈은 이름에서 알 수 있듯이 사용자가 지정할 수 있는 방법으로 누락 데이터를 정리합니다. 이 모듈을 다음과 같습니다.

![누락 데이터 정리](http://i.imgur.com/0ycXod6.png)

여기에서 모든 누락 값을 0으로 바꿉니다. 모듈의 드롭다운에서 확인할 수 있는 다른 옵션도 있습니다.

다음으로, 학습자를 선택해야 합니다. 향상된 2클래스 의사 결정 트리를 학습자로 사용합니다. 특히 고차원 범주 기능에서 count 기능을 사용하여 모델의 압축된 표현을 빌드하고 학습 및 테스트 효율성을 높이는 방법을 알아봅니다.

count 테이블은 실제로 클래스 조건부 count("조건부 count"라고도 함)입니다. 이는 기본적으로 각 클래스의 기능 값을 계산하는 방법이며, 이러한 count를 사용하여 승산비(log-odds)를 계산할 수 있습니다.


#### 모델링을 위해 미리 작성된 count 테이블에 액세스

미리 작성된 count 테이블에 액세스하려면 아래와 같이 **Gallery**를 클릭하세요.

![갤러리](http://i.imgur.com/TsWkig3.png)

**Gallery**를 클릭하면 다음과 같은 페이지로 이동합니다.

![갤러리 기본 페이지](http://i.imgur.com/dmXo0KR.png)

여기에서 "criteo counts" 구를 검색하고 결과 목록을 아래로 스크롤합니다. 다음과 같은 결과가 표시됩니다.

![Criteo count](http://i.imgur.com/JZ119Jf.png)

이 실험을 클릭하면 아래와 같은 페이지가 나타납니다.

![Count](http://i.imgur.com/dxdjMjh.png)

여기에서 **Open in studio**를 클릭하여 작업 영역으로 실험을 복사합니다. 그러면 데이터 집합에 자동으로 복사됩니다. 이 예제에서 관심 있는 두 가지 주요 데이터 집합은 count 테이블과 count 메타데이터입니다. 여기에 대해 좀 더 자세히 알아보겠습니다.

#### 데이터 집합의 count 기능

실험의 다음 모듈에서는 미리 작성된 count 테이블을 사용합니다. 이러한 미리 작성된 count 테이블을 사용하려면 새 실험의 검색 탭에서 "cr_count_"를 검색합니다. 그러면 두 개의 데이터 집합, 즉 "cr_count_cleanednulls_metadata"와 "cr_count_table_cleanednulls"라 표시됩니다. 둘 다 오른쪽의 실험 창으로 끌어 놓습니다. 출력 포트를 마우스 오른쪽 단추로 클릭하면 다음과 같은 항목을 시각화할 수 있습니다.

count 테이블 메타데이터 시각화는 다음과 같습니다.

![Count 테이블 메타데이터](http://i.imgur.com/A39PIe7.png)

메타데이터에는 조건부 count의 기반이 되는 열, 사전을 사용하여 작성했는지 여부(대안은 count-min sketch), 해시 시드 사용 여부, 기능을 해시하는 데 사용된 해시 비트 수, 클래스 수 등에 대한 정보가 들어 있습니다.

count 테이블의 시각화는 다음과 같습니다.

![Count 테이블](http://i.imgur.com/NJn1EQO.png)

count 테이블에는 클래스 조건부 count에 대한 정보가 있습니다. 범주 기능 값은 "Hash value"에 있으므로 기능 자체가 해시됩니다.

count 기능이 데이터 집합에 기본 제공되는 방법을 알아보겠습니다. 이를 위해 다음과 같은 count **Featurizer** 모듈을 사용합니다.

![Count Featurizer 모듈](http://i.imgur.com/dnMdrR1.png)

count 테이블이 작성되면(여기에서는 범주 기능의 클래스 조건부 count를 생성함) 위에 표시된 **Count Featurizer** 모듈을 사용하여 이러한 count 기능을 데이터 집합에 작성합니다. **Featurizer** 모듈에서는 계산할 기능, 승산비만 필요한지 개수도 필요한지 여부 및 기타 고급 옵션을 선택할 수 있습니다.

#### 처음부터 count 테이블 작성

"count 테이블에 대한 간략한 설명"에서 언급했듯이, 미리 작성된 count 테이블(자세한 내용은 위 섹션 참조)을 사용하는 것 외에 사용자가 처음부터 고유한 count 테이블을 작성할 수도 있습니다.

이 섹션에서는 count 테이블을 처음부터 작성하는 방법을 설명합니다. 이를 위해 아래에 표시된 **Build Count Table** 모듈을 해당 설정과 함께 사용합니다.

![Count 테이블 모듈 빌드](http://i.imgur.com/r7pP8Qq.png)

이 모듈에 대한 설정의 이후 부분은 다음과 같습니다.

![Count 테이블 모듈 빌드 설정](http://i.imgur.com/PvmSh3C.png)


**중요:** 클러스터 및 저장소 계정 설정에는 사용자와 관련된 값을 사용하세요.

**Run**을 클릭하면 선택한 클러스터에서 count 테이블을 작성할 수 있습니다. 이전에 표시된 대로 출력은 count 테이블 및 관련 메타데이터입니다. 이러한 테이블이 준비되면 실험을 빌드할 수 있습니다.


### <a name="step3"></a> 3단계: 모델 학습

이를 선택하려면 Search 상자에 "two class boosted"를 입력하고 모듈을 끌어옵니다. 아래와 같이 향상된 의사 결정 트리 학습자의 기본값을 사용합니다.

![BDT 학습자](http://i.imgur.com/dDk0Jtv.png)

기계 학습 실험을 실행하기 전에 세 가지 최종 구성 요소가 필요합니다.

첫째는 Train Model 모듈입니다. 첫 번째 포트에서는 학습자를 입력으로 사용하고 두 번째 포트에서는 train 데이터 집합을 학습에 사용합니다. 기본 모양과 모듈에서 설정해야 하는 매개 변수는 각각 아래와 같습니다.

![BDT Train Model 모듈 연결](http://i.imgur.com/szS2xBb.png)

![BDT Train Model 모듈 설정](http://i.imgur.com/nd7lHBL.png)

### <a name="step4"></a> 4단계: 테스트 데이터 집합에서 모델 점수 매기기

두 번째 구성 요소는 테스트 데이터 집합에서 점수를 매기는 방법입니다. **Score Model** 모듈을 사용하면 편리합니다. 이 모듈에서는 학습 데이터에서 학습된 모델을 입력으로 사용하고 테스트 데이터 집합을 통해 예측합니다. 다음과 같이 표시됩니다.

![Score BDT 모델 연결](http://i.imgur.com/AwIH1rH.png)

### <a name="step5"></a> 5단계: 모델 평가

마지막으로 모델 성능을 확인합니다. 일반적으로 2클래스(이진) 분류 문제의 경우 AUC를 측정하는 것이 좋습니다. 이를 시각화하려면 "Score Model" 모듈을 "Evaluate Model" 모듈에 연결합니다. **Evaluate Model** 모듈에서 **Visualize**를 클릭하면 다음과 같은 그림이 표시됩니다.

![Evaluate module BDT 모델](http://i.imgur.com/0Tl0cdg.png)

이진(또는 2클래스) 분류 문제에서 예측 정확도의 적절한 척도는 AUC입니다. 다음은 테스트 데이터 집합에서 이 모델을 사용한 결과입니다. 결과를 확인하려면 **Evaluate Model** 모듈의 출력 포트를 마우스 오른쪽 단추로 클릭한 다음 **Visualize**를 클릭합니다.

![Evaluate Model 모듈 시각화](http://i.imgur.com/IRfc7fH.png)

### <a name="step6"></a> 6단계: 모델을 웹 서비스로 게시
기계 학습 모델을 웹 서비스로 게시하는 기능에 대해 알아보겠습니다. 이 작업이 완료되면 예측해야 하는 데이터를 사용하여 웹 서비스를 호출할 수 있으며, 모델에서 이상적으로 예측을 반환합니다.

이렇게 하려면 먼저 학습한 모델을 Trained Model 개체로 저장합니다. **Train Model** 모듈을 마우스 오른쪽 단추로 클릭하고 "Save as Trained Model" 옵션을 사용하면 됩니다.

이제 웹 서비스에 대한 입력 및 출력 포트를 만듭니다. 입력 포트는 예측해야 하는 데이터와 동일한 형식으로 데이터를 가져오고, 출력 포트는 Scored Labels 및 관련 확률을 반환합니다.

#### 입력 포트에 대한 몇 가지 데이터 행 선택

이제 입력 포트에 대한 몇 가지 데이터 행을 선택하는 방법을 알아보겠습니다.

![입력 포트 데이터](http://i.imgur.com/XqVtSxu.png)

Apply SQL transformation을 사용하여 입력 포트 데이터로 사용할 10개 행만 편리하게 선택할 수 있습니다.

#### 웹 서비스
이제 웹 서비스 게시하는 데 사용할 수 있는 소규모 실험을 실행할 준비가 완료되었습니다.

#### 웹 서비스에 대한 입력 데이터 생성

count 테이블이 크기 때문에 먼저 테스트 데이터의 몇 줄을 가져와 count 기능과 함께 출력 데이터를 생성합니다. 이 데이터는 웹 서비스의 입력 데이터 형식으로 사용됩니다. 다음과 같습니다.

![BDT 입력 데이터 만들기](http://i.imgur.com/OEJMmst.png)

참고: 이제 입력 데이터 형식에 대해 **Count Featurizer** 모듈의 OUTPUT을 사용합니다. 이 실험의 실행이 완료되면 **Count Featurizer** 모듈의 출력을 Dataset으로 저장합니다. **중요:** 이 Dataset은 웹 서비스의 입력 데이터로 사용됩니다.

#### 웹 서비스 게시를 위한 실험 점수 매기기

먼저 구조를 확인합니다. 기본 구조는 학습된 모델 개체 및 이전 단계에서 **Count Featurizer** 모듈을 사용하여 생성한 입력 데이터 몇 줄을 허용하는 Score model 모델입니다. "Project Columns"를 사용하여 Scored 레이블 및 Score 확률을 표시합니다.

![Project Columns](http://i.imgur.com/kRHrIbe.png)

Project Columns 모듈을 사용하여 데이터 집합에서 데이터를 '필터링'하는 방법을 알아보는 것이 좋습니다. 아래 내용이 표시됩니다.

![Project Columns 모듈을 사용하여 필터링](http://i.imgur.com/oVUJC9K.png)

파란색 입력 및 출력 포트를 가져오려면 오른쪽 아래에서 "prepare webservice"를 클릭하면 됩니다. 이 실험을 실행하면 아래와 같이 오른쪽 아래에서 **PUBLISH WEB SERVICE** 아이콘을 클릭하여 웹 서비스를 게시할 수도 있습니다.

![웹 서비스 게시](http://i.imgur.com/WO0nens.png)

웹 서비스가 게시되면 다음과 같은 페이지로 리디렉션됩니다.

![](http://i.imgur.com/YKzxAA5.png)

왼쪽에 웹 서비스에 대한 두 개의 링크가 있습니다.

* **REQUEST/RESPONSE** 서비스(또는 RRS)는 단일 예측에 사용되며 이 워크숍에서는 이를 활용합니다. 
* **BATCH EXECUTION** 서비스(BES)는 일괄 처리 예측에 사용되며, 예측할 데이터가 Azure Blob에 있어야 합니다.

**REQUEST/RESPONSE** 링크를 클릭하면 C#, python 및 R로 미리 만든 코드를 제공하는 페이지가 나타납니다. 이 코드를 사용하여 웹 서비스를 간편하게 호출할 수 있습니다. 이 페이지의 API 키를 인증에 사용해야 합니다.

iPython notebook에서 이 python 코드를 새 셀에 복사하는 것이 편리합니다.

올바른 API 키가 있는 python 코드의 세그먼트는 다음과 같습니다.

![Python 코드](http://i.imgur.com/f8N4L4g.png)

기본 API 키를 웹 서비스의 API 키로 바꿨습니다. iPython notebook에서 이 셀에 대해 "Run"을 클릭하면 다음 응답이 나타납니다.

![iPython 응답](http://i.imgur.com/KSxmia2.png)

요청한 두 가지 테스트 예제(python 스크립트의 JSON 프레임워크)에 대해 "Scored Labels, Scored Probabilities" 형식의 응답이 제공됩니다. 여기에서는 미리 만든 코드에서 제공하는 기본값(모든 숫자 열의 경우 0, 모든 범주 열의 경우 문자열 "값")을 선택했습니다.

이것으로 Azure 기계 학습을 사용하여 대규모 데이터 집합을 처리하는 방법에 대한 설명을 마치겠습니다. TB의 데이터에서 클라우드에 웹 서비스로 배포된 예측 모델을 빌드하는 과정을 알아보았습니다.

 

<!---HONumber=58_postMigration-->