---
title: '빠른 시작: 이미지에서 얼굴 감지 - Face API, Java'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Java와 함께 Face API를 사용하여 이미지에서 얼굴을 감지합니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/10/2018
ms.author: pafarley
ms.openlocfilehash: 42a8b13b87623b92ee0fe92db4e6a0caff5c3a1b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339118"
---
# <a name="quickstart-detect-faces-in-an-image-using-java"></a>빠른 시작: Java를 사용하여 이미지에서 얼굴 감지

이 빠른 시작에서는 Face API를 사용하여 이미지에서 사람 얼굴을 감지합니다.

## <a name="prerequisites"></a>필수 조건

샘플을 실행하려면 구독 키가 있어야 합니다. [Cognitive Services 시도](https://azure.microsoft.com/try/cognitive-services/?api=face-api)에서 평가판 구독 키를 가져올 수 있습니다.

## <a name="detect-faces-in-an-image"></a>이미지에서 얼굴 감지

[Face - Detect](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 메서드를 사용하여 이미지에서 얼굴을 감지하고 다음을 포함한 얼굴 특성을 반환합니다.

* 얼굴 Face: 여러 Face API 시나리오에 사용되는 고유 ID입니다.
* 얼굴 사각형: 이미지에서 얼굴의 위치를 나타내는 왼쪽, 위쪽, 너비 및 높이입니다.
* 랜드마크: 얼굴 구성 요소의 중요한 위치를 가리키는 27포인트 얼굴 랜드마크 배열입니다.
* 연령, 성별, 웃는 정도, 머리 포즈, 얼굴의 털 등을 포함한 얼굴 특성입니다.

샘플을 실행하려면 다음 단계를 수행합니다.

1. 원하는 Java IDE에서 새 명령줄 앱을 만듭니다.
2. Main 클래스를 다음 코드로 바꿉니다(`package` 문 유지).
3. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
4. 필요한 경우 `uriBase` 값을 변경하여 구독 키를 가져온 위치를 사용합니다.
5. 이러한 전역 라이브러리를 Maven 리포지토리에서 프로젝트의 `lib` 디렉터리로 다운로드합니다.
   * `org.apache.httpcomponents:httpclient:4.2.4`
   * `org.json:json:20170516`
6. ‘Main’을 실행합니다.

### <a name="face---detect-request"></a>얼굴 - 감지 요청

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;

public class Main
{
    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // NOTE: You must use the same region in your REST call as you used to
    // obtain your subscription keys. For example, if you obtained your
    // subscription keys from westus, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the westcentralus region. If you
    // use a free trial subscription key, you shouldn't need to change this region.
    private static final String uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";

    private static final String imageWithFaces =
        "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

    private static final String faceAttributes =
        "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

    public static void main(String[] args)
    {
        HttpClient httpclient = new DefaultHttpClient();

        try
        {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("returnFaceId", "true");
            builder.setParameter("returnFaceLandmarks", "false");
            builder.setParameter("returnFaceAttributes", faceAttributes);

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity reqEntity = new StringEntity(imageWithFaces);
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null)
            {
                // Format and display the JSON response.
                System.out.println("REST Response:\n");

                String jsonString = EntityUtils.toString(entity).trim();
                if (jsonString.charAt(0) == '[') {
                    JSONArray jsonArray = new JSONArray(jsonString);
                    System.out.println(jsonArray.toString(2));
                }
                else if (jsonString.charAt(0) == '{') {
                    JSONObject jsonObject = new JSONObject(jsonString);
                    System.out.println(jsonObject.toString(2));
                } else {
                    System.out.println(jsonString);
                }
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

### <a name="face---detect-response"></a>얼굴 - 감지 응답

성공적인 응답이 JSON을 통해 반환됩니다.

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>다음 단계

Face 서비스를 사용하여 이미지에서 얼굴을 감지하는 Android 응용 프로그램을 만드는 방법에 대해 알아봅니다. 응용 프로그램은 각 얼굴 주위에 프레임이 그려진 이미지를 표시합니다.

> [!div class="nextstepaction"]
> [자습서: Android에서 Face API 시작](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
