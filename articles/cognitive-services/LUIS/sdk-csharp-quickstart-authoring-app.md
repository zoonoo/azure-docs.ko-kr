---
title: '빠른 시작: .NET용 LUIS(Language Understanding) 작성 클라이언트 라이브러리'
titleSuffix: Azure Cognitive Services
description: .NET용 LUIS 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: diberry
ms.openlocfilehash: 6af076f585e7fc9afe870acada744ead2d2e9118
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672085"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-net"></a>빠른 시작: .NET용 LUIS(Language Understanding) 작성 클라이언트 라이브러리

.NET용 LUIS(Language Understanding) 작성 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.  LUIS(Language Understanding)를 사용하면 사용자 지정 기계 학습 인텔리전스를 사용자의 대화형 자연어 텍스트에 적용하여 전반적인 의미를 예측하고 관련된 세부 정보를 끌어올 수 있습니다. 

.NET용 LUIS(Language Understanding) 작성 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 앱 만들기
* 의도, 엔터티 및 발화 예제 추가
* 구 목록과 같은 기능 추가
* 앱 학습 및 게시

[참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [작성 패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [C# 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>필수 조건

* LUIS(Language Understanding) 포털 계정- [체험 계정 만들기](https://www.luis.ai)
* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>설치

### <a name="get-your-language-understanding-luis-starter-key"></a>LUIS(Language Understanding) 시작 키 가져오기

[시작 키](luis-how-to-azure-subscription.md#starter-key)를 가져오고 `COGNITIVESERVICE_AUTHORING_KEY`라는 [키 환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

선호하는 편집기 또는 IDE에서 .NET Core 애플리케이션을 새로 만듭니다. 

1. 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 dotnet `new` 명령을 사용하여 `language-understanding-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 `Program.cs`라는 단일 소스 파일을 사용하여 간단한 "Hello World" C# 프로젝트를 만듭니다. 

    ```console
    dotnet new console -n language-understanding-quickstart
    ```

1. 새로 만든 앱 폴더로 디렉터리를 변경합니다. 

1. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

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


### <a name="install-the-sdk"></a>SDK 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 LUIS(Language Understanding) 작성 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Visual Studio IDE를 사용하는 경우 클라이언트 라이브러리는 다운로드 가능한 NuGet 패키지로 제공됩니다.


## <a name="object-model"></a>개체 모델

LUIS(Language Understanding) 작성 클라이언트는 Azure를 인증하는 [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) 개체이며, 이 개체에는 작성 키가 포함되어 있습니다.

클라이언트가 만들어지면 이 클라이언트를 사용하여 다음과 같은 기능에 액세스합니다.

* 앱- [만들기](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [삭제](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [게시](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* 발화 예제 - [일괄 처리로 추가](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), [ID로 삭제](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet) 
* 기능 - [구 목록](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet) 관리 
* 모델 - [의도](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) 및 엔터티 관리
* 패턴 - [패턴](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet) 관리
* 학습 - 앱 [학습](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) 및 [학습 상태](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet) 폴링
* [버전](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) - 복제, 내보내기 및 삭제를 사용하여 관리


## <a name="code-examples"></a>코드 예제

이러한 코드 조각에서는 .NET용 LUIS(Language Understanding) 작성 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여 줍니다.

* [앱 만들기](#create-a-luis-app)
* [엔터티 추가](#create-entities-for-the-app)
* [의도 추가](#create-intent-for-the-app)
* [예제 발화 추가](#add-example-utterance-to-intent)
* [앱 학습](#train-the-app)
* [앱 게시](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>종속성 추가

선호하는 편집기 또는 IDE에서 프로젝트 디렉터리의 **Program.cs** 파일을 엽니다. 기존 `using` 코드를 다음 `using` 지시문으로 바꿉니다.

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>클라이언트 인증

1. `COGNITIVESERVICES_AUTHORING_KEY`라는 환경 변수에서 가져온 작성 키를 관리하는 변수를 만듭니다. 애플리케이션이 시작된 후 환경 변수를 만들었다면 애플리케이션을 실행 중인 편집기, IDE 또는 셸을 닫고 다시 로드해야 변수에 액세스할 수 있습니다. 메서드는 나중에 생성됩니다.

1. 작성 지역 및 엔드포인트를 유지할 변수를 만듭니다. 작성 키의 지역은 작성하는 위치에 따라 달라집니다. [3개의 작성 지역](luis-reference-regions.md)은 다음과 같습니다.

    * 오스트레일리아 - `australiaeast`
    * 유럽 - `westeurope`
    * 미국 및 기타 지역 - `westus`(기본값)
    
    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. 키를 사용하여 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) 개체를 만들고, 엔드포인트에서 이를 사용하여 [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) 개체를 만듭니다.

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>LUIS 앱 만들기

1. 의도, 엔터티 및 발화 예제를 유지하는 NLP(자연어 처리) 모델이 포함된 LUIS 앱을 만듭니다. 

1. [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet)를 만듭니다. 이름과 언어 문화권은 필수 속성입니다. 

1. [Apps.AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) 메서드를 호출합니다. 응답은 앱 ID입니다. 
    
    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>앱용 의도 만들기
LUIS 앱 모델의 기본 개체는 의도입니다. 의도는 사용자 발화 _의도_의 그룹화를 통해 정렬됩니다. 사용자는 봇(또는 다른 클라이언트 애플리케이션)에서 _의도된_ 특정 응답을 찾는 질문을 하거나 명령문을 만들 수 있습니다. 의도의 예로 항공편 예약, 목적지 도시의 날씨 요청 및 고객 서비스에 대한 연락처 정보 요청이 있습니다.   

고유한 의도 이름을 사용하여 [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet)를 만든 다음, 앱 ID, 버전 ID 및 ModelCreateObject를 [Model.AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) 메서드에 전달합니다. 응답은 의도 ID입니다.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>앱용 엔터티 만들기

엔터티는 필수는 아니지만 대부분의 앱에서 찾을 수 있습니다. 엔터티는 사용자의 의도를 채우는 데 필요한 정보를 사용자 발화에서 추출합니다. [미리 작성된](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) 엔터티 및 사용자 지정 엔터티의 여러 형식이 있으며, 각 엔터티는 고유한 DTO(데이터 변환 개체) 모델을 사용합니다.  앱에 추가할 수 있는 일반적인 미리 작성된 엔터티에는 [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), [geographyV2](luis-reference-prebuilt-geographyv2.md), [ordinal](luis-reference-prebuilt-ordinal.md)이 있습니다. 

다음 **AddEntities** 메서드는 두 개의 역할(`Class` 단순 엔터티, `Flight` 복합 엔터티)을 사용하여 `Location` 단순 엔터티를 만들었으며, 몇 가지 미리 작성된 엔터티를 추가합니다.

엔터티는 의도에 표시되지 않는다는 것을 알고 있어야 합니다. 대부분의 의도에 적용할 수 있고 일반적으로 적용하고 있습니다. 사용자 발화 예제만 특정 단일 의도에 대해 표시됩니다.

엔터티를 만드는 메서드는 [Model](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) 클래스의 일부입니다. 각 엔터티 형식에는 자체의 고유한 DTO(데이터 변환 개체) 모델이 있으며, 일반적으로 [Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet) 네임스페이스에서 `model` 단어가 포함됩니다. 

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>의도에 발화 예제 추가

발화의 의도를 파악하고 엔터티를 추출하기 위해 앱에는 발화 예제가 필요합니다. 이러한 예제는 특정 단일 의도를 대상으로 해야 하며, 모든 사용자 지정 엔터티를 표시해야 합니다. 미리 작성된 엔터티는 표시할 필요가 없습니다. 

각 발화 예제에 대한 하나의 개체인 [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) 개체의 목록을 만들어 발화 예제를 추가합니다. 각 예제는 엔터티 이름과 엔터티 값의 이름/값 쌍으로 구성된 사전을 통해 모든 엔터티를 표시해야 합니다. 엔터티 값은 발화 예제의 텍스트에 표시된 것과 정확히 일치해야 합니다. 

앱 ID, 버전 ID 및 예제 목록을 사용하여 [Examples.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_)를 호출합니다. 호출은 결과 목록으로 응답합니다. 각 예제의 결과를 확인하여 모델에 성공적으로 추가되었는지 확인해야 합니다. 

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]
    
**CreateUtterance** 및 **CreateLabel** 메서드는 개체를 만드는 데 도움이 되는 유틸리티 메서드입니다.

## <a name="train-the-app"></a>앱 학습

모델이 만들어지면 이 버전의 모델에 대해 LUIS 앱을 학습시켜야 합니다. 학습된 모델은 [컨테이너](luis-container-howto.md)에서 사용하거나 준비 또는 제품 슬롯에 [게시](luis-how-to-publish-app.md)할 수 있습니다. 

[Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) 메서드에는 앱 ID와 버전 ID가 필요합니다. 

이 빠른 시작에서 보여 주는 것과 같은 매우 작은 모델은 매우 빠르게 학습됩니다. 프로덕션 수준 애플리케이션의 경우 앱을 학습시키려면 학습이 성공했는지 여부를 확인하기 위해 [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) 메서드에 대한 폴링 호출이 포함되어야 합니다. 응답은 각 개체에 대한 개별 상태가 있는 [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) 개체의 목록입니다. 모든 개체가 성공해야 학습이 완료된 것으로 간주합니다.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Language Understanding 앱 게시

[PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) 메서드를 사용하여 LUIS 앱을 게시합니다. 그러면 현재 학습된 버전이 엔드포인트의 지정된 슬롯에 게시됩니다. 클라이언트 애플리케이션에서 이 엔드포인트를 사용하여 의도 및 엔터티 추출의 예측을 위한 사용자 발화를 보냅니다.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 dotnet `run` 명령을 사용하여 애플리케이션을 실행합니다.

```console
dotnet run
```

## <a name="clean-up-resources"></a>리소스 정리

정리하려는 경우 LUIS 앱을 삭제할 수 있습니다. 앱 삭제는 [Apps.DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) 메서드를 통해 수행됩니다. [LUIS 포털](https://www.luis.ai)에서도 앱을 삭제할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[.Net SDK를 사용하여 예측 엔드포인트 쿼리](sdk-csharp-quickstart-query-prediction-endpoint.md)

* [LUIS(Language Understanding) API란?](what-is-luis.md)
* [새로운 기능](whats-new.md)
* [의도](luis-concept-intent.md), [엔터티](luis-concept-entity-types.md), [발화 예제](luis-concept-utterance.md) 및 [미리 작성된 엔터티](luis-reference-prebuilt-entities.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)에서 확인할 수 있습니다.
