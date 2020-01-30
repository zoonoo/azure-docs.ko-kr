---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 01/14/2020
ms.author: diberry
ms.openlocfilehash: fa0537b4009cf38b95cedff93850e5dac61baf34
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772440"
---
.NET용 LUIS(Language Understanding) 예측 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 슬롯별 예측 가져오기

[참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [예측 런타임 패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [C# 샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>사전 요구 사항

* LUIS(Language Understanding) 포털 계정- [체험 계정 만들기](https://www.luis.ai)
* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

자세한 설명서를 찾으시나요?

 * [SDK 참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)

## <a name="setting-up"></a>설치

### <a name="create-an-environment-variable"></a>환경 변수 만들기

키와 리소스 이름을 사용하여 인증을 위한 두 가지 환경 변수를 만듭니다.

* `LUIS_PREDICTION_KEY` - 요청을 인증하기 위한 리소스 키입니다.
* `LUIS_ENDPOINT_NAME` - 키와 연결된 리소스 이름입니다.

운영 체제에 대한 지침을 사용합니다.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

환경 변수를 추가한 후 콘솔 창을 다시 시작합니다.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

환경 변수를 추가한 후에는 콘솔 창에서 `source ~/.bashrc` 명령을 실행하여 변경 내용을 적용합니다.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

`.bash_profile`을 편집하고, 환경 변수를 추가합니다.

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

환경 변수를 추가한 후에는 콘솔 창에서 `source .bash_profile` 명령을 실행하여 변경 내용을 적용합니다.

---

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

선호하는 편집기 또는 IDE에서 .NET Core 애플리케이션을 새로 만듭니다.

1. 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 dotnet `new` 명령을 사용하여 `language-understanding-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 `Program.cs`라는 단일 소스 파일을 사용하여 간단한 "Hello World" C# 프로젝트를 만듭니다.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. 새로 만든 앱 폴더로 디렉터리를 변경합니다.

1. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

    ```dotnetcli
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

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 LUIS(Language Understanding) 예측 런타임 클라이언트 라이브러리를 설치합니다.

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Visual Studio IDE를 사용하는 경우 클라이언트 라이브러리는 다운로드 가능한 NuGet 패키지로 제공됩니다.

## <a name="object-model"></a>개체 모델

LUIS(Language Understanding) 예측 런타임 클라이언트는 Azure를 인증하는 [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) 개체이며, 리소스 키를 포함합니다.

클라이언트가 만들어지면 이 클라이언트를 사용하여 다음과 같은 기능에 액세스합니다.

* [스테이징 또는 제품 슬롯](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)별 예측
* [버전](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)별 예측


## <a name="code-examples"></a>코드 예제

이러한 코드 조각은 .NET용 LUIS(Language Understanding) 예측 런타임 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [슬롯별 예측](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>종속성 추가

선호하는 편집기 또는 IDE에서 프로젝트 디렉터리의 *Program.cs* 파일을 엽니다. 기존 `using` 코드를 다음 `using` 지시문으로 바꿉니다.

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>클라이언트 인증

1. 키, 이름 및 앱 ID에 대한 변수를 만듭니다.

    `LUIS_PREDICTION_KEY`라는 환경 변수에서 끌어온 예측 키를 관리하는 변수입니다. 애플리케이션이 시작된 후 환경 변수를 만들었다면 애플리케이션을 실행 중인 편집기, IDE 또는 셸을 닫고 다시 로드해야 변수에 액세스할 수 있습니다. 메서드는 나중에 생성됩니다.

    리소스 이름 `LUIS_ENDPOINT_NAME`을 보유할 변수를 만듭니다.

    `LUIS_APP_ID`라는 환경 변수로 앱 ID에 대한 변수를 만듭니다. 환경 변수를 퍼블릭 IoT 앱으로 설정합니다.

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. 키를 사용하여 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) 개체를 만들고, 엔드포인트에서 이를 사용하여 [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) 개체를 만듭니다.

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>런타임에서 예측 가져하기

다음 메서드를 추가하여 예측 런타임에 대한 요청을 만듭니다.

사용자 발화는 [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) 개체의 일부입니다.

**GetSlotPredictionAsync** 메서드는 요청을 처리할 여러 매개 변수(예: app ID, slot name, prediction request 개체)가 필요합니다. verbose, show all intents, log와 같은 다른 옵션은 선택 사항입니다.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>예측의 Main 코드

다음과 같은 Main 메서드를 사용하여 변수와 메서드를 연결하여 예측을 얻습니다.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `dotnet run` 명령을 사용하여 애플리케이션을 실행합니다.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>리소스 정리

예측을 완료했으면, program.cs 파일과 해당 하위 디렉터리를 삭제하여 이 빠른 시작의 작업을 정리합니다.
