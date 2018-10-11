---
title: Javascript 및 Anomaly Finder API를 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
description: Cognitive Services에서 Javascript와 함께 Anomaly Finder 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 확인합니다.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 8a880e5924ec6bfbabb5ae4d71742f2a29bdea7f
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48246932"
---
# <a name="use-the-anomaly-finder-api-with-javascript"></a>Javascript 및 Anomaly Finder API 사용

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

이 문서에서 제공하는 정보 및 코드 샘플을 통해 Javascript와 함께 Anomaly Finder API 사용을 빠르게 시작하여 시계열 데이터의 변칙 검색 결과를 가져오는 작업을 수행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-detection-api-using-javascript"></a>Javascript를 사용하여 변칙 검색 API로 변칙 요소 가져오기

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>시계열 데이터의 예

시계열 데이터 요소의 예는 다음과 같습니다.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-javascript-example"></a>데이터 분석 및 변칙 요소 Javascript 예제 가져오기

예제 사용 단계는 다음과 같습니다.

1. 새 HTML 파일 만들기
2. HTML 파일을 다음 코드로 바꿉니다.
3. `[YOUR_SUBSCRIPTION_KEY]` 값을 유효한 구독 키로 바꿉니다.
4. requestTextArea의 데이터를 해당 데이터 요소로 바꿉니다.
5. 웹 브라우저에서 HTML 파일을 열고 `Anomaly Detection` 단추를 클릭합니다.

```Javascript
<!DOCTYPE html>
<html>
<head>
    <title>Anomaly Detection Javascript Quick Starts</title>
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

    <script type="text/javascript">
        function detect() {
            // **********************************************
            // *** Update or verify the following values. ***
            // **********************************************
            // Replace the subscriptionKey string value with your valid subscription key.
            var subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";

            var uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

            var body = document.getElementById("requestTextArea").value;
            // Perform the REST API call.
            $.ajax({
                url: uriBase,
                // Request headers.
                beforeSend: function (xhrObj) {
                    xhrObj.setRequestHeader("Content-Type", "application/json");
                    xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                },
                type: "POST",
                // Request body.
                data: body,
            })
            .done(function (data) {
                // Show formatted JSON on webpage.
                $("#responseTextArea").val(JSON.stringify(data, null, 2));
            })
            .fail(function (jqXHR, textStatus, errorThrown) {
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : jQuery.parseJSON(jqXHR.responseText).message;
                alert(errorString);
            });
        };
    </script>

    <h1>Anomaly Detection Javascript Quick Starts:</h1>
    Enter the detection data, then click the <strong>Anomaly Detection</strong> button.

    <button onclick="detect()">Anomaly Detection</button>
    <br><br>
    <div id="wrapper" style="width:1020px; display:table;">
        <div id="imageDiv" style="width:420px; display:table-cell;">
            Request:
            <br><br>
            <textarea id="requestTextArea" class="UIInput" style="width:580px; height:400px;">
    {
        "Period": 7,
        "Points": [
            {
              "Timestamp": "2018-03-01T00:00:00Z",
              "Value": 32858923
            },
            {
              "Timestamp": "2018-03-02T00:00:00Z",
              "Value": 29615278
            },
            {
              "Timestamp": "2018-03-03T00:00:00Z",
              "Value": 22839355
            },
            {
              "Timestamp": "2018-03-04T00:00:00Z",
              "Value": 25948736
            },
            {
              "Timestamp": "2018-03-05T00:00:00Z",
              "Value": 34139159
            },
            {
              "Timestamp": "2018-03-06T00:00:00Z",
              "Value": 33843985
            },
            {
              "Timestamp": "2018-03-07T00:00:00Z",
              "Value": 33637661
            },
            {
              "Timestamp": "2018-03-08T00:00:00Z",
              "Value": 32627350
            },
            {
              "Timestamp": "2018-03-09T00:00:00Z",
              "Value": 29881076
            },
            {
              "Timestamp": "2018-03-10T00:00:00Z",
              "Value": 22681575
            },
            {
              "Timestamp": "2018-03-11T00:00:00Z",
              "Value": 24629393
            },
            {
              "Timestamp": "2018-03-12T00:00:00Z",
              "Value": 34010679
            },
            {
              "Timestamp": "2018-03-13T00:00:00Z",
              "Value": 33893888
            },
            {
              "Timestamp": "2018-03-14T00:00:00Z",
              "Value": 33760076
            },
            {
              "Timestamp": "2018-03-15T00:00:00Z",
              "Value": 33093515
            },
            {
              "Timestamp": "2018-03-16T00:00:00Z",
              "Value": 29945555
            },
            {
              "Timestamp": "2018-03-17T00:00:00Z",
              "Value": 22676212
            },
            {
              "Timestamp": "2018-03-18T00:00:00Z",
              "Value": 25262514
            },
            {
              "Timestamp": "2018-03-19T00:00:00Z",
              "Value": 33631649
            },
            {
              "Timestamp": "2018-03-20T00:00:00Z",
              "Value": 34468310
            },
            {
              "Timestamp": "2018-03-21T00:00:00Z",
              "Value": 34212281
            },
            {
              "Timestamp": "2018-03-22T00:00:00Z",
              "Value": 38144434
            },
            {
              "Timestamp": "2018-03-23T00:00:00Z",
              "Value": 34662949
            },
            {
              "Timestamp": "2018-03-24T00:00:00Z",
              "Value": 24623684
            },
            {
              "Timestamp": "2018-03-25T00:00:00Z",
              "Value": 26530491
            },
            {
              "Timestamp": "2018-03-26T00:00:00Z",
              "Value": 35445003
            },
            {
              "Timestamp": "2018-03-27T00:00:00Z",
              "Value": 34250789
            },
            {
              "Timestamp": "2018-03-28T00:00:00Z",
              "Value": 33423012
            },
            {
              "Timestamp": "2018-03-29T00:00:00Z",
              "Value": 30744783
            },
            {
              "Timestamp": "2018-03-30T00:00:00Z",
              "Value": 25825128
            },
            {
              "Timestamp": "2018-03-31T00:00:00Z",
              "Value": 21244209
            },
            {
              "Timestamp": "2018-04-01T00:00:00Z",
              "Value": 22576956
            },
            {
              "Timestamp": "2018-04-02T00:00:00Z",
              "Value": 31957221
            },
            {
              "Timestamp": "2018-04-03T00:00:00Z",
              "Value": 33841228
            },
            {
              "Timestamp": "2018-04-04T00:00:00Z",
              "Value": 33554483
            },
            {
              "Timestamp": "2018-04-05T00:00:00Z",
              "Value": 32383350
            },
            {
              "Timestamp": "2018-04-06T00:00:00Z",
              "Value": 29494850
            },
            {
              "Timestamp": "2018-04-07T00:00:00Z",
              "Value": 22815534
            },
            {
              "Timestamp": "2018-04-08T00:00:00Z",
              "Value": 25557267
            },
            {
              "Timestamp": "2018-04-09T00:00:00Z",
              "Value": 34858252
            },
            {
              "Timestamp": "2018-04-10T00:00:00Z",
              "Value": 34750597
            },
            {
              "Timestamp": "2018-04-11T00:00:00Z",
              "Value": 34717956
            },
            {
              "Timestamp": "2018-04-12T00:00:00Z",
              "Value": 34132534
            },
            {
              "Timestamp": "2018-04-13T00:00:00Z",
              "Value": 30762236
            },
            {
              "Timestamp": "2018-04-14T00:00:00Z",
              "Value": 22504059
            },
            {
              "Timestamp": "2018-04-15T00:00:00Z",
              "Value": 26149060
            },
            {
              "Timestamp": "2018-04-16T00:00:00Z",
              "Value": 35250105
            }
        ]
    }
        </textarea>
        </div>
        <div id="jsonOutput" style="width:600px; display:table-cell;">
            Response:
            <br><br>
            <textarea id="responseTextArea" class="UIInput" style="width:580px; height:400px;"></textarea>
        </div>
    </div>
</body>
</home>
```

### <a name="example-response"></a>예제 응답

성공적인 응답이 JSON을 통해 반환됩니다. 샘플 응답은 다음과 같습니다.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Javascript 앱](../tutorials/javascript-tutorial.md)
