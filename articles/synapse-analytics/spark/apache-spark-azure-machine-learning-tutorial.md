---
title: '자습서: Azure 자동화된 ML을 사용하여 실험 실행'
description: Apache Spark 및 Azure 자동화된 ML을 사용하여 기계 학습 실험을 실행하는 방법에 대한 자습서입니다.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick,
ms.openlocfilehash: da4cef50610b219689e2271e9f70fd1adb1a235f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540509"
---
# <a name="tutorial-run-experiments-using-azure-automated-ml-and-apache-spark"></a>자습서: Azure 자동화된 ML 및 Apache Spark를 사용하여 실험 실행

Azure Machine Learning은 기계 학습 모델을 학습, 배포, 자동화, 관리 및 추적할 수 있는 클라우드 기반 환경입니다. 

이 자습서에서는 Azure Machine Learning의 [자동화된 Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml)을 사용하여 NYC 택시 요금 가격을 예측하는 회귀 모델을 만듭니다. 이 프로세스는 학습 데이터 및 구성 설정을 적용하고, 다양한 기능 정규화/표준화 방법, 모델 및 하이퍼 매개 변수 설정의 조합을 자동으로 반복하여 최상의 모델에 도달합니다.

이 자습서에서는 다음 작업에 대해 알아봅니다.
- Apache Spark 및 Azure Open Datasets를 사용하여 데이터 다운로드
- Apache Spark 데이터 프레임을 사용하여 데이터 변환 및 정리
- 자동화된 기계 학습 회귀 모델 학습
- 모델 정확도 계산

### <a name="before-you-begin"></a>시작하기 전 주의 사항
- [Apache Spark 풀 만들기 자습서](../quickstart-create-apache-spark-pool-studio.md)에 따라 Apache Spark 풀을 만듭니다.
- 기존 Azure Machine Learning 작업 영역이 없는 경우 [Azure Machine Learning 작업 영역 설정 자습서](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup)를 완료합니다. 

### <a name="understand-regression-models"></a>회귀 모델 이해
*회귀 모델*은 독립 예측 변수를 기반으로 하여 숫자 출력 값을 예측합니다. 회귀 분석이 목표는 한 변수가 다른 변수에 미치는 영향을 추정하여 이러한 독립 예측 변수 간의 관계를 설정하는 데 도움을 주는 것입니다.  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>NYC 택시 데이터의 회귀 분석 예제
이 예제에서는 Spark를 사용하여 뉴욕의 택시 이동 팁 데이터를 분석합니다. 데이터는 [Azure 공개 데이터 세트](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)를 통해 사용할 수 있습니다. 이 데이터 세트의 하위 집합에는 각 이동에 대한 정보, 시작 및 종료 시간과 위치, 비용 및 기타 흥미로운 특성을 포함한 노랑 택시 이동에 대한 정보가 포함되어 있습니다.

> [!IMPORTANT]
> 
> 스토리지 위치에서 이 데이터를 풀하는 데 추가 요금이 있을 수 있습니다. 다음 단계에서는 NYC 택시 요금 가격을 예측하는 모델을 개발합니다. 
> 

##  <a name="download-and-prepare-the-data"></a>데이터 다운로드 및 준비

1. PySpark 커널을 사용하여 Notebook을 만듭니다. 자세한 지침은 [Notebook 만들기](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.)를 참조하세요.
   
   > [!Note]
   > 
   > PySpark 커널로 인해 컨텍스트를 명시적으로 만들 필요가 없습니다. 첫 번째 코드 셀을 실행하면 Spark 컨텍스트가 자동으로 만들어집니다.
   >

2. 원시 데이터는 Parquet 형식이기 때문에 Spark 컨텍스트를 사용하여 직접 데이터 프레임으로 파일을 메모리로 풀할 수 있습니다. Open Datasets API를 통해 데이터를 검색하여 Spark 데이터 프레임을 만듭니다. 여기서는 *read* 속성에서 Spark 데이터 프레임 스키마를 사용하여 데이터 형식 및 스키마를 유추합니다. 
   
   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "nyctlc"
   blob_relative_path = "yellow"
   blob_sas_token = r""

   # Allow Spark to read from Blob remotely
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

   # Spark read parquet, note that it won't load any data yet by now
   df = spark.read.parquet(wasbs_path)
   ```

3. Spark 풀(미리 보기)의 크기에 따라 원시 데이터가 너무 크거나 작업하는 데 너무 많은 시간이 걸릴 수 있습니다. ```start_date``` 및 ```end_date``` 필터를 사용하여 이 데이터를 더 작은 데이터로 필터링할 수 있습니다. 이 경우 한 달 분량의 데이터를 반환하는 필터를 적용합니다. 필터링된 데이터 프레임이 있으면 새 데이터 프레임에서 ```describe()``` 함수도 실행하여 각 필드에 대한 요약 통계를 확인할 수 있습니다. 

   요약 통계를 기반으로 하여 데이터에 몇 가지 불규칙성과 이상값이 있음을 알 수 있습니다. 예를 들어 통계에 따르면 최소 이동 거리가 0보다 작습니다. 이러한 불규칙한 데이터 요소를 필터링해야 합니다.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. 이제 열 세트를 선택하고 픽업 날짜/시간 필드에서 다양한 시간 기반 기능을 만들어 데이터 세트에서 기능을 생성합니다. 또한 이전 단계에서 확인된 이상값을 필터링한 다음, 학습하는 데 필요하지 않은 마지막 몇 개의 열을 제거합니다.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster and less expensive down sample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                , column('puMonth').alias('month_num') \
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                & (sampled_taxi_df.rateCodeId <= 5)\
                                & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
여기서 볼 수 있듯이 월의 날짜, 픽업 시간, 평일 및 총 이동 시간에 대한 추가 열이 포함된 새 데이터 프레임이 만들어집니다. 

![택시 데이터 프레임의 그림](./media/apache-spark-machine-learning-aml-notebook/aml-dataset.png)

## <a name="generate-test-and-validation-datasets"></a>테스트 및 유효성 검사 데이터 세트 생성

최종 데이터 세트가 있으면 Spark ```random_ split ``` 함수를 사용하여 데이터를 학습 및 테스트 세트로 분할할 수 있습니다. 이 함수는 제공된 가중치를 사용하여 모델 학습을 위한 학습 데이터 세트 및 테스트를 위한 유효성 검사 데이터 세트로 데이터를 임의로 분할합니다.

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```

이 단계에서는 데이터를 사용하여 모델을 학습시키는 데 사용되지 않은 완성된 모델을 테스트하도록 합니다. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning 작업 영역에 연결
Azure Machine Learning에서 **작업 영역**은 Azure 구독 및 리소스 정보를 수락하는 클래스입니다. 또한 클라우드 리소스를 만들어서 모델 실행을 모니터링하고 추적합니다. 이 단계에서는 기존 Azure Machine Learning 작업 영역에서 작업 영역 개체를 만듭니다.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>데이터 프레임을 Azure Machine Learning 데이터 세트로 변환
원격 실험을 제출하려면 데이터 세트를 Azure Machine Learning ```TabularDatset```로 변환해야 합니다. [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)는 제공된 파일을 구문 분석하여 데이터를 테이블 형식으로 나타냅니다.

다음 코드는 기존 작업 영역과 기본 Azure Machine Learning 기본 데이터 저장소를 가져옵니다. 그런 다음, 데이터 저장소 및 파일 위치를 경로 매개 변수에 전달하여 새 ```TabularDataset```를 만듭니다. 

```python
import pandas 
from azureml.core import Dataset

# Get the AML Default Datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into AML Tabular Dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```

![업로드된 데이터 세트의 그림](./media/apache-spark-machine-learning-aml-notebook/upload-dataset.png)

## <a name="submit-an-auto-ml-experiment"></a>자동화된 ML 실험 제출

#### <a name="define-training-settings"></a>학습 설정 정의

1. 실험을 제출하려면 학습을 위한 실험 매개 변수와 모델 설정을 정의해야 합니다. 설정의 전체 목록은 [여기](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train)서 볼 수 있습니다.

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

2. 이제 정의된 학습 설정을 \*\*kwargs 매개 변수로 AutoMLConfig 개체에 전달합니다. Spark에서 학습시키므로 ```sc``` 변수에서 자동으로 액세스할 수 있는 Spark 컨텍스트도 전달해야 합니다. 또한 학습 데이터와 모델 유형(이 경우 회귀)을 지정합니다.

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data = dataset_training,
                             spark_context = sc,
                             model_explainability = False, 
                             label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
> 자동화된 기계 학습 사전 처리 단계(기능 정규화, 누락된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용하는 경우 학습 중에 적용되는 동일한 전처리 단계가 입력 데이터에 자동으로 적용됩니다.

#### <a name="train-the-automatic-regression-model"></a>자동 회귀 모델 학습 
이제 Azure Machine Learning 작업 영역에서 실험 개체를 만듭니다. 실험은 개별 실행에 대한 컨테이너 역할을 합니다. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```

실험이 완료되면 출력에서 완료된 반복에 대한 세부 정보를 반환합니다. 각 반복의 경우 모델 유형, 실행 지속 및 학습 정확도가 표시됩니다. BEST 필드는 메트릭 유형에 따라 최상의 실행 학습 점수를 추적합니다.

![모델 출력의 스크린샷](./media/apache-spark-machine-learning-aml-notebook/aml-model-output.png)

> [!NOTE]
> 제출되면 AutoML 실험에서 다양한 반복 및 모델 유형을 실행합니다. 이 실행에는 일반적으로 1-1.5시간이 걸립니다. 

#### <a name="retrieve-the-best-model"></a>최적 모델 검색
반복 작업에서 가장 적합한 모델을 선택하기 위해 ```get_output``` 함수를 사용하여 최상의 실행 및 적합한 모델을 반환합니다. 아래 코드는 기록된 메트릭 또는 특정 반복에 대한 최상의 실행 및 적합한 모델을 검색합니다.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>모델 정확도 테스트

1. 모델 정확도를 테스트하기 위해 테스트 데이터 세트에서 택시 요금 예측을 실행하는 데 가장 적합한 모델을 사용합니다. ```predict``` 함수는 가장 적합한 모델을 사용하고 유효성 검사 데이터 세트에서 y 값(운임 금액)을 예측합니다. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

2. RMSE(제곱 평균 오차)는 모델에서 예측한 샘플 값과 관찰된 값 간의 차이를 측정하는 데 자주 사용됩니다. y_test 데이터 프레임을 모델에서 예측한 값과 비교하여 결과의 제곱 평균 오차를 계산합니다. 

   ```mean_squared_error``` 함수는 두 개의 배열을 가져와서 두 배열 간의 평균 제곱 오차를 계산합니다. 그런 다음, 결과의 제곱근을 사용합니다. 이 메트릭은 택시 요금 예측과 실제 요금 값의 차이를 대략적으로 나타냅니다.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate Root Mean Square Error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   
   제곱 평균 오차는 모델에서 예측하는 응답에 대한 정확도를 보여주는 좋은 척도입니다. 결과에서 모델이 데이터 세트의 기능에서 택시 요금(일반적으로 $2.00 내외)을 예측하는 데 상당히 적합하다는 것을 알 수 있습니다.

3. 다음 코드를 실행하여 MAPE(절대 백분율 평균 오차)를 계산합니다. 이 메트릭은 정확도를 오류에 대한 백분율로 나타냅니다. 이 작업은 각 예측 값과 실제 값 간의 절대 차이를 계산한 다음, 모든 차이의 합계를 계산하여 수행됩니다. 그런 다음, 해당 합계를 실제 값의 합계에 대한 백분율로 나타냅니다.

   ```python
   # Calculate MAPE and Model Accuracy 
   sum_actuals = sum_errors = 0

   for actual_val, predict_val in zip(y_actual, y_predict):
       abs_error = actual_val - predict_val
       if abs_error < 0:
           abs_error = abs_error * -1

       sum_errors = sum_errors + abs_error
       sum_actuals = sum_actuals + actual_val

   mean_abs_percent_error = sum_errors / sum_actuals

   print("Model MAPE:")
   print(mean_abs_percent_error)
   print()
   print("Model Accuracy:")
   print(1 - mean_abs_percent_error)
   ```

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   두 가지 예측 정확도 메트릭에서 모델이 데이터 세트의 기능에서 택시 요금을 예측하는 데 상당히 적합하다는 것을 알 수 있습니다. 

4. 적합한 선형 회귀 모델이 준비되면 이제 모델이 데이터에 적합한 정도를 확인해야 합니다. 이를 위해 예측 출력에 대한 실제 요금 값을 도표에 표시합니다. 또한 R 제곱 측정값도 계산하여 데이터가 적합 회귀선에 얼마나 가까운지 파악합니다.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the Actual vs Predicted Fare Amount Values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amont R^2={}".format(r2))
   plt.show()
   ```
   
   ![회귀 도표의 스크린샷](./media/apache-spark-machine-learning-aml-notebook/aml-fare-amount.png)

   결과에서 R 제곱 측정값이 분산의 95%를 차지한다는 것을 알 수 있습니다. 또한 이는 실제 값 및 관찰된 값의 도표를 통해서도 확인됩니다. 회귀 모델에서 설명하는 분산이 많을수록 데이터 요소가 적합 회귀선에 더 가깝습니다.  

## <a name="register-model-to-azure-machine-learning"></a>Azure Machine Learning에 모델 등록
최상의 모델이 확인되면 모델을 Azure Machine Learning에 등록할 수 있습니다. 모델이 등록되면 등록된 모델을 다운로드하거나 배포하고 등록된 모든 파일을 받을 수 있습니다.

```python
description = 'My AutoML Model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```

```Output
NYCGreenTaxiModel 1
```

## <a name="view-results-in-azure-machine-learning"></a>Azure Machine Learning에서 결과 보기
마지막으로 Azure Machine Learning 작업 영역에서 실험으로 이동하여 반복 결과에 액세스할 수도 있습니다. 여기서는 실행 상태, 시도된 모델 및 기타 모델 메트릭에 대한 추가 세부 정보를 살펴볼 수 있습니다. 

![AML 작업 영역의 스크린샷](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-aml-workspace.png)

## <a name="next-steps"></a>다음 단계
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark MLlib 자습서](./apache-spark-machine-learning-mllib-notebook.md)
