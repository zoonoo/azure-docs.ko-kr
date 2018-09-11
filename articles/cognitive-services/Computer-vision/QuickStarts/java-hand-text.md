---
title: Computer Vision API Java 빠른 시작 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 Java와 함께 Computer Vision을 사용하여 이미지의 필기 텍스트를 추출합니다.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 04e6beda3bdcb97b425190d2e4d5c564e8a5f205
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750603"
---
# <a name="quickstart-extract-handwritten-text---rest-java"></a>빠른 시작: 필기 텍스트 추출 - REST, Java

이 빠른 시작에서는 Computer Vision을 사용하여 이미지의 필기 텍스트를 추출합니다.

## <a name="prerequisites"></a>필수 조건

Computer Vision을 사용하려면 구독 키가 필요합니다. [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="recognize-text-request"></a>텍스트 인식 요청

[텍스트 인식](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) 및 [텍스트 인식 작업 결과 가져오기 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201)를 사용하면 이미지의 필기 텍스트를 감지하고 인식된 문자를 머신에서 사용 가능한 문자 스트림으로 추출할 수 있습니다.

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
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.apache.http.Header;
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
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/recognizeText";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
        "Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpTextClient = HttpClientBuilder.create().build();
        CloseableHttpClient httpResultClient = HttpClientBuilder.create().build();;

        try {
            // This operation requires two REST API calls. One to submit the image
            // for processing, the other to retrieve the text found in the image.

            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameter.
            // Note: The request parameter changed for APIv2.
            // For APIv1, it is "handwriting", "true".
            builder.setParameter("mode", "Handwritten");

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

            // Make the first REST API call to detect the text.
            HttpResponse response = httpTextClient.execute(request);

            // Check for success.
            if (response.getStatusLine().getStatusCode() != 202) {
                // Format and display the JSON error message.
                HttpEntity entity = response.getEntity();
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
                return;
            }

            // Stores the URI where you can get the text recognition operation result.
            String operationLocation = null;

            // 'Operation-Location' in the response contains the URI to
            // retrieve the recognized text.
            Header[] responseHeaders = response.getAllHeaders();
            for (Header header : responseHeaders) {
                if (header.getName().equals("Operation-Location")) {
                    operationLocation = header.getValue();
                    break;
                }
            }

            if (operationLocation == null) {
                System.out.println("\nError retrieving Operation-Location.\nExiting.");
                System.exit(1);
            }

            // Note: The response may not be immediately available. Handwriting
            // recognition is an asynchronous operation, which takes a variable
            // amount of time dependent on the length of the text analyzed. You
            // may need to wait or retry the Get operation.

            System.out.println("\nHandwritten text submitted.\n" +
                    "Waiting 10 seconds to retrieve the recognized text.\n");
            Thread.sleep(10000);

            // Make the second REST API call and get the response.
            HttpGet resultRequest = new HttpGet(operationLocation);
            resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            HttpResponse resultResponse = httpResultClient.execute(resultRequest);
            HttpEntity responseEntity = resultResponse.getEntity();

            if (responseEntity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(responseEntity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Text recognition result response: \n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="recognize-text-response"></a>텍스트 인식 응답

성공적인 응답이 JSON을 통해 반환됩니다. 필기 인식 결과에는 검색된 텍스트 및 영역의 경계 상자, 선 및 단어가 포함됩니다.

이 프로그램은 다음 JSON과 유사한 출력을 생성해야 합니다.

```json
Handwritten text submitted. Waiting 10 seconds to retrieve the recognized text.

Text recognition result response:

{
  "status": "Succeeded",
  "recognitionResult": {"lines": [
    {
      "boundingBox": [
        2,
        84,
        783,
        96,
        782,
        154,
        1,
        148
      ],
      "words": [
        {
          "boundingBox": [
            6,
            86,
            92,
            87,
            71,
            151,
            0,
            150
          ],
          "text": "Pack"
        },
        {
          "boundingBox": [
            86,
            87,
            172,
            88,
            150,
            152,
            64,
            151
          ],
          "text": "my"
        },
        {
          "boundingBox": [
            165,
            88,
            241,
            89,
            219,
            152,
            144,
            152
          ],
          "text": "box"
        },
        {
          "boundingBox": [
            234,
            89,
            343,
            90,
            322,
            154,
            213,
            152
          ],
          "text": "with"
        },
        {
          "boundingBox": [
            347,
            90,
            432,
            91,
            411,
            154,
            325,
            154
          ],
          "text": "five"
        },
        {
          "boundingBox": [
            432,
            91,
            538,
            92,
            516,
            154,
            411,
            154
          ],
          "text": "dozen"
        },
        {
          "boundingBox": [
            554,
            92,
            696,
            94,
            675,
            154,
            533,
            154
          ],
          "text": "liquor"
        },
        {
          "boundingBox": [
            710,
            94,
            800,
            96,
            800,
            154,
            688,
            154
          ],
          "text": "jugs"
        }
      ],
      "text": "Pack my box with five dozen liquor jugs"
    },
    {
      "boundingBox": [
        2,
        52,
        65,
        46,
        69,
        89,
        7,
        95
      ],
      "words": [{
        "boundingBox": [
          0,
          62,
          79,
          39,
          94,
          82,
          0,
          105
        ],
        "text": "dog"
      }],
      "text": "dog"
    },
    {
      "boundingBox": [
        6,
        2,
        771,
        13,
        770,
        75,
        5,
        64
      ],
      "words": [
        {
          "boundingBox": [
            8,
            4,
            92,
            5,
            77,
            71,
            0,
            71
          ],
          "text": "The"
        },
        {
          "boundingBox": [
            89,
            5,
            188,
            5,
            173,
            72,
            74,
            71
          ],
          "text": "quick"
        },
        {
          "boundingBox": [
            188,
            5,
            323,
            6,
            308,
            73,
            173,
            72
          ],
          "text": "brown"
        },
        {
          "boundingBox": [
            316,
            6,
            386,
            6,
            371,
            73,
            302,
            73
          ],
          "text": "fox"
        },
        {
          "boundingBox": [
            396,
            7,
            508,
            7,
            493,
            74,
            381,
            73
          ],
          "text": "jumps"
        },
        {
          "boundingBox": [
            501,
            7,
            604,
            8,
            589,
            75,
            487,
            74
          ],
          "text": "over"
        },
        {
          "boundingBox": [
            600,
            8,
            673,
            8,
            658,
            75,
            586,
            75
          ],
          "text": "the"
        },
        {
          "boundingBox": [
            670,
            8,
            800,
            9,
            787,
            76,
            655,
            75
          ],
          "text": "lazy"
        }
      ],
      "text": "The quick brown fox jumps over the lazy"
    }
  ]}
}
```

## <a name="next-steps"></a>다음 단계

Computer Vision을 사용하는 Java Swing 응용 프로그램을 탐색합니다. 이 프로그램은 OCR(광학 문자 인식)을 수행하고, 스마트하게 자른 썸네일을 만들고, 이미지에서 얼굴을 비롯한 시각적 특징을 감지하고, 분류하고, 태그를 지정하고, 설명합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API Java 자습서](../Tutorials/java-tutorial.md)
