---
title: '빠른 시작: 잉크 인식기 REST API를 사용 하 여 디지털 잉크를 인식 하 고C#'
description: 디지털 잉크 스트로크를 인식 하려면 잉크 인식기 API를 사용 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: d661d6eca6e4916946944c48cc2e5411aeaf8f14
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061001"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>빠른 시작: 잉크 인식기 REST API를 사용 하 여 디지털 잉크를 인식 하 고C#

잉크 인식기 api 디지털 잉크 스트로크를 보내도록이 빠른 시작을 사용 합니다. 이 C# 응용 프로그램은 JSON 형식 잉크 스트로크 데이터를 포함 하는 API 요청을 보내고 응답을 가져옵니다.

이 애플리케이션은 C#으로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

일반적으로 디지털 잉크 입력 기능 앱에서 API를 호출 합니다. 이 빠른 시작이에서는 JSON 파일에서 다음 필기 샘플에 대 한 잉크 스트로크 데이터를 보냅니다.

![필기 문자의 이미지](../media/handwriting-sample.jpg)

이 빠른 시작의 소스 코드는 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)의 모든 버전.
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Visual studio에서 NuGet 패키지로 Newtonsoft.Json을 설치 합니다.
        1. 마우스 오른쪽 단추로 클릭는 **솔루션 관리자**
        2. **NuGet 패키지 관리...**
        3. 검색할 `Newtonsoft.Json` 패키지 및 설치
- Linux/MacOS를 사용 하는 경우이 응용 프로그램 실행할 수 있습니다 사용 하 여 [Mono](https://www.mono-project.com/)합니다.

- 이 빠른 시작 예제 잉크 스트로크 데이터에서 찾을 수 있습니다 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json)합니다.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


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
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. 구독 키 및 끝점에 대 한 변수를 만듭니다. 다음은 URI 잉크 인식에 사용할 수 있습니다. 나중에 API를 만드는 서비스 끝점으로 추가 됩니다 URl을 요청 합니다.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>요청을 보내는 함수 만들기

1. 라는 새 비동기 함수를 만들고 `Request` 위에서 만든 변수를 사용 합니다.

2. 클라이언트의 보안 프로토콜 및 헤더 정보를 사용 하 여 설정 된 `HttpClient` 개체입니다. 구독 키를 추가 해야 합니다 `Ocp-Apim-Subscription-Key` 헤더입니다. 만든를 `StringContent` 요청에 대 한 개체입니다.
 
3. 사용 하 여 요청을 보낼 `PutAsync()`합니다. 요청이 성공 하면 응답을 반환 합니다.  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>잉크 인식 요청을 보내기

1. 라는 새 함수를 만들고 `recognizeInk()`합니다. 요청을 생성 하 고 호출 하 여 보낼는 `Request()` 끝점에 구독 키 API 및 디지털 잉크 스트로크 데이터에 대 한 URL 사용 하 여 함수입니다.

2. JSON 개체를 deserialize 하 고 콘솔에 작성 합니다. 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>디지털 잉크 데이터를 로드 합니다.

호출 하는 함수 만들기 `LoadJson()` 잉크 데이터 JSON 파일을 로드 합니다. 사용 하 여는 `StreamReader` 및 `JsonTextReader` 만들려는 `JObject` 를 반환 합니다.
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>API 요청을 보내기

1. 응용 프로그램의 main 메서드에서 위에서 만든 함수를 사용 하 여 JSON 데이터를 로드 합니다. 

2. 호출 된 `recognizeInk()` 위에서 만든 함수입니다. 사용 하 여 `System.Console.ReadKey()` 에 응용 프로그램을 실행 한 후 콘솔 창을 열어 둡니다.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>응용 프로그램을 실행 하 고 응답을 보려면

애플리케이션을 실행합니다. 성공적인 응답은 JSON 형식으로 반환 됩니다. JSON 응답에서 찾을 수 있습니다 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json)합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://go.microsoft.com/fwlink/?linkid=2089907)


Ink Recognizer API가 디지털 잉크 입력 앱에서 어떻게 작동하는지 알아보려면 GitHub에서 다음 샘플 애플리케이션을 살펴보세요.
* [C# 및 UWP(유니버설 Windows 플랫폼)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 및 WPF(Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript 웹 브라우저 앱](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 및 Android 모바일 앱](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 및 iOS 모바일 앱](https://go.microsoft.com/fwlink/?linkid=2089805)
