---
title: '빠른 시작: Anomaly Detector REST API 및 Python을 사용하여 일괄 처리로 변칙 검색'
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API를 사용하여 일괄 처리로 또는 스트리밍 데이터로 데이터 계열에서 변칙을 검색합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: aahi
ms.openlocfilehash: 571626da0f3f43c8c2a2e33e1147418158c5473b
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754211"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>빠른 시작: Anomaly Detector REST API 및 Python을 사용하여 시계열 데이터에서 변칙 검색

이 빠른 시작을 통해 Anomaly Detector API의 두 가지 검색 모드를 사용하여 시계열 데이터에서 변칙을 검색합니다. 이 Python 애플리케이션은 JSON 형식의 시계열 데이터가 포함된 2개의 API 요청을 보내고 응답을 받습니다.

| API 요청                                        | 애플리케이션 출력                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 일괄 처리로 변칙 검색                        | 시계열 데이터의 각 데이터 요소에 대한 변칙 상태(및 기타 데이터)와 검색된 변칙의 위치가 포함된 JSON 응답입니다. |
| 최신 데이터 요소의 변칙 상태 검색 | 시계열 데이터의 최신 데이터 요소에 대한 변칙 상태(및 기타 데이터)가 포함된 JSON 응답입니다.                                                                                                                                         |

 이 애플리케이션은 Python으로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

## <a name="prerequisites"></a>필수 조건

- [Python 2.x 또는 3.x](https://www.python.org/downloads/)

- Python에 대한 [요청 라이브러리](https://pypi.org/project/requests/)

- 시계열 데이터 요소가 포함된 JSON 파일입니다. 이 빠른 시작의 예제 데이터는 [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)에서 확인할 수 있습니다.

### <a name="create-an-anomaly-detector-resource"></a>Anomaly Detector 리소스 만들기

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]


## <a name="create-a-new-application"></a>새 애플리케이션 만들기

1. 새 Python 파일을 만들고 다음 가져오기를 추가합니다.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. 구독 키 및 엔드포인트에 대한 변수를 만듭니다. 아래는 변칙 검색에 사용할 수 있는 URI입니다. 나중에 API 요청 URL을 만드는 서비스 엔드포인트에 추가됩니다.

    |검색 방법  |URI  |
    |---------|---------|
    |일괄 검색    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |최신 데이터 요소에서 검색     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. JSON 데이터 파일을 열고 `json.load()`를 사용하여 읽습니다.

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>요청을 보내는 함수 만들기

1. 위에서 만든 변수를 사용하는 `send_request()`라는 새 함수를 만듭니다. 그런 후에 다음 단계를 수행합니다.

2. 요청 헤더에 대한 사전을 만듭니다. `Content-Type`을 `application/json`으로 설정하고 구독 키를 `Ocp-Apim-Subscription-Key` 헤더에 추가합니다.

3. `requests.post()`를 사용하여 요청을 보냅니다. 전체 요청 URL에 엔드포인트와 변칙 검색 URL을 결합하고 헤더 및 json 요청 데이터를 포함합니다. 그런 다음, 응답을 반환합니다.

[!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>일괄 처리로 변칙 검색

1. `detect_batch()`라는 메서드를 만들어서 데이터 전체의 변칙을 일괄 처리로 검색합니다. 위에서 만든 `send_request()` 메서드를 엔드포인트, URL, 구독 키 및 JSON 데이터로 호출합니다.

2. 결과에서 `json.dumps()`를 호출하여 형식을 지정하고 콘솔에 출력합니다.

3. 응답에 `code` 필드가 포함된 경우에는 오류 코드 및 오류 메시지를 출력합니다.

4. 그렇지 않으면 데이터 세트에서 변칙의 위치를 찾습니다. 응답의 `isAnomaly` 필드는 지정된 데이터 요소가 변칙인지 여부와 관련된 부울 값을 포함합니다. 목록을 반복하고 `True` 값의 인덱스를 출력합니다. 이 값은 변칙 데이터 요소의 인덱스와 일치합니다(발견된 경우).

[!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>최신 데이터 요소의 변칙 상태 검색

1. `detect_latest()`라는 메서드를 만들어 시계열의 최신 데이터 요소가 변칙인지 확인합니다. 위의 `send_request()` 메서드를 엔드포인트, URL, 구독 키 및 JSON 데이터로 호출합니다. 

2. 결과에서 `json.dumps()`를 호출하여 형식을 지정하고 콘솔에 출력합니다.

[!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>요청 보내기

1. 위에서 만든 변칙 검색 메서드를 호출합니다.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>예제 응답

성공 응답이 JSON 형식으로 반환됩니다. 아래 링크를 클릭하면 GitHub에서 JSON 응답을 볼 수 있습니다.
* [일괄 검색 응답 예제](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [최신 요소 검색 응답 예제](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Azure Databricks를 사용하여 스트리밍 변칙 검색](../tutorials/anomaly-detection-streaming-databricks.md)

* [Anomaly Detector API](../overview.md)란?
* Anomaly Detector API를 사용하는 경우 [모범 사례](../concepts/anomaly-detection-best-practices.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)에서 확인할 수 있습니다.
