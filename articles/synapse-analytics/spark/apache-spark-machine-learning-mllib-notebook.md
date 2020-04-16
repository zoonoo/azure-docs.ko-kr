---
title: 아파치 스파크 MLlib 및 Azure 시냅스 분석을 통해 기계 학습 앱 구축
description: Apache Spark MLlib를 사용하여 로지스틱 회귀를 통해 분류를 사용하여 데이터 집합을 분석하는 기계 학습 앱을 만드는 방법을 알아봅니다.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 9dc4047b9e95b088bb614858091f43286cefe361
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430007"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>아파치 스파크 MLlib 및 Azure 시냅스 분석을 통해 기계 학습 앱 구축

이 문서에서는 Apache Spark [MLlib를](https://spark.apache.org/mllib/) 사용하여 Azure 개방형 데이터 집합에서 간단한 예측 분석을 수행하는 기계 학습 응용 프로그램을 만드는 방법을 배웁니다. Spark는 기본 제공 기계 학습 라이브러리를 제공합니다. 이 예제에서는 로지스틱 회귀를 통한 *분류를* 사용합니다.

MLlib는 다음과 같은 유틸리티를 포함하여 기계 학습 작업에 유용한 많은 유틸리티를 제공하는 핵심 스파크 라이브러리입니다.

- 분류
- 회귀
- Clustering
- 항목 모델링
- SVD(특이값 분해) 및 PCA(주성분 분석)
- 가설 테스트 및 샘플 통계 계산

## <a name="understand-classification-and-logistic-regression"></a>분류 및 로지스틱 회귀의 이해

널리 사용되는 Machine Learning 작업인 *분류*는 입력 데이터를 범주로 정렬하는 프로세스입니다. 제공하는 입력 데이터에 *레이블을* 할당하는 방법을 파악하는 것은 분류 알고리즘의 작업입니다. 예를 들어 주식 정보를 입력으로 받아들이고 주식을 판매해야 하는 주식과 유지해야 하는 주식이라는 두 가지 범주로 나누는 기계 학습 알고리즘을 생각할 수 있습니다.

*로지스틱 회귀는* 분류에 사용할 수 있는 알고리즘입니다. Spark의 로지스틱 회귀 API는 *이진 분류*또는 입력 데이터를 두 그룹 중 하나로 분류하는 데 유용합니다. 로지스틱 회귀에 대한 자세한 내용은 [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression)를 참조하세요.

요약하자면, 로지스틱 회귀 프로세스는 입력 벡터가 한 그룹 또는 다른 그룹에 속할 확률을 예측할 수 있는 *로지스틱 함수* 를 만듭니다.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>NYC 택시 데이터에 대한 예측 분석 예

이 예제에서는 Spark를 사용하여 뉴욕의 택시 여행 팁 데이터에 대한 몇 가지 예측 분석을 수행합니다. 데이터는 [Azure Open 데이터 집합을](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)통해 사용할 수 있습니다. 데이터 집합의 이 하위 집합에는 각 여정, 시작 및 종료 시간 및 위치, 비용 및 기타 흥미로운 속성에 대한 정보를 포함하여 노란색 택시 여행에 대한 정보가 포함되어 있습니다.

> [!IMPORTANT]
> 저장소 위치에서 이 데이터를 가져오는 경우 추가 요금이 부과될 수 있습니다.

다음 단계에서는 특정 여행에 팁이 포함되어 있는지 여부를 예측하는 모델을 개발합니다.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Apache Spark MLlib 기계 학습 앱 만들기

1. PySpark 커널을 사용하여 전자 필기장을 만듭니다. 지침은 [전자 필기장 만들기를](./apache-spark-notebook-create-spark-use-sql.md#create-a-notebook)참조하십시오.
2. 이 애플리케이션에 필요한 형식을 가져옵니다. 다음 코드를 복사하여 빈 셀에 붙여넣은 다음 **SHIFT + ENTER를**누르거나 코드 왼쪽에 있는 파란색 재생 아이콘을 사용하여 셀을 실행합니다.

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

    PySpark 커널로 인해 컨텍스트를 명시적으로 만들 필요가 없습니다. Spark 컨텍스트는 첫 번째 코드 셀을 실행할 때 자동으로 만들어집니다.

## <a name="construct-the-input-dataframe"></a>입력 데이터 프레임 구축

원시 데이터는 마루 형식이므로 Spark 컨텍스트를 사용하여 파일을 데이터 프레임으로 직접 메모리로 가져올 수 있습니다. 아래 코드는 기본 옵션을 사용하지만 필요한 경우 데이터 형식 및 기타 스키마 특성의 매핑을 강제할 수 있습니다.

1. 다음 줄을 실행하여 코드를 새 셀에 붙여넣은 Spark 데이터 프레임을 만듭니다. 첫 번째 섹션에서는 Azure 저장소 액세스 정보를 변수에 할당합니다. 두 번째 섹션에서는 Spark가 Blob 저장소에서 원격으로 읽을 수 있도록 합니다. 코드의 마지막 줄은 마루를 읽지만 이 시점에서 데이터가 로드되지 않습니다.

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

2. 이 데이터를 모두 가져오는 경우 약 15억 개의 행이 생성됩니다. Spark 풀(미리 보기)의 크기에 따라 원시 데이터가 너무 커지거나 작동에 너무 많은 시간이 걸릴 수 있습니다. 이 데이터를 더 작은 것으로 필터링할 수 있습니다. 필요한 경우 다음 줄을 추가하여 데이터를 약 2백만 행으로 필터링하여 보다 신속하게 대응할 수 있습니다. 이러한 매개 변수를 사용하여 1주일의 데이터를 가져옵니다.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. 간단한 필터링의 단점은 통계적 관점에서 데이터에 편향이 발생할 수 있다는 것입니다. 또 다른 방법은 스파크에 내장된 샘플링을 사용하는 것입니다. 위의 코드 다음에 적용된 경우 다음 코드는 데이터 집합을 약 2000행으로 줄입니다. 이 샘플링 단계는 간단한 필터 대신 또는 간단한 필터와 함께 사용할 수 있습니다.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. 이제 데이터를 보고 읽은 내용을 확인할 수 있습니다. 일반적으로 데이터 집합의 크기에 따라 전체 집합보다는 하위 집합으로 데이터를 검토하는 것이 좋습니다. 다음 코드는 데이터를 보는 두 가지 방법을 제공합니다: 전자는 기본이고 후자는 훨씬 더 풍부한 그리드 환경을 제공하고 데이터를 그래픽으로 시각화하는 기능입니다.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. 생성된 데이터 집합 크기의 크기와 노트북을 여러 번 실험하거나 실행해야 하는 상황에 따라 작업 공간에서 데이터 집합을 로컬로 캐시하는 것이 좋습니다. 명시적 캐싱을 수행하는 방법에는 세 가지가 있습니다.

   - 데이터 프레임을 파일로 로컬로 저장
   - 데이터 프레임을 임시 테이블 또는 보기로 저장
   - 데이터 프레임을 영구 테이블로 저장

이러한 방법 중 처음 2개는 다음 코드 예제에 포함되어 있습니다.

임시 테이블 또는 뷰를 만들면 데이터에 대한 다른 액세스 경로가 제공되지만 Spark 인스턴스 세션 기간 동안만 지속됩니다.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>데이터 이해

일반적으로 이 시점에서 탐색 *데이터* 분석(EDA)의 단계를 거치면서 데이터에 대한 이해를 개발합니다. 다음 코드는 데이터의 상태 및 품질에 대한 결론으로 이어지는 팁과 관련된 데이터의 세 가지 시각화를 보여 줍니다.

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
![상자 수염](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![플롯 산란 플롯](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>데이터 준비

원시 형태의 데이터는 모델에 직접 전달하기에 적합하지 않은 경우가 종종 있습니다. 모델에서 데이터를 사용할 수 있는 상태로 전환하려면 데이터에 대해 일련의 작업을 수행해야 합니다.

아래의 코드에서 네 가지 작업 클래스가 수행됩니다.

- 필터링을 통해 이상값/잘못된 값을 제거합니다.
- 필요하지 않은 열의 제거입니다.
- 원시 데이터에서 파생된 새 열을 만들어 모델을 보다 효과적으로 작동하도록 합니다( 때로는 위화라고도 함).
- 라벨링, 이진 분류를 착수하는 경우 (주어진 여행에 팁이 있거나 없는지 여부) 팁 금액을 0 또는 1 값으로 변환할 필요가 있습니다.

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

그런 다음 데이터를 통해 두 번째 패스를 만들어 최종 피처를 추가합니다.

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

마지막 작업은 레이블이 지정된 데이터를 로지스틱 회귀로 분석할 수 있는 형식으로 변환하는 것입니다. 로지스틱 회귀 알고리즘에 대한 입력은 *특징 벡터가* 입력 점을 나타내는 숫자 벡터인 *레이블 특징 벡터 쌍의*집합이어야 합니다. 따라서 범주형 열을 숫자로 변환해야 합니다. `trafficTimeBins` 및 `weekdayString` 열을 정수 표현으로 변환해야 합니다. 변환을 수행하는 방법에는 여러 가지가 있지만 이 예제에서 수행하는 접근 방식은 일반적인 접근 방식인 *OneHotEncoding입니다.*

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

이렇게 하면 모든 열이 올바른 형식으로 모델을 학습할 수 있는 새 데이터 프레임이 생성됩니다.

## <a name="train-a-logistic-regression-model"></a>로지스틱 회귀 모델 학습

첫 번째 작업은 데이터 집합을 학습 집합과 테스트 또는 유효성 검사 집합으로 분할하는 것입니다. 여기서 분할은 임의적이며 다른 분할 설정으로 재생하여 모델에 영향을 미치는지 확인해야 합니다.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

이제 두 개의 DataFrame이 있으므로 다음 작업은 모델 수식을 만들고 학습 DataFrame에 대해 실행한 다음 DataFrame 테스트에 대해 유효성을 검사하는 것입니다. 다른 조합의 영향을 확인 하려면 모델 수식의 다른 버전을 사용 해 봐야 합니다.

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

이 셀의 출력은

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>예측의 시각적 표현 만들기

이제 이 테스트 결과의 이유를 파악하는 데 도움이 되는 최종 시각화를 만들 수 있습니다. [ROC 곡선은](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) 결과를 검토하는 한 가지 방법입니다.

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

![로지스틱 회귀 팁 모델용 ROC 곡선](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "로지스틱 회귀 팁 모델용 ROC 곡선")

## <a name="shut-down-the-spark-instance"></a>스파크 인스턴스 종료

응용 프로그램 실행이 완료되면 탭을 닫고 리소스를 해제하도록 전자 필기장을 종료하거나 전자 필기장의 아래쪽에 있는 상태 패널에서 **세션 종료를** 선택합니다.

## <a name="see-also"></a>참고 항목

- [개요: Azure 시냅스 분석의 아파치 스파크](apache-spark-overview.md)

## <a name="next-steps"></a>다음 단계

- [아파치 스파크 문서용 .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [아파치 스파크 공식 문서](https://spark.apache.org/docs/latest/)

>[!NOTE]
> 공식 아파치 스파크 문서 중 일부는 Azure Synapse 스파크에서 사용할 수 없는 스파크 콘솔을 사용하는 데 사용됩니다. 대신 [노트북](../spark/apache-spark-notebook-create-spark-use-sql.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 또는 [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 환경을 사용합니다.
