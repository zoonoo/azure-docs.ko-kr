---
author: PatrickFarley
ms.custom: devx-track-java
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/13/2020
ms.openlocfilehash: 83ff3d53ec44d4bafc1e677f0c496e6beb3dd41f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548223"
---
Java용 Custom Vision 클라이언트 라이브러리를 사용하여 개체 검색 모델을 빌드합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. 자체 이미지 인식 앱을 빌드하기 위한 템플릿으로 이 예제를 사용할 수 있습니다.

> [!NOTE]
> 코드를 작성하지 않고 개체 감지 모델을 빌드하고 학습시키려면 [브라우저 기반 지침](../../get-started-build-detector.md)을 대신 참조하세요.

Java용 Custom Vision 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 새 Custom Vision 프로젝트 만들기
* 프로젝트에 태그 추가
* 이미지 업로드 및 태그 지정
* 프로젝트 학습
* 현재 반복 게시
* 예측 엔드포인트 테스트

[참조 설명서](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/customvision?view=azure-java-stable) | 라이브러리 소스 코드 [(학습)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-training) [(예측)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-prediction) | 아티팩트(Maven) [(학습)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-training/1.1.0-preview.2/jar) [(예측)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-prediction/1.1.0-preview.2/jar) | 
[샘플](https://docs.microsoft.com/samples/browse/?products=azure&terms=custom%20vision)


## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)의 현재 버전
* [Gradle 빌드 도구](https://gradle.org/install/) 또는 다른 종속성 관리자
* Azure 구독이 있으면 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="Custom Vision 리소스를 만들어"  target="_blank">Custom Vision 리소스 만들기 <span class="docon docon-navigate-external x-hidden-focus"></span></a> 학습 및 예측 리소스를 만들고 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Custom Vision에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-gradle-project"></a>새 Gradle 프로젝트 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

작업 디렉터리에서 `gradle init` 명령을 실행합니다. 이 명령은 *build.gradle.kts* 를 포함하여 런타임에 애플리케이션을 만들고 구성하는 데 사용되는 Gradle용 필수 빌드 파일을 만듭니다.

```console
gradle init --type basic
```

**DSL** 을 선택하라는 메시지가 표시되면 **Kotlin** 을 선택합니다.

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

*build.gradle.kts* 를 찾고, 원하는 IDE 또는 텍스트 편집기에서 엽니다. 그런 다음, 다음 빌드 구성을 복사합니다. 이 구성은 프로젝트를 Java 애플리케이션(진입점이 **CustomVisionQuickstart** 클래스임)으로 정의합니다. Custom Vision 라이브러리를 가져옵니다.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "CustomVisionQuickstart"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-training", version = "1.1.0-preview.2")
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-prediction", version = "1.1.0-preview.2")
}
```

### <a name="create-a-java-file"></a>Java 파일 만들기


작업 디렉터리에서 다음 명령을 실행하여 프로젝트 원본 폴더를 만듭니다.

```console
mkdir -p src/main/java
```

새 폴더로 이동하여 *CustomVisionQuickstart.java* 라는 파일을 만듭니다. 원하는 편집기 또는 IDE에서 이 파일을 열고, 다음 `import` 문을 추가합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_imports)]

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.


애플리케이션의 **CustomVisionQuickstart** 클래스에서 리소스의 키 및 엔드포인트에 대한 변수를 만듭니다.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_creds)]


> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 [제품 이름] 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 키 및 엔드포인트를 찾을 수 있습니다. 
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 자세한 내용은 Cognitive Services [보안](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) 문서를 참조하세요.

애플리케이션의 **main** 메서드에서 이 빠른 시작에서 사용되는 메서드에 대한 호출을 추가합니다. 나중에 이를 정의합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_maincalls_od)]

## <a name="object-model"></a>개체 모델

다음 클래스 및 인터페이스는 Custom Vision Java 클라이언트 라이브러리의 몇 가지 주요 기능을 처리합니다.

|Name|설명|
|---|---|
|[CustomVisionTrainingClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-java-stable) | 이 클래스는 모델의 생성, 학습 및 게시를 처리합니다. |
|[CustomVisionPredictionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-java-stable)| 이 클래스는 개체 검색 예측에 대한 모델의 쿼리를 처리합니다.|
|[ImagePrediction](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction?view=azure-java-stable)| 이 클래스는 단일 이미지에서 단일 개체 예측을 정의합니다. 여기에는 개체 ID 및 이름, 개체의 경계 상자 위치 및 신뢰도 점수에 대한 속성이 포함됩니다.|

## <a name="code-examples"></a>코드 예제

이러한 코드 조각에서는 Java용 Custom Vision 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [새 Custom Vision 프로젝트 만들기](#create-a-new-custom-vision-project)
* [프로젝트에 태그 추가](#add-tags-to-the-project)
* [이미지 업로드 및 태그 지정](#upload-and-tag-images)
* [프로젝트 학습](#train-the-project)
* [현재 반복 게시](#publish-the-current-iteration)
* [예측 엔드포인트 테스트](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>클라이언트 인증

**main** 메서드에서 엔드포인트와 키를 사용하여 학습 및 예측 클라이언트를 인스턴스화합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>새 Custom Vision 프로젝트 만들기

다음 메서드는 개체 검색 프로젝트를 만듭니다. 생성된 프로젝트는 앞에서 방문한 [Custom Vision 웹 사이트](https://customvision.ai/)에 표시됩니다. 프로젝트를 만들 때 다른 옵션을 지정하려면 [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true) 메서드 오버로드를 참조하세요([탐지기 빌드](../../get-started-build-detector.md) 웹 포털 가이드에 설명되어 있음).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

## <a name="add-tags-to-your-project"></a>프로젝트에 태그 추가

다음 메서드는 모델을 학습시키는 태그를 정의합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

## <a name="upload-and-tag-images"></a>이미지 업로드 및 태그 지정

먼저 이 프로젝트에 대한 샘플 이미지를 다운로드합니다. [샘플 이미지 폴더](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images)의 내용을 로컬 디바이스에 저장합니다.

개체 검색 프로젝트의 이미지에 태그를 지정할 때 정규화된 좌표를 사용하여 태그가 지정된 각 개체의 지역을 지정해야 합니다. 다음 코드는 각 샘플 이미지를 태그가 지정된 지역과 연결합니다.

> [!NOTE]
> 영역의 좌표를 표시하는 클릭 및 끌기 유틸리티를 사용하지 않는 경우 [Customvision.ai](https://www.customvision.ai/)에서 웹 UI를 사용할 수 있습니다. 이 예제에서는 좌표가 이미 제공되어 있습니다.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

다음 코드 블록은 프로젝트에 이미지를 추가합니다. 다운로드한 **fork** 및 **scissors** 폴더의 위치를 가리키도록 `GetImage` 호출의 변수를 변경해야 합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

이전 코드 조각은 이미지를 리소스 스트림으로 검색하여 서비스에 업로드하는 두 개의 도우미 함수를 사용합니다(단일 일괄 처리에서 최대 64개의 이미지를 업로드할 수 있음). 이러한 메서드를 정의합니다. 

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project"></a>프로젝트 학습

다음 메서드는 프로젝트의 첫 번째 학습 반복을 만듭니다. 학습이 완료될 때까지 서비스를 쿼리합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]


## <a name="publish-the-current-iteration"></a>현재 반복 게시

다음 메서드는 모델의 현재 반복을 쿼리에 사용할 수 있도록 합니다. 모델 이름을 참조로 사용하여 예측 요청을 보낼 수 있습니다. `predictionResourceId`에 대한 고유한 값을 입력해야 합니다. Azure Portal에 있는 리소스의 **개요** 탭에서 **구독 ID** 로 나열된 예측 리소스 ID를 찾을 수 있습니다.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_publishOD)]

## <a name="test-the-prediction-endpoint"></a>예측 엔드포인트 테스트

이 메서드는 테스트 이미지를 로드하고, 모델 엔드포인트를 쿼리하고, 예측 데이터를 콘솔에 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict_od)]

## <a name="run-the-application"></a>애플리케이션 실행

다음을 사용하여 앱을 빌드할 수 있습니다.

```console
gradle build
```

`gradle run` 명령을 사용하여 애플리케이션을 실행합니다.

```console
gradle run
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>다음 단계

이제 코드에서 개체 감지 프로세스의 모든 단계를 완료했습니다. 이 샘플은 학습을 한 번만 반복하지만, 정확도를 높이기 위해 모델을 여러 차례 학습하고 테스트해야 하는 경우가 많습니다. 다음 가이드에서는 이미지 분류를 다루지만, 원칙은 개체 검색과 비슷합니다.

> [!div class="nextstepaction"]
> [모델 테스트 및 재교육](../../test-your-model.md)

* Custom Vision이란?
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)에서 확인할 수 있습니다.
