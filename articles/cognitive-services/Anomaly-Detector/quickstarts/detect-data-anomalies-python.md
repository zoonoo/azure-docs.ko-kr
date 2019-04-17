---
title: '빠른 시작: 비정상 탐지기 REST API 및 Python을 사용 하 여 일괄 처리로 변칙을 검색 합니다. | Microsoft Docs'
description: 일괄 처리 또는 스트리밍 데이터에서 데이터 계열에서 변칙을 비정상 탐지기 API를 사용 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 6b4ddcadfe63f74d115c155354a276e45c6b53f9
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544503"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>빠른 시작: 비정상 탐지기 REST API 및 Python을 사용 하 여 시계열 데이터에서 변칙을 검색 합니다.

비정상 탐지기 API의 두 가지 검색 모드를 사용 하 여 시계열 데이터에서 변칙을 검색 하도록 하려면이 빠른 시작을 사용 합니다. 이 Python 응용 프로그램 JSON 형식 시계열 데이터를 포함 하는 두 개의 API 요청을 보내고 응답을 가져옵니다.

| API 요청                                        | 애플리케이션 출력                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 일괄 처리로 변칙을 검색 합니다.                        | 시계열 데이터를 모든 검색 된 변칙의 위치에 각 데이터 요소에 대 한 비정상 상태 (및 기타 데이터)를 포함 하는 JSON 응답입니다. |
| 최신 데이터 요소의 비정상 상태 검색 | 시계열 데이터에서 최신 데이터 요소에 대 한 비정상 상태 (및 기타 데이터) 포함 된 JSON 응답입니다.                                                                                                                                         |

 이 애플리케이션은 Python으로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

## <a name="prerequisites"></a>필수 조건

- [Python 2.x 또는 3.x](https://www.python.org/downloads/)

- 합니다 [요청 라이브러리](http://docs.python-requests.org) python에 대 한

- JSON 파일이 포함 된 시계열 데이터 요소입니다. 이 빠른 시작에 대 한 예제 데이터에서 찾을 수 있습니다 [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)합니다.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>새 애플리케이션 만들기

1. 원하는 텍스트 편집기 또는 IDE에서 새 python 파일을 만듭니다. 다음 가져오기를 추가 합니다.

    ```python
    import requests
    import json
    ```

2. 구독 키 및 끝점에 대 한 변수를 만듭니다. 다음은 Uri 변칙 검색에 사용할 수 있습니다. 나중에 API를 만드는 서비스 끝점에 추가할 수는 이러한 Url을 요청 합니다.

    |검색 방법  |URI  |
    |---------|---------|
    |일괄 검색    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |최신 데이터 요소 검색     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. 열고 사용 하 여 JSON 데이터 파일을 읽어 `json.load()`합니다.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>요청을 보내는 함수 만들기

1. 라는 새 함수를 만들고 `send_request()` 위에서 만든 변수를 사용 합니다. 그런 후에 다음 단계를 수행합니다.

2. 요청 헤더에 대 한 사전을 만듭니다. 설정 합니다 `Content-Type` 에 `application/json`, 구독 키를 추가 하 고는 `Ocp-Apim-Subscription-Key` 헤더입니다.

3. 사용 하 여 요청을 보낼 `requests.post()`합니다. 헤더 및 json 요청 데이터가 포함 및 전체 요청 URL에 대 한 끝점 및 변칙 검색 URL을 결합 합니다. 및 다음 응답을 반환 합니다.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>일괄 처리로 변칙을 검색 합니다.

1. 이라는 메서드를 만듭니다 `detect_batch()` 에 전체 일괄 처리로 데이터에서 변칙을 검색 합니다. 호출 된 `send_request()` 끝점, url, 구독 키 및 json 데이터를 사용 하 여 위에서 만든 메서드.

2. 호출 `json.dumps()` 결과 서식을 지정 하 여 콘솔에 인쇄 합니다.

3. 응답에 포함 하는 경우 `code` 필드, 오류 코드 및 오류 메시지를 인쇄 합니다.

4. 이 고, 그렇지 데이터 집합의 한 위치를 찾습니다. 응답의 `isAnomaly` 필드에 지정 된 데이터 요소가 비정상 인지와 관련 된 부울 값을 포함 합니다. 목록을 반복 하 고 모든 인덱스를 인쇄 `True` 값입니다. 이러한 값은 발견 된 경우 비정상적인 데이터 요소의 인덱스에 해당 합니다.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print (x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>최신 데이터 요소의 비정상 상태 검색

1. 이라는 메서드를 만듭니다 `detect_latest()` 에 시계열의 최신 데이터 요소 비정상 인지 확인할 수 있습니다. 호출 된 `send_request()` 끝점, url, 구독 키 및 json 데이터를 사용 하 여 위의 메서드. 

2. 호출 `json.dumps()` 결과 서식을 지정 하 여 콘솔에 인쇄 합니다.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>시계열 데이터를 로드 하 고 요청을 보내기

1. JSON 시계열 데이터는 파일 처리기를 열고 사용 하 여 로드 `json.load()` 에 있습니다. 그런 다음 변칙 검색 방법을 위에서 만든 호출.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>예제 응답

성공적인 응답은 JSON 형식으로 반환 됩니다. GitHub에서 JSON 응답을 보려면 아래 링크를 클릭 합니다.
* [일괄 처리 검색 응답 예제](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [최신 지점 검색 응답 예제](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)