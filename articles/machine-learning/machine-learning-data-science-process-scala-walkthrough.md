<properties
	pageTitle="Azure에서 Spark와 함께 Scala를 사용하는 데이터 과학 | Microsoft Azure"
	description="Azure HDInsight Spark 클러스터에서 Spark 확장성 있는 기계 학습 라이브러리(MLlib) 및 SparkML 패키지를 사용하여 감독 기계 학습 작업에 대해 Scala를 사용하는 방법을 보여 줍니다."  
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,deguhath"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="bradsev;"/>


# Azure에서 Spark를 사용하는 데이터 과학

이 항목에서는 Azure HDInsight Spark 클러스터에서 Spark 확장성 있는 기계 학습 라이브러리(MLlib) 및 SparkML 패키지를 사용하여 감독 기계 학습 작업에 대해 Scala를 사용하는 방법을 보여 줍니다. [데이터 과학 프로세스](http://aka.ms/datascienceprocess): 데이터 수집 및 탐색, 시각화, 기능 엔지니어링, 모델링, 모델 사용으로 이루어진 작업을 단계별로 안내합니다. 작성된 모델은 로지스틱 및 선형 회귀, 임의 포리스트 및 그라데이션 향상된 트리를 포함합니다. 두 가지 일반적인 감독 기계 학습 작업에 대해 설명합니다.

- 회귀 문제: 팁 금액 예측($)
- 이진 분류: 택시 여정에 대한 팁 또는 노팁(1/0) 예측

모델링 프로세스에는 관련 정확도 메트릭을 통한 테스트 데이터 집합의 학습 및 평가가 필요합니다. 또한 이러한 모델을 Azure Blob 저장소(WASB)에 저장하고 예측 성능의 점수를 매기며 평가하는 방법도 보여 줍니다. 더 고급 항목에서는 교차 유효성 검사 및 하이퍼 매개 변수 비우기를 사용하여 모델을 최적화할 수 있는 방법을 다룹니다. 사용되는 데이터는 2013 NYC Taxi Trip 및 요금 데이터 집합의 샘플입니다.

[Scala](http://www.scala-lang.org/)는 개체 지향과 함수 언어 개념이 통합된 Java 가상 컴퓨터 기반 언어입니다. 클라우드의 분산형 처리 및 Azure Spark 클러스터에 실행하기에 적합한 확장성 있는 언어입니다.

[Spark](http://spark.apache.org/)는 메모리 내 처리를 지원하여 빅데이터 분석 응용 프로그램의 성능을 향상하는 오픈 소스 병렬 처리 프레임워크입니다. 속도, 간편한 사용 및 정교한 분석을 위해 Spark 처리 엔진이 빌드되었습니다. Spark는 메모리 내 분산형 계산 기능을 지원하여 기계 학습 및 그래프 계산의 반복 알고리즘에 적합합니다. [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) 패키지는 DataFrames 맨 위에 구축된 균일한 상위 수준 API 집합을 제공하여 사용자가 실제 기계 학습 파이프라인을 만들고 조정할 수 있도록 합니다. [MLlib](http://spark.apache.org/mllib/)는 Spark의 확장성 있는 기계 학습 라이브러리로, 분산형 환경에서 모델링 기능을 사용할 수 있습니다.

[HDInsight Spark](../hdinsight/hdinsight-apache-spark-overview.md)는 Azure에서 호스트하는 오픈 소스 Spark의 제품입니다. 또한 Azure Blob(WASB)에 저장된 데이터를 변환, 필터링 및 시각화하기 위해 Spark SQL 대화형 쿼리를 실행할 수 있는 Spark 클러스터 상의 **Jupyter Scala Notebook**에 대한 지원도 포함하고 있습니다. 솔루션을 제공하고 데이터 시각화를 위해 관련 플롯을 여기에 보여 주는 Scala 코드 조각은 Spark 클러스터에 설치된 Jupyter Notebook에서 실행됩니다. 이러한 항목의 모델링 단계는 각 모델 유형을 학습, 평가, 저장 및 사용하는 방법을 보여 주는 코드를 포함하고 있습니다.

이 연습에 제공되는 설치 단계와 코드는 HDInsight 3.4 Spark 1.6용입니다. 그러나 여기에 있는 코드와 연습용으로 [Scala Jupyter Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb)에 있는 코드는 일반적이므로 아무 Spark 클러스터에서나 작동할 것입니다. HDInsight Spark를 사용하지 않는 경우 클러스터 설치 및 관리 단계가 여기에 나오는 내용과 약간 다를 수 있습니다.

> [AZURE.NOTE] Scala가 아닌 Python을 사용하여 종단 간 데이터 과학 프로세스에 대한 작업을 완료하는 방법을 보여 주는 항목에 대해서는 [Azure HDInsight에서 Spark를 사용하는 데이터 과학](machine-learning-data-science-spark-overview.md)을 참조하세요.


## 필수 조건

1\. 이러한 토픽을 시작하기 전에 Azure 구독이 있어야 합니다. 아직 가지고 있지 않은 경우 [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

2\. 이 연습을 완료하는 데는 HDInsight 3.4 Spark 1.6 클러스터가 필요합니다. 만드는 방법은 [시작: Azure HDInsight에서 Apache Spark 만들기](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요. 클러스터 유형 및 버전은 **클러스터 유형 선택** 메뉴에서 지정됩니다.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-cluster-on-portal.png)


>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


NYC Taxi Trip 데이터에 대한 설명 및 Spark 클러스터의 Jupyter Notebook에서 코드를 실행하는 방법에 관한 지침은 [Azure HDInsight에서 Spark를 사용하는 데이터 과학 개요](machine-learning-data-science-spark-overview.md)의 관련 섹션을 참조하세요.


## Spark 클러스터의 Jupyter Notebook에서 Scala 코드 실행 

Azure 포털에서 Jupyter Notebook을 시작할 수 있습니다. 대시보드에서 Spark 클러스터를 찾아 클릭하여 클러스터에 대한 관리 페이지로 들어갑니다. 그런 다음 **Cluster 대시보드** -> **Jupyter Notebook**을 클릭하여 Spark 클러스터와 연결된 Notebook을 엽니다.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-on-portal.png)

또한 ***https://CLUSTERNAME.azurehdinsight.net/jupyter***를 탐색하여 Jupyter Notebook에 액세스할 수도 있습니다. 이 URL의 CLUSTERNAME 부분을 사용자 고유의 클러스터 이름으로 바꿉니다. Notebook에 액세스하려면 관리자 계정에 대한 암호가 필요합니다.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-notebook.png)

Scala를 선택하여 PySpark API를 사용하는 미리 패키지된 Notebook에 대한 몇 가지 예가 들어 있는 디렉터리를 표시합니다. 이 Spark 항목 모음에 대한 코드 예제가 있는 **Scala.ipynb를 사용하여 탐색 모델링 및 점수 매기기** Notebook은 [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)에 제공됩니다.


Notebook을 Github에서 Spark 클러스터의 Jupyter Notebook 서버에 직접 업로드할 수 있습니다. Jupyter의 홈 페이지에서 화면 오른쪽의 **업로드** 단추를 클릭합니다. 파일 탐색기가 열립니다. 여기서 Scala Notebook의 Github(원시 콘텐츠) URL을 붙여넣고 **열기**를 클릭할 수 있습니다. Scala Notebook은 다음 URL로 제공됩니다.

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)


## 설정: 사전 설정 컨텍스트, Spark 매직 및 라이브러리

### 사전 설정 Spark 및 Hive 컨텍스트

	# SET START TIME
	import java.util.Calendar
	val beginningTime = Calendar.getInstance().getTime()


Jupyter Notebook에 제공되는 Spark 커널에는 미리 설정된 컨텍스트가 있으므로 개발 중인 응용 프로그램으로 작업을 시작하기 전에 Spark 또는 Hive 컨텍스트를 명시적으로 설정할 필요가 없습니다. 즉, Spark 또는 Hive 컨텍스트를 기본적으로 사용할 수 있습니다. 이러한 컨텍스트는 다음과 같습니다.

- sc - SparkContext용
- sqlContext - HiveContext용


### Spark 매직

Spark 커널은 특수 명령인 일부 미리 정의된 "매직"을 제공하며 이러한 매직은 %%를 사용하여 호출할 수 있습니다. 이러한 코드 샘플에 사용되는 다음과 같은 두 가지 명령이 있습니다.

- **%%local** 다음 줄의 코드는 로컬로 실행됩니다. 코드는 유효한 Scala 코드여야 합니다.
- **%%sql -o <변수 이름>** sqlContext에 대해 Hive 쿼리를 실행합니다. -o 매개 변수가 전달된 경우 쿼리 결과가 %%local Scala 컨텍스트에서 Spark 데이터 프레임으로 유지됩니다.

Jupyter Notebook의 커널 및 여기에서 제공되고 %%(예: %%local)로 호출되는 미리 정의된 "매직"에 대한 자세한 내용은 [HDInsight의 HDInsight Spark Linux 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md)을 참조하세요.


### 라이브러리 가져오기

다음 코드로 Spark, MLlib 및 기타 필요한 라이브러리를 가져옵니다.

	# IMPORT SPARK AND JAVA LIBRARIES 
	import org.apache.spark.sql.SQLContext
	import org.apache.spark.sql.functions._
	import java.text.SimpleDateFormat
	import java.util.Calendar
	import sqlContext.implicits._
	import org.apache.spark.sql.Row
	
	# SPARK SQL FUNCTIONS
	import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
	import org.apache.spark.sql.functions.rand
	
	# SPARK ML FUNCTIONS
	import org.apache.spark.ml.Pipeline
	import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
	import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
	import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
	import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
	import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}
	
	# SPARK MLLIB FUNCTIONS
	import org.apache.spark.mllib.linalg.{Vector, Vectors}
	import org.apache.spark.mllib.util.MLUtils
	import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
	import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
	import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
	import org.apache.spark.mllib.tree.configuration.BoostingStrategy
	import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
	import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}
	
	# SPECIFY SQL CONTEXT
	val sqlContext = new SQLContext(sc)


## 데이터 수집
 
데이터 과학 프로세스의 첫 번째 단계는 외부 원본 또는 시스템에서 분석할 데이터를 수집하며 데이터 탐색 및 모델링 환경에 상주합니다. 이 연습에서는 택시 여정 및 요금 파일(.tsv 파일로 저장됨)의 연결된 0.1% 샘플을 읽습니다. 데이터 탐색 및 모델링 환경은 Spark입니다. 이 섹션은 다음과 같은 일련의 작업을 완료하는 코드를 포함합니다.

- 데이터 및 모델 저장소에 대한 디렉터리 경로 설정
- 입력 데이터 집합 읽기(.tsv 파일로 저장됨)
- 데이터에 대한 스키마 정리 및 데이터 정리
- 정리된 데이터 프레임 만들기 및 메모리에 캐시
- SQL 컨텍스트에 임시 테이블로 등록
- 테이블 쿼리 및 결과를 데이터 프레임으로 가져오기


### WASB의 저장소 위치에 대 한 디렉터리 경로를 설정합니다.

Spark는 Azure 저장소 Blob(WASB라고도 함)를 읽고 쓸 수 있습니다. 따라서 Spark 및 WASB에 다시 저장된 결과를 사용하여 해당 저장소에 저장된 기존 데이터를 처리할 수 있습니다.

모델 또는 파일을 WASB에 저장하려면 경로를 올바르게 지정해야 합니다. "wasb///"로 시작하는 경로를 사용하여 Spark 클러스터에 연결된 기본 컨테이너를 참조할 수 있습니다. 다른 위치를 “wasb://”에서 참조합니다.

다음 코드 샘플은 읽을 입력 데이터의 위치 및 모델이 저장될 Spark 클러스터에 연결된 Azure Blob에 대한 경로를 지정합니다.

	# SET PATHS TO DATA AND MODEL FILE LOCATIONS: 
	# INGEST DATA DATA AND SPECIFY HEADERS FOR COLUMNS
	val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
	val header = taxi_train_file.first;
	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
	val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### 데이터 가져오기, RDD 만들기 및 스키마에 따라 데이터 프레임 정의 

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE SCHEMA BASED ON HEADER OF THE FILE
	val sqlContext = new SQLContext(sc)
	val taxi_schema = StructType(
	    Array(
	        StructField("medallion", StringType, true), 
	        StructField("hack_license", StringType, true),
	        StructField("vendor_id", StringType, true), 
	        StructField("rate_code", DoubleType, true),
	        StructField("store_and_fwd_flag", StringType, true),
	        StructField("pickup_datetime", StringType, true),
	        StructField("dropoff_datetime", StringType, true),
	        StructField("pickup_hour", DoubleType, true),
	        StructField("pickup_week", DoubleType, true),
	        StructField("weekday", DoubleType, true),
	        StructField("passenger_count", DoubleType, true),
	        StructField("trip_time_in_secs", DoubleType, true),
	        StructField("trip_distance", DoubleType, true),
	        StructField("pickup_longitude", DoubleType, true),
	        StructField("pickup_latitude", DoubleType, true),
	        StructField("dropoff_longitude", DoubleType, true),
	        StructField("dropoff_latitude", DoubleType, true),
	        StructField("direct_distance", StringType, true),
	        StructField("payment_type", StringType, true),
	        StructField("fare_amount", DoubleType, true),
	        StructField("surcharge", DoubleType, true),
	        StructField("mta_tax", DoubleType, true),
	        StructField("tip_amount", DoubleType, true),
	        StructField("tolls_amount", DoubleType, true),
	        StructField("total_amount", DoubleType, true),
	        StructField("tipped", DoubleType, true),
	        StructField("tip_class", DoubleType, true)
	        )
	    )
	
	# CAST VARIABLES ACCORDING TO SCHEMA
	val taxi_temp = (taxi_train_file.map(_.split("\t"))
	                        .filter((r) => r(0) != "medallion")
	                        .map(p => Row(p(0), p(1), p(2),
	                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
	                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
	                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
	                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))
	
	
	# CREATE INITIAL DATA-FRAME, DROP COLUMNS, AND CREATE CLEANED DATA-FRAME BY FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
	
	val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
	        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
	        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
	        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
	        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
	        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
	        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
	        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
	        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));
	
	# CACHE AND MATERIALIZE CLEANED DATA-FRAME IN MEMORY
	taxi_df_train_cleaned.cache()
	taxi_df_train_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_train_cleaned.registerTempTable("taxi_train")
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**출력:**

위의 셀을 실행하는 데 걸린 시간: 8초.


### 테이블 쿼리 및 결과를 데이터 프레임으로 가져오기. 

테이블에서 요금, 승객 및 팁 데이터를 쿼리하고, 손상된 외부 데이터를 필터링하며 여러 행으로 인쇄합니다.

	# QUERY THE DATA
	val sqlStatement = """
	    SELECT fare_amount, passenger_count, tip_amount, tipped
	    FROM taxi_train 
	    WHERE passenger_count > 0 AND passenger_count < 7
	    AND fare_amount > 0 AND fare_amount < 200
	    AND payment_type in ('CSH', 'CRD')
	    AND tip_amount > 0 AND tip_amount < 25
	"""
	val sqlResultsDF = sqlContext.sql(sqlStatement)
	
	# SHOW ONLY THE TOP THREE ROWS
	sqlResultsDF.show(3)

**출력:**

fare\_amount|passenger\_count|tip\_amount|tipped
-----------|---------------|----------|------
 13\.5| 1\.0| 2\.9| 1\.0
 16\.0| 2\.0| 3\.4| 1\.0
 10\.5| 2\.0| 1\.0| 1\.0


## 데이터 탐색 및 시각화 

데이터를 Spark로 가져오면 데이터 과학 프로세스의 다음 단계에서 탐색 및 시각화를 통해 데이터를 더 잘 이해할 수 있습니다. 이 섹션에서는 SQL 쿼리를 사용하여 Taxi 데이터를 검사한 후 결과를 데이터 프레임으로 가져와 Jupyter의 자동 시각화 기능을 사용하여 시각적 조사에 대한 대상 변수 및 잠재 기능을 그립니다.

### 로컬 및 SQL 매직을 사용하여 데이터 그리기

기본적으로 Jupyter Notebook에서 실행하는 코드 조각의 출력은 작업자 노드에서 유지되는 세션 컨텍스트 내에서 사용할 수 있습니다. 여정을 모든 계산에 대한 작업자 노드에 저장하는 경우 및 계산에 필요한 모든 데이터를 Jupyter 서버 노드(헤드 노드)에서 로컬로 사용 가능한 경우 %%local 매직을 사용하여 Jupyter 서버에서 코드 조각을 실행할 수 있습니다.

- **SQL 매직(`%%sql`)** HDInsight Spark 커널은 sqlContext에 대해 간편한 인라인 HiveQL 쿼리를 지원합니다. (-o VARIABLE\_NAME) 인수는 Jupyter 서버에서 Pandas 데이터 프레임으로 SQL 쿼리의 출력을 유지합니다. 즉, 로컬 모드에서 사용할 수 있습니다.
- **`%%local` 매직**은 HDInsight 클러스터의 헤드 노드인 Jupyter 서버에서 코드를 로컬로 실행하는 데 사용됩니다. 일반적으로 -o 매개 변수를 사용하여 `%%local` 매직을 `%%sql` 매직과 함께 사용합니다. -o 매개 변수는 SQL 쿼리의 출력을 로컬로 유지하고 그 다음 %%local 매직은 로컬로 유지되는 SQL 쿼리의 출력에 대해 로컬로 실행할 다음 코드 조각 집합을 트리거합니다.

### SQL을 사용하여 데이터 쿼리
이 쿼리는 요금 금액, 승객 수 및 팁 금액에 따라 여정을 검색합니다.

	# SQL QUERY
	%%sql -q -o sqlResults
	SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

아래 코드에서 %%local 매직은 로컬 데이터 프레임 sqlResults를 만드는데, 이것은 matplotlib를 사용하여 그리는 데 사용할 수 있습니다.

> [AZURE.TIP] 로컬 매직은 이 연습에서 여러 번 사용됩니다. 데이터 양이 많은 경우 로컬 메모리에 맞게 데이터 프레임을 만들도록 샘플링하세요.

### 데이터 그리기

데이터 프레임이 pandas 데이터 프레임처럼 로컬 컨텍스트에 있으면 Python 코드를 사용하여 그릴 수 있습니다.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local
	
	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults


 Spark 커널은 코드를 실행한 후 SQL(HiveQL) 쿼리의 출력을 자동으로 시각화합니다. 다양한 시각화 형식 중에서 선택할 수 있는 옵션이 제공됩니다.

- 테이블
- 원형
- 꺾은선형
- 영역
- 가로 막대형

다음은 데이터를 그리는 코드입니다.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	import matplotlib.pyplot as plt
	%matplotlib inline
	
	# TIP BY PAYMENT TYPE AND PASSENGER COUNT
	ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
	ax1.set_title('Tip amount distribution')
	ax1.set_xlabel('Tip Amount ($)')
	ax1.set_ylabel('Counts')
	plt.suptitle('')
	plt.show()
	
	# TIP BY PASSENGER COUNT
	ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
	ax2.set_title('Tip amount by Passenger count')
	ax2.set_xlabel('Passenger count')
	ax2.set_ylabel('Tip Amount ($)')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
	ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
	ax.set_title('Tip amount by Fare amount')
	ax.set_xlabel('Fare Amount ($)')
	ax.set_ylabel('Tip Amount ($)')
	plt.axis([-2, 80, -2, 20])
	plt.show()


**출력:**

![팁 금액 히스토그램](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-histogram.png)

![승객 수에 따른 여정 수](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![금액으로 녀건 금액](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-fare-amount.png)



## 모델링 기능을 위한 기능 만들기 및 데이터 준비 

Spark ML 및 MLlib의 트리 기반 모델링 기능을 사용하기 위해 범주화, 인덱싱, 원 핫 인코딩(one hot encoding), 벡터화 등의 다양한 해당 기법을 사용하여 대상 및 기능을 준비해야 합니다. 이 섹션에서 안내하는 절차는 다음과 같습니다.

- 시간을 트래픽 시간 버킷으로 **범주화**하여 새로운 기능 만들기
- 범주 기능 **인덱싱 및 원 핫 인코딩(one-hot encoding)**
- 데이터 집합을 학습 및 테스트 부분으로 **샘플링 및 분할**
- **학습 변수 및 기능 지정**, 인덱싱되었거나 원 핫 인코딩된 학습 및 테스팅 입력 레이블이 지정된 지점 RDD 또는 데이터 프레임 만들기
- ML 모델에 대한 입력으로 사용할 **기능과 대상을 자동 범주화 및 벡터화**


### 시간을 트래픽 시간 버킷으로 범주화하여 새로운 기능 만들기 

이 코드는 트래픽 시간 버킷으로 시간을 범주화하여 새로운 기능을 만드는 방법 및 메모리에 결과 데이터 프레임을 캐시하는 방법을 보여줍니다. RDD(복원력 있는 분산된 데이터 집합) 및 데이터 프레임을 반복해서 사용하는 경우 캐시하면 실행 시간이 향상됩니다. 따라서 RDD 및 데이터 프레임을 연습의 여러 단계에서 캐시합니다.

	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	val sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_train 
	"""
	val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY 
	taxi_df_train_with_newFeatures.cache()
	taxi_df_train_with_newFeatures.count()


### 범주 기능 인덱싱 및 원 핫 인코딩 

이 섹션에는 모델링 기능에 입력에 대한 범주 기능을 인덱싱하거나 인코딩하는 방법을 보여줍니다. MLlib의 모델링 및 예측 함수는 사용하기 전에 범주 입력 데이터로 기능을 인덱싱 또는 인코딩해야 합니다.

모델에 따라 다양한 방식에서 인덱싱하거나 인코드해야 합니다. 예를 들어 로지스틱 및 선형 회귀 모델은 원 핫 인코딩(one-hot encoding)이 필요하며 여기에 예를 들어 3개의 범주가 있는 기능이 관찰 범주에 따라 각각 0 또는 1을 포함하는 3개의 기능 열로 확장될 수 있습니다. MLlib는 원 핫 인코딩 작업을 수행하는 [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) 함수를 제공합니다. 이 인코더는 레이블 인덱스의 열을 단 하나의 값을 가진 이진 벡터의 열에 매핑합니다. 이 인코딩을 사용하여 로지스틱 회귀 분석 등과 같은 숫자 값을 가진 기능을 예상하는 알고리즘을 범주 기능에 적용할 수 있습니다.

여기서는 문자열 예를 보여 주기 위해 4개의 변수만 변환합니다. 평일과 같이 숫자 값으로 표현되는 기타 변수는 범주 변수로 인덱싱할 수도 있습니다.

인덱싱에는 `StringIndexer()`를 사용했고 원 핫 인코딩에는 MLlib의 `OneHotEncoder()` 함수를 사용했습니다. 다음은 범주 기능을 인덱스 및 인코딩하는 코드입니다.


	# HERE WE CREATE INDEXES, AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# INDEX AND ENCODE VENDOR_ID
	val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
	val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
	val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
	val encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
	val indexed = stringIndexer.transform(encoded1)
	val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
	val encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
	val indexed = stringIndexer.transform(encoded2)
	val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
	val encoded3 = encoder.transform(indexed)
	
	# INDEX AND TRAFFIC TIME BINS
	val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
	val indexed = stringIndexer.transform(encoded3)
	val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
	val encodedFinal = encoder.transform(indexed)
	
	# GET TIME TO RUN THE CELL 
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**출력:**

위의 셀을 실행하는 데 걸린 시간: 4초.



### 데이터 집합을 학습 및 테스트 부분으로 샘플링 및 분할

이 코드는 데이터의 무작위 샘플링을 만듭니다(25%가 여기에서 사용됨). 데이터 집합의 크기로 인해 이 예제에 필요하지 않지만 필요한 경우 자신의 문제에 사용하는 방식을 알 수 있도록 여기서 샘플링할 수 있는 방법을 설명합니다. 샘플이 큰 경우 모델을 학습하는 동안 상당한 시간을 절약할 수 있습니다. 다음 샘플을 교육 부분(여기서 75%)와 테스트 부분(여기서 25%)로 분할하여 분류 및 회귀 모델링에 사용합니다.

학습 중에 교차 유효성 검사 접기를 선택하는 데 사용할 수 있는 행("rand" 열에서)에 도달하기 위해 난수(0과 1 사이)를 추가합니다.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# SPECIFY SAMPLING AND SPLITTING FRACTIONS
	val samplingFraction = 0.25;
	val trainingFraction = 0.75; 
	val testingFraction = (1-trainingFraction);
	val seed = 1234;
	val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
	val sampledDFcount = encodedFinalSampledTmp.count().toInt
	
	val generateRandomDouble = udf(() => {
	    scala.util.Random.nextDouble
	})
	
	# ADD RANDOM NUMBER FOR CV
	val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());
	
	# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
	# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
	val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
	val trainData = splits(0)
	val testData = splits(1)
	
	# GET TIME TO RUN THE CELL 
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**출력:**

위의 셀을 실행하는 데 걸린 시간: 2초.


### 학습 변수 및 기능 지정, 인덱싱되었거나 원 핫 인코딩된 학습 및 테스팅 입력 레이블이 지정된 지점 RDD 또는 데이터 프레임 만들기. 

이 섹션은 범주 텍스트 데이터를 레이블이 지정된 지점 데이터 형식으로 인덱싱하고 인코딩하는 방법을 보여주는 코드를 포함하여 MLlib 로지스틱 회귀 및 다른 분류 모델을 학습하고 테스트하는 데 사용될 수 있습니다. 레이블이 지정된 지점 개체는 대부분 MLlib의 기계 학습 알고리즘에서 입력 데이터로 필요한 방식으로 형식이 지정된 RDD(Resilient Distributed Datasets)입니다. [레이블이 지정된 지점](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point)은 레이블/응답과 연결된 로컬 벡터, 밀도 또는 스파스입니다.

이 코드에서는 학습에 사용할 대상(종속) 변수 및 기능을 지정하고, 인덱싱되었거나 원 핫 인코딩된 학습 및 테스팅 입력 레이블이 지정된 지점 RDD 또는 데이터 프레임을 만듭니다.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS.
	val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
	val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
	
	# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
	val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
	val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))
	
	# INDEXED LAELEDPOINT RDD OBJECTS
	val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	
	# Indexed DFs that can be used for training using Spark ML functions
	val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
	val indexedTESTbinaryDF = indexedTESTbinary.toDF()
	val indexedTRAINregDF = indexedTRAINreg.toDF()
	val indexedTESTregDF = indexedTESTreg.toDF()
	
	# One-hot encoded (vectorized) DFs that can be used for training using Spark ML functions
	val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
	val OneHotTRAIN = assemblerOneHot.transform(trainData) 
	val OneHotTEST = assemblerOneHot.transform(testData)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**출력:**

위의 셀을 실행하는 데 걸린 시간: 4초.


### ML 모델에 대한 입력으로 사용할 기능과 대상을 자동 범주화 및 벡터화

Spark ML에서 트리 기반 모델링 기능에 사용할 대상 및 기능을 적절히 범주화합니다. 이 코드는 다음 두 작업을 완료합니다.

1. 0\.5의 임계값을 사용하는 0과 1 사이의 각 데이터 요소에 0 또는 1 값을 할당하여 분류의 이진 대상을 만듭니다.
2. 기능을 자동으로 범주화합니다. 기능에 대한 고유 숫자 값이 32보다 작은 경우 해당 기능은 범주화된 기능입니다.

이러한 두 작업에 대한 코드는 다음과 같습니다.

	# CATEGORIZE FEATURES AND BINARIZE TARGET FOR BINARY CLASSIFICATION PROBLEM

	# TRAIN DATA
	val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
	val indexerModel = indexer.fit(indexedTRAINbinaryDF)
	val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
	val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
	val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)
	
	# TEST DATA
	val indexerModel = indexer.fit(indexedTESTbinaryDF)
	val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
	val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
	val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)
	
	# CATEGORIZE FEATURES FOR REGRESSION PROBLEM
	# CREATE PROPERLY INDEXED AND CATEGORIED DFS FOR TREE-BASED MODELS

	# TRAIN DATA
	val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
	val indexerModel = indexer.fit(indexedTRAINregDF)
	val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)
	
	# TEST DATA
	val indexerModel = indexer.fit(indexedTESTbinaryDF)
	val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)


## 이진 분류: 팁이 지불되었는지 여부 예측

이 섹션에서는 팁이 지불되었는지 여부를 예측하기 위해 이진 분류 모델을 세 가지 형식으로 만듭니다.

- SparkML의 `LogisticRession()` 함수를 사용하는 **로지스틱 회귀 모델**
- Spark ML `RandomForestClassifier()` 함수를 사용하는 **임의 포리스트 분류 모델**
- MLlib의 `GradientBoostedTrees()` 함수를 사용하는 **그라데이션 향상 트리 분류 모델**

### 로지스틱 회귀 모델 만들기

여기서는 SparkML의 `LogisticRession()` 함수를 사용하는 로지스틱 회귀 모델을 만듭니다. 이 섹션에 있는 모델 구축 코드는 일련의 단계로 이루어집니다.

1. 하나의 매개 변수 집합이 있는 **모델 교육** 데이터
2. 메트릭이 있는 테스트 데이터 집합에서 **모델 평가**
3. 나중에 사용할 Blob의 **모델 저장**
4. 테스트 데이터에 대한 **모델 점수 매기기**
5. **결과 그리기** - ROC 곡선

이러한 절차에 대한 코드는 다음과 같습니다.

	# CREATE LOGISTIC REGRESSION MODEL 
	val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
	val lrModel = lr.fit(OneHotTRAIN)
	
	# PREDICT ON TEST DATA SET
	val predictions = lrModel.transform(OneHotTEST)
	
	# SELECT BinaryClassificationEvaluator() TO COMPUTE TEST ERROR
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	println("ROC on test data = " + ROC)
	
	# SAVE THE MODEL
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "LogisticRegression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	lrModel.save(filename);

그런 다음 결과를 로드하고 점수를 매기며 저장합니다.

	# RECORD START TIME 
	val starttime = Calendar.getInstance().getTime()

	# LOAD SAVED MODEL AND SCORE THE TEST DATA SET
	val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
	println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")
	
	# SCORE THE MODEL ON THE TEST DATA
	val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
	predictions.registerTempTable("testResults")
	
	# SELECT BinaryClassificationEvaluator() TO COMPUTE TEST ERROR
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT THE ROC RESULTS
	println("ROC on test data = " + ROC)


**출력:**

ROC on test data = 0.9827381497557599


로컬 pandas 데이터 프레임에서 Python을 사용하여 ROC 곡선을 그립니다.


	# QUERY RESULTS
	%%sql -q -o sqlResults
	SELECT tipped, probability from testResults


	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	%matplotlib inline
	from sklearn.metrics import roc_curve,auc
	
	sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
	predictions_pddf = sqlResults[["tipped","probFloat"]]
	
	# ROC CURVE
	# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
	prob = predictions_pddf["probFloat"] 
	fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	#PLOT
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


**출력:**

![탭 여부 ROC 곡선](./media/machine-learning-data-science-process-scala-walkthrough/plot-roc-curve-tip-or-not.png)


### 임의 포리스트 분류 모델 만들기

여기서는 Spark ML `RandomForestClassifier()` 함수를 사용하는 임의 포리스트 분류 모델을 만들고 테스트 데이터에 대해 모델을 평가합니다.


	# RECORD START TIME 
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE THE RANDOM FOREST CLASSIFIER MODEL
	val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)
	
	# FIT THE MODEL
	val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
	val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)
	
	# EVALUATE THE MODEL
	val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
	val Test_f1Score = evaluator.evaluate(predictions)
	println("F1 score on test data: " + Test_f1Score);
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# CALCULATE BINARY CLASSIFICATION EVALUATION METRICSS
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	println("ROC on test data = " + ROC)


**출력:**

ROC on test data = 0.9847103571552683


### 그라데이션 향상 트리 분류 모델 만들기

여기서는 MLlib의 `GradientBoostedTrees()` 함수를 사용하는 그라데이션 향상 트리 분류 모델을 만들고 테스트 데이터에 대해 모델을 평가합니다.


	# TRAIN A GBT CLASSIFICATION MODEL USING MLIB AND LABELEDPOINT

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE THE GBT CLASSIFICATION MODEL
	val boostingStrategy = BoostingStrategy.defaultParams("Classification")
	boostingStrategy.numIterations = 20
	boostingStrategy.treeStrategy.numClasses = 2
	boostingStrategy.treeStrategy.maxDepth = 5
	boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))
	
	# TRAIN THE MODEL
	val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)
	
	# SAVE THE MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "GBT_Classification__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	gbtModel.save(sc, filename);
	
	# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
	val labelAndPreds = indexedTESTbinary.map { point =>
	  val prediction = gbtModel.predict(point.features)
	  (point.label, prediction)
	}
	val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
	//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
	println("Test Error = " + testErr)
	
	# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
	val metrics = new MulticlassMetrics(labelAndPreds)
	println(s"Precision: ${metrics.precision}")
	println(s"Recall: ${metrics.recall}")
	println(s"F1 Score: ${metrics.fMeasure}")
	
	val metrics = new BinaryClassificationMetrics(labelAndPreds)
	println(s"Area under PR curve: ${metrics.areaUnderPR}")
	println(s"Area under ROC curve: ${metrics.areaUnderROC}")
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT THE ROC METRIC
	println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**출력:**

Area under ROC curve: 0.9846895479241554


## 회귀: 팁 금액 예측 

이 섹션에서는 팁 금액을 예측하기 위해 두 가지 형식의 회귀 모델을 만듭니다.

- Spark ML `LinearRegression()` 함수를 사용하는 **정칙 선형 회귀 모델**을 만들고 모델을 저장한 후 테스트 데이터에 대해 모델을 평가합니다.
- Spark ML `GBTRegressor() ` 함수를 사용하는 **그라데이션 향상 트리 회귀 모델**


### 정칙 선형 회귀 모델 만들기

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE REGULARIZED LINEAR REGRESSION MODEL USING SPARK ML FUNCTION AND DATA-FRAME
	val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
	
	# FIT THE MODEL USING DATA-FRAME
	val lrModel = lr.fit(OneHotTRAIN)
	println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")
	
	# SUMMARIZE THE MODEL OVER THE TRAIINNG SET AND PRINT OUT SOME METRICS
	val trainingSummary = lrModel.summary
	println(s"numIterations: ${trainingSummary.totalIterations}")
	println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
	trainingSummary.residuals.show()
	println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
	println(s"r2: ${trainingSummary.r2}")
	
	# SAVE THE MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "LinearRegression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	lrModel.save(filename);
	
	# PRINT COEFFICIENTS
	println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")
	
	# SCORE THE MODEL ON TEST DATA
	val predictions = lrModel.transform(OneHotTEST)
	
	# EVALUATE THE MODEL ON TEST DATA
	val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
	val r2 = evaluator.evaluate(predictions)
	println("R-sqr on test data = " + r2)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**출력:**

위의 셀을 실행하는 데 걸린 시간: 13초.


	# LOAD A SAVED LINEARREGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET. 

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# LOAD A SAVED LINEARREGRESSION MODEL FROM BLOB STORAGE
	val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
	println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")
	
	# SCORE THE MODEL ON TEST DATA
	val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
	predictions.registerTempTable("testResults")
	
	# EVALUATE THE MODEL ON TEST DATA
	val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
	val r2 = evaluator.evaluate(predictions)
	println("R-sqr on test data = " + r2)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT RESULTS
	println("R-sqr on test data = " + r2)


**출력:**

R-sqr on test data = 0.5960320470835743


다음으로 데이터 프레임으로 테스트 결과를 쿼리하고 Jupyter autoviz 및 Python matplotlib를 사용하여 시각화합니다.


	# SQL QUERY
	%%sql -q -o sqlResults
	select * from testResults

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local

	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults

이 코드는 쿼리 출력에서 로컬 데이터 프레임을 만들고 데이터를 그립니다. `%%local` 매직은 로컬 데이터 프레임 `sqlResults`를 만드는데, 이것은 matplotlib로 그리는 데 사용할 수 있습니다.

>[AZURE.NOTE] 이 Spark 매직은 이 연습에서 여러 번 사용됩니다. 데이터 양이 많은 경우 로컬 메모리에 맞게 데이터 프레임을 만들도록 샘플링해야 합니다.

Python matplotlib를 사용하여 도표를 만듭니다.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	sqlResults
	%matplotlib inline
	import numpy as np
	
	# PLOT THE RESULTS
	ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
	fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
	ax.set_title('Actual vs. Predicted Tip Amounts ($)')
	ax.set_xlabel("Actual")
	ax.set_ylabel("Predicted")
	#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
	plt.axis([-1, 15, -1, 8])
	plt.show(ax)

**출력:**

![팁 금액: 실제 및 예측](./media/machine-learning-data-science-process-scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)


### 그라데이션 향상 트리 회귀 모델 만들기

여기서는 Spark `ML GBTRegressor()` 함수를 사용하는 그라데이션 향상 트리 회귀 모델을 만들고 테스트 데이터에 대해 모델을 평가합니다.

[GBT](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts)(그라데이션 승격 트리)는 결정 트리의 결합체입니다. GBT는 기능 손실을 최소화하기 위해 결정 트리를 반복적으로 학습합니다. GBT는 회귀 및 분류에 사용되며 범주 기능을 처리하고 기능 크기 조정을 필요로 하지 않으며 비선형 및 기능 상호 작용을 캡처할 수 있습니다. 또한 다중 클래스 분류 설정에도 사용할 수 있습니다.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# TRAIN A GBT REGRESSION MODEL
	val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
	val gbtModel = gbt.fit(indexedTRAINwithCatFeat)
	
	# MAKE PREDICTIONS
	val predictions = gbtModel.transform(indexedTESTwithCatFeat)
	
	# COMPUTE TEST SET R2
	val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
	val Test_R2 = evaluator.evaluate(predictions)
	
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT RESULTS
	println("Test R-sqr is: " + Test_R2);


**출력:**

Test R-sqr is: 0.7655383534596654



## 최적화를 위한 고급 모델링 유틸리티

이 섹션에서는 모델 최적화에 자주 사용되는 ML 유틸리티를 사용하는 방법을 보여 줍니다. 특히, 매개 변수 비우기 및 교차 유효성 검사를 사용하여 ML 모델을 최적화하는 세 가지 다른 방법을 보여 줍니다.

1\.데이터를 학습 및 유효성 검사 집합으로 분할하고 학습 집합에서 하이퍼 매개 변수 비우기를, 유효성 검사 집합에서 평가를 사용하여 모델을 최적화합니다(선형 회귀).

2\.교차 유효성 검사 및 하이퍼 매개 변수 비우기를 사용하고 Spark ML의 CrossValidator 함수(이진 분류)를 사용하여 모델을 최적화합니다.

3\.사용자 지정 교차 유효성 검사 및 매개 변수 비우기 코드를 사용하고 ML 함수 및 매개 변수 집합을 활용하여 모델을 최적화합니다(선형 회귀).


**CV(교차 유효성 검사)**는 알려진 데이터 집합에서 학습된 모델이 학습되지 않은 데이터 집합의 기능 예측을 얼마나 잘 일반화하는지 평가하는 기술입니다. 이 기술의 일반 개념은 알려진 데이터의 데이터 집합에서 모델을 학습한 다음 독립된 데이터 집합에 대해 예측 정확도를 테스트하는 것입니다. 여기에 사용된 일반적인 구현은 데이터 집합을 K 접기로 나눈 다음 접기 중 하나를 제외한 모든 접기에서 라운드 로빈 방식으로 모델을 학습하는 것입니다.

**하이퍼 매개 변수 최적화**는 학습 알고리즘에 대한 하이퍼 매개 변수 집합을 선택하는 문제이며, 일반적으로 독립된 데이터 집합에서의 알고리즘 성능 측정값을 최적화하는 것을 목표로 합니다. **하이퍼 매개 변수**는 모델 학습 절차 외부에서 지정해야 하는 값입니다. 이러한 값에 대한 가정은 모델의 유연성 및 정확도에 영향을 줄 수 있습니다. 의사 결정 트리에는 원하는 깊이와 트리의 리프 수와 같은 하이퍼 매개 변수가 있습니다. SVM(Support Vector Machine)은 오분류 페널티 조건을 설정해야 합니다.

여기에 사용된 하이퍼 매개 변수 최적화를 수행하는 일반적인 방법은 그리드 검색 또는 **매개 변수 비우기**입니다. 이 구성으로 학습 알고리즘에 대한 하이퍼 매개 변수 공간의 지정된 하위 집합 값으로 철저한 검색을 수행합니다. 교차 유효성 검사는 그리드 검색 알고리즘에 의해 생성되는 최적의 결과를 분류하는 성능 메트릭을 제공할 수 있습니다. 하이퍼 매개 변수 비우기와 함께 사용된 CV를 통해 데이터 학습 모델의 과잉 맞춤과 같은 문제를 제한하여 모델이 학습 데이터가 추출되는 일반 데이터 집합에 적용할 용량을 유지하도록 합니다.

### 하이퍼 매개 변수 비우기로 선형 회귀 모델 최적화

데이터를 학습 및 유효성 검사 집합으로 분할하고 학습 집합에서 하이퍼 매개 변수 비우기를, 유효성 검사 집합에서 평가를 사용하여 모델을 최적화합니다(선형 회귀).

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# RENAME tip_amount AS LABEL
	val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label") 
	val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
	OneHotTRAINLabeled.cache()
	OneHotTESTLabeled.cache()
	
	# DEFINE THE ESTIMATOR FUNCTION: THE LinearRegression() FUNCTION
	val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)
	
	# DEFINE THE PARAMETER GRID
	val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()
	
	# DEFINE PIPELINE WITH TRAIN-TEST VALIDATION SPLIT, WITH 75% IN THE TRAIING SET, SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
	val trainPct = 0.75
	val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)
	
	# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
	val model = trainValidationSplit.fit(OneHotTRAINLabeled)
	
	# MAKE PREDICTIONS ON THE TEST DATA USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORM THE BEST
	val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")
	
	# COMPUTE TEST SET R2
	val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
	val Test_R2 = evaluator.evaluate(testResults)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	println("Test R-sqr is: " + Test_R2);


**출력:**

Test R-sqr is: 0.6226484708501209


### 교차 유효성 검사 및 하이퍼 매개 변수 비우기를 사용하여 이진 분류 모델 최적화

여기서는 교차 유효성 검사 및 하이퍼 매개 변수 비우기를 사용하여 이진 분류 모델을 최적화하는 방법을 보여 줍니다. 여기서는 Spark ML의 CrossValidator 함수를 사용합니다.

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE DATA-FRAMES WITH PROPERLY LABELED COLUMNS FOR USE WITH TRAIN/TEST SPLIT
	val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
	val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
	indexedTRAINwithCatFeatBinTargetRF.cache()
	indexedTESTwithCatFeatBinTargetRF.cache()
	
	# DEFINE THE ESTIMATOR FUNCTION
	val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)
	
	# DEFINE THE PARAMETER GRID
	val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()
	
	# SPECIFY THE NUMBER OF FOLDS
	val numFolds = 3
	
	# DEFINE THE TRAIN-TEST VALIDATION SPLIT WITH 75% IN THE TRAIING SET
	val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)
	
	# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
	val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)
	
	# MAKE PREDICTIONS ON THE TEST DATA USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORM THE BEST
	val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")
	
	# COMPUTE TEST F1 SCORE
	val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
	val Test_f1Score = evaluator.evaluate(testResults)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**출력:**

위의 셀을 실행하는 데 걸린 시간: 33초.


### 사용자 지정 교차 유효성 검사 및 매개 변수 비우기 코드를 사용하여 선형 회귀 모델 최적화

여기서는 사용자 지정 코드를 사용하여 모델을 최적화하고 최고 정확도 기준을 사용하여 최적의 모델 매개 변수를 식별합니다. 그런 다음 최종 모델을 만들고 테스트 데이터로 모델을 평가하고 Blob 저장소에 모델을 저장합니다. 마지막으로 모델을 로드하고 테스트 데이터의 점수를 매기며 해당 정확도를 평가합니다.

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE PARAMETER GRID AND THE NUMBER OF FOLDS
	val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()
	
	val nFolds = 3
	val numModels = paramGrid.size
	val numParamsinGrid = 2
	
	# SPECIFY THE NUMBER OF CATEGORIES FOR THE CATEGORIAL VARIBLES
	val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))
	
	var maxDepth = -1
	var numTrees = -1
	var param = ""
	var paramval = -1
	var validateLB = -1.0
	var validateUB = -1.0
	val h = 1.0 / nFolds;
	val RMSE  = Array.fill(numModels)(0.0)
	
	# CREATE K FOLDS
	val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)
	
	
	# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY ACCURACY
	for (i <- 0 to (nFolds-1)) {
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
	    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
	    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
	    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
	    validationLabPt.cache()
	    trainCVLabPt.cache()
	
	    for (nParamSets <- 0 to (numModels-1)) {
	        for (nParams <- 0 to (numParamsinGrid-1)) {
	            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
	            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
	            if (param == "maxDepth") {maxDepth = paramval}
	            if (param == "numTrees") {numTrees = paramval}
	        }
	        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                  numTrees=numTrees, maxDepth=maxDepth,
	                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
	        val labelAndPreds = validationLabPt.map { point =>
	                                                 val prediction = rfModel.predict(point.features)
	                                                 ( prediction, point.label )
	                                                }
	        val validMetrics = new RegressionMetrics(labelAndPreds)
	        val rmse = validMetrics.rootMeanSquaredError
	        RMSE(nParamSets) += rmse
	    }
	    validationLabPt.unpersist();
	    trainCVLabPt.unpersist();
	}
	val minRMSEindex = RMSE.indexOf(RMSE.min)
	
	# GET BEST PARAMETERS FROM CV AND PARAMETER SWEEP 
	var best_maxDepth = -1
	var best_numTrees = -1
	for (nParams <- 0 to (numParamsinGrid-1)) {
	    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
	    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
	    if (param == "maxDepth") {best_maxDepth = paramval}
	    if (param == "numTrees") {best_numTrees = paramval}
	}
	
	# CREATE BEST MODEL WITH BEST PARAMETERS AND FULL TRAIING DATASET
	val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
	                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
	
	# SAVE BEST RF MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "BestCV_RF_Regression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	best_rfModel.save(sc, filename);
	
	# PREDICT ON TRAINING SET WITH BEST MODEL AND EVALUATE
	val labelAndPreds = indexedTESTreg.map { point =>
	                                        val prediction = best_rfModel.predict(point.features)
	                                        ( prediction, point.label )
	                                       }
	
	val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
	val test_rsqr = new RegressionMetrics(labelAndPreds).r2
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


	# LOAD THE MODEL
	val savedRFModel = RandomForestModel.load(sc, filename)
	
	val labelAndPreds = indexedTESTreg.map { point =>
	                                        val prediction = savedRFModel.predict(point.features)
	                                        ( prediction, point.label )
	                                       }
	# TEST THE MODEL
	val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
	val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**출력:**

위의 셀을 실행하는 데 걸린 시간: 61초.


## Scala를 사용하여 Spark에서 만든 ML 모델을 자동으로 사용

Scala 코드를 사용하여 Spark에서 만든 ML 모델과 함께 Azure Blob에 저장된 새 데이터 집합을 자동으로 로드하고 점수를 매기는 절차는 [Spark에서 만든 기계 학습 모델 점수 매기기](machine-learning-data-science-spark-model-consumption.md) 항목에 나와 있습니다. 사용자는 제공된 지침을 따르고 Python 코드를 위에 제공된 Scala 코드로 바꾸어 자동화된 사용을 이용할 수 있습니다.

## 다음 단계

Azure에서 데이터 과학 프로세스를 구성하는 작업을 안내하는 항목에 대한 개요는 [팀 데이터 과학 프로세스](http://aka.ms/datascienceprocess)를 참조하세요.

완전한 연습에 대한 설명은 **특정 시나리오**에 대한 팀 데이터 과학 프로세스의 단계를 시연하고 클라우드, 온-프레미스 도구 및 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다. [팀 데이터 과학 프로세스 연습](data-science-process-walkthroughs.md)을 참조하세요.

<!---HONumber=AcomDC_0803_2016-->