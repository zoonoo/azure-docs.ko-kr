---
title: '빠른 시작: 비정상 탐지기 REST API를 사용 하 여 시계열 데이터에서 이상을 감지 하 고 C# | Microsoft Docs'
description: 일괄 처리 또는 스트리밍 데이터에서 데이터 계열에서 변칙을 비정상 탐지기 API를 사용 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: ffa42eb2247d163dd6a146ffb26c1f8cc5cf6d82
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922532"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>빠른 시작: 비정상 탐지기 REST API를 사용 하 여 시계열 데이터에서 이상을 감지 하 고C# 

비정상 탐지기 API의 두 가지 검색 모드를 사용 하 여 시계열 데이터에서 변칙을 검색 하도록 하려면이 빠른 시작을 사용 합니다. 이 C# 응용 프로그램이 JSON 형식 시계열 데이터를 포함 하는 두 개의 API 요청을 보내고 응답을 가져옵니다.

| API 요청                                        | 애플리케이션 출력                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 일괄 처리로 변칙을 검색 합니다.                        | 시계열 데이터를 모든 검색 된 변칙의 위치에 각 데이터 요소에 대 한 비정상 상태 (및 기타 데이터)를 포함 하는 JSON 응답입니다. |
| 최신 데이터 요소의 비정상 상태 검색 | 시계열 데이터에서 최신 데이터 요소에 대 한 비정상 상태 (및 기타 데이터) 포함 된 JSON 응답입니다.                                                                                                                                         |

 이 애플리케이션은 C#으로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

## <a name="prerequisites"></a>필수 조건

- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)의 모든 버전.
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Visual studio에서 NuGet 패키지로 Newtonsoft.Json을 설치 합니다.
        1. 마우스 오른쪽 단추로 클릭는 **솔루션 관리자**
        2.  **NuGet 패키지 관리...**
        3. 검색할 `Newtonsoft.Json` 패키지 및 설치
- Linux/MacOS를 사용 하는 경우이 응용 프로그램 실행할 수 있습니다 사용 하 여 [Mono](https://www.mono-project.com/)합니다.

- JSON 파일이 포함 된 시계열 데이터 요소입니다. 이 빠른 시작에 대 한 예제 데이터에서 찾을 수 있습니다 [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)합니다.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>새 애플리케이션 만들기

1. Visual Studio에서 새 콘솔 솔루션을 만들고 다음 패키지를 추가 합니다. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. 구독 키 및 끝점에 대 한 변수를 만듭니다. 다음은 Uri 변칙 검색에 사용할 수 있습니다. 나중에 API를 만드는 서비스 끝점에 추가할 수는 이러한 Url을 요청 합니다.

    |검색 방법  |URI  |
    |---------|---------|
    |일괄 검색    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |최신 데이터 요소 검색     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    ```csharp
    // Replace the subscriptionKey string value with your valid subscription key.
    const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    // Replace the endpoint URL with the correct one for your subscription. 
    // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
    const string endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    const string dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    const string latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    const string batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

## <a name="create-a-function-to-send-requests"></a>요청을 보내는 함수 만들기

1. 라는 새 비동기 함수를 만들고 `Request` 위에서 만든 변수를 사용 합니다.

2. 클라이언트의 보안 프로토콜 및 헤더 정보를 사용 하 여 설정 된 `HttpClient` 개체입니다. 구독 키를 추가 해야 합니다 `Ocp-Apim-Subscription-Key` 헤더입니다. 만든를 `StringContent` 요청에 대 한 개체입니다.

3. 사용 하 여 요청을 보낼 `PostAsync()`, 한 다음 응답을 반환 합니다.

```csharp
static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        return await res.Content.ReadAsStringAsync();
    }
}
```

## <a name="detect-anomalies-as-a-batch"></a>일괄 처리로 변칙을 검색 합니다.

1. 라는 새 함수를 만들고 `detectAnomaliesBatch()`합니다. 요청을 생성 하 고 호출 하 여 보낼는 `Request()` 끝점, 구독 키, 일괄 처리 변칙 검색에 대 한 URL 및 시계열 데이터를 사용 하 여 함수입니다.

2. JSON 개체를 deserialize 하 고 콘솔에 작성 합니다.

3. 응답에 포함 하는 경우 `code` 필드, 오류 코드 및 오류 메시지를 인쇄 합니다. 

4. 이 고, 그렇지 데이터 집합의 한 위치를 찾습니다. 응답의 `isAnomaly` 필드 데이터 요소가 비정상 인지를 나타냅니다 각각 부울 값의 배열을 포함 합니다. 이 값을 응답 개체의 문자열 배열을 변환 `ToObject<bool[]>()` 함수입니다. 배열 반복 되며 모든 인덱스를 인쇄 `true` 값입니다. 이러한 값은 발견 된 경우 비정상적인 데이터 요소의 인덱스에 해당 합니다.

```csharp
static void detectAnomaliesBatch(string requestData){
    System.Console.WriteLine("Detecting anomalies as a batch");

    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);

    if (jsonObj["code"] != null){
        System.Console.WriteLine($"Detection failed. ErrorCode:{jsonObj["code"]}, ErrorMessage:{jsonObj["message"]}");
    }
    else{
        bool[] anomalies = jsonObj["isAnomaly"].ToObject<bool[]>();
        System.Console.WriteLine("\nAnomalies detected in the following data positions:");
        for (var i = 0; i < anomalies.Length; i++){
            if (anomalies[i])
            {
                System.Console.Write(i + ", ");
            }
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>최신 데이터 요소의 비정상 상태 검색

1. 라는 새 함수를 만들고 `detectAnomaliesLatest()`합니다. 요청을 생성 하 고 호출 하 여 보낼는 `Request()` 끝점, 구독 키, 최신 지점 변칙 검색에 대 한 URL 및 시계열 데이터를 사용 하 여 함수입니다.

2. JSON 개체를 deserialize 하 고 콘솔에 작성 합니다.

```csharp
static void detectAnomaliesLatest(string requestData){
    System.Console.WriteLine("\n\nDetermining if latest data point is an anomaly");
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>시계열 데이터를 로드 하 고 요청을 보내기

1. 응용 프로그램의 main 메서드를 사용 하 여 JSON 시계열 데이터를 로드 `File.ReadAllText()`합니다. 

2. 위에서 만든 변칙 검색 함수를 호출 합니다. 사용 하 여 `System.Console.ReadKey()` 에 응용 프로그램을 실행 한 후 콘솔 창을 열어 둡니다.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>예제 응답

성공적인 응답은 JSON 형식으로 반환 됩니다. GitHub에서 JSON 응답을 보려면 아래 링크를 클릭 합니다.
* [일괄 처리 검색 응답 예제](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [최신 지점 검색 응답 예제](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
