---
title: '빠른 시작: .NET 및 C#용 Azure SDK를 사용하여 Text Analytics 서비스 호출'
titleSuffix: Azure Cognitive Services
description: Text Analytics 서비스 및 C#을 사용하여 시작하는 데 도움이 되는 정보 및 코드 샘플
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: assafi
ms.openlocfilehash: 6bd3907392dad626c1eeb1823c929f1a35d544dd
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697664"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>빠른 시작: .NET SDK 및 C#을 사용하여 Text Analytics 서비스 호출
<a name="HOLTop"></a>

이 빠른 시작을 사용하면 .NET 및 C#용 Azure SDK를 사용하여 시작하는 데 도움이 됩니다. [Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) REST API는 대부분의 프로그래밍 언어와 호환되지만, SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다.

> [!NOTE]
> 이 문서의 데모 코드는 간단히 하기 위해 Text Analytics .NET SDK의 동기 메서드를 사용합니다. 하지만 프로덕션 시나리오에서는 자체 애플리케이션에서 일괄 처리된 비동기 메서드를 사용하여 확장성과 응답성을 유지하는 것이 좋습니다. 예를 들어 `Sentiment` 대신 `SentimentBatchAsync`를 사용할 수 있습니다.
>
> 이 예제의 일괄 처리된 비동기 버전은 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics)에서 찾을 수 있습니다.

기술 세부 정보는 .NET용 SDK [Text Analytics](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Visual Studio 2017 이상의 모든 버전
* Text Analytics [.NET용 SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Visual Studio 솔루션 만들기 및 SDK 설치

1. 새로운 콘솔 앱(.NET Core) 프로젝트를 만듭니다. [Visual Studio에 액세스](https://visualstudio.microsoft.com/vs/)합니다.
1. 솔루션을 마우스 오른쪽 단추로 클릭하고 **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.
1. **찾아보기** 탭을 선택합니다. **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**를 검색합니다.

## <a name="authenticate-your-credentials"></a>자격 증명 인증

1. 다음과 같은 `using` 문을 기본 클래스 파일(기본적으로 Program.cs)에 추가합니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. 새 `ApiKeyServiceClientCredentials` 클래스를 만들어 자격 증명을 저장하고 각 요청에 대해 추가합니다.

    ```csharp
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string apiKey;

        public ApiKeyServiceClientCredentials(string apiKey)
        {
            this.apiKey = apiKey;
        }

        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }
            request.Headers.Add("Ocp-Apim-Subscription-Key", this.apiKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. `Program` 클래스를 업데이트합니다. Text Analytics API 키에 대한 상수 멤버 및 서비스 엔드포인트에 대한 또 다른 멤버를 추가합니다. Text Analytics 리소스에 대한 올바른 Azure 위치를 사용해야 합니다.

    ```csharp
    //Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    //You can get the resource location from Azure Portal -> your TA resource -> Overview
    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://<your-location>.api.cognitive.microsoft.com";
    ```

> [!Tip]
> 프로덕션 시스템에서 비밀의 보안을 강화하기 위해 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)를 사용하는 것이 좋습니다.
>

## <a name="create-a-text-analytics-client"></a>Text Analytics 클라이언트 만들기

프로젝트의 `Main` 함수에서 호출하려는 샘플 메서드를 호출합니다. 정의한 `Endpoint` 및 `ApiKey` 매개 변수를 전달합니다.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(ApiKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client);
        // DetectLanguageExample(client);
        // RecognizeEntitiesExample(client);
        // KeyPhraseExtractionExample(client);
        Console.ReadLine();
    }
```

다음 섹션에서는 각 서비스 기능을 호출하는 방법을 설명합니다.

## <a name="perform-sentiment-analysis"></a>감정 분석 수행

1. 이전에 만든 클라이언트를 사용하는 새 함수 `SentimentAnalysisExample()`을 만듭니다.
2. 동일한 함수에서 `client.Sentiment()`를 호출하고 결과를 확인합니다. 결과에는 성공하면 `Score` 감정이 포함되고, 그렇지 않으면 `errorMessage`가 포함됩니다. 점수가 0에 가까울수록 부정적인 감정을 나타내고, 1에 가까울수록 긍정적인 감정을 나타냅니다.

    ```csharp
    var result = client.Sentiment("I had the best day of my life.", "en");

    // Printing sentiment results
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
    ```

### <a name="output"></a>출력

```console
Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>언어 검색 수행

1. 이전에 만든 클라이언트를 사용하는 새 함수 `DetectLanguageExample()`을 만듭니다.
2. 동일한 함수에서 `client.DetectLanguage()`를 호출하고 결과를 확인합니다. 결과에는 성공하면 `DetectedLanguages`에서 검색된 언어 목록이 포함되고, 그렇지 않으면 `errorMessage`가 포함됩니다. 그런 다음, 반환된 첫 번째 언어를 출력합니다.

    ```csharp
    var result = client.DetectLanguage("This is a document written in English.");

    // Printing detected languages
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
    ```

> [!Tip]
> 일부 경우에는 입력에 따라 언어를 명확하게 구분하는 것이 어려울 수 있습니다. 2자로 된 국가 코드는 `countryHint` 매개 변수를 사용하여 지정할 수 있습니다. 기본적으로 API는 "US"를 기본 countryHint로 사용합니다. 이 동작을 제거하려면 이 값을 빈 문자열의 `countryHint = ""`로 설정하여 이 매개 변수를 다시 설정하면 됩니다.

### <a name="output"></a>출력

```console
Language: English
```

## <a name="perform-entity-recognition"></a>엔터티 인식 수행

1. 이전에 만든 클라이언트를 사용하는 새 함수 `RecognizeEntitiesExample()`을 만듭니다.
2. 동일한 함수에서 `client.Entities()`를 호출하고 결과를 확인합니다. 그런 다음, 결과를 반복합니다. 결과에는 성공하면 `Entities`에서 검색된 엔터티 목록이 포함되고, 그렇지 않으면 `errorMessage`가 포함됩니다. 검색된 각 엔터티에 대해 해당 Type(형식), Sub-Type(하위 형식), Wikipedia 이름(있는 경우) 외에도 원본 텍스트의 위치를 출력합니다.

    ```csharp
    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");

    // Printing recognized entities
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
    ```

### <a name="output"></a>출력

```console
Entities:
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0,      Length: 9,      Score: 1.000
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25,     Length: 10,     Score: 1.000
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40,     Length: 10,     Score: 0.999
    Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54,     Length: 7,      Score: 0.800
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54,     Length: 13,     Score: 0.800
    Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89,     Length: 5,      Score: 0.800
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116,    Length: 11,     Score: 0.800
```

## <a name="perform-key-phrase-extraction"></a>핵심 구 추출 수행

1. 이전에 만든 클라이언트를 사용하는 새 함수 `KeyPhraseExtractionExample()`을 만듭니다.
2. 동일한 함수에서 `client.KeyPhrases()`를 호출하고 결과를 확인합니다. 결과에는 성공하면 `KeyPhrases`에서 검색된 핵심 구 목록이 포함되고, 그렇지 않으면 `errorMessage`가 포함됩니다. 그런 다음, 검색된 핵심 구를 출력합니다.

    ```csharp
    var result = client.KeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in result.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
    ```

### <a name="output"></a>출력

```console
Key phrases:
    cat
    veterinarian
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 및 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Text Analytics 개요](../overview.md)
* [FAQ(질문과 대답)](../text-analytics-resource-faq.md)
