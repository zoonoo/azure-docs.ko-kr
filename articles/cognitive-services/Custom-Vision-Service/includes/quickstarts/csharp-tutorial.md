---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8286c0d45700eb976825403ae4321878f87f00ad
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91989452"
---
.NET용 Custom Vision 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 이미지 분류 모델을 빌드하기 위한 예제 코드를 사용해 봅니다. 프로젝트를 만들고, 태그를 추가하고, 프로젝트를 학습하고, 프로젝트의 예측 엔드포인트 URL을 사용하여 프로그래밍 방식으로 테스트합니다. 자체 이미지 인식 앱을 빌드하기 위한 템플릿으로 이 예제를 사용할 수 있습니다.

> [!NOTE]
> 코드를 작성하지 _않고_ 분류 모델을 빌드하고 학습시키려면 [브라우저 기반 지침](../../getting-started-build-a-classifier.md)을 대신 참조하세요.

.NET용 Custom Vision 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 새 Custom Vision 프로젝트 만들기
* 프로젝트에 태그 추가
* 이미지 업로드 및 태그 지정
* 프로젝트 학습
* 현재 반복 게시
* 예측 엔드포인트 테스트

[참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet) | 라이브러리 소스 코드 [(학습)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(예측)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) | 패키지(NuGet) [(학습)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) [(예측)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/) | [샘플](https://docs.microsoft.com/samples/browse/?products=azure&term=vision&terms=vision)


## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 또는 현재 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Azure 구독이 있으면 Azure Portal에서 <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="Custom Vision 리소스를 만들어"  target="_blank">Custom Vision 리소스 만들기 <span class="docon docon-navigate-external x-hidden-focus"></span></a> 학습 및 예측 리소스를 만들고 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Custom Vision에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio를 사용하여 새 .NET Core 애플리케이션을 만듭니다. 

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치 

새 프로젝트가 만들어지면 **솔루션 탐색기**에서 마우스 오른쪽 단추로 프로젝트 솔루션을 클릭하고, **NuGet 패키지 관리**를 선택하여 클라이언트 라이브러리를 설치합니다. 열리는 패키지 관리자에서 **찾아보기**를 선택하고, **시험판 포함**을 선택하고, `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` 및 `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction`을 검색합니다. 최신 버전을 선택한 다음, **설치**를 선택합니다. 

#### <a name="cli"></a>[CLI](#tab/cli)

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `custom-vision-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *program.cs*라는 단일 소스 파일을 사용하여 간단한 "Hello World" C# 프로젝트를 만듭니다. 

```console
dotnet new console -n custom-vision-quickstart
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

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치 

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 Custom Vision 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

프로젝트 디렉터리에서 *program.cs* 파일을 열고 `using` 지시문을 추가합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_imports)]


애플리케이션의 **Main** 메서드에서 리소스의 키 및 엔드포인트에 대한 변수를 만듭니다. 또한 나중에 사용할 몇 가지 기본 개체도 선언합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Custom Vision 리소스가 성공적으로 배포되면 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 키와 엔드포인트를 찾을 수 있습니다. 학습 및 예측 키를 모두 가져와야 합니다.
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 자세한 내용은 Cognitive Services [보안](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) 문서를 참조하세요.

애플리케이션의 **Main** 메서드에서 이 빠른 시작에서 사용되는 메서드에 대한 호출을 추가합니다. 이러한 메서드는 나중에 구현합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>개체 모델

|Name|설명|
|---|---|
|[CustomVisionTrainingClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-dotnet) | 이 클래스는 모델의 생성, 학습 및 게시를 처리합니다. |
|[CustomVisionPredictionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-dotnet-preview)| 이 클래스는 이미지 분류 예측에 대한 모델의 쿼리를 처리합니다.|
|[PredictionModel](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel?view=azure-dotnet-preview)| 이 클래스는 단일 이미지에 대한 단일 예측을 정의합니다. 여기에는 개체 ID 및 이름에 대한 속성과 신뢰도 점수가 포함됩니다.|

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 .NET용 Custom Vision 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [새 Custom Vision 프로젝트 만들기](#create-a-new-custom-vision-project)
* [프로젝트에 태그 추가](#add-tags-to-the-project)
* [이미지 업로드 및 태그 지정](#upload-and-tag-images)
* [프로젝트 학습](#train-the-project)
* [현재 반복 게시](#publish-the-current-iteration)
* [예측 엔드포인트 테스트](#test-the-prediction-endpoint)


## <a name="authenticate-the-client"></a>클라이언트 인증

새 메서드에서 엔드포인트와 키를 사용하여 학습 및 예측 클라이언트를 인스턴스화합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>새 Custom Vision 프로젝트 만들기

여기에 있는 코드의 다음 비트는 이미지 분류 프로젝트를 만듭니다. 만든 프로젝트는 [Custom Vision 웹 사이트](https://customvision.ai/)에 표시됩니다. 프로젝트를 만들 때 다른 옵션을 지정하려면 [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) 메서드를 참조하세요([분류자 빌드](../../getting-started-build-a-classifier.md) 웹 포털 가이드에 설명되어 있음).  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>프로젝트에 태그 추가

다음 메서드는 모델을 학습시키는 태그를 정의합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_addtags)]

## <a name="upload-and-tag-images"></a>이미지 업로드 및 태그 지정

먼저 이 프로젝트에 대한 샘플 이미지를 다운로드합니다. [샘플 이미지 폴더](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images)의 콘텐츠를 로컬 디바이스에 저장합니다.

그런 다음, 이미지를 이 디렉터리에 업로드하는 도우미 메서드를 정의합니다. 이미지가 저장되는 위치를 가리키도록 **GetFiles** 인수를 편집해야 할 수 있습니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_loadimages)]

다음으로 이미지를 업로드하는 메서드를 정의하고, 폴더 위치에 따라 태그를 적용합니다(이미지는 이미 정렬되어 있음). 이미지를 반복적으로 또는 일괄적으로(일괄 처리당 최대 64개) 업로드하고 태그를 지정할 수 있습니다. 다음 코드 조각에는 두 가지 예제가 모두 포함되어 있습니다. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]


## <a name="train-the-project"></a>프로젝트 학습

다음 메서드는 프로젝트의 첫 번째 학습 반복을 만듭니다. 학습이 완료될 때까지 서비스를 쿼리합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

> [!TIP]
> 선택한 태그로 학습
>
> 적용된 태그의 하위 집합에 대해서만 선택적으로 학습을 수행할 수 있습니다. 특정 태그는 아직 충분히 적용하지 않았지만 다른 태그는 충분히 적용한 경우 이 작업을 수행할 수 있습니다. [TrainProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) 호출에서 *trainingParameters* 매개 변수를 사용합니다. [TrainingParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters?view=azure-dotnet&preserve-view=true)를 생성하고 **SelectedTags** 속성을 사용하려는 태그의 ID 목록으로 설정합니다. 모델은 해당 목록의 태그만 인식하도록 학습됩니다.

## <a name="publish-the-current-iteration"></a>현재 반복 게시

다음 메서드는 모델의 현재 반복을 쿼리에 사용할 수 있도록 합니다. 모델 이름을 참조로 사용하여 예측 요청을 보낼 수 있습니다. `predictionResourceId`에 대한 고유한 값을 입력해야 합니다. Azure Portal에 있는 리소스의 **개요** 탭에서 **구독 ID**로 나열된 예측 리소스 ID를 찾을 수 있습니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>예측 엔드포인트 테스트

이 스크립트 부분은 테스트 이미지를 로드하고, 모델 엔드포인트를 쿼리하고, 예측 데이터를 콘솔에 출력합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_test)]


## <a name="run-the-application"></a>애플리케이션 실행

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

IDE 창의 위쪽에서 **디버그** 단추를 클릭하여 애플리케이션을 실행합니다.

#### <a name="cli"></a>[CLI](#tab/cli)

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```dotnet
dotnet run
```

---

애플리케이션이 실행되면 콘솔 창이 열리고 다음 출력이 기록됩니다.

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

그러면 테스트 이미지(**Images/Test/** 에 있음)에 태그가 적절하게 지정되는지 확인할 수 있습니다. 애플리케이션을 종료하려면 아무 키나 누릅니다. [Custom Vision 웹 사이트](https://customvision.ai)로 돌아가서 새로 만든 프로젝트의 현재 상태를 살펴볼 수도 있습니다.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>다음 단계

이제 코드에서 이미지 분류 프로세스의 모든 단계를 완료했습니다. 이 샘플은 학습을 한 번만 반복하지만, 정확도를 높이기 위해 모델을 여러 차례 학습하고 테스트해야 하는 경우가 많습니다.

> [!div class="nextstepaction"]
> [모델 테스트 및 재교육](../../test-your-model.md)

* Custom Vision이란?
* [SDK 참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet)