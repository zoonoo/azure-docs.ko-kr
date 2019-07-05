---
title: '빠른 시작: 잉크 인식기 REST API 및 Java를 사용 하 여 디지털 잉크를 인식 합니다.'
description: 디지털 잉크 스트로크를 인식 하려면 잉크 인식기 API를 사용 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 04f2ac17871bbaf0506fe18122507167b23869a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060944"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>빠른 시작: 잉크 인식기 REST API 및 Java를 사용 하 여 디지털 잉크를 인식 합니다.

디지털 잉크 스트로크에 잉크 인식기 API 사용을 시작 하려면이 빠른 시작을 사용 합니다. 이 Java 응용 프로그램 JSON 형식 잉크 스트로크 데이터를 포함 하는 API 요청을 보내고 응답을 가져옵니다.

이 애플리케이션은 Java로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

일반적으로 디지털 잉크 입력 기능 앱에서 API를 호출 합니다. 이 빠른 시작이에서는 JSON 파일에서 다음 필기 샘플에 대 한 잉크 스트로크 데이터를 보냅니다.

![필기 문자의 이미지](../media/handwriting-sample.jpg)

이 빠른 시작의 소스 코드는 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- 합니다 [Java&trade; 개발 Kit(JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 이상.

- Maven 리포지토리에서 이러한 라이브러리 가져오기
    - [Java에서 JSON](https://mvnrepository.com/artifact/org.json/json) 패키지
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) 패키지

- 이 빠른 시작 예제 잉크 스트로크 데이터에서 찾을 수 있습니다 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json)합니다.

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

2. 구독 키 및 끝점에 대 한 변수를 만듭니다. 다음은 URI 잉크 인식에 사용할 수 있습니다. API 요청 URL을 만드는 데는 나중에 서비스 끝점으로 추가 됩니다.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>요청을 보내는 함수 만들기

1. 라는 새 함수를 만들고 `sendRequest()` 위에서 만든 변수를 사용 합니다. 그런 후에 다음 단계를 수행합니다.

2. 만들기는 `CloseableHttpClient` API에 요청을 보낼 수 있는 개체입니다. 요청을 보낼는 `HttpPut` 잉크 인식기 URL과 끝점에 결합 하 여 요청 개체입니다.

3. 요청을 사용 하 여 `setHeader()` 함수를 설정 합니다 `Content-Type` 헤더를 `application/json`, 구독 키를 추가 하 고는 `Ocp-Apim-Subscription-Key` 헤더.

4. 요청을 사용 하 여 `setEntity()` 데이터 전송에 함수입니다.   

5. 클라이언트를 사용 하 여 `execute()` 요청을 보내도록 함수 및 저장 하는 `CloseableHttpResponse` 개체입니다. 

6. 만들기는 `HttpEntity` 응답 콘텐츠를 저장할 개체입니다. 사용 하 여 콘텐츠를 가져올 `getEntity()`합니다. 응답 비어 있지 않은 경우이 반환 합니다.
    
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

## <a name="send-an-ink-recognition-request"></a>잉크 인식 요청을 보내기

이라는 메서드를 만듭니다 `recognizeInk()` 잉크 스트로크 데이터를 인식 하도록 합니다. 호출 된 `sendRequest()` 끝점, url, 구독 키 및 json 데이터를 사용 하 여 위에서 만든 메서드. 결과 가져오고 콘솔에 인쇄 합니다.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>디지털 잉크 데이터를 로드 하 고 요청을 보내기

1. 응용 프로그램의 main 메서드를 요청에 추가 될 데이터를 포함 하는 JSON 파일에서을 읽습니다.

2. 위에서 만든 잉크 인식 함수를 호출 합니다.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>응용 프로그램을 실행 하 고 응답을 보려면

애플리케이션을 실행합니다. 성공적인 응답은 JSON 형식으로 반환 됩니다. JSON 응답에서 찾을 수 있습니다 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json)합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://go.microsoft.com/fwlink/?linkid=2089907)


Ink Recognizer API가 디지털 잉크 입력 앱에서 어떻게 작동하는지 알아보려면 GitHub에서 다음 샘플 애플리케이션을 살펴보세요.
* [C# 및 UWP(유니버설 Windows 플랫폼)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 및 WPF(Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript 웹 브라우저 앱](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 및 Android 모바일 앱](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 및 iOS 모바일 앱](https://go.microsoft.com/fwlink/?linkid=2089805)
