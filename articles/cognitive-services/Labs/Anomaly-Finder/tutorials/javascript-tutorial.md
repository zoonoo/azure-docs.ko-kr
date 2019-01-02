---
title: '자습서: Javascript를 사용하여 변칙 검색'
titlesuffix: Azure Cognitive Services
description: 변칙 검색 API를 사용하는 Javacript 웹앱을 탐색합니다. 원래 데이터 요소를 API에 전송하고 예상 값과 변칙 요소를 가져옵니다.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: cd65a5275fac651968197c9000b2d4cd3962693b
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883706"
---
# <a name="tutorial-anomaly-detection-with-javascript-application"></a>자습서: Javascript 애플리케이션을 사용하여 변칙 검색

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

변칙을 검색하기 위해 변칙 검색 REST API를 사용하는 웹 애플리케이션을 탐색합니다. 예제는 시계열 데이터를 구독 키가 있는 변칙 검색 API에 제출한 후 API에서 각 데이터 요소에 대한 모든 변칙 요소와 예상 값을 가져옵니다.

## <a name="prerequisites"></a>필수 조건

### <a name="platform-requirements"></a>플랫폼 요구 사항

이 자습서는 간단한 텍스트 편집기를 사용하여 개발되었습니다.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>변칙 검색을 구독하고 구독 키 받기 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>예제 가져오기 및 사용

이 자습서는 시계열 데이터 변칙 검색에 대한 두 가지 시나리오를 제공합니다. 이제 시작하겠습니다.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>자습서 프로젝트 다운로드

[Cognitive Services JavaScript 변칙 검색 자습서](https://github.com/MicrosoftAnomalyDetection/javascript-sample)를 복제하거나 .zip 파일을 다운로드하여 빈 디렉터리에 추출합니다.

<a name="Step2"></a>
### <a name="run-the-example"></a>예제 실행

예제를 시도할 수 있는 두 가지 시나리오가 있습니다.
1. **구독 키**를 anomalydetection.html에서 검색 함수에 대한 구독 키 필드에 배치합니다.
2. 변칙 검색 API 엔드포인트를 배치하고 구독 지역에서 올바른 지역을 사용하고 있는지 확인합니다.
3. 웹 브라우저에서 **anomalydetection.html** 파일을 엽니다.

**시나리오 1 주간 시계열 데이터 검색**
1. 기간 필드에 기간 **7**을 입력합니다. 
2. 요소 필드에서 샘플 데이터를 주간 시계열 데이터 요소(Json)로 바꾸거나 직접 샘플 데이터를 사용합니다.
3. 변칙 검색 단추를 클릭하고 오른쪽 응답 텍스트 상자에서 결과를 확인합니다.

**시나리오 2 기간 없는 시계열 데이터 검색**
1. 기간 필드에 기간을 빈 상태로 두고 시계열의 기간을 모르는 것으로 가정합니다.
2. 동일한 시계열 데이터를 시나리오 1으로 사용합니다.
3. 변칙 검색 단추를 클릭하고 오른쪽 응답 텍스트 상자에서 기간 필드를 확인합니다.

<a name="Step3"></a>
### <a name="read-the-result"></a>결과 읽기

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>검토 및 학습

이제 실행 중인 애플리케이션을 가져옵니다. 예제 앱을 Cognitive Services 기술과 통합하는 방법을 검토해보겠습니다. 이 단계를 사용하면 계속해서 이 앱에 빌드하거나 Microsoft 변칙 검색을 사용하여 고유한 앱을 개발하는 작업이 더 쉬워집니다.
이 예제 앱은 변칙 검색 Restful API 엔드포인트를 활용합니다.
Restful API가 예제 애플리케이션에서 사용되는 방법을 검토하면서 anomalydetection.html에서 코드 조각을 살펴보겠습니다.
```JavaScript
function anomalyDetection(url, subscriptionKey, points, period) {
    var obj = new Object();
    obj.Points = JSON.parse(points); // this points are read from text box.
    obj.Period = parseInt(period);//period=7 weekly period
    var tsData = JSON.stringify(obj);
    // Perform the REST API call.
    $.ajax({
        url: url, //Anomaly Detection API endpoint
        // Request headers.
        beforeSend: function (xhrObj) {
            xhrObj.setRequestHeader("Content-Type", "application/json");
            xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey); // Replace your subscription key
        },
        type: "POST",
        // Request body.
        data: tsData, // json format
        })
        .done(function (data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })
        .fail(function (jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" : jQuery.parseJSON(jqXHR.responseText).message;
            $("#responseTextArea").val(errorString);           
        });
}

```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
