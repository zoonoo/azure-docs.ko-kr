---
title: Emotion API JavaScript 빠른 시작 | Microsoft Docs
description: Cognitive Services에서 JavaScript로 Emotion API를 빠르게 사용하는 데 도움이 되는 정보 및 코드 샘플을 가져옵니다.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 2578b0212f9b4a6483402074d7c9eff73e51ca6b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374095"
---
# <a name="emotion-api-javascript-quick-start"></a>Emotion API JavaScript 빠른 시작

> [!IMPORTANT]
> Video API 미리 보기는 2017년 10월 30일에 종료됩니다. 새 [Video Indexer API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/) 미리 보기를 사용하여 비디오에서 정보를 쉽게 추출하고 말이나 얼굴, 성격, 감정을 감지하여 검색 결과를 제시하는 등 콘텐츠 검색 경험을 향상하세요. [자세히 알아보기](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

이 문서에서는 JavaScript로 [Emotion API Recognize 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)를 빠르게 사용하여 이미지에서 한 명 이상의 사용자가 표현하는 감정을 인식하는 데 도움이 되는 정보 및 코드 샘플을 제공합니다.

## <a name="prerequisite"></a>필수 요소
* [여기에서](https://azure.microsoft.com/try/cognitive-services/) 무료 구독 키를 가져오거나 Azure 구독이 있는 경우 Emotion API 리소스를 만들고 거기에서 구독 키 및 엔드포인트를 가져옵니다.

![Emotion API 리소스 만들기](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>감정 인식 JavaScript 예제 요청

다음을 복사하고 `test.html` 같은 파일에 저장합니다. 구독 키를 구입한 위치를 사용하도록 요청 `url`을 변경하고, "Ocp-Apim-Subscription-Key" 값을 유효한 구독 키로 바꿉니다. Emotion API 리소스의 개요 섹션 및 키 섹션 각각의 Azure Portal에서 찾을 수 있습니다. 

![API 끝점](../Images/api-url.png)

![API 구독 키](../Images/keys.png)

사용하려는 이미지의 위치로 요청 본문을 변경합니다. 샘플을 실행하려면 파일을 브라우저로 끌어서 놓습니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title>JSSample</title>
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>

<h2>Face Rectangle</h2>
<ul id="faceRectangle">
<!-- Will populate list with response content -->
</ul>

<h2>Emotions</h2>
<ul id="scores">
<!-- Will populate list with response content -->
</ul>

<body>

<script type="text/javascript">
    $(function() {
        // No query string parameters for this API call.
        var params = { };
      
        $.ajax({
            // NOTE: You must use the same location in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URL below with "westcentralus".
            url: "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?" + $.param(params),
            beforeSend: function(xhrObj){
                // Request headers, also supports "application/octet-stream"
                xhrObj.setRequestHeader("Content-Type","application/json");

                // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
                xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key","<your subscription key>");
            },
            type: "POST",
            // Request body
            data: '{"url": "<your image url>"}',
        }).done(function(data) {
            // Get face rectangle dimensions
            var faceRectangle = data[0].faceRectangle;
            var faceRectangleList = $('#faceRectangle');

            // Append to DOM
            for (var prop in faceRectangle) {
                faceRectangleList.append("<li> " + prop + ": " + faceRectangle[prop] + "</li>");
            }
            
            // Get emotion confidence scores
            var scores = data[0].scores;
            var scoresList = $('#scores');

            // Append to DOM
            for(var prop in scores) {
                scoresList.append("<li> " + prop + ": " + scores[prop] + "</li>")
            }
        }).fail(function(err) {
            alert("Error: " + JSON.stringify(err));
        });
    });
</script>
</body>
</html>
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
