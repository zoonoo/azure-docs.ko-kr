---
title: '빠른 시작: Ink Recognizer REST API와 Java로 디지털 잉크 인식'
titleSuffix: Azure Cognitive Services
description: Ink Recognizer API를 사용하여 디지털 잉크 스트로크 인식을 시작합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: 36ff0fe4550b140a722ed25f4e372f7c88581211
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212691"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>빠른 시작: Ink Recognizer REST API와 Java로 디지털 잉크 인식

이 빠른 시작을 사용하여 디지털 잉크 스트로크에 Ink Recognizer API를 사용하기 시작합니다. 이 Java 애플리케이션은 JSON 형식의 잉크 스트로크 데이터가 포함된 API 요청을 보내고 응답을 받습니다.

이 애플리케이션은 Java로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

일반적으로 API는 디지털 잉크 입력 앱에서 호출합니다. 이 빠른 시작은 JSON 파일에서 다음 필기 샘플에 대한 잉크 스트로크 데이터를 보냅니다.

![필기 문자 이미지](../media/handwriting-sample.jpg)

이 빠른 시작의 소스 코드는 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- [Java&trade; Development Kit(JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 이상.

- Maven 리포지토리에서 다음과 같은 라이브러리 가져오기
    - [JSON(Java](https://mvnrepository.com/artifact/org.json/json) 패키지)
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) 패키지

- 이 빠른 시작의 잉크 스트로크 데이터 예제는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json)에서 찾을 수 있습니다.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

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
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. 구독 키 및 엔드포인트에 대한 변수를 만듭니다. 아래 엔드포인트를 Ink Recognizer 리소스에 대해 생성된 엔드포인트로 바꿉니다. API에 연결하려면 Ink Recognizer URI에 추가합니다.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://<your-custom-subdomain>.cognitiveservices.azure.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>요청을 보내는 함수 만들기

1. 위에서 만든 변수를 사용하는 `sendRequest()`라는 새 함수를 만듭니다. 그런 후에 다음 단계를 수행합니다.

2. API에 요청을 보낼 수 있는 `CloseableHttpClient` 개체를 만듭니다. 엔드포인트와 Ink Recognizer URL을 결합하여 `HttpPut` 요청 개체에 요청을 보냅니다.

3. 요청의 `setHeader()` 함수를 사용하여 `Content-Type` 헤더를 `application/json`으로 설정하고 구독 키를 `Ocp-Apim-Subscription-Key` 헤더에 추가합니다.

4. 요청의 `setEntity()` 함수를 전송할 데이터에 사용합니다.   

5. 클라이언트의 `execute()` 함수를 사용하여 요청을 보내고 `CloseableHttpResponse` 개체에 저장합니다. 

6. 응답 콘텐츠를 저장할 `HttpEntity` 개체를 만듭니다. `getEntity()`를 사용하여 콘텐츠를 받습니다. 응답이 비어 있지 않으면 반환합니다.
    
    ```java
    static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPut request = new HttpPut(endpoint + apiAddress);
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
            System.err.println("Exception recognizing ink: " + ex.getMessage());
            ex.printStackTrace();
        }
        return null;
    }
    ```

## <a name="send-an-ink-recognition-request"></a>잉크 인식 요청 보내기

잉크 스트로크 데이터를 인식하는 `recognizeInk()`라는 메서드를 만듭니다. 위에서 만든 `sendRequest()` 메서드를 엔드포인트, URL, 구독 키 및 JSON 데이터로 호출합니다. 결과를 받아서 콘솔에 출력합니다.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>디지털 잉크 데이터를 로드하고 요청 보내기

1. 애플리케이션의 main 메서드에서 요청에 추가할 데이터가 포함된 JSON 파일을 읽습니다.

2. 위에서 만든 잉크 인식 함수를 호출합니다.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>애플리케이션을 실행하고 응답 보기

애플리케이션을 실행합니다. 성공 응답이 JSON 형식으로 반환됩니다. 또한 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json)에서 JSON 응답을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://go.microsoft.com/fwlink/?linkid=2089907)


Ink Recognizer API가 디지털 잉크 입력 앱에서 어떻게 작동하는지 알아보려면 GitHub에서 다음 샘플 애플리케이션을 살펴보세요.
* [C# 및 UWP(유니버설 Windows 플랫폼)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 및 WPF(Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript 웹 브라우저 앱](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 및 Android 모바일 앱](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 및 iOS 모바일 앱](https://go.microsoft.com/fwlink/?linkid=2089805)
