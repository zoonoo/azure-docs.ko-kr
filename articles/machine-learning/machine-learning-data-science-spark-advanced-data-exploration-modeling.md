<properties
	pageTitle="고급 Spark로 데이터 탐색 및 모델링 | Microsoft Azure"
	description="TBD."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,deguhath,gokuma"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="deguhath;bradsev" />

# 고급 Spark로 데이터 탐색 및 모델링 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

이 연습에서는 HDInsight Spark를 사용하여 데이터 탐색 및 이진 분류를 수행하며 교차 유효성 검사 및 하이퍼 매개 변수 최적화를 사용하는 회귀 모델링 작업을 수행하여 NYC Taxi Trip 및 요금 2013 데이터 집합 샘플에서 모델을 학습합니다. 처리를 위한 HDInsight Spark 클러스터와 데이터 및 모델을 저장하는 Azure Blob을 사용하여 [데이터 과학 프로세스](http://aka.ms/datascienceprocess)의 단계를 종단 간 안내합니다. 프로세스는 Azure 저장소 Blob에서 가져온 데이터를 탐색하고 시각화한 다음 데이터를 준비하여 예측 모델을 빌드합니다. Python은 솔루션을 코딩하고 관련 차트를 표시하는 데 사용합니다. 이러한 모델은 Spark MLlib 도구 키트를 사용하여 이진 분류 및 회귀 모델링 작업을 수행합니다.

- **이진 분류** 작업은 여정에 대해 팁이 지불되었는지 여부를 예측합니다. 
- **회귀** 작업은 다른 팁 기능을 기반으로 하는 팁의 금액을 예측합니다. 

또한 모델링 단계는 각 모델 유형을 학습, 평가 및 저장하는 방법을 보여주는 코드를 포함합니다. 이 항목에서는 [Spark로 데이터 탐색 및 모델링](machine-learning-data-science-spark-data-exploration-modeling.md) 항목과 일부 동일한 측면을 다루지만 그중에서도 "고급" 항목을 다루고 교차 유효성 검사와 하이퍼 매개 변수 비우기도 함께 사용하여 정확한 분류 및 회귀 모델을 최적으로 학습합니다.

**CV(교차 유효성 검사)**는 알려진 데이터 집합에서 학습된 모델이 학습되지 않은 데이터 집합의 기능 예측을 얼마나 잘 일반화하는지 평가하는 기술입니다. 이 기술의 일반 개념은 알려진 데이터의 데이터 집합에서 모델을 학습한 다음 독립된 데이터 집합에 대해 예측 정확도를 테스트하는 것입니다. 여기에 사용된 일반적인 구현은 데이터 집합을 K 접기로 나눈 다음 접기 중 하나를 제외한 모든 접기에서 라운드 로빈 방식으로 모델을 학습하는 것입니다.

**하이퍼 매개 변수 최적화**는 학습 알고리즘에 대한 하이퍼 매개 변수 집합을 선택하는 문제이며, 일반적으로 독립된 데이터 집합에서의 알고리즘 성능 측정값을 최적화하는 것을 목표로 합니다. **하이퍼 매개 변수**는 모델 학습 절차 외부에서 지정해야 하는 값입니다. 이러한 값에 대한 가정은 모델의 유연성 및 정확도에 영향을 줄 수 있습니다. 의사 결정 트리에는 원하는 깊이와 트리의 리프 수와 같은 하이퍼 매개 변수가 있습니다. SVM(Support Vector Machine)은 오분류 페널티 조건을 설정해야 합니다.

여기에 사용된 하이퍼 매개 변수 최적화를 수행하는 일반적인 방법은 그리드 검색 또는 **매개 변수 비우기** 입니다. 이 구성으로 학습 알고리즘에 대한 하이퍼 매개 변수 공간의 지정된 하위 집합 값으로 철저한 검색을 수행합니다. 교차 유효성 검사는 그리드 검색 알고리즘에 의해 생성되는 최적의 결과를 분류하는 성능 메트릭을 제공할 수 있습니다. 하이퍼 매개 변수 비우기와 함께 사용된 CV를 통해 데이터 학습 모델의 과잉 맞춤과 같은 문제를 제한하여 모델이 학습 데이터가 추출되는 일반 데이터 집합에 적용할 용량을 유지하도록 합니다.

사용하는 모델은 로지스틱 및 선형 회귀, 임의 포리스트 및 그라데이션 향상된 트리를 포함합니다.

- [SGD로 선형 회귀](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD)는 SGD(Stochastic Gradient Descent) 메서드를 사용하는 선형 회귀 모델이며 최적화 및 기능에 대해 크기를 조정하여 결재된 팁 금액을 예측합니다. 
- [LBFGS로 로지스틱 회귀 분석](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) 또는 "로짓" 회귀는 종속 변수가 데이터 분류를 수행하는 범주인 경우 사용할 수 있는 회귀 모델입니다. LBFGS는 제한된 양의 컴퓨터 메모리를 사용하여 BFGS(Broyden–Fletcher–Goldfarb–Shanno) 알고리즘을 비슷하게 만들고 기계 학습에서 널리 사용되는 준 뉴턴 최적화 알고리즘입니다.
- [임의 포리스트](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests)는 결정 트리의 결합체입니다. 과잉 맞춤의 위험을 줄이기 위해 많은 결정 트리를 결합합니다. 임의 포리스트는 회귀 및 분류에 사용되며 범주 기능을 처리하고 다중 클래스 분류 설정으로 확장하고 기능 크기 조정을 필요로 하지 않으며 비선형 및 기능 상호 작용을 캡처할 수 있습니다. 임의 포리스트는 분류 및 회귀에 대한 매우 성공적인 기계 학습 모델 중 하나입니다.
- [GBT](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts)(그라데이션 승격 트리)는 결정 트리의 결합체입니다. GBT는 기능 손실을 최소화하기 위해 결정 트리를 반복적으로 학습합니다. GBT는 회귀 및 분류에 사용되며 범주 기능을 처리하고 기능 크기 조정을 필요로 하지 않으며 비선형 및 기능 상호 작용을 캡처할 수 있습니다. 또한 다중 클래스 분류 설정에도 사용할 수 있습니다.

이진 분류 문제에 대한 CV 및 하이퍼 매개 변수 비우기를 사용하는 모델링 예제가 표시됩니다. 더 간단한 예제(매개 변수 비우기 없음)가 회귀 작업에 대한 주요 항목으로 표시됩니다. 하지만 부록에서는 선형 회귀에 대해 탄력적 net을 사용한 유효성 검사와 임의 포리스트 회귀에 대해 사용한 매개 변수 비우기가 있는 CV도 표시됩니다. **탄력적 net**은 L1 및 L2 메트릭을 선형으로 결합하는 선형 회귀 모델을 [lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) 및 [ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization) 메서드의 단점으로 맞추기 위한 정칙 회귀 메서드입니다.



>[AZURE.NOTE] Spark MLlib 도구 키트가 큰 데이터 집합에서 작동하도록 디자인되었지만 해당 모델링 기능을 보여 줄 목적으로 편의상 비교적 작은 샘플을 사용합니다(30Mb보다 작은 170,000개 행인 원래 NYC 데이터 집합의 약 0.1%를 사용함). 여기에 제공한 연습은 2개의 작업자 노드를 가진 HDInsight 클러스터에서 효율적으로 실행됩니다(약 10분 동안). 동일한 코드를 약간만 수정하면 데이터를 메모리에서 캐시하고 클러스터 크기를 변경하도록 적절하게 수정하여 더 큰 데이터 집합을 처리하는 데 사용될 수 있습니다.


## 필수 조건

이 연습을 시작하려면 Azure 계정 및 HDInsight Spark 클러스터, 버전 Spark 1.5.2(HDI 3.3)가 필요합니다. 이러한 요구 사항, 여기에 사용한 NYC 2013 Taxi 데이터에 대한 설명 및 Spark 클러스터의 Jupyter Notebook에서 코드를 실행하는 방법에 관한 지침은 [Azure HDInsight에서 Spark를 사용하는 데이터 과학 개요](machine-learning-data-science-spark-overview.md)를 참조하세요. 이 항목에서 코드 샘플을 포함하는 **machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb** Notebook은 [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Python)에서 사용할 수 있습니다.


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## 설정: 저장소 위치, 라이브러리 및 Spark 컨텍스트

Spark는 Azure 저장소 Blob(WASB라고도 함)를 읽고 쓸 수 있습니다. 따라서 Spark 및 WASB에 다시 저장된 결과를 사용하여 해당 저장소에 저장된 기존 데이터를 처리할 수 있습니다.

모델 또는 파일을 WASB에 저장하려면 경로를 올바르게 지정해야 합니다. "wasb///"로 시작하는 경로를 사용하여 Spark 클러스터에 연결된 기본 컨테이너를 참조할 수 있습니다. 다른 위치를 “wasb://”에서 참조합니다.

### WASB의 저장소 위치에 대한 디렉터리 경로를 설정합니다.

다음 코드 샘플은 읽을 데이터의 위치 및 모델 출력이 저장될 모델 저장소 디렉터리에 대한 경로를 지정합니다.

	# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

	# LOCATION OF TRAINING DATA
	taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
	modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

	# PRINT START TIME
	import datetime
	datetime.datetime.now()

**출력**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)


### 필요한 라이브러리 가져오기 및 Spark 컨텍스트 설정 

Spark 컨텍스트를 설정하고 다음 코드를 사용하여 필요한 라이브러리 가져오기

	# LOAD PYSPARK LIBRARIES
	import pyspark
	from pyspark import SparkConf
	from pyspark import SparkContext
	from pyspark.sql import SQLContext
	%matplotlib inline
	import matplotlib
	import matplotlib.pyplot as plt
	#matplotlib.style.use('ggplot')
	from pyspark.sql import Row
	from pyspark.sql.functions import UserDefinedFunction
	from pyspark.sql.types import *
	import atexit
	from numpy import array
	import numpy as np
	import datetime
	
	# SET SPARK CONTEXT
	sc = SparkContext(conf=SparkConf().setMaster('yarn-client'))
	sqlContext = SQLContext(sc)
	atexit.register(lambda: sc.stop())
	
	sc.defaultParallelism

**출력**

4

## 데이터 수집: 

이 섹션은 모델링할 데이터 샘플을 수집하는 데 필요한 일련의 작업에 대한 코드를 포함합니다. Taxi Trip 및 요금 파일(.tsv 파일로 저장됨)의 조인된 0.1% 샘플을 읽고 데이터를 포맷하고 정리하며 메모리에서 데이터 프레임을 만들고 캐시한 다음 SQL 컨텍스트에 임시 테이블로 등록합니다.

데이터 과학 프로세스의 첫 번째 단계는 외부 원본 또는 시스템에서 분석할 데이터를 수집하며 데이터 탐색 및 모델링 환경에 상주합니다. 이 환경은 이 연습에서 Spark입니다. 이 섹션은 일련의 작업을 완료하는 코드를 포함합니다.

- 모델링할 데이터 샘플 수집
- 입력 데이터 집합 읽기(.tsv 파일로 저장됨)
- 데이터 포맷 및 정리
- 메모리에 개체 만들기 및 캐시(RDD 또는 데이터 프레임)
- SQL 컨텍스트에 임시 테이블로 등록

데이터 수집에 대한 코드는 다음과 같습니다.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# IMPORT FILE FROM PUBLIC BLOB
	taxi_train_file = sc.textFile(taxi_train_file_loc)
	
	# GET SCHEMA OF THE FILE FROM HEADER
	schema_string = taxi_train_file.first()
	fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
	fields[7].dataType = IntegerType() #Pickup hour
	fields[8].dataType = IntegerType() # Pickup week
	fields[9].dataType = IntegerType() # Weekday
	fields[10].dataType = IntegerType() # Passenger count
	fields[11].dataType = FloatType() # Trip time in secs
	fields[12].dataType = FloatType() # Trip distance
	fields[19].dataType = FloatType() # Fare amount
	fields[20].dataType = FloatType() # Surcharge
	fields[21].dataType = FloatType() # Mta_tax
	fields[22].dataType = FloatType() # Tip amount
	fields[23].dataType = FloatType() # Tolls amount
	fields[24].dataType = FloatType() # Total amount
	fields[25].dataType = IntegerType() # Tipped or not
	fields[26].dataType = IntegerType() # Tip class
	taxi_schema = StructType(fields)
	
	# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
	taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
	taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
	        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
	                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
	                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
	
	    
	# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
	    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
	    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
	    .drop('direct_distance').drop('surcharge')\
	    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_train_cleaned.cache()
	taxi_df_train_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_train_cleaned.registerTempTable("taxi_train")
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**출력**

Time taken to execute above cell: 17.73 seconds


## 데이터 탐색 및 시각화 

데이터를 Spark로 가져오면 데이터 과학 프로세스의 다음 단계에서 탐색 및 시각화를 통해 데이터를 더 잘 이해할 수 있습니다. 이 섹션에서는 SQL 쿼리를 사용하여 Taxi 데이터를 검사하고 시각적 조사에 대한 대상 변수 및 잠재 기능을 그립니다. 특히, Taxi Trip에서 승객 수의 빈도, 팁 금액의 빈도 및 지불 금액 및 형식에 따른 팁의 변화를 그립니다.

### Taxi Trip의 샘플에서 승객 수 빈도의 히스토그램을 그립니다.

코드는 SQL squery를 사용하여 데이터를 샘플링하고 그릴 Pandas 데이터 프레임으로 결과를 변환합니다.

	# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

	# SQL SQUERY
	sqlStatement = """
	    SELECT passenger_count, COUNT(*) as trip_counts 
	    FROM taxi_train 
	    WHERE passenger_count > 0 and passenger_count < 7
	    GROUP BY passenger_count 
	"""
	sqlResults = sqlContext.sql(sqlStatement)
	
	#CONVERT TO PANDAS DATA-FRAMES FOR PLOTTING IN PYTHON
	resultsPDDF = sqlResults.toPandas()
	
	# PLOT PASSENGER NUMBER VS. TRIP COUNTS
	x_labels = resultsPDDF['passenger_count'].values
	fig = resultsPDDF[['trip_counts']].plot(kind='bar', facecolor='lightblue')
	fig.set_xticklabels(x_labels)
	fig.set_title('Counts of trips by passenger count')
	fig.set_xlabel('Passenger counts')
	fig.set_ylabel('Trip counts')
	plt.show()

**출력**

![승객 수에 따른 여정 빈도](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)


### 승객 수 및 요금 금액에 따라 팁 금액이 어떻게 달라지는지와 팁 금액에 대한 히스토그램을 그립니다.

코드는 SQL squery를 사용하여 데이터를 샘플링하고 그릴 Pandas 데이터 프레임으로 결과를 변환합니다.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# SQL SQUERY
	sqlStatement = """
	    SELECT fare_amount, passenger_count, tip_amount, tipped
	    FROM taxi_train 
	    WHERE passenger_count > 0 AND passenger_count < 7
	    AND fare_amount > 0 AND fare_amount < 200
	    AND payment_type in ('CSH', 'CRD')
	    AND tip_amount > 0 AND tip_amount < 25
	"""
	sqlResults = sqlContext.sql(sqlStatement)
	
	# CONVERT TO PANDAS DATA-FRAME FOR PLOTTING IN PYTHON
	resultsPDDF= sqlResults.toPandas()
	
	# TIP BY PAYMENT TYPE AND PASSENGER COUNT
	ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
	ax1.set_title('Tip amount distribution')
	ax1.set_xlabel('Tip Amount ($)')
	ax1.set_ylabel('Counts')
	plt.suptitle('')
	plt.show()
	
	# TIP BY PASSENGER COUNT
	ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
	ax2.set_title('Tip amount ($) by Passenger count')
	ax2.set_xlabel('Passenger count')
	ax2.set_ylabel('Tip Amount ($)')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
	ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
	ax.set_title('Tip amount by Fare amount ($)')
	ax.set_xlabel('Fare Amount')
	ax.set_ylabel('Tip Amount')
	plt.axis([-2, 120, -2, 30])
	plt.show()
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력:**

![팁 금액 분포](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![승객 수에 따른 여정 수](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![금액에 따른 팁 금액](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

Time taken to execute above cell: 10.42 seconds


## 모델링에 대한 기능 엔지니어링, 변환 및 데이터 준비

이 섹션에서는 기계 학습 모델링에 사용할 데이터를 준비하는 데 사용되는 프로시저에 대한 코드를 설명하고 제공합니다. 다음 작업을 수행하는 방법을 보여줍니다.

- 시간을 트래픽 시간 버킷으로 범주화하여 새로운 기능 만들기
- 범주 기능 인덱스 및 원 핫 인코딩(one-hot encoding)
- 기계 학습 함수에 입력에 대한 레이블이 지정된 지점 개체 만들기
- 데이터의 하위 무작위 샘플링을 만들고 학습 및 테스트 집합으로 분할합니다
- 기능 크기 조정
- 메모리에서 개체 캐시


### 시간을 트래픽 시간 버킷으로 범주화하여 새로운 기능 만들기

이 코드는 트래픽 시간 버킷으로 시간을 범주화하여 새로운 기능을 만드는 방법 및 메모리에 결과 데이터 프레임을 캐시하는 방법을 보여줍니다. RDD(복원력 있는 분산된 데이터 집합) 및 데이터 프레임을 반복해서 사용하는 경우 캐시하면 실행 시간이 향상됩니다. 따라서 RDD 및 데이터 프레임을 연습의 여러 단계에서 캐시합니다.

	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_train 
	"""
	taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_train_with_newFeatures.cache()
	taxi_df_train_with_newFeatures.count()

**출력**

126050


### 범주 기능 인덱스 및 원 핫 인코딩(one-hot encoding)

이 섹션에는 모델링 기능에 입력에 대한 범주 기능을 인덱싱하거나 인코딩하는 방법을 보여줍니다. MLlib의 모델링 및 예측 함수는 사용하기 전에 범주 입력 데이터로 기능을 인덱싱 또는 인코딩해야 합니다.

모델에 따라 다양한 방식에서 인덱싱하거나 인코드해야 합니다. 예를 들어 로지스틱 및 선형 회귀 모델은 원 핫 인코딩(one-hot encoding)이 필요하며 여기에 예를 들어 3개의 범주가 있는 기능이 관찰 범주에 따라 각각 0 또는 1을 포함하는 3개의 기능 열로 확장될 수 있습니다. MLlib는 원 핫 인코딩(one-hot encoding) 작업을 수행하는 [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) 함수를 제공합니다. 이 인코더는 레이블 인덱스의 열을 단 하나의 값을 가진 이진 벡터의 열에 매핑합니다. 이 인코딩을 사용하여 로지스틱 회귀 분석 등과 같은 숫자 값을 가진 기능을 예상하는 알고리즘을 범주 기능에 적용할 수 있습니다.

다음은 범주 기능을 인덱스 및 인코딩하는 코드입니다.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer
	
	# INDEX AND ENCODE VENDOR_ID
	stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
	model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
	indexed = model.transform(taxi_df_train_with_newFeatures)
	encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
	encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
	model = stringIndexer.fit(encoded1)
	indexed = model.transform(encoded1)
	encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
	encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
	model = stringIndexer.fit(encoded2)
	indexed = model.transform(encoded2)
	encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
	encoded3 = encoder.transform(indexed)
	
	# INDEX AND TRAFFIC TIME BINS
	stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
	model = stringIndexer.fit(encoded3)
	indexed = model.transform(encoded3)
	encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
	encodedFinal = encoder.transform(indexed)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**출력**

Time taken to execute above cell: 1.22 seconds


### 기계 학습 함수에 입력에 대한 레이블이 지정된 지점 개체 만들기

이 섹션은 범주 텍스트 데이터를 레이블이 지정된 지점 데이터 형식으로 인덱싱하고 인코딩하는 방법을 보여주는 코드를 포함하여 MLlib 로지스틱 회귀 및 다른 분류 모델을 학습하고 테스트하는 데 사용될 수 있습니다. 레이블이 지정된 지점 개체는 대부분 MLlib의 기계 학습 알고리즘에서 입력 데이터로 필요한 방식으로 형식이 지정된 RDD(Resilient Distributed Datasets)입니다. [레이블이 지정된 지점](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point)은 레이블/응답과 연결된 로컬 벡터, 밀도 또는 스파스입니다.


	# FUNCTIONS FOR BINARY CLASSIFICATION

	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.regression import LabeledPoint
	from numpy import array

	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingBinary(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
	                         line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
	    labPt = LabeledPoint(line.tipped, features)
	    return  labPt
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
	def parseRowOneHotBinary(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                               line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
	    labPt = LabeledPoint(line.tipped, features)
	    return  labPt


	# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingRegression(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    labPt = LabeledPoint(line.tip_amount, features)
	    return  labPt
	
	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
	def parseRowOneHotRegression(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
	    labPt = LabeledPoint(line.tip_amount, features)
	    return  labPt



### 데이터의 하위 무작위 샘플링을 만들고 학습 및 테스트 집합으로 분할합니다

이 코드는 데이터의 무작위 샘플링을 만듭니다(25%가 여기에서 사용됨). 데이터 집합의 크기로 인해 이 예제에 필요하지 않지만 필요한 경우 자신의 문제에 사용하는 방식을 알 수 있도록 여기서 샘플링할 수 있는 방법을 설명합니다. 샘플이 큰 경우 모델을 학습하는 동안 상당한 시간을 절약할 수 있습니다. 다음 샘플을 교육 부분(여기서 75%)와 테스트 부분(여기서 25%)로 분할하여 분류 및 회귀 모델링에 사용합니다.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# SPECIFY SAMPLING AND SPLITTING FRACTIONS
	from pyspark.sql.functions import rand
	
	samplingFraction = 0.25;
	trainingFraction = 0.75; testingFraction = (1-trainingFraction);
	seed = 1234;
	encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
	
	# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
	# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
	dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
	trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
	
	# CACHE TRAIN AND TEST DATA
	trainData.cache()
	testData.cache()
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
	indexedTESTbinary = testData.map(parseRowIndexingBinary)
	oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
	oneHotTESTbinary = testData.map(parseRowOneHotBinary)
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg = trainData.map(parseRowIndexingRegression)
	indexedTESTreg = testData.map(parseRowIndexingRegression)
	oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
	oneHotTESTreg = testData.map(parseRowOneHotRegression)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력**

Time taken to execute above cell: 0.4 seconds


### 기능 크기 조정

데이터 정규화라고도 하는 기능 크기 조정은 폭 넓게 분배된 값을 가진 기능이 목적 함수에서 과도한 가중치를 부여하지 않도록 합니다. 기능 크기 조정에 대한 코드는 단위 분산에 대한 기능의 크기를 조정하는 [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler)를 사용합니다. 정칙 회귀 또는 SVM(support vector machine)과 같은 광범위한 다른 기계 학습 모델을 학습하기 위한 인기 있는 알고리즘인 SGD(Stochastic Gradient Descent)와 함께 선형 회귀에 사용하기 위해 MLlib에서 제공합니다.


>[AZURE.NOTE] LinearRegressionWithSGD 알고리즘이 기능 크기 조정에 민감하다는 점을 발견했습니다.


여기에 정칙 선형 SGD 알고리즘에 사용할 변수의 크기를 조정하는 코드가 있습니다.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.regression import LabeledPoint
	from pyspark.mllib.linalg import Vectors
	from pyspark.mllib.feature import StandardScaler, StandardScalerModel
	from pyspark.mllib.util import MLUtils
	
	# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
	label = oneHotTRAINreg.map(lambda x: x.label)
	features = oneHotTRAINreg.map(lambda x: x.features)
	scaler = StandardScaler(withMean=False, withStd=True).fit(features)
	dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
	oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
	
	label = oneHotTESTreg.map(lambda x: x.label)
	features = oneHotTESTreg.map(lambda x: x.features)
	scaler = StandardScaler(withMean=False, withStd=True).fit(features)
	dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
	oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력**

Time taken to execute above cell: 7.33 seconds

### 메모리에서 개체 캐시

분류, 회귀 및 확장된 기능에 사용되는 입력 데이터 프레임 개체를 캐시하여 기계 학습 알고리즘의 학습 및 테스트에 소요된 시간을 줄일 수 있습니다.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary.cache()
	indexedTESTbinary.cache()
	oneHotTRAINbinary.cache()
	oneHotTESTbinary.cache()
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg.cache()
	indexedTESTreg.cache()
	oneHotTRAINreg.cache()
	oneHotTESTreg.cache()
	
	# SCALED FEATURES
	oneHotTRAINregScaled.cache()
	oneHotTESTregScaled.cache()
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력:**

위의 셀을 실행하는 데 걸린 시간: 0.11초


## 팁이 지불되었는지 여부를 이진 분류 모델로 예측합니다.

이 섹션에서는 Taxi Trip에 팁을 지불했는지 여부를 예측하는 이진 분류 작업에 대한 세 개의 모델을 사용하는 방법을 보여줍니다. 제공된 모델은 다음과 같습니다.

- 로지스틱 회귀 
- 임의 포리스트
- 그라데이션 향상 트리

코드 섹션을 빌드하는 각 모델은 다음과 같은 단계로 분할됩니다.

1. 하나의 매개 변수 집합이 있는 **모델 교육** 데이터
2. 메트릭이 있는 테스트 데이터 집합에서 **모델 평가**
3. 나중에 사용할 Blob의 **모델 저장**

매개 변수 비우기를 사용하여 CV(교차 유효성 검사)를 수행하는 두 가지 방법을 보여 줍니다.

1. MLlib의 모든 알고리즘과 알고리즘 내의 모든 매개 변수 집합에 적용할 수 있는 **제네릭** 사용자 지정 코드 사용 
1. **pySpark CrossValidator 파이프라인 함수** 사용 CrossValidator는 경험에 따르면 편리하기는 하지만 Spark 1.5.0에 대한 몇 가지 제한 사항이 있습니다. 

	- 파이프라인 모델은 향후 사용을 위해 저장되거나 유지될 수 없습니다.
	- 모델의 모든 매개 변수에 사용할 수 없습니다.
	- 모든 MLlib 알고리즘에 사용할 수 없습니다.


### 이진 분류에 대한 로지스틱 회귀 분석 알고리즘과 함께 사용되는 일반적인 교차 유효성 검사 및 하이퍼 매개 변수 비우기

이 섹션의 코드에서는 NYC Taxi Trip 및 요금 데이터 집합에서 팁이 여정에 지불되었는지 여부를 예측하는 [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm)로 로지스틱 회귀 분석 모델을 학습하고 평가하며 저장하는 방법을 보여 줍니다. MLlib 내의 모든 학습 알고리즘에 적용할 수 있는 사용자 지정 코드로 구현된 CV(교차 유효성 검사) 및 하이퍼 매개 변수 비우기를 사용하여 모델을 학습합니다.


>[AZURE.NOTE] 이 사용자 지정 CV 코드를 실행하는 데 몇 분 정도 걸릴 수 있습니다.


	# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	
	# CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
	from sklearn.grid_search import ParameterGrid
	grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
	paramGrid = list(ParameterGrid(grid))
	numModels = len(paramGrid)
	
	# SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
	nFolds = 3;
	h = 1.0 / nFolds;
	metricSum = np.zeros(numModels);
	
	# BEGIN CV WITH PARAMETER SWEEP
	for i in range(nFolds):
	    # Create training and x-validation sets
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
	    validation = trainData.filter(condition)
	    # Create labeled points from data-frames
	    if i > 0:
	        trainCVLabPt.unpersist()
	        validationLabPt.unpersist()
	    trainCV = trainData.filter(~condition)
	    trainCVLabPt = trainCV.map(parseRowOneHotBinary)
	    trainCVLabPt.cache()
	    validationLabPt = validation.map(parseRowOneHotBinary)
	    validationLabPt.cache()
	    # For parameter sets compute metrics from x-validation
	    for j in range(numModels):
	        regt = paramGrid[j]['regType']
	        regp = paramGrid[j]['regParam']
	        iters = paramGrid[j]['iterations']
	        tol = paramGrid[j]['tolerance']
	        # Train logistic regression model with hypermarameter set
	        model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
	                                                  regParam=regp, tolerance = tol, intercept=True)
	        predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
	        # Use ROC-AUC as accuracy metrics
	        validMetrics = BinaryClassificationMetrics(predictionAndLabels)
	        metric = validMetrics.areaUnderROC
	        metricSum[j] += metric
	
	avgAcc = metricSum / nFolds;
	bestParam = paramGrid[np.argmax(avgAcc)];
	
	# UNPERSIST OBJECTS
	trainCVLabPt.unpersist()
	validationLabPt.unpersist()
	    
	# TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
	logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
	                                              iterations=bestParam['iterations'], 
	                                              regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
	                                              intercept=True)
	
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**출력**

Time taken to execute above cell: 160.47 seconds


**표준 메트릭을 사용한 이진 분류 모델 평가 및 ROC 곡선 그리기**

이 섹션의 코드는 ROC 곡선 그림을 포함하는 테스트 데이터 집합에 대해 로지스틱 회귀 분석 모델을 평가하는 방법을 보여 줍니다.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from sklearn.metrics import roc_curve,auc
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	from pyspark.mllib.evaluation import MulticlassMetrics
	
	# PREDICT ON TEST DATA WITH BEST/FINAL MODEL
	predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))
	
	# INSTANTIATE METRICS OBJECT
	metrics = BinaryClassificationMetrics(predictionAndLabels)

	# AREA UNDER PRECISION-RECALL CURVE
	print("Area under PR = %s" % metrics.areaUnderPR)

	# AREA UNDER ROC CURVE
	print("Area under ROC = %s" % metrics.areaUnderROC)
	metrics = MulticlassMetrics(predictionAndLabels)

	# OVERALL STATISTICS
	precision = metrics.precision()
	recall = metrics.recall()
	f1Score = metrics.fMeasure()
	print("Summary Stats")
	print("Precision = %s" % precision)
	print("Recall = %s" % recall)
	print("F1 Score = %s" % f1Score)
	
	
	# CREATE A PANDAS DATA-FRAME AND PLOT ROC-CURVE, FROM PREDICTED PROBS AND LABELS                                     
	logitBest.clearThreshold() # This clears threshold for classification (0.5) and outputs probabilities
	predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))
	predictionAndLabelsDF = predictionAndLabels.toDF()
	test_predictions = predictionAndLabelsDF.toPandas()
	predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
	
	prob = predictions_pddf["probability"] 
	fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	# PLOT ROC CURVE
	plt.figure(figsize=(5,5))
	plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
	plt.plot([0, 1], [0, 1], 'k--')
	plt.xlim([0.0, 1.0])
	plt.ylim([0.0, 1.05])
	plt.xlabel('False Positive Rate')
	plt.ylabel('True Positive Rate')
	plt.title('ROC Curve')
	plt.legend(loc="lower right")
	plt.show()
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력**

Area under PR = 0.985319161941

Area under ROC = 0.983511076103

Summary Stats

Precision = 0.984187223276

Recall = 0.984187223276

F1 Score = 0.984187223276

![일반적인 접근 방식에 대한 로지스틱 회귀 분석 ROC 곡선](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

Time taken to execute above cell: 5.02 seconds


**나중에 사용할 Blob의 모델 유지**

이 섹션의 코드는 소비에 대한 로지스틱 회귀 분석 모델을 저장하는 방법을 보여 줍니다.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.classification import LogisticRegressionModel
	
	# PERSIST MODEL
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
	dirfilename = modelDir + logisticregressionfilename;
	
	logitBest.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**출력**

Time taken to execute above cell: 9.96 seconds


### LogisticRegression(탄력적 회귀) 모델과 함께 MLlib의 CrossValidator 파이프라인 함수 사용

이 섹션의 코드에서는 NYC Taxi Trip 및 요금 데이터 집합에서 팁이 여정에 지불되었는지 여부를 예측하는 [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm)로 로지스틱 회귀 분석 모델을 학습하고 평가하며 저장하는 방법을 보여 줍니다. 매개 변수 비우기와 함께 CV에 대한 MLlib CrossValidator 파이프라인 함수로 구현되는 CV(교차 유효성 검사) 및 하이퍼 매개 변수 비우기를 사용하여 모델을 학습합니다.


>[AZURE.NOTE] 이 MLlib CV 코드를 실행하는 데 몇 분 정도 걸릴 수 있습니다.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.classification import LogisticRegression
	from pyspark.ml import Pipeline
	from pyspark.ml.evaluation import BinaryClassificationEvaluator
	from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
	from sklearn.metrics import roc_curve,auc
	
	# DEFINE ALGORITHM / MODEL
	lr = LogisticRegression()
	
	# DEFINE GRID PARAMETERS
	paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
	                              .addGrid(lr.maxIter, (5, 10))\
	                              .addGrid(lr.tol, (1e-4, 1e-5))\
	                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
	                              .build()
	
	# DEFINE CV WITH PARAMETER SWEEP
	cv = CrossValidator(estimator= lr,
	                    estimatorParamMaps=paramGrid,
	                    evaluator=BinaryClassificationEvaluator(),
	                    numFolds=3)
	
	# CONVERT TO DATA-FRAME: THIS WILL NOT RUN ON RDDs
	trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])
	
	# TRAIN WITH CROSS-VALIDATION
	cv_model = cv.fit(trainDataFrame)
	

	## PREDICT AND EVALUATE ON TEST DATA-SET

	# USE TEST DATASET FOR PREDICTION
	testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
	test_predictions = cv_model.transform(testDataFrame)
	
	# CONVERT RTO PANDAS DATA-FRAME FOR CALCULATING AND PLOTTING ROC CURVE
	predictions_pddf = test_predictions.toPandas()
	predictions_pddf.dtypes
	prob = [x[1] for x in predictions_pddf["probability"]]
	fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	# PLOT ROC CURVE
	plt.figure(figsize=(5,5))
	plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
	plt.plot([0, 1], [0, 1], 'k--')
	plt.xlim([0.0, 1.0])
	plt.ylim([0.0, 1.05])
	plt.xlabel('False Positive Rate')
	plt.ylabel('True Positive Rate')
	plt.title('ROC Curve')
	plt.legend(loc="lower right")
	plt.show()
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**출력**

![MLlib의 CrossValidator를 사용하는 로지스틱 회귀 분석 ROC 곡선](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

Time taken to execute above cell: 118.25 seconds


### 임의 포리스트 분류

이 섹션의 코드에서는 NYC Taxi Trip 및 요금 데이터 집합에서 팁이 여정에 지불되었는지 여부를 예측하는 임의 포리스트 회귀를 학습하고 평가하며 저장하는 방법을 보여 줍니다.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	from pyspark.mllib.evaluation import MulticlassMetrics
	
	# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	# TRAIN RANDOMFOREST MODEL
	rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
	                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                       numTrees=25, featureSubsetStrategy="auto",
	                                       impurity='gini', maxDepth=5, maxBins=32)
	## UN-COMMENT IF YOU WANT TO PRING TREES
	#print('Learned classification forest model:')
	#print(rfModel.toDebugString())
	
	# PREDICT ON TEST DATA AND EVALUATE
	predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
	predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
	
	# AREA UNDER ROC CURVE
	metrics = BinaryClassificationMetrics(predictionAndLabels)
	print("Area under ROC = %s" % metrics.areaUnderROC)
	
	# PERSIST MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfclassificationfilename = "RandomForestClassification_" + datestamp;
	dirfilename = modelDir + rfclassificationfilename;
	
	rfModel.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**출력**

Area under ROC = 0.985240932843

Time taken to execute above cell: 22.9 seconds


### 그라데이션 향상 트리 분류

이 섹션의 코드에서는 NYC Taxi Trip 및 요금 데이터 집합에서 팁이 여정에 지불되었는지 여부를 예측하는 그라데이션 향상 트리 모델을 학습하고 평가하며 저장하는 방법을 보여줍니다.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	
	# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                numIterations=10)
	## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
	#print('Learned classification GBT model:')
	#print(bgtModel.toDebugString())
	
	# PREDICT ON TEST DATA AND EVALUATE
	predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
	predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
	
	# Area under ROC curve
	metrics = BinaryClassificationMetrics(predictionAndLabels)
	print("Area under ROC = %s" % metrics.areaUnderROC)
	
	# PERSIST MODEL IN A BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
	dirfilename = modelDir + btclassificationfilename;
	
	gbtModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력**

Area under ROC = 0.985240932843

Time taken to execute above cell: 22.41 seconds


## 회귀 모델로 CV를 사용하지 않고 팁 금액 예측

이 섹션에서는 다른 팁 기능에 따라 지불한 팁의 금액을 예측하는 회귀 작업에 대한 세 가지 모델을 사용하는 방법을 보여줍니다. 제공된 모델은 다음과 같습니다.

- 정칙 선형 회귀
- 임의 포리스트
- 그라데이션 향상 트리

이러한 모델은 소개에서 설명했습니다. 코드 섹션을 빌드하는 각 모델은 다음과 같은 단계로 분할됩니다.

1. 하나의 매개 변수 집합이 있는 **모델 교육** 데이터
2. 메트릭이 있는 테스트 데이터 집합에서 **모델 평가**
3. 나중에 사용할 Blob의 **모델 저장**   


>AZURE 참고: 이 섹션에서는 세 개의 회귀 모델에 교차 유효성 검사를 사용하지 않습니다. 그러나 선형 회귀에 대한 탄력적 net과 함께 CV를 사용하는 방법을 보여 주는 예제를 이 항목의 부록에 제공합니다.


>AZURE NOTE: 경험에 따르면 LinearRegressionWithSGD 모델의 수렴과 관련된 문제가 발생할 수 있으며 매개 변수는 유효한 모델을 얻기 위해 신중하게 변경/최적화되어야 합니다. 변수의 크기를 조정하면 수렴에 큰 도움이 됩니다. 이 항목의 부록에 나와 있는 바와 같이 탄력적 net 회귀를 사용하면 수렴하는 것을 개선할 수도 있습니다.


### SGD가 있는 선형 회귀

이 섹션의 코드는 크기 조정된 기능을 사용하여 최적화를 위해 SGD(Stochastic Gradient Descent)를 사용하는 선형 회귀를 학습하는 방법 및 WASB(Azure Blob 저장소)에서 모델의 점수를 매기고 평가하며 저장하는 방법을 보여줍니다.


	# LINEAR REGRESSION WITH SGD 

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
	from pyspark.mllib.evaluation import RegressionMetrics
	from scipy import stats
	
	# USE SCALED FEATURES TO TRAIN MODEL
	linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)
	
	# SCORE ON SCALED TEST DATA-SET & EVALUATE
	predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
	testMetrics = RegressionMetrics(predictionAndLabels)
	
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
	dirfilename = modelDir + linearregressionfilename;
	
	linearModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력**

RMSE = 1.29395294535

R-sqr = 0.588405443258

Time taken to execute above cell: 36.14 seconds


### 임의 포리스트 회귀

이 섹션의 코드에서는 NYC Taxi Trip 데이터에서 팁 금액을 예측하는 임의 포리스트 모델을 학습하고 평가하며 저장하는 방법을 보여줍니다.


>[AZURE.NOTE] 사용자 지정 코드로 매개 변수 비우기를 사용하는 교차 유효성 검사는 부록에 제공됩니다.


	#PREDICT TIP AMOUNTS USING RANDOM FOREST

	# RECORD START TIME
	timestart= datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import RegressionMetrics
	
	
	# TRAIN MODEL
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=25, featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=10, maxBins=32)
	# UN-COMMENT IF YOU WANT TO PRING TREES
	#print('Learned classification forest model:')
	#print(rfModel.toDebugString())
	
	# PREDICT AND EVALUATE ON TEST DATA-SET
	predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)
	
	testMetrics = RegressionMetrics(predictionAndLabels)
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfregressionfilename = "RandomForestRegression_" + datestamp;
	dirfilename = modelDir + rfregressionfilename;
	
	rfModel.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력**

RMSE = 0.962262172157

R-sqr = 0.69142848223

Time taken to execute above cell: 29.3 seconds


### 그라데이션 향상 트리 회귀

이 섹션의 코드에서는 NYC Taxi Trip 데이터에서 팁 금액을 예측하는 그라데이션 향상 트리 모델을 학습하고 평가하며 저장하는 방법을 보여줍니다.


	#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

	# RECORD START TIME
	timestart= datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	from pyspark.mllib.util import MLUtils
	
	# TRAIN MODEL
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
	                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
	
	# EVALUATE A TEST DATA-SET
	predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
	
	testMetrics = RegressionMetrics(predictionAndLabels)
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
	test_predictions= sqlContext.createDataFrame(predictionAndLabels)
	test_predictions_pddf = test_predictions.toPandas()
	
	ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
	fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
	ax.set_title('Actual vs. Predicted Tip Amounts ($)')
	ax.set_xlabel("Actual")
	ax.set_ylabel("Predicted")
	ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
	plt.axis([-1, 20, -1, 20])
	plt.show(ax)
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
	dirfilename = modelDir + btregressionfilename;
	
	gbtModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**출력**

RMSE = 0.962160568829

R-sqr = 0.717354800581

![Actual-vs-predicted-tip-amounts](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)


## 부록: 매개 변수 비우기를 사용하는 교차 유효성 검사로 추가 회귀 작업

이 부록에는 선형 회귀에 대한 탄력적 net을 사용하여 CV를 수행하는 방법 및 임의 포리스트 회귀에 대한 사용자 지정 코드로 매개 변수 비우기를 사용하여 CV를 수행하는 방법을 보여 주는 코드가 있습니다.


### 선형 회귀에 대한 탄력적 net을 사용하여 교차 유효성 검사

이 섹션의 코드는 선형 회귀에 대한 탄력적 net을 사용하여 교차 유효성 검사를 실행하는 방법 및 테스트 데이터에 대한 모델을 평가하는 방법을 보여 줍니다.

	###  CV USING ELASTIC NET FOR LINEAR REGRESSION

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.regression import LinearRegression
	from pyspark.ml import Pipeline
	from pyspark.ml.evaluation import RegressionEvaluator
	from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
	
	# Define algo / model
	lr = LinearRegression()
	
	# Define grid parameters
	paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
	                              .addGrid(lr.maxIter, (5, 10))\
	                              .addGrid(lr.tol, (1e-4, 1e-5))\
	                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
	                              .build() 
	
	# Define pipeline, in this case, simply the modeling (without any transformations etc.)
	pipeline = Pipeline(stages=[lr])
	
	# Define CV with parameter sweep
	cv = CrossValidator(estimator= lr,
	                    estimatorParamMaps=paramGrid,
	                    evaluator=RegressionEvaluator(),
	                    numFolds=3)
	
	# Convert to data-frame, as CrossValidator won't run on RDDs
	trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])
	
	# Train with cross-validation
	cv_model = cv.fit(trainDataFrame)
	
	timeend = datetime.datetime.now()
	timeend-timestart
	

	# EVALUATE MODEL ON TEST SET
	testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])
	
	# MAKE PREDICTIONS ON TEST DOCUMENTS 
	#THE cvModel USES THE BEST MODEL FOUND (lrModel).
	test_predictions = cv_model.transform(testDataFrame)
	predictions_pddf = test_predictions.toPandas()
	
	corstats = stats.linregress(predictions_pddf['label'],predictions_pddf['prediction'])
	r2 = (corstats[2]*corstats[2])
	print("R-sqr = %s" % r2)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**출력**

R-sqr = 0.594830601664

Time taken to execute above cell: 129.51 seconds


### 임의 포리스트 회귀에 대한 사용자 지정 코드로 교차 유효성 검사를 사용한 매개 변수 비우기

이 섹션의 코드는 임의 포리스트 회귀에 대한 사용자 지정 코드로 교차 유효성 검사를 사용하여 매개 변수 비우기를 수행하는 방법과 테스트 데이터에 대한 모델을 평가하는 방법을 보여 줍니다.


	# RECORD START TIME
	timestart= datetime.datetime.now()

	# LOAD PYSPARK LIBRARIES
	# GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import RegressionMetrics
	from sklearn.grid_search import ParameterGrid
	
	## CREATE PARAMETER GRID
	grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
	paramGrid = list(ParameterGrid(grid))
	
	## SPECIFY LEVELS OF CATEGORICAL VARIBLES
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	# SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
	nFolds = 3;
	numModels = len(paramGrid)
	h = 1.0 / nFolds;
	metricSum = np.zeros(numModels);
	
	for i in range(nFolds):
	    # Create training and x-validation sets
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
	    validation = trainData.filter(condition)
	    # Create labeled points from data-frames
	    if i > 0:
	        trainCVLabPt.unpersist()
	        validationLabPt.unpersist()
	    trainCV = trainData.filter(~condition)
	    trainCVLabPt = trainCV.map(parseRowIndexingRegression)
	    trainCVLabPt.cache()
	    validationLabPt = validation.map(parseRowIndexingRegression)
	    validationLabPt.cache()
	    # For parameter sets compute metrics from x-validation
	    for j in range(numModels):
	        maxD = paramGrid[j]['maxDepth']
	        numT = paramGrid[j]['numTrees']
	        # Train logistic regression model with hypermarameter set
	        rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=numT, featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=maxD, maxBins=32)
	        predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
	        predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
	        # Use ROC-AUC as accuracy metrics
	        validMetrics = RegressionMetrics(predictionAndLabels)
	        metric = validMetrics.rootMeanSquaredError
	        metricSum[j] += metric
	
	avgAcc = metricSum/nFolds;
	bestParam = paramGrid[np.argmin(avgAcc)];
	
	# UNPERSIST OBJECTS
	trainCVLabPt.unpersist()
	validationLabPt.unpersist()
	        
	## TRAIN FINAL MODL WIHT BEST PARAMETERS
	rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

	# EVALUATE MODEL ON TEST DATA
	predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
	
	#PRINT TEST METRICS
	testMetrics = RegressionMetrics(predictionAndLabels)
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**출력**

RMSE = 0.990182456723

R-sqr = 0.609523627251

Time taken to execute above cell: 72.5 seconds


### 메모리에서 개체 정리 및 모델 위치 인쇄

`unpersist()`을 사용하여 메모리에 캐시된 개체를 삭제합니다.

	# UNPERSIST OBJECTS CACHED IN MEMORY

	# REMOVE ORIGINAL DFs
	taxi_df_train_cleaned.unpersist()
	taxi_df_train_with_newFeatures.unpersist()
	trainData.unpersist()
	trainData.unpersist()
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary.unpersist()
	indexedTESTbinary.unpersist()
	oneHotTRAINbinary.unpersist()
	oneHotTESTbinary.unpersist()
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg.unpersist()
	indexedTESTreg.unpersist()
	oneHotTRAINreg.unpersist()
	oneHotTESTreg.unpersist()
	
	# SCALED FEATURES
	oneHotTRAINregScaled.unpersist()
	oneHotTESTregScaled.unpersist()

	# PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
	print "logisticRegFileLoc = modelDir + "" + logisticregressionfilename + """;
	print "linearRegFileLoc = modelDir + "" + linearregressionfilename + """;
	print "randomForestClassificationFileLoc = modelDir + "" + rfclassificationfilename + """;
	print "randomForestRegFileLoc = modelDir + "" + rfregressionfilename + """;
	print "BoostedTreeClassificationFileLoc = modelDir + "" + btclassificationfilename + """;
	print "BoostedTreeRegressionFileLoc = modelDir + "" + btregressionfilename + """;


**출력**

PythonRDD[119] at RDD at PythonRDD.scala:43

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS\_2016-04-1817\_40\_35.796789"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD\_2016-04-1817\_44\_00.993832"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification\_2016-04-1817\_42\_58.899412"

randomForestRegFileLoc = modelDir + "RandomForestRegression\_2016-04-1817\_44\_27.204734"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification\_2016-04-1817\_43\_16.354770"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression\_2016-04-1817\_44\_46.206262"

## 다음 작업

Spark MlLib로 회귀 및 분류 모델을 만든 경우 이러한 모델의점수를 매기고 평가하는 방법을 배울 수 있습니다.

**모델 사용:** 이 항목에서 만든 분류 및 회귀 모델의 점수를 매기고 평가하는 방법을 알아보려면 [Spark에서 만든 기계 학습 모델 점수 매기기 및 평가](machine-learning-data-science-spark-model-consumption.md)를 참조하세요.

<!---HONumber=AcomDC_0427_2016-->