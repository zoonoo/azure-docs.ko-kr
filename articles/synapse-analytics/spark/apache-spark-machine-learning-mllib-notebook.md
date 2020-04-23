---
title: Apache Spark MLlib 및 Azure Synapse Analytics를 사용 하 여 machine learning 앱 빌드
description: Apache Spark MLlib를 사용 하 여 로지스틱 회귀를 통해 분류를 사용 하 여 데이터 집합을 분석 하는 기계 학습 앱을 만드는 방법을 알아봅니다.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 25d11d2cf41f8653c5a54007f121c1251bb24b1f
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096302"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Apache Spark MLlib 및 Azure Synapse Analytics를 사용 하 여 machine learning 앱 빌드

이 문서에서는 Apache Spark [Mllib](https://spark.apache.org/mllib/) 를 사용 하 여 Azure open 데이터 집합에 대 한 간단한 예측 분석을 수행 하는 기계 학습 응용 프로그램을 만드는 방법에 대해 알아봅니다. Spark는 기본 제공 기계 학습 라이브러리를 제공 합니다. 이 예제에서는 로지스틱 회귀를 통해 *분류* 를 사용 합니다.

MLlib는에 적합 한 유틸리티를 비롯 하 여 기계 학습 작업에 유용한 여러 유틸리티를 제공 하는 핵심 Spark 라이브러리입니다.

- 분류
- 재발
- 클러스터링
- 항목 모델링
- SVD(특이값 분해) 및 PCA(주성분 분석)
- 가설 테스트 및 샘플 통계 계산

## <a name="understand-classification-and-logistic-regression"></a>분류 및 로지스틱 회귀의 이해

널리 사용되는 Machine Learning 작업인 *분류*는 입력 데이터를 범주로 정렬하는 프로세스입니다. 사용자가 제공 하는 입력 데이터에 *레이블을* 할당 하는 방법을 파악 하는 분류 알고리즘의 작업입니다. 예를 들어 주식 정보를 입력으로 수락 하 고 재고를 두 가지 범주 (판매 해야 하는 주식 및 유지 해야 하는 주식)로 나누는 기계 학습 알고리즘을 생각해 볼 수 있습니다.

*로지스틱 회귀* 는 분류에 사용할 수 있는 알고리즘입니다. Spark의 로지스틱 회귀 API는 *이진 분류*또는 입력 데이터를 두 그룹 중 하나로 분류하는 데 유용합니다. 로지스틱 회귀에 대한 자세한 내용은 [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression)를 참조하세요.

요약하자면, 로지스틱 회귀 프로세스는 입력 벡터가 한 그룹 또는 다른 그룹에 속할 확률을 예측할 수 있는 *로지스틱 함수* 를 만듭니다.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>NYC Taxi data에 대 한 예측 분석 예제

이 예제에서는 Spark를 사용 하 여 뉴욕의 taxi 여행 팁 데이터에 대 한 예측 분석을 수행 합니다. 데이터는 [Azure Open 데이터 집합](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)을 통해 사용할 수 있습니다. 이 데이터 집합의 하위 집합에는 각 여행에 대 한 정보, 시작 및 종료 시간 및 위치, 비용 및 기타 흥미로운 특성을 포함 하 여 노랑 taxi 여행에 대 한 정보가 포함 되어 있습니다.

> [!IMPORTANT]
> 저장소 위치에서이 데이터를 끌어오는 데 추가 요금이 있을 수 있습니다.

다음 단계에서는 특정 여행에 팁이 포함 되어 있는지 여부를 예측 하는 모델을 개발 합니다.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Apache Spark MLlib 기계 학습 앱 만들기

1. PySpark 커널을 사용 하 여 노트북을 만듭니다. 지침은 [노트북 만들기](../quickstart-apache-spark-notebook.md#create-a-notebook)를 참조 하세요.
2. 이 애플리케이션에 필요한 형식을 가져옵니다. 다음 코드를 복사 하 여 빈 셀에 붙여넣은 다음 **shift + enter**를 누르거나 코드 왼쪽의 파란색 재생 아이콘을 사용 하 여 셀을 실행 합니다.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    PySpark 커널로 인해 컨텍스트를 명시적으로 만들 필요가 없습니다. 첫 번째 코드 셀을 실행 하면 Spark 컨텍스트가 자동으로 만들어집니다.

## <a name="construct-the-input-dataframe"></a>입력 데이터 프레임 구축

원시 데이터는 Parquet 형식이 기 때문에 Spark 컨텍스트를 사용 하 여 파일을 데이터 프레임로 직접 메모리로 가져올 수 있습니다. 아래 코드에서는 기본 옵션을 사용 하지만 필요에 따라 데이터 형식 및 기타 스키마 특성을 강제로 매핑할 수도 있습니다.

1. 다음 줄을 실행 하 여 새 셀에 코드를 붙여 넣어 Spark 데이터 프레임을 만듭니다. 첫 번째 섹션은 변수에 Azure storage 액세스 정보를 할당 합니다. 두 번째 섹션에서는 Spark가 blob storage를 원격으로 읽을 수 있습니다. 코드의 마지막 줄은 parquet를 읽지만 이때 데이터는 로드 되지 않습니다.

    ```python
    # Azure storage access info
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow SPARK to read from Blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # SPARK read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)
    ```

2. 이 데이터를 모두 가져오면 약 15억 행이 생성 됩니다. Spark 풀의 크기 (미리 보기)에 따라 원시 데이터가 너무 크거나 작업 하는 데 너무 많은 시간이 걸릴 수 있습니다. 이 데이터를 더 작은 값으로 필터링 할 수 있습니다. 필요한 경우 다음 줄을 추가 하 여 응답성이 높은 환경에서 약 200만 행까지 데이터를 필터링 합니다. 이러한 매개 변수를 사용 하 여 데이터의 한 주를 가져옵니다.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. 단순 필터링의 단점은 통계 관점에서 데이터에 대 한 바이어스를 도입할 수 있다는 것입니다. 또 다른 방법은 Spark에 기본 제공 되는 샘플링을 사용 하는 것입니다. 다음 코드는 위의 코드 뒤에 적용 되는 경우 데이터 집합을 약 2000 행으로 줄입니다. 단순 필터 대신이 샘플링 단계를 사용 하거나 단순 필터와 함께 사용할 수 있습니다.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. 이제 데이터를 확인 하 여 읽은 내용을 확인할 수 있습니다. 일반적으로 데이터 집합의 크기에 따라 전체 집합이 아닌 하위 집합을 사용 하 여 데이터를 검토 하는 것이 좋습니다. 다음 코드는 두 가지 방법으로 데이터를 볼 수 있습니다. 즉, 처음에는 기본으로 제공 되 고 후자는 훨씬 풍부한 그리드 환경을 제공 하며 데이터를 그래픽으로 시각화 하는 기능을 제공 합니다.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. 생성 된 데이터 집합 크기와 노트북을 여러 번 실험 하거나 실행 해야 하는 크기에 따라 작업 영역에서 로컬로 데이터 집합을 캐시 하는 것이 좋습니다. 다음 세 가지 방법으로 명시적 캐싱을 수행할 수 있습니다.

   - 로컬로 데이터 프레임를 파일로 저장
   - 임시 테이블 또는 뷰로 데이터 프레임 저장
   - 영구 테이블로 데이터 프레임 저장

이러한 방법 중 처음 두 가지는 다음 코드 예제에 포함 되어 있습니다.

임시 테이블 또는 뷰를 만들면 데이터에 대해 서로 다른 액세스 경로가 제공 되지만 Spark 인스턴스 세션의 기간 동안만 지속 됩니다.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>데이터 이해

일반적으로 데이터를 이해 하기 위해이 시점에서 일반적으로는 .Eda ( *예비 데이터 분석* )의 단계를 수행 합니다. 다음 코드는 데이터의 상태와 품질에 대해 결론을 주는 팁과 관련 된 데이터의 세 가지 시각화를 보여 줍니다.

```python
# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()

# Look at tips by amount count histogram
ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# How many passengers tip'd by various amounts
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# Look at the relationship between fare and tip amounts
ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```

![히스토그램](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![상자 수염 그림](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![산 점도](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>데이터 준비

원시 형식의 데이터는 모델에 직접 전달 하는 데 적합 하지 않은 경우가 많습니다. 데이터에 대해 일련의 작업을 수행 하 여 모델에서 사용할 수 있는 상태로 가져와야 합니다.

아래 코드에서는 네 가지 작업 클래스가 수행 됩니다.

- 필터링을 통해 이상 값/잘못 된 값을 제거한 경우
- 필요 하지 않은 열을 제거 합니다.
- 모델을 보다 효율적으로 작동 하도록 원시 데이터에서 파생 된 새 열을 만드는 것 (기능화 라고도 함)
- 레이블 지정, 이진 분류를 수행 하는 동안 (지정 된 여행에 팁이 있을 수 있습니다.) 팁 크기를 0 또는 1 값으로 변환 해야 합니다.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

그런 다음 데이터에 대 한 두 번째 단계를 수행 하 여 최종 기능을 추가 합니다.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>로지스틱 회귀 모델 만들기

마지막 작업은 레이블이 지정된 데이터를 로지스틱 회귀로 분석할 수 있는 형식으로 변환하는 것입니다. 로지스틱 회귀 알고리즘에 대 한 입력은 *레이블-기능 벡터 쌍*의 집합 이어야 하며, 여기서 *기능 벡터* 는 입력 지점을 나타내는 숫자의 벡터입니다. 따라서 범주 열을 숫자로 변환 해야 합니다. `trafficTimeBins` 및 `weekdayString` 열을 정수 표현으로 변환 해야 합니다. 변환을 수행 하는 방법에는 여러 가지가 있지만이 예제에서 사용 되는 방법은 일반적인 방법 *OneHotEncoding*입니다.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

그러면 모델을 학습 하는 데 모든 열이 올바른 형식으로 새 데이터 프레임 됩니다.

## <a name="train-a-logistic-regression-model"></a>로지스틱 회귀 모델 학습

첫 번째 작업은 데이터 집합을 학습 집합과 테스트 또는 유효성 검사 집합으로 분할 하는 것입니다. 여기서 분할 하는 것은 임의의 분할 설정에 따라 달라 지 며 모델에 영향을 주는지 여부를 확인 해야 합니다.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

이제 두 개의 데이터 프레임가 있으므로 다음 작업은 모델 수식을 만들고 학습 데이터 프레임에 대해 실행 한 다음 테스트 데이터 프레임에 대해 유효성을 검사 하는 것입니다. 여러 가지 모델 수식 버전을 사용 하 여 다양 한 조합의 영향을 확인 해야 합니다.

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

이 셀의 출력은입니다.

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>예측의 시각적 표현 만들기

이제 이 테스트 결과의 이유를 파악하는 데 도움이 되는 최종 시각화를 만들 수 있습니다. [ROC 곡선은](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) 결과를 검토 하는 한 가지 방법입니다.

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![로지스틱 회귀 팁 모델에 대 한 ROC Curve](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "로지스틱 회귀 팁 모델에 대 한 ROC Curve")

## <a name="shut-down-the-spark-instance"></a>Spark 인스턴스 종료

응용 프로그램 실행을 완료 한 후에는 노트북을 종료 하 여 탭을 닫거나 노트북 하단의 상태 패널에서 **세션 종료** 를 선택 하 여 리소스를 해제 합니다.

## <a name="see-also"></a>참고 항목

- [개요: Azure Synapse Analytics에서 Apache Spark](apache-spark-overview.md)

## <a name="next-steps"></a>다음 단계

- [Apache Spark용 .NET 설명서](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 공식 설명서](https://spark.apache.org/docs/latest/)

>[!NOTE]
> 일부 공식 Apache Spark 설명서에서는 Azure Synapse Spark에서 사용할 수 없는 Spark 콘솔을 사용합니다. [Notebook](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 또는 [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 환경을 대신 사용하세요.
