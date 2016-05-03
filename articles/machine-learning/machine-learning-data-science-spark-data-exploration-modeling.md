<properties
	pageTitle="Spark로 데이터 탐색 및 모델링 | Microsoft Azure"
	description="Spark MLlib 도구 키트의 데이터 탐색 및 모델링 기능을 보여줍니다."
	services="machine-learning"
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
	ms.date="04/18/2016"
	ms.author="deguhath;bradsev" />

# Spark로 데이터 탐색 및 모델링

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

## 소개 

이 연습에서는 HDInsight Spark를 사용하여 NYC Taxi Trip 및 요금 2013 데이터 집합의 샘플에 데이터 탐색과 이진 분류 및 회귀 모델링 작업을 수행합니다. 프로세스를 위한 HDInsight Spark 클러스터 및 데이터 및 모델을 저장하는 Azure Blob를 사용하여 [데이터 과학 프로세스](http://aka.ms/datascienceprocess)의 단계를 종단 간 안내합니다. 프로세스는 Azure 저장소 Blob에서 가져온 데이터를 탐색하고 시각화한 다음 데이터를 준비하여 예측 모델을 빌드합니다. 이러한 모델은 Spark MLlib 도구 키트를 사용하여 이진 분류 및 회귀 모델링 작업을 수행합니다.

- **이진 분류** 작업은 여정에 대해 팁이 지불되었는지 여부를 예측합니다. 
- **회귀** 작업은 다른 팁 기능을 기반으로 하는 팁의 금액을 예측합니다. 

또한 모델링 단계는 각 모델 유형을 학습, 평가 및 저장하는 방법을 보여주는 코드를 포함합니다. Python은 솔루션을 코딩하고 관련 차트를 표시하는 데 사용합니다.

사용하는 모델은 로지스틱 및 선형 회귀, 임의 포리스트 및 그라데이션 향상된 트리를 포함합니다.

- [SGD로 선형 회귀](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD)는 SGD(Stochastic Gradient Descent) 메서드를 사용하는 선형 회귀 모델이며 최적화 및 기능에 대해 크기를 조정하여 결재된 팁 금액을 예측합니다. 
- [LBFGS로 로지스틱 회귀](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) 또는 "로짓" 회귀는 종속 변수가 데이터 분류를 수행하는 범주인 경우 사용할 수 있는 회귀 모델입니다. LBFGS는 제한된 양의 컴퓨터 메모리를 사용하여 BFGS(Broyden–Fletcher–Goldfarb–Shanno) 알고리즘을 비슷하게 만들고 기계 학습에서 널리 사용되는 준 뉴턴 최적화 알고리즘입니다.
- [임의 포리스트](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests)는 결정 트리의 결합체입니다. 과잉 맞춤의 위험을 줄이기 위해 많은 결정 트리를 결합합니다. 임의 포리스트는 회귀 및 분류에 사용되며 범주 기능을 처리하고 다중 클래스 분류 설정으로 확장하고 기능 크기 조정을 필요로 하지 않으며 비선형 및 기능 상호 작용을 캡처할 수 있습니다. 임의 포리스트는 분류 및 회귀에 대한 매우 성공적인 기계 학습 모델 중 하나입니다.
- [그라데이션 향상된 트리](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts)(GBT)는 결정 트리의 결합체입니다. GBT는 기능 손실을 최소화하기 위해 결정 트리를 반복적으로 학습합니다. GBT는 회귀 및 분류에 사용되며 범주 기능을 처리하고 기능 크기 조정을 필요로 하지 않으며 비선형 및 기능 상호 작용을 캡처할 수 있습니다. 또한 다중 클래스 분류 설정에도 사용할 수 있습니다.


>AZURE.NOTE: Spark MLlib 도구 키트가 큰 데이터 집합에서 작동하도록 디자인되었지만 해당 모델링 기능을 보여줄 목적으로 편의상 비교적 작은 샘플을 사용합니다(~30Mb 170,000개 행인 원래 NYC 데이터 집합의 약 0.1%를 사용함). 여기에 제공한 연습은 2개의 작업자 노드를 가진 HDInsight 클러스터에서 효율적으로 실행됩니다(약 10분 동안). 동일한 코드를 약간만 수정하면 데이터를 메모리에서 캐시하고 클러스터 크기를 변경하도록 적절하게 수정하여 더 큰 데이터 집합을 처리하는 데 사용될 수 있습니다.

## 필수 조건

이 연습을 시작하려면 Azure 계정 및 HDInsight Spark 클러스터가 필요합니다. 이러한 요구 사항, 여기에 사용한 NYC 2013 Taxi 데이터에 대한 설명 및 Spark 클러스터의 Jupyter Notebook에서 코드를 실행하는 방법에 관한 지침은 [Azure HDInsight에서 Spark를 사용하는 데이터 과학 개요](machine-learning-data-science-spark-overview.md)를 참조하세요. 이 항목에서 코드 샘플을 포함하는 **machine-learning-data-science-spark-data-exploration-modeling.ipynb** Notebook은 [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Python)에서 사용할 수 있습니다.


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## 설정: 저장소 위치, 라이브러리 및 Spark 컨텍스트

Spark는 Azure 저장소 Blob(WASB라고도 함)를 읽고 쓸 수 있습니다. 따라서 Spark 및 WASB에 다시 저장된 결과를 사용하여 해당 저장소에 저장된 기존 데이터를 처리할 수 있습니다.

모델 또는 파일을 WASB에 저장하려면 경로를 올바르게 지정해야 합니다. "wasb///"로 시작하는 경로를 사용하여 Spark 클러스터에 연결된 기본 컨테이너를 참조할 수 있습니다. 다른 위치를 “wasb://”에서 참조합니다.

또한 필요한 라이브러리를 가져오거나 Spark 컨텍스트를 설정하여 설정합니다.

### WASB의 저장소 위치에 대한 디렉터리 경로를 설정합니다.

다음 코드 샘플은 읽을 데이터의 위치 및 모델 출력이 저장될 모델 저장소 디렉터리에 대한 경로를 지정합니다.


	# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

	# LOCATION OF TRAINING DATA
	taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

	# SET THE MODEL STORAGE DIRECTORY PATH 
	# Note that the final backslash in the path is needed.
	modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### 필요한 라이브러리 가져오기 및 Spark 컨텍스트 설정 

Spark 컨텍스트를 설정하고 다음 코드를 사용하여 필요한 라이브러리 가져오기


	# IMPORT LIBRARIES
	import pyspark
	from pyspark import SparkConf
	from pyspark import SparkContext
	from pyspark.sql import SQLContext
	%matplotlib inline
	import matplotlib
	import matplotlib.pyplot as plt
	from pyspark.sql import Row
	from pyspark.sql.functions import UserDefinedFunction
	from pyspark.sql.types import *
	import atexit
	from numpy import array
	import numpy as np
	import datetime
	datetime.datetime.now()
	
	# SET SPARK CONTEXT
	sc = SparkContext(conf=SparkConf().setMaster('yarn-client'))
	sqlContext = SQLContext(sc)
	atexit.register(lambda: sc.stop())
	
	sc.defaultParallelism

**출력:**

4


## 데이터 수집

이 섹션은 모델링할 데이터 샘플을 수집하는 데 필요한 일련의 작업에 대한 코드를 포함합니다. Taxi Trip 및 요금 파일(.tsv 파일로 저장됨)의 조인된 0.1% 샘플을 읽고 데이터를 포맷하고 정리하며 메모리에서 데이터 프레임을 만들고 캐시한 다음 SQL 컨텍스트에 임시 테이블로 등록합니다.

데이터 과학 프로세스의 첫 번째 단계는 외부 원본 또는 시스템에서 분석할 데이터를 수집하며 데이터 탐색 및 모델링 환경에 상주합니다. 이 환경은 이 연습에서 Spark입니다. 이 섹션은 일련의 작업을 완료하는 코드를 포함합니다.

- 모델링할 데이터 샘플 수집
- 입력 데이터 집합 읽기(.tsv 파일로 저장됨)
- 데이터 포맷 및 정리
- 메모리에 개체 만들기 및 캐시(RDD 또는 데이터 프레임)
- SQL 컨텍스트에 임시 테이블로 등록

데이터 수집에 대한 코드는 다음과 같습니다.

	# INGEST DATA

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
	
	    
	# CREATE DATA FRAME
	taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
	
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

**출력:**

위의 셀을 실행하는 데 걸린 시간: 19.8초


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
	
	#CONVERT TO PANDAS DATA FRAME FOR PLOTTING IN PYTHON
	resultsPDDF = sqlResults.toPandas()
	
	# PLOT PASSENGER NUMBER VS. TRIP COUNTS
	x_labels = resultsPDDF['passenger_count'].values
	fig = resultsPDDF[['trip_counts']].plot(kind='bar', facecolor='lightblue')
	fig.set_xticklabels(x_labels)
	fig.set_title('Frequency of trips by passenger count')
	plt.show()

**출력:**

![승객 수에 따른 여정 빈도](./media/machine-learning-data-science-spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

	
### 팁 금액 및 지불 금액 및 형식에 따른 팁 금액의 변화의 히스토그램을 그립니다.

코드는 SQL squery를 사용하여 데이터를 샘플링하고 그릴 Pandas 데이터 프레임으로 결과를 변환합니다.

	#PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

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
	
	#CONVERT TO PANDAS DATA FRAME FOR PLOTTING IN PYTHON
	resultsPDDF= sqlResults.toPandas()
	
	# HISTOGRAM OF TIP AMOUNTS
	ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
	ax1.set_title('Tip amount distribution')
	ax1.set_xlabel('Tip Amount ($)')
	ax1.set_ylabel('Counts')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY PASSENGER COUNT
	ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
	ax2.set_title('Tip amount by Passenger count')
	ax2.set_xlabel('Passenger count')
	ax2.set_ylabel('Tip Amount ($)')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
	ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
	ax.set_title('Tip amount by Fare amount')
	ax.set_xlabel('Fare Amount ($)')
	ax.set_ylabel('Tip Amount ($)')
	plt.axis([-2, 120, -2, 30])
	plt.show()
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**출력:**

![팁 금액 분포](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-distribution.png)

![승객 수에 따른 여정 수](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![금액으로 녀건 금액](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

위의 셀을 실행하는 데 걸린 시간: 10.61초


## 모델링에 대한 기능 엔지니어링, 변환 및 데이터 준비
이 섹션에서는 기계 학습 모델링에 사용할 데이터를 준비하는 데 사용되는 프로시저에 대한 코드를 설명하고 제공합니다. 다음 작업을 수행하는 방법을 보여줍니다.

- 시간을 트래픽 시간 버킷으로 범주화하여 새로운 기능 만들기
- 범주 기능 인덱스 및 인코딩
- 기계 학습 함수에 입력에 대한 레이블이 지정된 지점 개체 만들기
- 데이터의 하위 무작위 샘플링을 만들고 학습 및 테스트 집합으로 분할합니다
- 기능 크기 조정
- 메모리에서 개체 캐시


### 시간을 트래픽 시간 버킷으로 범주화하여 새로운 기능 만들기

이 코드는 트래픽 시간 버킷으로 시간을 범주화하여 새로운 기능을 만드는 방법 및 메모리에 결과 데이터 프레임을 캐시하는 방법을 보여줍니다. RDD(복원력 있는 분산된 데이터 집합) 및 데이터 프레임을 반복해서 사용하는 경우 캐시하면 실행 시간이 향상됩니다. 따라서 RDD 및 데이터 프레임을 연습의 여러 단계에서 캐시합니다.

	## CREATE FOUR BUCKETS FOR TRAFFIC TIMES
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
	
	## CACHE DATA FRAME IN MEMORY & MATERIALIZE IT IN MEMORY
	taxi_df_train_with_newFeatures.cache()
	taxi_df_train_with_newFeatures.count()

**출력:**

126050

### 모델링 기능에 입력에 대한 범주 기능 인덱스 및 인코딩

이 섹션에는 모델링 기능에 입력에 대한 범주 기능을 인덱싱하거나 인코딩하는 방법을 보여줍니다. MLlib의 모델링 및 예측 함수는 사용하기 전에 범주 입력 데이터로 기능을 인덱싱 또는 인코딩해야 합니다. 모델에 따라 다양한 방식에서 인덱싱하거나 인코딩해야 합니다.

- **트리 기반 모델링**은 범주를 숫자 값(예: 3개 범주로 기능을 0, 1, 2로 인코딩할 수 있음)으로 인코딩되어야 합니다. MLlib의 [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) 함수에서 제공됩니다. 이 함수는 레이블의 문자열 열을 레이블 주파수에서 정렬한 레이블 인덱스의 열로 인코딩합니다. 트리 기반 알고리즘이 입력 및 데이터 처리에 대한 숫자 값으로 인덱스되더라도 항목으로 적절하게 처리되도록 지정할 수 있습니다. 

- **로지스틱 및 선형 회귀 모델**은 원 핫 인코딩(one-hot encoding)이 필요하며 여기에 예를 들어 3개의 범주가 있는 기능이 관찰 범주에 따라 각각 0 또는 1을 포함하는 3개의 기능 열로 확장될 수 있습니다. MLlib는 [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) 원 핫 인코딩(one-hot encoding) 작업을 수행하는 함수를 제공합니다. 이 인코더는 레이블 인덱스의 열을 단 하나의 값을 가진 이진 벡터의 열에 매핑합니다. 이 인코딩을 사용하여 로지스틱 회귀 등과 같은 연속 값을 가진 기능을 예상하는 알고리즘을 범주 기능에 적용할 수 있습니다.

다음은 범주 기능을 인덱스 및 인코딩하는 코드입니다.


	# INDEX AND ENCODE CATEGORICAL FEATURES

	# RECORD START TIME
	timestart = datetime.datetime.now()

	# LOAD PYSPARK LIBRARIES	
	from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
	
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

**출력:**

위의 셀을 실행하는 데 걸린 시간: 1.42초

### 기계 학습 함수에 입력에 대한 레이블이 지정된 지점 개체 만들기

이 섹션은 범주 텍스트 데이터를 레이블이 지정된 지점 데이터 형식으로 인덱싱하고 인코딩하는 방법을 보여주는 코드를 포함하여 MLlib 로지스틱 회귀 및 다른 분류 모델을 학습하고 테스트하는 데 사용될 수 있습니다. 레이블이 지정된 지점 개체는 대부분 MLlib의 기계 학습 알고리즘에서 입력 데이터로 필요한 방식으로 형식이 지정된 RDD(Resilient Distributed Datasets)입니다. [레이블이 지정된 지점](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point)은 레이블/응답과 연결된 로컬 벡터, 밀도 또는 스파스입니다.

이 섹션은 범주 텍스트 데이터를 [레이블이 지정된 지점](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) 데이터 형식으로 인덱싱하고 인코딩하는 방법을 보여주는 코드를 포함하여 MLlib 로지스틱 회귀 및 다른 분류 모델을 학습하고 테스트하는 데 사용될 수 있습니다. 레이블이 지정된 지점 개체는 레이블(대상/응답 변수) 및 기능 벡터로 구성된 RDD(Resilient Distributed Datasets)입니다. 이 서식은 MLlib의 많은 기계 학습 알고리즘에서 입력으로 필요합니다.


	# FUNCTIONS FOR BINARY CLASSIFICATION

	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.regression import LabeledPoint
	from numpy import array

	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingBinary(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    labPt = LabeledPoint(line.tipped, features)
	    return  labPt
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
	def parseRowOneHotBinary(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
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
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.sql.functions import rand

	# SPECIFY SAMPLING AND SPLITTING FRACTIONS
	samplingFraction = 0.25;
	trainingFraction = 0.75; testingFraction = (1-trainingFraction);
	seed = 1234;
	encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
	
	# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
	# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
	dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
	trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
	
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

**출력:**

위의 셀을 실행하는 데 걸린 시간: 0.26초


### 기능 크기 조정

데이터 정규화라고도 하는 기능 크기 조정은 폭 넓게 분배된 값을 가진 기능이 목적 함수에서 과도한 가중치를 부여하지 않도록 합니다. 기능 크기 조정에 대한 코드는 단위 분산에 대한 기능의 크기를 조정하는 [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler)를 사용합니다. 정칙 회귀 또는 SVM(support vector machine)과 같은 광범위한 다른 기계 학습 모델을 학습하기 위한 인기 있는 알고리즘인 SGD(Stochastic Gradient Descent)와 함께 선형 회귀에 사용하기 위해 MLlib에서 제공합니다.

>AZURE.NOTE: LinearRegressionWithSGD 알고리즘이 기능 크기 조정에 민감하다는 점을 발견했습니다.


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

**출력:**

위의 셀을 실행하는 데 걸린 시간: 6.63초


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

- 정칙 로지스틱 회귀 
- 임의 포리스트 모델
- 그라데이션 향상 트리

코드 섹션을 빌드하는 각 모델은 다음과 같은 단계로 분할됩니다.

1. 하나의 매개 변수 집합이 있는 **모델 교육** 데이터
2. 메트릭이 있는 테스트 데이터 집합에서 **모델 평가**
3. 나중에 사용할 Blob의 **모델 저장**

### 로지스틱 회귀를 사용하는 분류

이 섹션의 코드에서는 NYC Taxi Trip 및 요금 데이터 집합에서 팁이 여정에 지불되었는지 여부를 예측하는 [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm)로 로지스틱 회귀 모델을 학습하고 평가하며 저장하는 방법을 보여줍니다.


	#PREDICT WHETHER A TIP IS PAID OR NOT USING LOGISTIC REGRESSION WITH LBFGS

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
	from sklearn.metrics import roc_curve,auc
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	from pyspark.mllib.evaluation import MulticlassMetrics
	
	
	# CREATE MODEL WITH ONE SET OF PARAMETERS
	logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
	                                               regParam=0.01, regType='l2', intercept=True, corrections=10, 
	                                               tolerance=0.0001, validateData=True, numClasses=2)
	
	# PREDICT ON TEST DATA WITH MODEL
	predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))
	
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
	logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
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
	
	## SAVE MODEL WITH DATE-STAMP
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
	dirfilename = modelDir + logisticregressionfilename;
	
	logitModel.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력:**

PR 이하 영역 = 0.985319161941 ROC 이하 영역 = 0.983511076103 요약 통계 자릿수 = 0.984187223276 회수 = 0.984187223276 F1 점수 = 0.984187223276

![로지스틱 회귀 ROC curve.png](./media/machine-learning-data-science-spark-data-exploration-modeling/logistic-regression-roc-curve.png)

위의 셀을 실행하는 데 걸린 시간: 26.63초

### 임의 포리스트 모델 분류

이 섹션의 코드에서는 NYC Taxi Trip 및 요금 데이터 집합에서 팁이 여정에 지불되었는지 여부를 예측하는 임의 포리스트 모델을 학습하고 평가하며 저장하는 방법을 보여줍니다.
	
	#PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

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
	## UN-COMMENT IF YOU WANT TO PRINT TREES
	#print('Learned classification forest model:')
	#print(rfModel.toDebugString())
	
	# PREDICT ON TEST DATA AND EVALUATE
	predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
	predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
	
	# AREA UNDER ROC CURVE
	metrics = BinaryClassificationMetrics(predictionAndLabels)

	# PRINT TEST METRICS
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

**출력:**

ROC 이하 영역 = 0.985240932843 위의 셀을 실행하는 데 걸린 시간: 25.62초


### 그라데이션 향상 트리 분류

이 섹션의 코드에서는 NYC Taxi Trip 및 요금 데이터 집합에서 팁이 여정에 지불되었는지 여부를 예측하는 그라데이션 향상 트리 모델을 학습하고 평가하며 저장하는 방법을 보여줍니다.

	#PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	
	# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
	## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
	#print('Learned classification GBT model:')
	#print(bgtModel.toDebugString())
	
	# PREDICT ON TEST DATA AND EVALUATE
	predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
	predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
	
	# AREA UNDER ROC CURVE
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


## 회귀 모델로 Taxi Trip에 대한 팁 금액을 예측합니다.

이 섹션에서는 다른 팁 기능에 따라 지불한 팁의 금액을 예측하는 회귀 작업에 대한 세 가지 모델을 사용하는 방법을 보여줍니다. 제공된 모델은 다음과 같습니다.

- 정칙 선형 회귀
- 임의 포리스트 모델
- 그라데이션 향상 트리

이러한 모델은 소개에서 설명했습니다. 코드 섹션을 빌드하는 각 모델은 다음과 같은 단계로 분할됩니다.

1. 하나의 매개 변수 집합이 있는 **모델 교육** 데이터
2. 메트릭이 있는 테스트 데이터 집합에서 **모델 평가**
3. 나중에 사용할 Blob의 **모델 저장**

### SGD가 있는 선형 회귀 

이 섹션의 코드는 크기 조정된 기능을 사용하여 최적화를 위해 SGD(Stochastic Gradient Descent)를 사용하는 선형 회귀를 학습하는 방법 및 WASB(Azure Blob 저장소)에서 모델의 점수를 매기고 평가하며 저장하는 방법을 보여줍니다.

>AZURE NOTE: 경험에 따르면 LinearRegressionWithSGD 모델의 수렴과 관련된 문제가 자주 발생할 수 있으며 매개 변수는 유효한 모델을 얻기 위해 신중하게 변경/최적화되어야 합니다. 변수의 크기 조정하는 데 큰 도움이 됩니다(아래 참조).

	#PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

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
	
	# PRINT TEST METRICS
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


### 임의 포리스트 회귀

이 섹션의 코드에서는 NYC Taxi Trip 데이터에서 팁 금액을 예측하는 임의 포리스트 모델을 학습하고 평가하며 저장하는 방법을 보여줍니다.


	#PREDICT TIP AMOUNTS USING RANDOM FOREST

	# RECORD START TIME
	timestart= datetime.datetime.now()

	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import RegressionMetrics
	
	
	## TRAIN MODEL
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=25, featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=10, maxBins=32)
	## UN-COMMENT IF YOU WANT TO PRING TREES
	#print('Learned classification forest model:')
	#print(rfModel.toDebugString())
	
	## PREDICT AND EVALUATE ON TEST DATA-SET
	predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)
	testMetrics = RegressionMetrics(predictionAndLabels)

	# PRINT TEST METRICS
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


### 그라데이션 향상 트리 회귀

이 섹션의 코드에서는 NYC Taxi Trip 데이터에서 팁 금액을 예측하는 그라데이션 향상 트리 모델을 학습하고 평가하며 저장하는 방법을 보여줍니다.


	#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

	# RECORD START TIME
	timestart= datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	from pyspark.mllib.util import MLUtils
	
	## TRAIN MODEL
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
	                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
	
	## EVALUATE A TEST DATA-SET
	predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
	testMetrics = RegressionMetrics(predictionAndLabels)

	# PRINT TEST METRICS
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
	test_predictions = sqlContext.createDataFrame(predictionAndLabels)
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

**OUTLOOK**

RMSE = 0.962160568829

R-sqr = 0.717354800581

![Actual-vs-predicted-tip-amounts](./media/machine-learning-data-science-spark-data-exploration-modeling/actual-vs-predicted-tips.png)

	
## 메모리에서 개체 정리

`unpersist()`을 사용하여 메모리에 캐시된 개체를 삭제합니다.
		
	# REMOVE ORIGINAL DFs
	taxi_df_train_cleaned.unpersist()
	taxi_df_train_with_newFeatures.unpersist()
	
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


## 소비와 점수 매기기에 대한 모델의 저장소 위치 레코드

[Spark 빌드된 기계 학습 모델 점수 매기기 및 평가](machine-learning-data-science-spark-model-consumption.md) 항목에서 설명한 독립적인 데이터 집합을 사용하고 점수를 매기는 경우 여기서 Jupyter Notebook에 만든 저장된 모델을 포함하는 이러한 파일 이름을 복사하고 붙여넣어야 합니다. 필요한 모델 파일에 대한 경로를 출력하는 코드는 다음과 같습니다.

	# MODEL FILE LOCATIONS FOR CONSUMPTION
	print "logisticRegFileLoc = modelDir + "" + logisticregressionfilename + """;
	print "linearRegFileLoc = modelDir + "" + linearregressionfilename + """;
	print "randomForestClassificationFileLoc = modelDir + "" + rfclassificationfilename + """;
	print "randomForestRegFileLoc = modelDir + "" + rfregressionfilename + """;
	print "BoostedTreeClassificationFileLoc = modelDir + "" + btclassificationfilename + """;
	print "BoostedTreeRegressionFileLoc = modelDir + "" + btregressionfilename + """;


## 다음 작업

Spark MlLib로 회귀 및 분류 모델을 만든 경우 이러한 모델의점수를 매기고 평가하는 방법을 배울 수 있습니다.

**모델 사용:** 이 항목에서 만든 분류 및 회귀 모델의 점수를 매기고 평가하는 방법을 알아보려면 [Spark에서 만든 기계 학습 모델 점수 매기기 및 평가](machine-learning-data-science-spark-model-consumption.md)를 참조하세요.

<!---HONumber=AcomDC_0420_2016-->