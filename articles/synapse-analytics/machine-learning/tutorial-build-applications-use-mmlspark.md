---
title: '자습서: Microsoft Machine Learning for Apache Spark를 사용하여 기계 학습 애플리케이션 빌드(미리 보기)'
description: Microsoft Machine Learning for Apache Spark를 사용하여 Azure Synapse Analytics에서 기계 학습 애플리케이션을 만드는 방법에 대해 알아봅니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 03/08/2021
author: ruixinxu
ms.author: ruxu
ms.openlocfilehash: 928e2ef8b373626a91a291b1798f3ebb7ef290e8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608836"
---
# <a name="tutorial-build-machine-learning-applications-using-microsoft-machine-learning-for-apache-spark-preview"></a>자습서: Microsoft Machine Learning for Apache Spark를 사용하여 기계 학습 애플리케이션 빌드(미리 보기)

이 문서에서는 Microsoft Machine Learning for Apache Spark([MMLSpark](https://github.com/Azure/mmlspark))를 사용하여 기계 학습 애플리케이션을 만드는 방법에 대해 설명합니다. MMLSpark는 [Azure Cognitive Services](../../cognitive-services/big-data/cognitive-services-for-big-data.md), [OpenCV](https://opencv.org/), [LightGBM](https://github.com/Microsoft/LightGBM) 등과 같은 다양한 딥 러닝 및 데이터 과학 도구를 추가하여 Apache Spark의 분산 기계 학습 솔루션을 확장합니다.  MMLSpark를 사용하면 다양한 Spark 데이터 원본에서 강력하고 확장성이 뛰어난 예측 및 분석 모델을 빌드할 수 있습니다.
Synapse Spark는 다음을 비롯한 기본 제공 MMLSpark 라이브러리를 제공합니다.

- [Vowpal Wabbit](https://github.com/Azure/mmlspark/blob/master/docs/vw.md) – 트윗의 감정 분석과 같은 텍스트 분석을 가능하게 하는 기계 학습을 위한 라이브러리 서비스입니다.
- [Spark의 Cognitive Services](https://github.com/Azure/mmlspark/blob/master/docs/cogsvc.md) - 변칙 검색과 같은 인식 데이터 모델링 서비스를 위한 솔루션 디자인을 도출하기 위해 SparkML 파이프라인에서 Azure Cognitive Services의 기능을 결합합니다.
- [LightBGM](https://github.com/Azure/mmlspark/blob/master/docs/lightgbm.md) – LightBGM은 트리 기반 학습 알고리즘을 사용하는 경사 부스팅 프레임워크입니다. 배포하고 효율성을 높일 수 있도록 설계되었습니다.
- 조건부 KNN - 조건부 쿼리를 사용하는 확장 가능한 KNN 모델.
- [Spark의 HTTP](https://github.com/Azure/mmlspark/blob/master/docs/http.md) – Spark 및 HTTP 프로토콜 기반 접근성 통합에서 분산 마이크로 서비스 오케스트레이션을 지원합니다.

이 자습서에서는 다음에 대한 MMLSpark의 Azure Cognitive Services를 사용하는 샘플을 다룹니다. 

- Text Analytics - 문장 세트의 감정(또는 분위기)을 가져옵니다.
- Computer Vision - 이미지 세트와 연결된 태그(한 단어 설명)를 가져옵니다.
- Bing Image Search - 웹에서 자연어 쿼리와 관련된 이미지를 검색합니다.
- Anomaly Detector - 시계열 데이터 내의 변칙을 감지합니다.

Azure 구독이 없는 경우 [시작하기 전에 체험 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>필수 구성 요소 

- [Azure Synapse Analytics 작업 영역](../get-started-create-workspace.md)(기본 스토리지로 구성된 Azure Data Lake Storage Gen2 스토리지 계정이 있음). 사용하는 Data Lake Storage Gen2 파일 시스템의 *Storage Blob 데이터 기여자* 여야 합니다.
- Azure Synapse Analytics 작업 영역의 Spark 풀. 자세한 내용은 [Azure Synapse에서 Spark 풀 만들기](../quickstart-create-sql-pool-studio.md)를 참조하세요.
- [Azure Synaps에서 Cognitive Services 구성](./tutorial-configure-cognitive-services-synapse.md) 자습서에서 설명하는 사전 구성 단계


## <a name="get-started"></a>시작
시작하려면 mmlspark를 가져오고 서비스 키를 구성합니다. 

```python
import mmlspark
mmlspark.__spark_package_version__ # current version: 1.0.0-rc3-6-a862d6b1-SNAPSHOT

from mmlspark.cognitive import *
from notebookutils import mssparkutils

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY" 
# An Anomaly Dectector subscription key
anomaly_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# Your linked key vault for Synapse workspace
key_vault = "YOUR_KEY_VAULT_NAME"


cognitive_service_key = mssparkutils.credentials.getSecret(key_vault, service_key)
bingsearch_service_key = mssparkutils.credentials.getSecret(key_vault, bing_search_key)
anomalydetector_key = mssparkutils.credentials.getSecret(key_vault, anomaly_key)

```


## <a name="text-analytics-sample"></a>텍스트 분석 샘플

[Text Analytics](../../cognitive-services/text-analytics/index.yml) 서비스는 텍스트에서 인텔리전트 인사이트를 추출하기 위한 몇 가지 알고리즘을 제공합니다. 예를 들어 지정된 입력 텍스트의 감정을 찾을 수 있습니다. 이 서비스는 0.0과 1.0 사이의 점수를 반환합니다. 여기서 낮은 점수는 부정적인 감정을 나타내고, 높은 점수는 긍정적인 감정을 나타냅니다. 이 샘플에서는 세 개의 간단한 문장을 사용하고, 각각에 대한 감정을 반환합니다.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df_sentences = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"), 
  ("this is a dog", "en-US"), 
  ("I am frustrated by this rush hour traffic!", "en-US") 
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format

display(sentiment.transform(df_sentences).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-results"></a>예상 결과

|text | 감정|
|--|--|
| 이 러시아워 교통 체증 때문에 답답해요! | 부정 |
| 강아지입니다. | 중립 |
| I am so happy today, its sunny! | 긍정 |

## <a name="computer-vision-sample"></a>컴퓨터 버전 샘플
[Computer Vision](../../cognitive-services/computer-vision/index.yml)은 이미지를 분석하여 얼굴, 개체 및 자연어 설명과 같은 구조를 식별합니다. 이 샘플에서는 다음 이미지에 태그를 지정합니다. 태그는 인식 가능한 개체, 사람, 경치, 동작과 같이 이미지의 항목에 대한 한 단어 설명입니다.


![이미지](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg)

```python
# Create a dataframe with the image URL
df_images = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts information from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df_images).select("image", "analysis_results.description.tags"))
```
### <a name="expected-results"></a>예상 결과

|이미지 | tags|
|--|--|
| `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg` | [skating, person, man, outdoor, riding, sport, skateboard, young, board, shirt, air, park, boy, side, jumping, ramp, trick, doing, flying] |

## <a name="bing-image-search-sample"></a>Bing 이미지 검색 샘플
[Bing Image Search](../../cognitive-services/bing-image-search/overview.md)는 웹을 검색하여 사용자의 자연어 쿼리와 관련된 이미지를 검색합니다. 이 샘플에서는 따옴표를 사용하여 이미지를 찾는 텍스트 쿼리를 사용합니다. 쿼리와 관련된 사진이 포함된 이미지 URL의 목록을 반환합니다.


```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 2
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(10)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bingsearch_service_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")
pipeline_bingsearch = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
res_bingsearch = pipeline_bingsearch.transform(bingParameters)
display(res_bingsearch.dropDuplicates())
```

### <a name="expected-results"></a>예상 결과

|이미지 | 
|--|
|`http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg` |
|`http://www.scrolldroll.com/wp-content/uploads/2017/06/6-25.png` |
| `http://abettertodaymedia.com/wp-content/uploads/2017/01/86783bd7a92960aedd058c91a1d10253.jpg`|
| `https://weneedfun.com/wp-content/uploads/2016/05/martin-luther-king-jr-quotes-11.jpg` |
| `http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg` |
| `https://cdn.quotesgram.com/img/72/57/1104209728-martin_luther_king_jr_quotes_16.jpg` |
| `http://comicbookandbeyond.com/wp-content/uploads/2019/05/Martin-Luther-King-Jr.-Quotes.jpg` |
| `https://exposingthepain.files.wordpress.com/2015/01/martin-luther-king-jr-quotes-08.png` |
| `https://topmemes.me/wp-content/uploads/2020/01/Top-10-Martin-Luther-King-jr.-Quotes2-1024x538.jpg` |
| `http://img.picturequotes.com/2/581/580286/dr-martin-luther-king-jr-quote-1-picture-quote-1.jpg` |
| `http://parryz.com/wp-content/uploads/2017/06/Amazing-Martin-Luther-King-Jr-Quotes.jpg` |
| `http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes1.jpg` |
| `https://lessonslearnedinlife.net/wp-content/uploads/2020/05/Martin-Luther-King-Jr.-Quotes-2020.jpg` |
| `https://quotesblog.net/wp-content/uploads/2015/10/Martin-Luther-King-Jr-Quotes-Wallpaper.jpg` |

## <a name="anomaly-detector-sample"></a>변칙 감지기 샘플

[Anomaly Detector](../../cognitive-services/anomaly-detector/index.yml)는 시계열 데이터의 변칙을 감지하는 데 유용합니다. 이 샘플에서는 서비스를 사용하여 전체 시계열에서 변칙을 찾습니다.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df_timeseriesdata = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0), # anomaly
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0) # anomaly
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomalydetector_key)
  .setLocation("eastasia")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df_timeseriesdata).select("timestamp", "value", "anomalies.isAnomaly"))

```

### <a name="expected-results"></a>예상 결과

|timestamp | 값 | isAnomaly |
|--|--|--|
| 1972-01-01T00:00:00Z|826.0|false|
|1972-02-01T00:00:00Z|799.0|false|
|1972-03-01T00:00:00Z|890.0|false|
|1972-04-01T00:00:00Z|900.0|false|
|1972-05-01T00:00:00Z|766.0|false|
|1972-06-01T00:00:00Z|805.0|false|
|1972-07-01T00:00:00Z|821.0|false|
|1972-08-01T00:00:00Z|20000.0|true|
|1972-09-01T00:00:00Z|883.0|false|
|1972-10-01T00:00:00Z|898.0|false|
|1972-11-01T00:00:00Z|957.0|false|
|1972-12-01T00:00:00Z|924.0|false|
|1973-01-01T00:00:00Z|881.0|false|
|1973-02-01T00:00:00Z|837.0|false|
|1973-03-01T00:00:00Z|9000.0|true|

## <a name="next-steps"></a>다음 단계

* [Synapse 샘플 Notebooks 체크 아웃](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks) 
* [MMLSpark GitHub 리포지토리](https://github.com/Azure/mmlspark)