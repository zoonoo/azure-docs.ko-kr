---
title: 변칙 검색 C# 앱 - Microsoft Cognitive Services | Microsoft Docs
description: Microsoft Cognitive Services의 변칙 검색 API를 사용하는 C# 앱을 탐색합니다. 원래 데이터 요소를 API에 전송하고 예상 값과 변칙 요소를 가져옵니다.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 2e4100fd7d8e85a6b103c31000176aaaeb3d7151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375375"
---
# <a name="anomaly-detection-c-application"></a>변칙 검색 C# 응용 프로그램

변칙 검색 API를 사용하여 입력에서 변칙을 검색하는 기본 Windows 응용 프로그램을 탐색합니다. 예제는 시계열 데이터를 구독 키가 있는 변칙 검색 API에 제출한 후 API에서 각 데이터 요소에 대한 모든 변칙 요소와 예상 값을 가져옵니다.

## <a name="prerequisites"></a>필수 조건

### <a name="platform-requirements"></a>플랫폼 요구 사항

예제는 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs)을 사용하여 .NET Framework용으로 개발되었습니다. 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>변칙 검색을 구독하고 구독 키 받기 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>예제 가져오기 및 사용

변칙 검색 예제 응용 프로그램을 [Github](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git)에서 컴퓨터로 복제할 수 있습니다. 
<a name="Step1"></a>
### <a name="install-the-example"></a>예제 설치

GitHub 데스크톱에서 Sample\AnomalyDetectionSample.sln을 엽니다.

<a name="Step2"></a>
### <a name="build-the-example"></a>예제 빌드

Ctrl+Shift+B를 누르거나, 리본 메뉴에서 빌드를 클릭하고 솔루션 빌드를 선택합니다.

<a name="Step3"></a>
### <a name="run-the-example"></a>예제 실행

1. 빌드가 완료된 후 **F5** 키를 누르거나, 리본 메뉴에서 **시작**을 클릭하여 예제를 실행합니다.
2. 텍스트 편집 상자에 “{your_subscription_key}”가 표시된 변칙 검색 사용자 인터페이스 창을 찾습니다.
3. 샘플 데이터가 포함된 request.json 파일을 사용자 고유의 데이터로 바꾸고 “보내기” 단추를 클릭합니다. Microsoft는 업로드되는 데이터를 수신하고 사용하여 모든 변칙 요소를 검색합니다. 업데이트한 데이터는 Microsoft 서버에서 유지되지 않습니다. 변칙 요소를 다시 검색하려면 데이터를 다시 업로드해야 합니다.
4. 데이터가 양호한 경우 “응답” 필드에서 변칙 검색 결과를 확인할 수 있습니다. 오류가 발생하면 오류 정보도 응답 필드에 표시됩니다.

<a name="Review"></a>
### <a name="read-the-result"></a>결과 읽기

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>검토 및 학습

이제 실행 중인 응용 프로그램이 있으므로 예제 앱이 Cognitive Services 기술과 어떻게 통합되는지를 살펴보겠습니다. 이 단계를 사용하면 계속해서 이 앱에 빌드하거나 Microsoft 변칙 검색을 사용하여 고유한 앱을 개발하는 작업이 더 쉬워집니다.

이 예제 앱은 변칙 검색 Restful API 끝점을 활용합니다.

Restful API가 예제 응용 프로그램에서 사용되는 방법을 검토하면서 **AnomalyDetectionClient.cs**의 코드 조각을 살펴보겠습니다. 이 파일에는 아래에 재현된 코드 조각을 찾는 데 도움이 되도록 “KEY SAMPLE CODE STARTS HERE” 및 “KEY SAMPLE CODE ENDS HERE”를 표시하는 코드 주석이 포함되어 있습니다.

```csharp
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

```
**요청(…)** 아래 코드 조각은 HttlClient를 사용하여 구독 키와 데이터 요소를 변칙 검색 API의 끝점에 제출하는 방법을 보여 줍니다.

```csharp
    public async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
    {
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
        {
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Build the request content
            // ---------------------------------------------------------------------- 
            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Send the request content with POST method.
            // ---------------------------------------------------------------------- 
            var res = await client.PostAsync(endpoint, content);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------
            if (res.IsSuccessStatusCode)
            {
                return await res.Content.ReadAsStringAsync();
            }
            else
            {
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
