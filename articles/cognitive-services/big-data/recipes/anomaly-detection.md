---
title: '레시피: 빅 데이터용 Cognitive Services로 예측 유지 관리'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 빅 데이터용 Cognitive Services로 분산형 변칙 검색을 수행하는 방법을 보여 줍니다.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a583d74ae0bf421a7a863a65442d250489a2f8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775394"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>레시피: 빅 데이터용 Cognitive Services로 예측 유지 관리

이 레시피는 IoT 디바이스의 예측 유지 관리를 위해 Apache Spark에서 Azure Synapse Analytics 및 Cognitive Services를 사용하는 방법을 보여 줍니다. [CosmosDB 및 Synapse Link](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) 샘플을 사용합니다. 간단하게 하기 위해 이 레시피에서는 CosmosDB 및 Synapse Link를 통해 스트리밍된 데이터를 가져오는 대신 CSV 파일에서 직접 데이터를 읽습니다. Synapse Link 샘플을 살펴보는 것이 좋습니다.

## <a name="hypothetical-scenario"></a>가상 시나리오

가상 시나리오는 IoT 디바이스가 [증기 터빈](https://en.wikipedia.org/wiki/Steam_turbine)을 모니터링하는 발전소입니다. IoTSignals 컬렉션은 각 터빈에 대한 RPM(분당 회전 수) 및 MW(메가와트) 데이터를 포함합니다. 증기 터빈의 신호를 분석하고 비정상적인 신호를 검색합니다.

임의 빈도의 데이터에는 이상값이 있을 수 있습니다. 이러한 상황에서는 회로 보호를 위해 RPM 값은 올라가고 MW 출력은 내려갑니다. 데이터가 동시에 변하지만 신호가 다른 것을 확인하는 개념입니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [서버리스 Apache Spark 풀](../../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)로 구성된 [Azure Synapse 작업 영역](../../../synapse-analytics/quickstart-create-workspace.md)

## <a name="setup"></a>설치 프로그램

### <a name="create-an-anomaly-detector-resource"></a>Anomaly Detector 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. [Azure Portal](../../cognitive-services-apis-create-account.md) 또는 [Azure CLI](../../cognitive-services-apis-create-account-cli.md)를 사용하여 Translator용 리소스를 만듭니다. 또한 다음을 수행할 수 있습니다.

- [Azure Portal](https://portal.azure.com/)에서 기존 리소스를 봅니다.

이 리소스에 대한 엔드포인트 및 키를 기록해 둡니다. 이 가이드에서 필요합니다.

## <a name="enter-your-service-keys"></a>서비스 키 입력

키 및 위치를 추가하는 것으로 시작하겠습니다.

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>DataFrame으로 데이터 가져오기

다음으로 IoTSignals 파일을 DataFrame으로 읽어오겠습니다. Synapse 작업 영역에서 새 Notebook을 열고 파일에서 DataFrame을 만듭니다.

```python
df_signals = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>Spark에서 Cognitive Services를 사용하여 변칙 검색 실행

IoT 디바이스의 신호가 비정상적인 값을 출력하는 인스턴스를 찾아서 언제 문제가 발생하는지 확인하고 예측 유지 관리를 수행하는 것이 목표입니다. 이를 위해 Spark에서 Anomaly Detector를 사용하겠습니다.

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

데이터를 살펴보겠습니다.

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

이 셀에는 다음과 같은 결과가 산출되어야 합니다.

| timestamp           |   값 | deviceId   | isAnomaly   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | dev-7      | False       |
| 2020-05-01 18:33:52 |    2976 | dev-7      | False       |
| 2020-05-01 18:33:53 |    2714 | dev-7      | False       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>디바이스 중 하나에 대한 변칙 시각화

IoTSignals.csv에는 여러 IoT 디바이스의 신호가 포함되어 있습니다. 특정 디바이스에 집중하고 디바이스의 비정상적인 출력을 시각화합니다.

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

이제 특정 디바이스에 대해 변칙을 나타내는 데이터 프레임을 만들었으므로 다음과 같은 변칙을 시각화할 수 있습니다.

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

성공하면 출력이 다음과 같이 표시됩니다.

![Anomaly Detector 플롯](../media/anomaly-output.png)

## <a name="next-steps"></a>다음 단계

Azure Cognitive Services, Azure Synapse Analytics, Azure CosmosDB로 대규모의 예측 유지 관리를 수행하는 방법을 알아봅니다. 자세한 내용은 [GitHub](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples)에서 전체 샘플을 참조하세요.
