---
title: Android용 Emotion API Java 빠른 시작 | Microsoft Docs
description: Cognitive Services에서 Android용 Java로 Emotion API를 빠르게 사용하는 데 도움이 되는 정보 및 코드 샘플을 가져옵니다.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 0e7d3991b195a83a8b87e306b3b34fbed2098581
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018029"
---
# <a name="emotion-api-java-for-android-quick-start"></a>Android용 Emotion API Java 빠른 시작

> [!IMPORTANT]
> Video API 미리 보기는 2017년 10월 30일에 종료됩니다. 새 [Video Indexer API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/) 미리 보기를 사용하여 비디오에서 정보를 쉽게 추출하고 말이나 얼굴, 성격, 감정을 감지하여 검색 결과를 제시하는 등 콘텐츠 검색 경험을 향상하세요. [자세히 알아보기](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

이 문서에서는 Emotion API Android 클라이언트 라이브러리의 [Emotion Recognize 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)를 빠르게 사용하는 데 도움이 되는 정보 및 코드 샘플을 제공합니다. 이 샘플은 Java를 사용하여 사람이 나타내는 감정을 인식하는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 조건
* [여기](https://github.com/Microsoft/Cognitive-emotion-android)에서 Android SDK용 Emotion API Java 가져오기
* [여기](https://azure.microsoft.com/try/cognitive-services/)에서 무료 구독 키 가져오기

## <a name="recognize-emotions-java-for-android-example-request"></a>Android용 감정 인식 Java 예제 요청

```java
// // This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;

public class Main
{
    public static void main(String[] args)
    {
        HttpClient httpClient = new DefaultHttpClient();

        try
        {
            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URL below with "westcentralus".
            URIBuilder uriBuilder = new URIBuilder("https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize");

            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers. Replace the example key below with your valid subscription key.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

            // Request body. Replace the example URL below with the URL of the image you want to analyze.
            StringEntity reqEntity = new StringEntity("{ \"url\": \"http://example.com/images/test.jpg\" }");
            request.setEntity(reqEntity);

            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null)
            {
                System.out.println(EntityUtils.toString(entity));
            }
        }
        catch (Exception e)
        {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>감정 인식 샘플 응답
호출이 성공하면 얼굴 사각형 크기의 내림차순으로 얼굴 항목 및 연결된 감정 점수 배열이 반환됩니다. 빈 응답은 검색된 얼굴이 없는 것을 나타냅니다. 감정 항목에는 다음 필드가 포함됩니다.
* faceRectangle - 이미지에서 얼굴의 사각형 위치입니다.
* scores - 이미지의 각 얼굴에 대한 감정 점수입니다. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
