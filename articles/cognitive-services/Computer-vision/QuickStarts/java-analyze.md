---
title: '빠른 시작: 원격 이미지 분석 - REST, Java'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Java와 함께 Computer Vision API를 사용하여 이미지를 분석합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 98eb2574ab427f2d5727d74a077628d3a7f829bc
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311461"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-java"></a>빠른 시작: Computer Vision REST API 및 Java를 사용하여 원격 이미지 분석

이 빠른 시작에서는 Computer Vision의 REST API.를 사용하여 시각적 기능을 추출하기 위해 원격으로 저장된 이미지를 분석합니다. [이미지 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) 메서드를 사용하면 이미지 콘텐츠를 기반으로 하여 시각적 특징을 추출할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

- [Java&trade; 플랫폼, Standard Edition Development Kit 7 또는 8](https://aka.ms/azure-jdks)(JDK 7 또는 8)이 설치되어 있어야 합니다.
- Computer Vision에 대한 구독 키가 있어야 합니다. 구독 키를 가져오려면 [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="create-and-run-the-sample-application"></a>애플리케이션 예제 만들기 및 실행

샘플을 만들고 실행하려면 다음 단계를 수행합니다.

1. 즐겨 찾는 IDE 또는 편집기에서 새 Java 프로젝트를 만듭니다. 옵션을 사용할 수 있는 경우 명령줄 애플리케이션 템플릿에서 Java 프로젝트를 만듭니다.
1. Java 프로젝트로 다음 라이브러리를 가져옵니다. Maven을 사용하는 경우 각 라이브러리에 대한 Maven 좌표가 제공됩니다.
   - [Apache HTTP 클라이언트](https://hc.apache.org/downloads.cgi)(org.apache.httpcomponents:httpclient:4.5.5)
   - [Apache HTTP 코어](https://hc.apache.org/downloads.cgi)(org.apache.httpcomponents:httpcore:4.4.9)
   - [JSON 라이브러리](https://github.com/stleary/JSON-java)(org.json:json:20180130)
1. 다음 `import` 명령문을 프로젝트에 대한 `Main` 공용 클래스를 포함하는 파일에 추가합니다.  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. `Main` 공용 클래스를 다음 코드로 바꾼 다음, 필요한 코드에 다음 변경 내용을 만듭니다.
   1. `subscriptionKey`의 값을 구독 키로 바꿉니다.
   1. 필요한 경우 `uriBase`의 값을 구독 키를 가져온 Azure 지역의 [이미지 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) 메서드에 대한 엔드포인트 URL로 바꿉니다.
   1. 필요한 경우 `imageToAnalyze`의 값을 분석하려는 다른 이미지의 URL로 바꿉니다.
1. 저장한 다음, Java 프로젝트를 빌드합니다.
1. IDE를 사용하는 경우 `Main`을 실행합니다. 그렇지 않은 경우 명령 프롬프트 창을 연 다음, `java` 명령을 사용하여 컴파일된 클래스를 실행합니다. 예: `java Main`

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westus" region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    private static final String uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze";

    private static final String imageToAnalyze =
            "https://upload.wikimedia.org/wikipedia/commons/" +
                    "1/12/Broadway_and_Times_Square_by_night.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>응답 검사

성공적인 응답이 JSON을 통해 반환됩니다. 애플리케이션 예제는 다음 예제와 유사하게 콘솔 창에서 성공한 응답을 구문 분석하고 표시합니다.

```json
REST Response:

{
  "metadata": {
    "width": 1826,
    "format": "Jpeg",
    "height": 2436
  },
  "color": {
    "dominantColorForeground": "Brown",
    "isBWImg": false,
    "accentColor": "B74314",
    "dominantColorBackground": "Brown",
    "dominantColors": ["Brown"]
  },
  "requestId": "bbffe1a1-4fa3-4a6b-a4d5-a4964c58a811",
  "description": {
    "captions": [{
      "confidence": 0.8241405091548035,
      "text": "a group of people on a city street filled with traffic at night"
    }],
    "tags": [
      "outdoor",
      "building",
      "street",
      "city",
      "busy",
      "people",
      "filled",
      "traffic",
      "many",
      "table",
      "car",
      "group",
      "walking",
      "bunch",
      "crowded",
      "large",
      "night",
      "light",
      "standing",
      "man",
      "tall",
      "umbrella",
      "riding",
      "sign",
      "crowd"
    ]
  },
  "categories": [{
    "score": 0.625,
    "name": "outdoor_street"
  }]
}
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 컴파일된 클래스 및 가져온 라이브러리를 포함하여 Java 프로젝트를 삭제합니다.

## <a name="next-steps"></a>다음 단계

Computer Vision을 사용하여 OCR(광학 문자 인식)을 수행하고, 스마트하게 잘리는 썸네일을 만들고, 이미지에서 얼굴을 비롯한 시각적 기능을 검색하고, 분류하고, 태그를 지정하고, 설명하는 Java Swing 애플리케이션을 탐색합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API Java 자습서](../Tutorials/java-tutorial.md)
