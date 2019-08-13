---
title: '빠른 시작: Java용 Computer Vision 클라이언트 라이브러리'
titleSuffix: Azure Cognitive Services
description: Java용 Computer Vision 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: 15baf5ee2418581056d571340ba6e8009c33e4ca
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828202"
---
# <a name="quickstart-computer-vision-client-library-for-java"></a>빠른 시작: Java용 Computer Vision 클라이언트 라이브러리

Java용 Computer Vision 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Computer Vision은 이미지를 처리하고 정보를 반환하는 고급 알고리즘에 대한 액세스를 제공합니다.

Java용 Computer Vision 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 태그, 텍스트 설명, 얼굴, 성인 콘텐츠 등에 대한 이미지를 분석합니다.

[참조 설명서](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [아티팩트(Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [샘플](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>필수 조건

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)의 현재 버전
* [Gradle 빌드 도구](https://gradle.org/install/) 또는 다른 종속성 관리자

## <a name="setting-up"></a>설치

### <a name="create-a-computer-vision-azure-resource"></a>Computer Vision Azure 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. 로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 Computer Vision용 리소스를 만듭니다. 또한 다음을 수행할 수 있습니다.

* 7일 동안 유효한 [평가판 키](https://azure.microsoft.com/try/cognitive-services/#decision)를 가져옵니다. 이 키는 가입 후 [Azure 웹 사이트](https://azure.microsoft.com/try/cognitive-services/my-apis/)에서 사용할 수 있습니다.  
* [Azure Portal](https://portal.azure.com/)에서 리소스를 확인합니다.

평가판 구독 또는 리소스에서 키를 가져오면 `COMPUTER_VISION_SUBSCRIPTION_KEY`라는 키에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

### <a name="create-a-new-gradle-project"></a>새 Gradle 프로젝트 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

작업 디렉터리에서 `gradle init` 명령을 실행합니다. 이 명령은 *build.gradle.kts*를 포함하여 런타임에 애플리케이션을 만들고 구성하는 데 사용되는 Gradle용 필수 빌드 파일을 만듭니다.

```console
gradle init --type basic
```

**DSL**을 선택하라는 메시지가 표시되면 **Kotlin**을 선택합니다.

*build.gradle.kts*를 찾고, 원하는 IDE 또는 텍스트 편집기에서 엽니다. 그런 다음, 다음 빌드 구성을 복사합니다. 이 구성은 프로젝트를 Java 애플리케이션(진입점이 **ComputerVisionQuickstarts** 클래스임)으로 정의합니다. Computer Vision 라이브러리를 가져옵니다.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

작업 디렉터리에서 다음 명령을 실행하여 프로젝트 원본 폴더를 만듭니다.

```console
mkdir -p src/main/java
```

새 폴더로 이동하여 *ComputerVisionQuickstarts.java*라는 파일을 만듭니다. 원하는 편집기 또는 IDE에서 이 파일을 열고, 다음 `import` 문을 추가합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_imports)]

그런 다음, **ComputerVisionQuickstarts**에 대한 클래스 정의를 추가합니다.

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

이 빠른 시작에서는 Gradle 종속성 관리자를 사용합니다. 다른 종속성 관리자에 대한 클라이언트 라이브러리 및 정보는 [Maven 중앙 리포지토리](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)에서 찾을 수 있습니다.

프로젝트의 *build.gradle.kts* 파일에서 Computer Vision 클라이언트 라이브러리를 종속성으로 포함시킵니다.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>개체 모델

Computer Vision Java SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|설명|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | 이 클래스는 모든 Computer Vision 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| 이 클래스는 클라이언트 개체에서 제공되며, 이미지 분석, 텍스트 검색 및 썸네일 생성과 같은 모든 이미지 작업을 직접 처리합니다.
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| 이 열거형은 표준 Analyze(분석) 작업에서 수행할 수 있는 다양한 유형의 이미지 분석을 정의합니다. 필요에 따라 VisualFeatureTypes 값 세트를 지정합니다. |

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Java용 Computer Vision 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [이미지 분석](#analyze-an-image)

## <a name="authenticate-the-client"></a>클라이언트 인증

> [!NOTE]
> 이 빠른 시작에서는 `COMPUTER_VISION_SUBSCRIPTION_KEY`라는 Computer Vision 키에 대한 [환경 변수를 만들었다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)고 가정합니다.

다음 코드에서는 `main` 메서드를 클래스에 추가하고, 리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. Azure Portal의 **개요** 섹션을 확인하여 찾을 수 있는 고유한 엔드포인트 문자열을 입력해야 합니다. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_mainvars)]

다음으로, 다음 코드를 추가하여 [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) 개체를 만들고, 나중에 정의하는 다른 메서드에 전달합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> 애플리케이션을 시작한 후에 환경 변수를 만든 경우 해당 변수에 액세스하려면 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어야 합니다.

## <a name="analyze-an-image"></a>이미지 분석

다음 코드에서는 클라이언트 개체를 사용하여 로컬 이미지를 분석하고 결과를 출력하는 `AnalyzeLocalImage` 메서드를 정의합니다. 이 메서드는 텍스트 설명, 분류, 태그 목록, 감지된 얼굴, 성인 콘텐츠 플래그, 기본 색 및 이미지 형식을 반환합니다.

### <a name="set-up-test-image"></a>테스트 이미지 설정

먼저, **resources/** 폴더를 프로젝트의 **src/main/** 폴더에 만들고, 분석하려는 이미지를 추가합니다. 그런 다음, 다음 메서드 정의를 **ComputerVisionQuickstarts** 클래스에 추가합니다. 필요한 경우 이미지 파일과 일치하도록 `pathToLocalImage`의 값을 변경합니다. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

### <a name="specify-visual-features"></a>시각적 기능 지정

다음으로, 분석에서 추출하려는 시각적 기능을 지정합니다. 전체 목록은 [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) 열거형을 참조하세요.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>분석
이 메서드는 각 이미지 분석 범위에 대한 자세한 결과를 콘솔에 출력합니다. 이 메서드 호출은 Try/Catch 블록으로 묶는 것이 좋습니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

### <a name="display-results"></a>결과 표시

위의 메서드 호출에서는 추출된 모든 정보가 포함된 ImageAnalysis 개체를 반환합니다. 다음과 같은 코드 블록을 사용하여 지정된 시각적 기능의 세부 정보를 출력할 수 있습니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_display)]

표시 옵션에 대한 전체 세트는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java)의 샘플 코드를 참조하세요.

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

* [포털](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Computer Vision Java 라이브러리를 사용하여 기본 작업을 수행하는 방법을 알아보았습니다. 다음으로 라이브러리에 대해 자세히 알아보려면 참조 설명서를 살펴보세요.

> [!div class="nextstepaction"]
>[Computer Vision 참조(Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Computer Vision이란?](../Home.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java)에서 확인할 수 있습니다.