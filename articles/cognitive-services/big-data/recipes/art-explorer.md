---
title: '레시피: 빅 데이터용 Cognitive Services로 지능형 예술 탐색'
titleSuffix: Azure Cognitive Services
description: 이 레시피는 Azure Search 및 MMLSpark를 사용하여 검색 가능한 예술 데이터베이스를 만드는 방법을 보여 줍니다.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a65ff28a38e42e05844063a330c0325f16b2247
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94363292"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>레시피: 빅 데이터용 Cognitive Services로 지능형 예술 탐색

이 예에서는 빅 데이터용 Cognitive Services를 사용하여 MET(메트로폴리탄 미술관)의 오픈 액세스 컬렉션에 지능형 주석을 추가합니다. 이렇게 하면 수동 주석 없이도 Azure Search를 사용하여 지능형 검색 엔진을 만들 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Computer Vision 및 Cognitive Search에 대한 구독 키가 있어야 합니다. [Cognitive Services 계정 만들기](../../cognitive-services-apis-create-account.md)의 지침에 따라 Computer Vision을 구독하고 키를 가져옵니다.
  > [!NOTE]
  > 가격 책정 정보는 [Azure Cognitive Search](https://azure.microsoft.com/services/search/#pricing)를 참조하세요.

## <a name="import-libraries"></a>라이브러리 가져오기

다음 명령을 실행하여 이 레시피의 라이브러리를 가져옵니다.

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>구독 키 설정

다음 명령을 실행하여 서비스 키에 대한 변수를 설정합니다. Computer Vision 및 Azure Cognitive Search에 대한 구독 키를 삽입합니다.

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>데이터 읽기

다음 명령을 실행하여 MET의 오픈 액세스 컬렉션에서 데이터를 로드합니다.

```python
data = spark.read\
  .format("csv")\
  .option("header", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/metartworks_sample.csv")\
  .withColumn("searchAction", lit("upload"))\
  .withColumn("Neighbors", split(col("Neighbors"), ",").cast("array<string>"))\
  .withColumn("Tags", split(col("Tags"), ",").cast("array<string>"))\
  .limit(25)
```

<a name="AnalyzeImages"></a>

## <a name="analyze-the-images"></a>이미지 분석

다음 명령을 실행하여 MET의 오픈 액세스 예술 작품 컬렉션에서 Computer Vision을 사용합니다. 결과적으로 예술 작품의 시각적 특징을 얻게 됩니다.

```python
from mmlspark.cognitive import AnalyzeImage
from mmlspark.stages import SelectColumns

#define pipeline
describeImage = (AnalyzeImage()
  .setSubscriptionKey(VISION_API_KEY)
  .setLocation("eastus")
  .setImageUrlCol("PrimaryImageUrl")
  .setOutputCol("RawImageDescription")
  .setErrorCol("Errors")
  .setVisualFeatures(["Categories", "Tags", "Description", "Faces", "ImageType", "Color", "Adult"])
  .setConcurrency(5))

df2 = describeImage.transform(data)\
  .select("*", "RawImageDescription.*").drop("Errors", "RawImageDescription")
```

<a name="CreateSearchIndex"></a>

## <a name="create-the-search-index"></a>검색 인덱스 만들기

Azure Search에 결과를 기록하는 다음 명령을 실행하여 Computer Vision의 보강된 메타데이터로 예술 작품 검색 엔진을 만듭니다.

```python
from mmlspark.cognitive import *
df2.writeToAzureSearch(
  subscriptionKey=AZURE_SEARCH_KEY,
  actionCol="searchAction",
  serviceName=search_service,
  indexName=search_index,
  keyCol="ObjectID"
)
```

## <a name="query-the-search-index"></a>검색 인덱스 쿼리

다음 명령을 실행하여 Azure Search 인덱스를 쿼리합니다.

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>다음 단계

[변칙 검색을 위한 빅 데이터용 Cognitive Services](anomaly-detection.md)를 사용하는 방법을 알아봅니다.