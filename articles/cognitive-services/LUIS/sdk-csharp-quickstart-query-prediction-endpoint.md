---
title: '빠른 시작: C# SDK 예측 엔드포인트 쿼리'
titleSuffix: Azure Cognitive Services
description: C# SDK를 사용하여 LUIS에 사용자 발화를 보내고 예측을 수신합니다.
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: diberry
ms.openlocfilehash: e009ef159a51a22c56409cd1564fa9fde3c67b55
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56344216"
---
# <a name="quickstart-query-prediction-endpoint-with-c-net-sdk"></a>빠른 시작: C# .NET SDK를 통해 예측 엔드포인트 쿼리

[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)에서 찾은 .NET SDK를 사용하여 LUIS(Language Understanding)에 사용자 발화를 보내고 사용자 의도의 예측을 수신합니다. 

이 빠른 시작에서는 `turn on the bedroom light`와 같은 사용자 발화를 공용 Language Understanding 애플리케이션에 보낸 다음, 예측을 수신하고 발화 내에서 찾은 상위 채점 의도 `HomeAutomation.TurnOn` 및 엔터티 `HomeAutomation.Room`을 표시합니다. 

## <a name="prerequisites"></a>필수 조건

* [Visual Studio Community 2017 버전(Edition)](https://visualstudio.microsoft.com/vs/community/)
* C# 프로그래밍 언어(VS Community 2017에 포함됨)
* 공용 앱 ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> 전체 솔루션은 [cognitive-services-language-understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime) GitHub 리포지토리에서 사용할 수 있습니다.

자세한 설명서를 찾으시나요?

 * [SDK 참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Cognitive Services 또는 Language Understanding 키 가져오기

홈 자동화를 위한 공용 앱을 사용하려면 엔드포인트 예측에 유효한 키가 필요합니다. 많은 Cognitive Services에 유효한 Cognitive Services 키(Azure CLI를 사용하여 아래에 생성됨) 또는 `Language Understanding` 키를 사용할 수 있습니다. 

다음 [Cognitive Service 키를 만들기 위한 Azure CLI 명령](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create)을 사용합니다.

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>.NET Core 프로젝트 만들기

Visual Studio Community 2017에서 .NET Core 콘솔 프로젝트를 만듭니다.

1. Visual Studio Community 2017을 엽니다.
1. **Visual C#**  섹션에서 새 프로젝트를 만들고, **콘솔 앱(.NET Core)** 을 선택합니다.
1. `QueryPrediction`이라는 프로젝트 이름을 입력하고 나머지 기본값은 그대로 둔 상태로 **확인**을 선택합니다.
    그러면 **Program.cs**라는 기본 코드 파일이 포함된 간단한 프로젝트가 만들어집니다.

## <a name="add-sdk-with-nuget"></a>NuGet으로 SDK 추가

1. **솔루션 탐색기**에서 **QueryPrediction**이라는 트리 보기에서 프로젝트를 선택한 다음, 마우스 오른쪽 단추를 클릭합니다. 메뉴에서 **NuGet 패키지 관리...** 를 선택합니다.
1. **찾아보기**를 선택한 다음, `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`을 입력합니다. 패키지 정보가 표시되면 **설치**를 선택하여 패키지를 프로젝트에 설치합니다. 
1. **Program.cs** 위에 다음 _using_ 문을 추가합니다. `System`에 대한 기존의 _using_ 문은 제거하지 마십시오. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>예측에 대한 새 메서드 만들기

예측 엔드포인트 쿼리에 쿼리를 보내는 새 메서드 `GetPrediction`을 만듭니다. 메서드는 필요한 모든 개체를 만들고 구성한 다음, [`LuisResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-dotnet) 예측 결과를 통해 `Task`를 반환합니다. 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>자격 증명 개체 만들기

다음 코드를 `GetPrediction` 메서드에 추가하여 Cognitive Service 키로 클라이언트 자격 증명을 만듭니다.

`<REPLACE-WITH-YOUR-KEY>`를 사용자의 Cognitive Service 키의 지역으로 바꿉니다. 키는 [Azure Portal](https://portal.azure.com)의 해당 리소스에 대한 키 페이지에 있습니다.

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Language Understanding 클라이언트 만들기

`GetPrediction` 메서드에서 위의 코드 뒤에 자격 증명을 사용하기 위한 다음 코드를 추가하여 [`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___) 클라이언트 개체를 만듭니다. 

`<REPLACE-WITH-YOUR-KEY-REGION>`을 `westus`와 같이 사용자의 키 지역으로 바꿉니다. 키 지역은 [Azure Portal](https://portal.azure.com)의 해당 리소스에 대한 개요 페이지에 있습니다.

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>쿼리 매개 변수 설정

`GetPrediction` 메서드에서 위의 코드 뒤에 쿼리 매개 변수를 설정하기 위한 다음 코드를 추가합니다.

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>예측 엔드포인트 쿼리

`GetPrediction` 메서드에서 위의 코드 뒤에 쿼리 매개 변수를 설정하기 위한 다음 코드를 추가합니다.

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>예측 결과 표시

**Main** 메서드를 변경하여 새 `GetPrediction` 메서드를 호출하고 예측 결과를 반환합니다.

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>프로젝트 실행

Studio에서 프로젝트를 빌드하고 프로젝트를 실행하여 쿼리의 출력을 확인합니다.

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>다음 단계

[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) 및 [.NET 참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)에 대해 자세히 알아봅니다. 

> [!div class="nextstepaction"] 
> [자습서: 사용자 의도를 확인하는 LUIS 앱 빌드](luis-quickstart-intents-only.md) 