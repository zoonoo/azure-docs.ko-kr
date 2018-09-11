---
title: Computer Vision API Java 빠른 시작 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 Java와 함께 Computer Vision을 사용하여 이미지를 분석합니다.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 9faa8d05a747855ceb0a468845c3d3a82aa71337
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750664"
---
# <a name="quickstart-analyze-a-remote-image---rest-java"></a>빠른 시작: 원격 이미지 분석 - REST, Java

이 빠른 시작에서는 Computer Vision을 사용하여 시각적 기능을 추출하기 위해 이미지를 분석합니다.

## <a name="prerequisites"></a>필수 조건

Computer Vision을 사용하려면 구독 키가 필요합니다. [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="analyze-image-request"></a>이미지 분석 요청

[이미지 분석 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)를 사용하여 이미지 콘텐츠를 기반으로 하는 시각적 기능을 추출할 수 있습니다. 이미지를 업로드하거나 이미지 URL을 지정하고 다음을 비롯한 반환할 기능을 선택할 수 있습니다.

* 이미지 콘텐츠와 관련된 자세한 태크 목록입니다.
* 완전한 문장에서 이미지 콘텐츠의 설명입니다.
* 이미지에 포함된 모든 얼굴의 좌표, 성별 및 나이입니다.
* ImageType(클립 아트 또는 선 그리기)
* 기조색, 강조색, 이미지가 흑백인지 여부입니다.
* 이 [분류](../Category-Taxonomy.md)에 정의된 범주입니다.
* 이미지에 성인 또는 성적인 콘텐츠가 포함되어 있나요?

샘플을 실행하려면 다음 단계를 수행합니다.

1. 새 명령줄 앱을 만듭니다.
1. Main 클래스를 다음 코드로 바꿉니다(`package` 문 유지).
1. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
1. 필요한 경우 `uriBase` 값을 구독 키를 가져온 위치로 변경합니다.
1. 필요에 따라 `imageToAnalyze` 값을 다른 이미지로 변경합니다.
1. 이러한 라이브러리를 Maven 리포지토리에서 프로젝트의 `lib` 디렉터리로 다운로드합니다.
   * `org.apache.httpcomponents:httpclient:4.5.5`
   * `org.apache.httpcomponents:httpcore:4.4.9`
   * `org.json:json:20180130`
1. ‘Main’을 실행합니다.

```java
// This sample uses the following libraries:
//  - Apache HTTP client (org.apache.httpcomponents:httpclient:4.5.5)
//  - Apache HTTP core (org.apache.httpcomponents:httpccore:4.4.9)
//  - JSON library (org.json:json:20180130).

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

public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same region in your REST call as you used to get your
    // subscription keys. For example, if you got your subscription keys from
    // westus, replace "westcentralus" in the URI below with "westus".
    //
    // Free trial subscription keys are generated in the westcentralus region. If you
    // use a free trial subscription key, you shouldn't need to change this region.
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

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Make the REST API call and get the response entity.
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

## <a name="analyze-image-response"></a>이미지 분석 응답

성공적인 응답을 JSON으로 반환합니다. 예:

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

## <a name="next-steps"></a>다음 단계

Computer Vision을 사용하여 OCR(광학 문자 인식)을 수행하고, 스마트하게 잘리는 썸네일을 만들고, 이미지에서 얼굴을 비롯한 시각적 기능을 검색하고, 분류하고, 태그를 지정하고, 설명하는 Java Swing 응용 프로그램을 탐색합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API Java 자습서](../Tutorials/java-tutorial.md)
