---
title: 레 레 레 레 레 레 레 레 레 레 레 레 레 레 레 Cognitive Services 레 커
titleSuffix: Azure Cognitive Services
description: 이 작성법은 Azure Search 및 MMLSpark를 사용 하 여 검색 가능한 art 데이터베이스를 만드는 방법을 보여 줍니다.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 0a94c66eb51298db226ceec5da5c86666576052a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87850494"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>레 레 레 레 레 레 레 레 레 레 레 레 레 레 레 Cognitive Services 레 커

이 예제에서는 빅 데이터에 대 한 Cognitive Services를 사용 하 여 대도시 박물관 (충족)에서 개방형 액세스 컬렉션에 지능형 주석을 추가 합니다. 이렇게 하면 수동 주석을 사용 하지 않아도 Azure Search를 사용 하 여 지능형 검색 엔진을 만들 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

* Computer Vision 및 Cognitive Search에 대 한 구독 키가 있어야 합니다. [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 의 지침에 따라 Computer Vision를 구독 하 고 키를 가져옵니다.
  > [!NOTE]
  > 가격 책정 정보는 [Azure Cognitive Search](https://azure.microsoft.com/services/search/#pricing)를 참조 하세요.

## <a name="import-libraries"></a>라이브러리 가져오기

다음 명령을 실행 하 여이 조리법의 라이브러리를 가져옵니다.

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>구독 키 설정

다음 명령을 실행 하 여 서비스 키에 대 한 변수를 설정 합니다. Computer Vision 및 Azure Cognitive Search에 대 한 구독 키를 삽입 합니다.

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>데이터 읽기

다음 명령을 실행 하 여 충족 된의 열려 있는 액세스 컬렉션에서 데이터를 로드 합니다.

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

다음 명령을 실행 하 여 충족 된 Open Access 아트 워크 collection에 Computer Vision를 사용 합니다. 결과적으로 아트 워크의 시각적 기능을 얻을 수 있습니다.

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

다음 명령을 실행 하 Azure Search에 결과를 기록 하 여 Computer Vision에서 보강 메타 데이터를 사용 하 여 아트 워크의 검색 엔진을 만듭니다.

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

Azure Search 인덱스를 쿼리하려면 다음 명령을 실행 합니다.

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>다음 단계

[변칙 검색을 위해 빅 데이터에 Cognitive Services](anomaly-detection.md)를 사용 하는 방법을 알아봅니다.

