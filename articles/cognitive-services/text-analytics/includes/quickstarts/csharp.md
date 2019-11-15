---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: fd3d53dce398c445d309a19f1f58a8d298080c45
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750196"
---
<a name="HOLTop"></a>

[참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

> [!NOTE]
> 이 문서의 코드는 간단히 하기 위해 Text Analytics .NET SDK의 동기 메서드를 사용합니다. 프로덕션 시나리오의 경우 성능 및 확장성을 위해 일괄 처리된 비동기 메서드를 사용하는 것이 좋습니다. 예를 들어 [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) 대신 [SentimentBatchAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentimentbatchasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview)를 호출합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 최신 버전의 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>설치

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure 리소스 만들기

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-net-core-application"></a>새 .NET Core 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `text-analytics quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *program.cs*라는 단일 C# 원본 파일을 사용하여 간단한 "Hello World" 프로젝트를 만듭니다. 

```console
dotnet new console -n text-analytics-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```console
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

프로젝트 디렉터리에서 *program.cs* 파일을 열고 `using` 지시문을 추가합니다.

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

애플리케이션의 `Program` 클래스에서 이전에 만든 환경 변수에서 리소스 키 및 엔드포인트에 대한 변수를 만듭니다. 애플리케이션 편집을 시작한 후 이러한 환경 변수를 만든 경우 변수에 액세스하기 위해 사용 중인 편집기, IDE 또는 셸을 닫았다가 다시 열어야 합니다.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

[!code-csharp[initial variables](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=vars)]

애플리케이션의 `Main` 메서드를 바꿉니다. 나중에 여기서 호출되는 메서드를 정의합니다.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 Text Analytics 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.TextAnalytics --version 4.0.0
```

## <a name="object-model"></a>개체 모델

Text Analytics 클라이언트는 키를 사용하여 Azure에 인증하고 텍스트를 단일 문자열 또는 일괄 처리로 허용하는 함수를 제공하는 [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) 개체입니다. 텍스트는 API에 동기식 또는 비동기식으로 보낼 수 있습니다. 응답 개체에는 보내는 각 문서에 대한 분석 정보가 포함됩니다. 

## <a name="code-examples"></a>코드 예제

* [클라이언트 인증](#authenticate-the-client)
* [감정 분석](#sentiment-analysis)
* [언어 감지](#language-detection)
* [엔터티 인식](#entity-recognition)
* [핵심 구 추출](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>클라이언트 인증

자격 증명을 저장하여 클라이언트의 요청에 추가하는 새 `ApiKeyServiceClientCredentials` 클래스를 만듭니다. 이 안에 `Ocp-Apim-Subscription-Key` 헤더에 키를 추가하는 `ProcessHttpRequestAsync()`에 대한 재정의를 만듭니다.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

엔드포인트 및 키를 포함하는 `ApiKeyServiceClientCredentials` 개체를 사용하여 [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) 개체를 인스턴스화하는 메서드를 만듭니다.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

프로그램의 `main()` 메서드에서 인증 메서드를 호출하여 클라이언트를 인스턴스화합니다.

## <a name="sentiment-analysis"></a>정서 분석

앞에서 만든 클라이언트를 사용하고 해당 [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) 함수를 호출하는 `SentimentAnalysisExample()`이라는 새 함수를 만듭니다. 반환된 [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) 개체에는 성공하면 감정 `Score`가 포함되고 그렇지 않으면 `errorMessage`가 포함됩니다. 

점수가 0에 가까울수록 부정적인 감정을 나타내고, 1에 가까울수록 긍정적인 감정을 나타냅니다.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

### <a name="output"></a>출력

```console
Sentiment Score: 0.87
```

## <a name="language-detection"></a>언어 검색

앞에서 만든 클라이언트를 사용하고 해당 [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 함수를 호출하는 `languageDetectionExample()`이라는 새 함수를 만듭니다. 반환된 [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) 개체에는 성공하면 `DetectedLanguages`에서 검색된 언어 목록이 포함되고 그렇지 않으면 `errorMessage`가 포함됩니다.  처음 반환된 언어를 출력합니다.

> [!Tip]
> 일부 경우에는 입력에 따라 언어를 명확하게 구분하는 것이 어려울 수 있습니다. 2자로 된 국가 코드는 `countryHint` 매개 변수를 사용하여 지정할 수 있습니다. 기본적으로 API는 "US"를 기본 countryHint로 사용합니다. 이 동작을 제거하려면 이 값을 빈 문자열의 `countryHint = ""`로 설정하여 이 매개 변수를 다시 설정하면 됩니다.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>출력

```console
Language: English
```

## <a name="entity-recognition"></a>엔터티 인식

앞에서 만든 클라이언트를 사용하고 해당 [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 함수를 호출하는 `RecognizeEntitiesExample()`이라는 새 함수를 만듭니다. 결과를 반복합니다. 반환된 [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) 개체에는 성공하면 `Entities`에서 검색된 엔티티 목록이 포함되고 그렇지 않으면 `errorMessage`가 포함됩니다. 검색된 각 엔터티에 대해 해당 Type(형식), Sub-Type(하위 형식), Wikipedia 이름(있는 경우) 외에도 원본 텍스트의 위치를 출력합니다.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]


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

## <a name="key-phrase-extraction"></a>핵심 문구 추출

앞에서 만든 클라이언트를 사용하고 해당 [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 함수를 호출하는 `KeyPhraseExtractionExample()`이라는 새 함수를 만듭니다. 결과에는 성공하면 `KeyPhrases`에서 검색된 핵심 구 목록이 포함되고, 그렇지 않으면 `errorMessage`가 포함됩니다. 검색된 핵심 구를 출력합니다.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>출력

```console
Key phrases:
    cat
    veterinarian
```
