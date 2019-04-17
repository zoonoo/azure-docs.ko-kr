---
title: '빠른 시작: 비정상 탐지기 REST API 및 Java를 사용 하 여 시계열 데이터에서 이상을 감지 | Microsoft Docs'
description: 일괄 처리 또는 스트리밍 데이터에서 데이터 계열에서 변칙을 비정상 탐지기 API를 사용 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 1c8ce91a0fd8805b307e1e21bc08f9050b8a47d4
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547042"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>빠른 시작: 비정상 탐지기 REST API 및 Java를 사용 하 여 시계열 데이터에서 변칙을 검색 합니다.

비정상 탐지기 API의 두 가지 검색 모드를 사용 하 여 시계열 데이터에서 변칙을 검색 하도록 하려면이 빠른 시작을 사용 합니다. 이 Java 응용 프로그램 JSON 형식 시계열 데이터를 포함 하는 두 개의 API 요청을 보내고 응답을 가져옵니다.

| API 요청                                        | 애플리케이션 출력                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 일괄 처리로 변칙을 검색 합니다.                        | 시계열 데이터를 모든 검색 된 변칙의 위치에 각 데이터 요소에 대 한 비정상 상태 (및 기타 데이터)를 포함 하는 JSON 응답입니다. |
| 최신 데이터 요소의 비정상 상태 검색 | 시계열 데이터에서 최신 데이터 요소에 대 한 비정상 상태 (및 기타 데이터) 포함 된 JSON 응답입니다.                                                                                                                                         |

 이 애플리케이션은 Java로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

## <a name="prerequisites"></a>필수 조건

- 합니다 [Java&trade; 개발 Kit(JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 이상.

- Maven 리포지토리에서 이러한 라이브러리 가져오기
    - [Java에서 JSON](https://mvnrepository.com/artifact/org.json/json) 패키지
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) 패키지

- JSON 파일이 포함 된 시계열 데이터 요소입니다. 이 빠른 시작에 대 한 예제 데이터에서 찾을 수 있습니다 [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)합니다.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>새 애플리케이션 만들기

1. 즐겨 찾는 IDE 또는 편집기에서 새 Java 프로젝트를 만들고 다음 라이브러리를 가져옵니다.

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONObject;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. 구독 키 및 끝점에 대 한 변수를 만듭니다. 다음은 Uri 변칙 검색에 사용할 수 있습니다. 나중에 API를 만드는 서비스 끝점에 추가할 수는 이러한 Url을 요청 합니다.

    |검색 방법  |URI  |
    |---------|---------|
    |일괄 검색    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |최신 데이터 요소 검색     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    //replace the endpoint URL with the correct one for your subscription. Your endpoint can be found in the Azure portal. 
    //For example: https://westus2.api.cognitive.microsoft.com
    static final String endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    static final String dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    static final String latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    static final String batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

3. JSON 데이터 파일에서 읽기

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    ```

## <a name="create-a-function-to-send-requests"></a>요청을 보내는 함수 만들기

1. 라는 새 함수를 만들고 `sendRequest()` 위에서 만든 변수를 사용 합니다. 그런 후에 다음 단계를 수행합니다.

2. 만들기는 `CloseableHttpClient` API에 요청을 보낼 수 있는 개체입니다. 요청을 보낼는 `HttpPost` 끝점에 및 비정상 탐지기 URL을 결합 하 여 요청 개체입니다.

3. 요청을 사용 하 여 `setHeader()` 함수를 설정 합니다 `Content-Type` 헤더를 `application/json`, 구독 키를 추가 하 고는 `Ocp-Apim-Subscription-Key` 헤더.

4. 요청을 사용 하 여 `setEntity()` 데이터 전송에 함수입니다.

5. 클라이언트를 사용 하 여 `execute()` 요청을 보내도록 함수 및 저장 하는 `CloseableHttpResponse` 개체입니다.

6. 만들기는 `HttpEntity` 응답 콘텐츠를 저장할 개체입니다. 사용 하 여 콘텐츠를 가져올 `getEntity()`합니다. 응답 비어 있지 않은 경우이 반환 합니다.

```java
static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
    try (CloseableHttpClient client = HttpClients.createDefault()) {
        HttpPost request = new HttpPost(endpoint + apiAddress);
        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
        request.setEntity(new StringEntity(requestData));
        try (CloseableHttpResponse response = client.execute(request)) {
            HttpEntity respEntity = response.getEntity();
            if (respEntity != null) {
                return EntityUtils.toString(respEntity, "utf-8");
            }
        } catch (Exception respEx) {
            respEx.printStackTrace();
        }
    } catch (IOException ex) {
        System.err.println("Exception on Anomaly Detector: " + ex.getMessage());
        ex.printStackTrace();
    }
    return null;
}
```

## <a name="detect-anomalies-as-a-batch"></a>일괄 처리로 변칙을 검색 합니다.

1. 이라는 메서드를 만듭니다 `detectAnomaliesBatch()` 에 전체 일괄 처리로 데이터에서 변칙을 검색 합니다. 호출 된 `sendRequest()` 끝점, url, 구독 키 및 json 데이터를 사용 하 여 위에서 만든 메서드. 결과 가져오고 콘솔에 인쇄 합니다.

2. 응답에 포함 하는 경우 `code` 필드, 오류 코드 및 오류 메시지를 인쇄 합니다.

3. 이 고, 그렇지 데이터 집합의 한 위치를 찾습니다. 응답의 `isAnomaly` 필드에 지정 된 데이터 요소가 비정상 인지와 관련 된 부울 값을 포함 합니다. JSON 배열을 가져오고 인쇄의 인덱스를 통해 반복 `true` 값입니다. 이러한 값은 발견 된 경우 비정상적인 데이터 요소의 인덱스에 해당 합니다.

```java
static void detectAnomaliesBatch(String requestData) {
    System.out.println("Detecting anomalies as a batch");
    String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
    if (result != null) {
        System.out.println(result);
        JSONObject jsonObj = new JSONObject(result);
        if (jsonObj.has("code")) {
            System.out.println(String.format("Detection failed. ErrorCode:%s, ErrorMessage:%s", jsonObj.getString("code"), jsonObj.getString("message")));
        } else {
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>최신 데이터 요소의 비정상 상태 검색

* 이라는 메서드를 만듭니다 `detectAnomaliesLatest()` 데이터 집합의 마지막 데이터 요소의 비정상 상태를 검색 하 합니다. 호출 된 `sendRequest()` 끝점, url, 구독 키 및 json 데이터를 사용 하 여 위에서 만든 메서드. 결과 가져오고 콘솔에 인쇄 합니다.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>시계열 데이터를 로드 하 고 요청을 보내기

1. 응용 프로그램의 main 메서드를 요청에 추가 될 데이터를 포함 하는 JSON 파일에서을 읽습니다.

2. 위에서 만든 두 변칙 검색 함수를 호출 합니다.

```java
public static void main(String[] args) throws Exception {
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);
}
```

### <a name="example-response"></a>예제 응답

성공적인 응답은 JSON 형식으로 반환 됩니다. GitHub에서 JSON 응답을 보려면 아래 링크를 클릭 합니다.
* [일괄 처리 검색 응답 예제](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [최신 지점 검색 응답 예제](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)