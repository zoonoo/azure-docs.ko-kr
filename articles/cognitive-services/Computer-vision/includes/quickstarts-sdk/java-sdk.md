---
title: '빠른 시작: Java용 광학 인식 클라이언트 라이브러리'
description: 이 빠른 시작에서는 Java용 광학 인식 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 0df856c00a77fa73ec5065f3e62febb55f1423d0
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112362436"
---
<a name="HOLTop"></a>

광학 인식 클라이언트 라이브러리를 사용하여 이미지에서 인쇄 및 필기 텍스트를 읽을 수 있습니다.

[참조 설명서](/java/api/overview/azure/cognitiveservices/client/computervision) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision) |[아티팩트(Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision) | [샘플](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)의 현재 버전
* [Gradle 빌드 도구](https://gradle.org/install/) 또는 다른 종속성 관리자
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 Computer Vision 서비스에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
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

이 빠른 시작에서는 Gradle 종속성 관리자를 사용합니다. 다른 종속성 관리자에 대한 클라이언트 라이브러리 및 정보는 [Maven 중앙 리포지토리](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision)에서 찾을 수 있습니다.

*build.gradle.kts* 를 찾고, 원하는 IDE 또는 텍스트 편집기에서 엽니다. 그런 다음, 다음 빌드 구성을 복사합니다. 이 구성은 프로젝트를 Java 애플리케이션(진입점이 **ComputerVisionQuickstart** 클래스임)으로 정의합니다. Computer Vision 라이브러리를 가져옵니다.

```kotlin
plugins {
    java
    application
}
application { 
    mainClass.set("ComputerVisionQuickstart")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.6-beta")
}
```

### <a name="create-a-java-file"></a>Java 파일 만들기

작업 디렉터리에서 다음 명령을 실행하여 프로젝트 원본 폴더를 만듭니다.

```console
mkdir -p src/main/java
```

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

새 폴더로 이동하여 *ComputerVisionQuickstart.java* 라는 파일을 만듭니다. 선호하는 편집기 또는 IDE에서 엽니다.

### <a name="find-the-subscription-key-and-endpoint"></a>구독 키 및 엔드포인트 찾기

Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Computer Vision 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 구독 키와 엔드포인트를 찾을 수 있습니다. 

**ComputerVisionQuickstart** 클래스를 정의합니다. Computer Vision 구독 키와 엔드포인트에 대한 변수를 만듭니다. 구독 키와 엔드포인트를 표시된 다음 코드에 붙여넣습니다. Computer Vision 엔트포인트의 형식은 `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`입니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> 완료되면 코드에서 구독 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 예를 들어 [Azure Key Vault](../../../../key-vault/general/overview.md)입니다.

애플리케이션의 **main** 메서드에서 이 빠른 시작에서 사용되는 메서드에 대한 호출을 추가합니다. 나중에 이를 정의합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_main)]


> [!div class="nextstepaction"]
> [클라이언트를 설정했습니다.](?success=set-up-client#object-model) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=set-up-client&product=computer-vision&page=java-sdk)

## <a name="object-model"></a>개체 모델

OCR Java SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|Description|
|---|---|
| [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | 이 클래스는 모든 Computer Vision 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다.|

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Java용 OCR 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [인쇄 텍스트 및 필기 텍스트 읽기](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>클라이언트 인증

새 메서드에서 엔드포인트와 키를 사용하여 [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) 개체를 인스턴스화합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> [클라이언트를 인증했습니다.](?success=authenticate-client#read-printed-and-handwritten-text) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=authenticate-client&product=computer-vision&page=java-sdk)



## <a name="read-printed-and-handwritten-text"></a>인쇄 텍스트 및 필기 텍스트 읽기

OCR 서비스는 이미지 속의 시각적 텍스트를 읽고 문자 스트림으로 변환할 수 있습니다. 이 섹션에서는 로컬 파일 경로를 사용하고 이미지의 텍스트를 콘솔에 인쇄하는 `ReadFromFile` 메서드를 정의합니다.

> [!TIP]
> URL에서 참조하는 원격 이미지의 텍스트를 읽을 수도 있습니다. [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision) 메서드(예: **read**)를 참조하세요. 또는 원격 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java)의 샘플 코드를 참조하세요.

### <a name="set-up-test-image"></a>테스트 이미지 설정

**resources/** 폴더를 프로젝트의 **src/main/** 폴더에 만들고, 텍스트를 읽을 이미지를 추가합니다. [샘플 이미지](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg)를 다운로드하여 여기에서 사용할 수 있습니다.

그런 다음, 다음 메서드 정의를 **ComputerVisionQuickstart** 클래스에 추가합니다. 이미지 파일과 일치하도록 `localFilePath`의 값을 변경합니다. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_setup)]

### <a name="call-the-read-api"></a>읽기 API 호출

그런 후, 다음 코드를 추가하여 지정된 이미지에 대해 **readInStreamWithServiceResponseAsync** 메서드를 호출합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

다음 코드 블록은 읽기 호출의 응답에서 작업 ID를 추출합니다. 이 ID를 도우미 메서드와 함께 사용하여 텍스트 읽기 결과를 콘솔에 인쇄합니다. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_response)]

try/catch 블록과 메서드 정의를 닫습니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

### <a name="get-read-results"></a>읽기 결과 가져오기

그런 다음, 도우미 메서드에 대한 정의를 추가합니다. 이 메서드는 이전 단계의 작업 ID를 사용하여 읽기 작업을 쿼리하고 사용할 수 있을 때 OCR 결과를 가져옵니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_call)]

나머지 메서드는 OCR 결과를 구문 분석하여 콘솔에 인쇄합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_print)]

마지막으로, 위에서 사용된 다른 도우미 메서드를 추가합니다. 이 메서드는 초기 응답에서 작업 ID를 추출합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_opid_extract)]

> [!div class="nextstepaction"]
> [텍스트를 읽었습니다.](?success=read-printed-handwritten-text#run-the-application) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=read-printed-handwritten-text&product=computer-vision&page=java-sdk)

## <a name="run-the-application"></a>애플리케이션 실행

다음을 사용하여 앱을 빌드할 수 있습니다.

```console
gradle build
```

`gradle run` 명령을 사용하여 애플리케이션을 실행합니다.

```console
gradle run
```

> [!div class="nextstepaction"]
> [애플리케이션을 실행했습니다.](?success=run-the-application#clean-up-resources) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=run-the-application&product=computer-vision&page=java-sdk)

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [리소스를 정리했습니다.](?success=clean-up-resources#next-steps) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=clean-up-resources&product=computer-vision&page=java-sdk)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 OCR 클라이언트 라이브러리를 설치하고 Read API를 사용하는 방법을 알아보았습니다. 다음으로, Read API 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
>[읽기 API 호출](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR 개요](../../overview-ocr.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java)에서 확인할 수 있습니다.
