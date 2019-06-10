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
ms.date: 05/28/2019
ms.author: assafi
ms.openlocfilehash: a8b41f6853e9c91e64de903960b880e44f22ed55
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297813"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>빠른 시작: .NET SDK 및 C#을 사용하여 Text Analytics 서비스 호출
<a name="HOLTop"></a>

이 빠른 시작을 사용하면 .NET 및 C#용 Azure SDK를 사용하여 시작하는 데 도움이 됩니다. [Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) REST API는 대부분의 프로그래밍 언어와 호환되지만, SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다.

> [!NOTE]
> 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics)에 제공됩니다.

기술 세부 정보는 .NET용 SDK [Text Analytics](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* [Visual Studio 2017 이상]의 모든 버전
* Text Analytics [.NET용 SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

가입하는 동안 생성된 [엔드포인트 및 액세스 키](../How-tos/text-analytics-how-to-access-key.md)도 필요합니다.

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
    /// <summary>
    /// Allows authentication to the API by using a basic apiKey mechanism
    /// </summary>
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string subscriptionKey;

        /// <summary>
        /// Creates a new instance of the ApiKeyServiceClientCredentails class
        /// </summary>
        /// <param name="subscriptionKey">The subscription key to authenticate and authorize as</param>
        public ApiKeyServiceClientCredentials(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
        }

        /// <summary>
        /// Add the Basic Authentication Header to each outgoing request
        /// </summary>
        /// <param name="request">The outgoing request</param>
        /// <param name="cancellationToken">A token to cancel the operation</param>
        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }

            request.Headers.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. `Program` 클래스를 업데이트합니다. Text Analytics 구독 키에 대한 상수 멤버 및 서비스 엔드포인트에 대한 또 다른 멤버를 추가합니다. Text Analytics 구독에 대한 올바른 Azure 지역을 사용해야 합니다.

    ```csharp
    private const string SubscriptionKey = "enter-your-key-here";

    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://westus.api.cognitive.microsoft.com";
    ```
> [!Tip]
> 프로덕션 시스템에서 비밀의 보안을 강화하기 위해 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)를 사용하는 것이 좋습니다.
>

## <a name="create-a-text-analytics-client"></a>Text Analytics 클라이언트 만들기

프로젝트의 `Main` 함수에서 호출하려는 샘플 메서드를 호출합니다. 정의한 `Endpoint` 및 `SubscriptionKey` 매개 변수를 전달합니다.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(SubscriptionKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

다음 섹션에서는 각 서비스 기능을 호출하는 방법을 설명합니다.

## <a name="perform-sentiment-analysis"></a>감정 분석 수행

1. 이전에 만든 클라이언트를 사용하는 새 함수 `SentimentAnalysisExample()`을 만듭니다.
2. 분석하려는 문서가 포함된 `MultiLanguageInput` 개체 목록을 생성합니다.

    ```csharp
    public static async Task SentimentAnalysisExample(TextAnalyticsClient client)
    {
        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life."),
                new MultiLanguageInput("en", "2", "This was a waste of my time. The speaker put me to sleep."),
                new MultiLanguageInput("es", "3", "No tengo dinero ni nada que dar..."),
                new MultiLanguageInput("it", "4", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
            });
        //...
    }
    ```

3. 동일한 함수에서 `client.SentimentAsync()`를 호출하고 결과를 확인합니다. 그런 다음, 결과를 반복합니다. 각 문서의 ID 및 감정 점수를 인쇄합니다. 점수가 0에 가까울수록 부정적인 감정을 나타내고, 1에 가까울수록 긍정적인 감정을 나타냅니다.

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>출력

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="perform-language-detection"></a>언어 검색 수행

1. 이전에 만든 클라이언트를 사용하는 새 함수 `DetectLanguageExample()`을 만듭니다.
2. 문서가 포함된 `LanguageInput` 개체 목록을 생성합니다.

    ```csharp
    public static async Task DetectLanguageExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English."),
                        new LanguageInput(id: "2", text: "Este es un document escrito en Español."),
                        new LanguageInput(id: "3", text: "这是一个用中文写的文件")
                    });
        //...
    }
    ```

3. 동일한 함수에서 `client.DetectLanguageAsync()`를 호출하고 결과를 확인합니다. 그런 다음, 결과를 반복합니다. 각 문서의 ID 및 처음 반환된 언어를 인쇄합니다.

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>출력

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="perform-entity-recognition"></a>엔터티 인식 수행

1. 이전에 만든 클라이언트를 사용하는 새 함수 `RecognizeEntitiesExample()`을 만듭니다.
2. 문서가 포함된 `MultiLanguageBatchInput` 개체 목록을 생성합니다.

    ```csharp
    public static async Task RecognizeEntitiesExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
                new MultiLanguageInput("es", "2", "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.")
            });
        //...
    }
    ```

3. 동일한 함수에서 `client.EntitiesAsync()`를 호출하고 결과를 확인합니다. 그런 다음, 결과를 반복합니다. 각 문서의 ID를 인쇄합니다. 검색된 각 엔터티에 대해 원본 텍스트의 위치뿐만 아니라 Wikipedia 이름, 형식 및 하위 형식(존재하는 경우)도 인쇄합니다.

    ```csharp
    var entitiesResult = await client.EntitiesAsync(false, inputDocuments);

    // Printing recognized entities
    foreach (var document in entitiesResult.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Entities:");
        foreach (var entity in document.Entities)
        {
            Console.WriteLine($"\t\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
            foreach (var match in entity.Matches)
            {
                Console.WriteLine($"\t\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
            }
        }
    }
    ```

### <a name="output"></a>출력

```console
Document ID: 1
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
Document ID: 2
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 21,     Length: 9,      Score: 1.000
                Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
                        Offset: 60,     Length: 7,      Score: 0.991
                Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
                        Offset: 71,     Length: 13,     Score: 0.800
                Name: Seattle,  Type: Location, Sub-Type: N/A
                        Offset: 88,     Length: 7,      Score: 1.000
```

## <a name="perform-key-phrase-extraction"></a>핵심 구 추출 수행

1. 이전에 만든 클라이언트를 사용하는 새 함수 `KeyPhraseExtractionExample()`을 만듭니다.
2. 문서가 포함된 `MultiLanguageBatchInput` 개체 목록을 생성합니다.

    ```csharp
    public static async Task KeyPhraseExtractionExample(TextAnalyticsClient client)
    {
        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("ja", "1", "猫は幸せ"),
                        new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                        new MultiLanguageInput("en", "3", "My cat might need to see a veterinarian."),
                        new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                    });
        //...
    }
    ```

3. 동일한 함수에서 `client.KeyPhrasesAsync()`를 호출하고 결과를 확인합니다. 그런 다음, 결과를 반복합니다. 각 문서의 ID 및 검색된 모든 핵심 구를 인쇄합니다.

    ```csharp
    var kpResults = await client.KeyPhrasesAsync(false, inputDocuments);

    // Printing keyphrases
    foreach (var document in kpResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Key phrases:");

        foreach (string keyphrase in document.KeyPhrases)
        {
            Console.WriteLine($"\t\t{keyphrase}");
        }
    }
    ```

### <a name="output"></a>출력

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 및 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Text Analytics 개요](../overview.md)
* [FAQ(질문과 대답)](../text-analytics-resource-faq.md)
