<properties 
	pageTitle="실제 Azure 데이터 과학 프로세스: HDInsight Hadoop 클러스터 사용 | Azure" 
	description="HDInsight Hadoop 클러스터를 사용하는 종단 간 Azure 데이터 과학 프로세스에 따라 공용 데이터 집합을 사용하는 모델을 구축하여 배포합니다." 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="hangzh;bradsev" /> 

                
# 실제 Azure 데이터 과학 프로세스: HDInsight Hadoop 클러스터 사용

이 자습서에서는 Azure HDInsight Hadoop 클러스터를 사용하는 종단 간 Azure 데이터 과학 프로세스 맵에 따라 공용 데이터 집합인 [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) 데이터 집합을 사용하는 모델을 구축하여 배포할 것입니다. 


## <a name="dataset"></a>NYC Taxi Trips 데이터 집합 설명

NYC Taxi Trip 데이터는 1억 7,300만 개가 넘는 개별 여정 및 각 여정의 요금으로 구성된 약 20GB의 압축된 CSV(쉼표로 구분된 값) 파일(압축되지 않은 경우 약 48GB)입니다. 각 여정 레코드는 승차 및 하차 위치, 익명 처리된 hack(기사) 면허증 번호 및 medallion(택시의 고유 ID) 번호를 포함합니다. 데이터는 2013년의 모든 여정을 포괄하며, 매월 다음 두 개의 데이터 집합으로 제공됩니다.

1.  'trip_data' CSV 파일은 승객 수, 승차 및 하차 지점, 여정 기간, 여정 거리 등 여정 세부 정보를 포함합니다. 다음은 몇 가지 샘플 레코드입니다.

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2.  'trip_fare' CSV 파일은 지불 유형, 금액, 추가 요금 및 세금, 팁 및 통행료, 총 지불 금액 등 각 여정의 요금에 대한 세부 정보를 포함합니다. 다음은 몇 가지 샘플 레코드입니다.

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

trip_data와 trip_fare를 조인할 고유 키는 medallion, hack_licence 및 pickup_datetime 필드로 구성됩니다.

각각 12개의 여정  'trip_data' 및 'trip_fare' 파일이 있습니다. 파일 이름의 숫자 1~12는 여정이 이루어진 월을 나타냅니다. 

## <a name="mltasks"></a>예측 작업의 예제
데이터에 접근할 때 분석을 기반으로 수행할 예측의 종류를 결정하면 프로세스에 포함해야 하는 작업을 명확하게 확인할 수 있습니다.
다음은 이 연습에서 우리가 해결할 예측 문제에 대한 3가지 예제입니다. 예제의 공식은 *tip_amount*를 기반으로 합니다.

1. **이진 분류**: 여정에 대해 팁이 지불되었는지 여부를 예측합니다. 즉, *tip_amount*가 $0보다 크면 지불된 것이고, *tip_amount*가 $0이면 지불되지 않은 것입니다.

2. **다중 클래스 분류**: 여정에 대해 지불된 팁 금액의 범위를 예측합니다. *tip_amount*를  5개의 bin 또는 클래스로 나눕니다.
	
		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **회귀 작업**: 여정에 대해 지불된 팁의 금액을 예측합니다.  


## <a name="setup"></a>Azure 데이터 과학 환경 설정

[환경 계획](machine-learning-data-science-plan-your-environment.md) 가이드에서 볼 수 있듯이 Azure에서 NYC Taxi Trips 데이터 집합으로 작업할 때 선택할 수 있는 여러 방법이 있습니다.

- Azure Blob의 데이터로 작업한 다음 Azure 기계 학습에서 모델링
- SQL Server 데이터베이스로 데이터를 로드한 다음 Azure 기계 학습에서 모델링
- 데이터를 HDInsight Hive 테이블에 로드한 후 Azure 기계 학습에서 모델링

데이터 집합 크기, 데이터 원본 위치 및 선택한 Azure 대상 환경에 따라 이 시나리오는 [시나리오 7: Azure HDInsight Hadoop 클러스터의 대상 Hive에 있는 로컬 파일의 대용량 데이터 집합](machine-learning-data-science-plan-sample-scenarios.md#largedbtohive)과 비슷할 수 있습니다.

Azure HDInsight Hadoop 클러스터를 사용하는, 보다 구체적으로 말해서 Hive 테이블 및 쿼리를 사용하여 데이터를 저장하고 처리하는 방법을 위한 Azure 데이터 과학 환경을 설정하려면 다음 단계를 완료하세요.

1. [저장소 계정 만들기](storage-whatis-account.md)

2. [Azure 기계 학습 작업 영역 만들기](machine-learning-create-workspace.md)

3. [데이터 과학 **Windows** 가상 컴퓨터 프로비전](machine-learning-data-science-setup-virtual-machine.md).

	> [AZURE.NOTE] 가상 컴퓨터 설정 프로세스가 진행되는 동안 샘플 스크립트가 데이터 과학 가상 컴퓨터에 다운로드됩니다. VM 설치 후 스크립트가 완료되면 *C:\Users<user_name>\Documents\Data Science Scripts*의 VM 문서 라이브러리에 샘플이 있을 것입니다.  아래의 **Sample Scripts**가 바로 이 폴더입니다. **Sample Scripts** 폴더에 있는 파일 중 확장명이 .hql인 파일에 샘플 Hive 쿼리가 들어 있습니다. 이 폴더의 경로에서 참조되는 *<user_name>*은 Azure 사용자 이름이 아니라 VM의 Windows 로그인 이름입니다.

4. [데이터 과학용 Azure HDInsight Hadoop 클러스터 사용자 지정](machine-learning-data-science-customize-hadoop-cluster.md). 이 단계에서는 모든 노드에 64비트 Anaconda Python 2.7이 설치된 Azure HDInsight Hadoop 클러스터를 만듭니다. 사용자 지정된 Hadoop 클러스터를 만든 후에는 이 사용자 지정 항목에 설명된 절차에 따라 Azure 포털에서 Hadoop 클러스터의 헤드 노드에 대한 원격 액세스를 설정합니다.


## <a name="getdata"></a>공용 원본에서 데이터 가져오기

해당 공용 위치에서 [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) 데이터 집합을 가져오려면 [Azure Blob 저장소에서 데이터 이동](machine-learning-data-science-move-azure-blob.md)에 설명된 방법 중 하나를 사용하여 데이터를 새 가상 컴퓨터에 복사하면 됩니다.

AzCopy를 사용하여 데이터를 복사하려면

1. VM(가상 컴퓨터)에 로그인합니다.

2. VM의 데이터 디스크에 새 디렉터리를 만듭니다(참고: VM과 함께 제공된 임시 디스크를 데이터 디스크로 사용하지 마세요).

3. 명령 프롬프트 창에서 다음 AzCopy 명령을 실행하고, <path_to_data_folder>를 (2)에서 만든 데이터 폴더 경로로 바꿉니다.

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxitrip /Dest:<path_to_data_folder> /S

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxifare /Dest:<path_to_data_folder> /S

	AzCopy가 완료되면 데이터 폴더에 총 24개의 압축된 CSV 파일(trip_data 파일 12개와 trip_fare 12개)이 있어야 합니다.

	>[AZURE.NOTE] *Source:https://getgoing.blob.core.windows.net/nyctaxitrip* 및 *Source:https://getgoing.blob.core.windows.net/nyctaxifare*는 압축을 푼 NYC Taxi 데이터를 사용자와 공유하는 데 사용되는 두 공용 Azure 컨테이너를 지정합니다. 이 두 공용 Azure 컨테이너는 액세스 키가 없어도 읽을 수 있습니다. 

## <a name="upload"></a>Azure HDInsight Hadoop 클러스터의 기본 컨테이너에 데이터 업로드

가상 컴퓨터의 명령 프롬프트 또는 Windows PowerShell 창에서 다음 AzCopy 명령을 실행하고, 다음 매개 변수를 Hadoop 클러스터를 만들 때 지정한 값으로 대체합니다. 따라서 다음과 같이 대체합니다.

* ***&#60;path_to_data_folder>***를 이전 섹션에서 만든 데이터 폴더로 대체 
* ***&#60;storage account name of Hadoop cluster>***를 클러스터에서 사용하는 저장소 계정으로 대체
* ***&#60;default container of Hadoop cluster>***를 클러스터에서 사용하는 기본 컨테이너로 대체
* ***&#60;storage account key>***를 클러스터에서 사용하는 저장소 계정의 키로 대체

이 명령에서 참조한 ***nyctaxitripraw*** 또는 ***nyctaxifareraw***가 없으면 컨테이너에 자동으로 생성됩니다. 

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
		
		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

## <a name="#hive-db-tables"></a>월별로 분할된 Hive 데이터베이스 및 테이블 만들기

이 연습에 사용되는 Hive 쿼리에 액세스하여 실행하려면 Hadoop 클러스터의 헤드 노드에 로그인하고, 헤드 노드의 바탕 화면에서 Hadoop 명령줄을 열고, 명령을 입력하여 Hive 디렉터리로 전환합니다.

    cd %hive_home%\bin

이러한 절차에 대한 도움 또는 다른 방법이 필요한 경우 [Hadoop 명령줄에서 직접 Hive 쿼리 제출](machine-learning-data-science-process-hive-tables.md#submit) 섹션을 참조하세요. 

[Azure 데이터 과학 환경 설정](#setup) 지침에 따라 Azure 가상 컴퓨터를 만들었다면 **Sample Scripts** 폴더에 Hive 쿼리 예제가 이미 다운로드되어 있을 것입니다. 또는 사용자가 [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)에서 .hql 파일을 체크아웃하는 방법도 있습니다. 

Hive 쿼리를 .hql 파일로 제출하려면 .hql 파일을 Hadoop 클러스터의 헤드 노드에 있는 로컬 디렉터리로 전송해야 합니다. 다음 예제에서는 .hql 파일이 헤드 노드의 ***C:\temp&#92;*** 디렉터리에 전송된 것으로 가정합니다.

다음은 Hive 데이터베이스 ***nyctaxidb***와 테이블 ***trip*** 및 ***fare***를 만드는 ***C:\temp\sample_hive_create_db_and_tables.hql*** 파일의 콘텐츠입니다.

	create database if not exists nyctaxidb;

	create external table if not exists nyctaxidb.trip
	( 
	    medallion string, 
	    hack_license string,
	    vendor_id string, 
	    rate_code string, 
	    store_and_fwd_flag string, 
	    pickup_datetime string, 
	    dropoff_datetime string, 
	    passenger_count int, 
	    trip_time_in_secs double, 
	    trip_distance double, 
	    pickup_longitude double, 
	    pickup_latitude double, 
	    dropoff_longitude double, 
	    dropoff_latitude double)  
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

	create external table if not exists nyctaxidb.fare 
	( 
	    medallion string, 
	    hack_license string, 
	    vendor_id string, 
	    pickup_datetime string, 
	    payment_type string, 
	    fare_amount double, 
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double)
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

헤드 노드의 Hadoop 명령줄에 다음 명령을 입력하여 다음 Hive 쿼리를 제출합니다.
	
	hive -f "C:\temp\sample_hive_create_db_and_tables.hql"


## <a name="#load-data"></a>분할된 테이블에 데이터 로드
Hadoop 명령줄에서 다음 PowerShell 명령을 실행하여 월별로 분할된 Hive 테이블에 데이터를 로드합니다.

	for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

다음은 검사할 *sample_hive_load_data_by_partitions.hql* 파일의 콘텐츠입니다.

	LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
	LOAD DATA INPATH 'wasb:///nyctaxifareraw/fare_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

### <a name="#show-db"></a>HDInsight Hadoop 클러스터에서 데이터베이스 표시

HDInsight Hadoop 클러스터에서 만든 데이터베이스를 Hadoop 명령줄 창 내에 표시하려면 Hadoop 명령줄에서 다음 명령을 실행합니다.

	hive -e "show databases;"

### <a name="#show-tables"></a>nyctaxidb 데이터베이스에서 테이블 표시 
	
nyctaxidb 데이터베이스에서 테이블을 표시하려면 Hadoop 명령줄에서 다음 명령을 실행합니다.
	
	hive -e "show tables in nyctaxidb;"
   
## <a name="#explore-hive"></a>Hive에서 데이터 탐색 및 기능 엔지니어링
Hive 테이블에 로드된 데이터에 대한 데이터 탐색 및 기능 엔지니어링 작업은 Hive 쿼리를 사용하여 수행할 수 있습니다. 다음은 이 섹션에서 설명할 이러한 작업의 예입니다.

- 두 테이블의 상위 10개 레코드를 봅니다.
- 다양한 기간에 걸쳐 몇몇 필드의 데이터 분포를 탐색합니다.
- 경도 및 위도 필드의 데이터 품질을 조사합니다.
- **tip_amount**에 따라 이진 및 다중 클래스 분류 레이블을 생성합니다.
- 직접 여정 거리를 계산하여 기능을 생성합니다.
- 두 테이블을 조인하고 모델을 빌드하는 데 사용할 무작위 샘플을 추출합니다.

예측을 위해 Hive에서 이러한 데이터 탐색 및 기능 엔지니어링을 완료하면 Azure 기계 학습을 진행할 준비가 완료됩니다. 마지막 Hive 쿼리를 저장하여 데이터를 추출 및 샘플링한 다음 쿼리를 복사하여 Azure 기계 학습의 판독기 모듈에 직접 붙여 넣을 수 있습니다. 그러면 이 데이터에 대한 모델을 구축할 준비가 완료됩니다.

### 탐색: trip 테이블의 상위 10개 레코드 보기

사용자는 각 테이블의 상위 10개 레코드를 추출하여 데이터 스키마의 특성과 데이터의 모양을 이해할 수 있습니다. Hadoop 명령줄 콘솔에서 다음 두 쿼리를 따로 실행하여 레코드를 검사합니다.

*table _trip_*의 상위 10개 레코드를 가져오려면

	hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
    
*table _fare_*의 상위 10개 레코드를 가져오려면
	
	hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

### 탐색: 각 12개 파티션의 각 레코드 수 보기

각 월별 파티션 12개의 레코드 수를 보려면 Hadoop 명령줄 콘솔에서 다음 명령을 실행 합니다.
	
	hive -e "select month, count(*) from nyctaxidb.trip group by month;"

### 탐색: medallion별 여정 분포

이 예제에서는 지정된 기간 내의 여정이 100개가 넘는 medallion(택시 번호)을 식별합니다. 쿼리는 파티션 변수 **month**의 영향을 받기 때문에 테이블을 분할하면 쿼리 성능이 개선됩니다. 

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql"

다음은 검사할 *sample_hive_trip_count_by_medallion.hql* 파일의 콘텐츠입니다.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100 
	ORDER BY med_count desc;

### 탐색: medallion 및 hack_license별 여정 분포

다음은 테이블의 여러 열을 사용하여 그룹화하는 예제입니다. 이 예에서는 medallion 및 hack_license 열을 사용하여 그룹화합니다. Hadoop 명령줄 콘솔에서 다음 명령을 실행합니다.

	hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql"

다음은 검사할 *sample_hive_trip_count_by_medallion_license.hql* 파일의 콘텐츠입니다.
	
    SELECT medallion, hack_license, COUNT(*) as trip_count
	FROM nyctaxidb.fare
	WHERE month=1
	GROUP BY medallion, hack_license
	HAVING trip_count > 100
	ORDER BY trip_count desc;

### 데이터 품질 평가: 경도 및/또는 위도가 잘못된 레코드 확인

이 예제에서는 경도 및/또는 위도 필드에 유효하지 않은 값(라디안이 -90도~90도에 속해야 함)이 포함되거나 (0, 0) 좌표가 있는지 조사합니다.

Hadoop 명령줄 콘솔에서 다음 명령을 실행합니다.

	hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

이 명령에 포함된 *-S* 인수는 Hive 맵/감소 작업의 상태 화면 인쇄를 표시하지 않습니다. 이렇게 하면 Hive 쿼리 출력의 화면 인쇄를 좀 더 쉽게 읽을 수 있으므로 유용합니다. 

다음은 검사할 *sample_hive_quality_assessment.hql* 파일의 콘텐츠입니다.

    	SELECT COUNT(*) FROM nyctaxidb.trip
    	WHERE month=1
    	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'));

### 탐색: 팁을 받은 여정과 팁을 받지 않은 여정의 빈도

이 예제에서는 지정된 기간 동안(또는 전체 연도를 포괄하는 경우 전체 데이터 집합에서) 왕복 여정 수와 비왕복 여정 수를 확인합니다. 이 분포는 나중에 이진 분류 모델링에 사용할 이진 레이블 분포를 반영합니다.

Hadoop 명령줄 콘솔에서 다음 명령을 실행합니다.

	hive -f "C:\temp\sample_hive_tipped_frequencies.hql"

다음은 검사할 *sample_hive_tipped_frequencies.hql* 파일의 콘텐츠입니다.

    SELECT tipped, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

### 탐색: 팁 빈도 범위

이 예제에서는 지정된 기간(또는 해당 연도 전체를 다룰 경우 전체 데이터 집합)의 팁 범위 분포를 계산합니다. 이는 나중에 다중 클래스 분류 모델링에 사용할 레이블 클래스의 분포입니다.

Hadoop 명령줄 콘솔에서 다음 명령을 실행합니다.

	hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

다음은 검사할 *sample_hive_tip_range_frequencies.hql*의 콘텐츠입니다.

	SELECT tip_class, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount=0, 0, 
            if(tip_amount>0 and tip_amount<=5, 1, 
            if(tip_amount>5 and tip_amount<=10, 2, 
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

### 탐색: 직접 거리를 계산하여 여정 거리와 비교

이 예제에서는 직접 여정 거리(단위: 마일)를 계산합니다. 앞서 설명한 데이터 품질 평가 쿼리를 사용하여 유효한 좌표로만 결과가 제한됩니다.

Hadoop 명령줄 콘솔에서 다음 명령을 실행합니다.

	hive -f "C:\temp\sample_hive_trip_direct_distance.hql"

다음은 검사할 *sample_hive_trip_direct_distance.hql* 파일의 콘텐츠입니다.

    set R=3959;
    set pi=radians(180);
	
	insert overwrite directory 'wasb:///queryoutputdir'
    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
        pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
    from nyctaxidb.trip 
    where month=1 
        and pickup_longitude between -90 and -30
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and -30
        and dropoff_latitude between 30 and 90;

쿼리가 완료되면 Hadoop 클러스터 기본 컨테이너의 Azure blob ***queryoutputdir/000000_0***에 결과가 작성됩니다. Azure 저장소 탐색기를 사용하는 경우 다음 그림에 표시된 것처럼 Hadoop 클러스터의 기본 컨테이너에 이 blob가 표시되어야 합니다.

![Create workspace][2]

필터(빨간색 상자로 강조 표시됨)를 적용하여 이름에 지정된 문자가 포함된 blob만 검색할 수 있습니다. 

쿼리가 완료되면 Azure SDK를 사용하여 Azure blob에서 Pandas 데이터 프레임으로 쿼리 출력을 읽어 들여서 추가 탐색 및 처리를 수행할 수 있습니다. Azure blob를 Pandas 데이터 프레임으로 읽어 들이는 방법에 대한 자세한 내용은 [데이터 과학 환경에서 Azure Blob 데이터 처리](machine-learning-data-science-process-data-blob.md)를 참조하세요. 
	
### 모델 구축에 사용할 데이터를 준비합니다.

이 섹션에 제공된 쿼리는 **nyctaxidb.trip**과 **nyctaxidb.fare**를 조인하고, 이진 분류 레이블 **tipped** 및 다중 클래스 분류 레이블 **tip_class**를 생성합니다. 이 쿼리를 복사하여 [Azure 기계 학습 스튜디오](https://studio.azureml.net) 판독기 모듈에 직접 붙여 넣으면 Azure에서 **Hive 쿼리**로 직접 데이터를 수집할 수 있습니다. 또한 이 쿼리는 위도 및 경도 좌표가 잘못된 레코드를 제외합니다.

이 쿼리는 Hive에 포함된 UDF를 직접 적용하여 Hive 레코드로 _pickup_datetime_ 필드의 시간, 주, 평일(1은 월요일, 7은 일요일을 나타냄)을 포함한 여러 기능을 생성합니다. Hive에 포함된 UDF 전체 목록은 [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)에서 확인할 수 있습니다.

또한 이 쿼리는 쿼리 결과가 Azure 기계 학습 스튜디오에 적합하도록 데이터를 다운 샘플링합니다. 스튜디오로 가져오는 비율은 약 1%에 불과합니다.

Hadoop 명령줄 콘솔에서 다음 명령을 실행하여 쿼리를 제출합니다.

	hive -f "C:\temp\sample_hive_prepare_for_aml.hql"

다음은 검사할 *sample_hive_prepare_for_aml.hql* 파일의 콘텐츠입니다.
	
    select 
        t.medallion, 
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        f.payment_type, 
        f.fare_amount, 
        f.surcharge, 
        f.mta_tax, 
        f.tip_amount, 
        f.tolls_amount, 
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
            if(tip_amount>0 and tip_amount<=5,1,
            if(tip_amount>5 and tip_amount<=10,2,
            if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
    from
    (
        select medallion, 
            hack_license,
            vendor_id,
            rate_code,
            store_and_fwd_flag,
            pickup_datetime,
            dropoff_datetime,
            passenger_count,
            trip_time_in_secs,
            trip_distance,
            pickup_longitude,
            pickup_latitude,
            dropoff_longitude,
            dropoff_latitude,
            rand() as sample_key 
        from nyctaxidb.trip
        where pickup_latitude between 30 and 60
            and pickup_longitude between -90 and -60
            and dropoff_latitude between 30 and 60
            and dropoff_longitude between -90 and -60
    )t
    join
    (
        select 
            medallion, 
            hack_license, 
            vendor_id, 
            pickup_datetime, 
            payment_type, 
            fare_amount, 
            surcharge, 
            mta_tax, 
            tip_amount, 
            tolls_amount, 
            total_amount
        from nyctaxidb.fare 
    )f
    on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
    where t.sample_key<=0.01

## <a name="mlmodel"></a>Azure 기계 학습에서 모델 구축

이제 [Azure 기계 학습](https://studio.azureml.net)에서 모델 빌드 및 모델 배포를 진행할 준비가 완료되었습니다. 이제 위에서 파악된 다음과 같은 예측 문제를 해결하는 데 데이터를 사용할 수 있습니다.

1. **이진 분류**: 여정에 대해 팁이 지불되었는지 여부를 예측합니다.

2. **다중 클래스 분류**: 이전에 정의된 클래스를 사용하여 여정에 대해 지불된 팁 금액 범위를 예측합니다. 

3. **회귀 작업**: 여정에 대해 지불된 팁의 금액을 예측합니다.  

모델링 연습을 시작하려면 Azure 기계 학습 작업 영역에 로그인합니다. 기계 학습 작업 영역을 아직 만들지 않은 경우 [Azure 기계 학습 작업 영역 만들기](machine-learning-create-workspace.md)를 참조하세요.

1. Azure 기계 학습을 시작하려면 [Azure 기계 학습 스튜디오란?](machine-learning-what-is-ml-studio.md)을 참조하세요.

2. [Azure 기계 학습 스튜디오](https://studio.azureml.net)에 로그인합니다.

3. 스튜디오 홈 페이지에서는 다양한 정보, 비디오, 자습서, 모듈 참조 링크 및 기타 리소스를 제공합니다. Azure 기계 학습에 대한 자세한 내용은 [Azure 기계 학습 설명서 센터](http://azure.microsoft.com/documentation/services/machine-learning/)를 참조하세요.

일반적인 학습 실험은 다음으로 구성됩니다.

1. **+새** 실험을 만듭니다.
2. Azure 기계 학습으로 데이터를 가져옵니다.
3. 필요에 따라 데이터를 전처리, 변환 및 조작합니다.
4. 필요에 따라 기능을 생성합니다.
5. 데이터를 학습/유효성 검사/테스트 데이터 집합으로 분할하거나, 각각에 대한 별도의 데이터 집합을 만듭니다.
6. 해결할 학습 문제에 따라 하나 이상의 기계 학습 알고리즘(예: 이진 분류, 다중 클래스 분류, 회귀)을 선택합니다.
7. 학습 데이터 집합을 사용하여 하나 이상의 모델을 학습합니다.
8. 학습된 모델을 사용하여 유효성 검사 데이터 집합의 점수를 매깁니다.
9. 모델을 평가하여 학습 문제에 대한 관련 메트릭을 계산합니다.
10. 모델을 미세 조정하고 배포할 가장 적합한 모델을 선택합니다.

이 연습에서는 이미 Hive에서 데이터를 탐색 및 엔지니어링하고(1~4단계) Azure 기계 학습에서 수집할 샘플 크기를 결정했습니다. 예측 모델 중 하나 이상을 빌드하려면 다음을 수행합니다.

1. **데이터 입력 및 출력** 섹션에서 제공되는 **판독기** 모듈을 사용하여 Azure 기계 학습으로 데이터를 가져옵니다. 자세한 내용은 [판독기](http://msdn.microsoft.com/library/dn784775) 모듈 참조 페이지를 참조하세요.

	![Create workspace][15]

2. **속성** 패널에서 **데이터 원본**으로 **Hive 쿼리**를 선택합니다.

3. 다음과 같이 HDInsight Hadoop 클러스터 정보를 입력합니다. **Azure 저장소 계정 이름**은 HDInsight Hadoop 클러스터를 만드는 데 사용되는 저장소 계정이어야 하고, **Azure 컨테이너 이름**은 Hadoop 클러스터의 기본 컨테이너여야 합니다. 

	![Create workspace][11]

4. **Hive 데이터베이스 쿼리** 편집 텍스트 영역에서 필요한 데이터베이스 필드를 추출하는 쿼리(레이블과 같은 모든 계산된 필드 포함)를 붙여 넣고 데이터를 원하는 샘플 크기로 다운 샘플링합니다.

아래 그림은 Hive 쿼리에서 직접 데이터를 읽는 이진 분류 실험 예제입니다. 다중 클래스 분류 및 회귀 문제에 대한 유사한 실험을 생성할 수 있습니다.

![Create workspace][12]

> [AZURE.IMPORTANT] 이전 섹션에 제공된 모델링 데이터 추출 및 샘플링 쿼리 예제에서는 **세 가지 모델링 연습에 대한 모든 레이블이 쿼리에 포함되어 있습니다**. 각 모델링 연습의 중요한(필수) 단계는 다른 두 문제에 대한 필요 없는 레이블 및 다른 모든 **목표 누설**을 **제외**하는 것입니다. 예를 들어 이진 분류를 사용할 때는 레이블 **tipped**를 사용하고, **tip_class**, **tip_amount** 및 **total_amount** 필드를 제외합니다. 이러한 필드는 지불된 팁을 의미하므로 목표 누설입니다.

> 이 실험에서 첫 번째 **메타데이터 편집기** 모듈은 판독기 모듈의 출력 데이터에 열 이름을 추가합니다. Hive 쿼리에서 데이터를 읽는 판독기 모듈은 출력 데이터에 대한 열 이름을 만들지 않기 때문에 이 모듈이 필요합니다.. 

> 필요 없는 열 및/또는 목표 누설을 제외하려면 **프로젝트 열** 모듈 또는 **메타데이터 편집기**를 사용하면 됩니다. 자세한 내용은 [프로젝트 열](http://msdn.microsoft.com/library/dn784740) 및 [메타데이터 편집기](http://msdn.microsoft.com/library/dn784761) 참조 페이지를 참조하세요.

## <a name="mldeploy"></a>Azure 기계 학습에서 모델 배포

모델이 준비된 경우 실험에서 직접 웹 서비스로 배포할 수 있습니다. Azure ML 웹 서비스 게시에 대한 자세한 내용은 [Azure 기계 학습 API 서비스 작업](machine-learning-overview-of-azure-ml-process.md)을 참조하세요.

새 웹 서비스를 배포하려면 다음을 수행해야 합니다.

1. 점수 매기기 실험을 만듭니다.
2. 웹 서비스를 게시합니다.

**완료된** 학습 실험에서 점수 매기기 실험을 만들려면 아래쪽 작업 모음에서 **점수 매기기 실험 만들기**를 클릭합니다.

![Azure Scoring][13]

Azure 기계 학습에서는 학습 실험의 구성 요소를 기반으로 점수 매기기 실험을 만듭니다. 특히 다음 작업을 수행합니다.

1. 학습된 모델을 저장하고 모델 학습 모듈을 제거합니다.
2. 필요한 입력 데이터 스키마를 나타내는 논리적 **입력 포트**를 식별합니다.
3. 필요한 웹 서비스 출력 스키마를 나타내는 논리적 **출력 포트**를 식별합니다.

점수 매기기 실험을 만들 때 필요에 따라 검토하고 조정합니다. 일반적인 조정은 입력 데이터 집합 및/또는 쿼리를 레이블 필드를 제외한 것으로 바꾸는 것입니다. 레이블 필드는 서비스를 호출할 때 사용할 수 없기 때문입니다. 또한 입력 데이터 집합 및/또는 쿼리 크기를 입력 스키마를 나타내는 데 충분한 정도의 몇몇 레코드로 줄이는 것이 좋습니다. 출력 포트의 경우 일반적으로 **프로젝트 열** 모듈을 사용하여 모든 입력 필드를 제외하고 **점수가 매겨진 레이블** 및 **점수가 매겨진 확률**만 출력에 포함합니다.

샘플 점수 매기기 실험은 아래 그림에 나와 있습니다. 게시할 준비가 되면 아래쪽 작업 모음에서 **웹 서비스 게시** 단추를 클릭합니다.

![Create workspace][14]

정리하자면, 이 연습 자습서에서는 대형 공용 데이터 집합을 작업하는 Azure 데이터 과학 환경을 만들었습니다. 데이터 과학 프로세서에서 가장 먼저 데이터를 수집하고, 데이터 탐색 및 기능 엔지니어링 작업을 수행한 후 모델을 학습하고 Azure 기계 학습 웹 서비스를 게시했습니다.

## 라이선스 정보

이 샘플 연습 및 함께 제공되는 스크립트는 Microsoft에서 MIT 라이선스에 따라 공유하고 있습니다. 자세한 내용은 GitHub의 샘플 코드 디렉터리에 있는 LICENSE.txt 파일을 참조하세요.

## 참조

*	[Andrés Monroy NYC Taxi Trips 다운로드 페이지](http://www.andresmh.com/nyctaxitrips/)  
*	[Chris Whong의 FOILing NYC Taxi Trip 데이터](http://chriswhong.com/open-data/foil_nyc_taxi/)   
*	[NYC Taxi and Limousine Commission Research and Statistics](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!--HONumber=49-->