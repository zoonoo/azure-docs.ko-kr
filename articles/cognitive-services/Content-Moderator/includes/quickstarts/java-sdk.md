---
title: Content Moderator Java 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Java용 Azure Content Moderator 클라이언트 라이브러리를 시작하는 방법에 대해 알아봅니다. 앱에 콘텐츠 필터링 소프트웨어를 빌드하여 규정을 준수하거나 사용자를 위한 의도된 환경을 유지 관리합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 6f5d1fd8a179f88677ddd6d7b1875f60836ade51
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92918753"
---
Java용 Azure Content Moderator 클라이언트 라이브러리를 시작합니다. 다음 단계에 따라 Maven 패키지를 설치하고 기본 작업에 대한 예제 코드를 사용해 보세요. 

Content Moderator는 공격을 받을 수 있거나 위험한 또는 바람직하지 않은 콘텐츠를 처리할 수 있는 AI 서비스입니다. AI 지원 콘텐츠 조정 서비스를 사용하여 텍스트, 이미지 및 비디오를 검색하고 콘텐츠 플래그를 자동으로 적용합니다. 앱에 콘텐츠 필터링 소프트웨어를 빌드하여 규정을 준수하거나 사용자를 위한 의도된 환경을 유지 관리합니다.

Java용 Content Moderator 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 이미지 조정
* 텍스트 조정

[참조 설명서](/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator) |[아티팩트(Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [샘플](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)의 현재 버전
* [Gradle 빌드 도구](https://gradle.org/install/) 또는 다른 종속성 관리자
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Content Moderator 리소스 만들기"  target="_blank">Content Moderator 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Content Moderator에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
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

## <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

*build.gradle.kts* 를 찾고, 원하는 IDE 또는 텍스트 편집기에서 엽니다. 그런 다음, 다음 빌드 구성을 복사합니다. 이 구성은 프로젝트를 Java 애플리케이션(진입점이 **ContentModeratorQuickstart** 클래스임)으로 정의합니다. JSON 직렬화를 위한 GSON SDK와 Content Moderator 클라이언트 라이브러리를 가져옵니다.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

### <a name="create-a-java-file"></a>Java 파일 만들기


작업 디렉터리에서 다음 명령을 실행하여 프로젝트 원본 폴더를 만듭니다.

```console
mkdir -p src/main/java
```

새 폴더로 이동하여 *ContentModeratorQuickstart.java* 라는 파일을 만듭니다. 원하는 편집기 또는 IDE에서 이 파일을 열고, 다음 `import` 문을 추가합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

애플리케이션의 **ContentModeratorQuickstart** 클래스에서 리소스의 키 및 엔드포인트에 대한 변수를 만듭니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 [제품 이름] 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 키 및 엔드포인트를 찾을 수 있습니다. 
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 자세한 내용은 Cognitive Services [보안](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) 문서를 참조하세요.

애플리케이션의 **main** 메서드에서 이 빠른 시작에서 사용되는 메서드에 대한 호출을 추가합니다. 나중에 이를 정의합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>개체 모델

Content Moderator Java 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스는 다음과 같습니다.

|이름|설명|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|이 클래스는 모든 Content Moderator 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다.|
|[ImageModeration](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|이 클래스는 성인 콘텐츠, 개인 정보 또는 사람 얼굴에 대한 이미지를 분석하는 기능을 제공합니다.|
|[TextModerations](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|이 클래스는 언어, 욕설, 오류 및 개인 정보에 대한 텍스트를 분석하는 기능을 제공합니다.|
|[검토](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|이 클래스는 작업, 사용자 지정 워크플로 및 사용자 검토를 만드는 메서드를 포함하여 검토 API의 기능을 제공합니다.|


## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Java용 Content Moderator 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [이미지 조정](#moderate-images)
* [텍스트 조정](#moderate-text)

## <a name="authenticate-the-client"></a>클라이언트 인증

애플리케이션의 `main` 메서드에서 구독 엔드포인트 값 및 구독 키를 사용하여 [ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) 개체를 만듭니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>이미지 조정

### <a name="set-up-sample-image"></a>샘플 이미지 설정

새 메서드에서 이미지를 가리키는 지정된 URL 문자열을 사용하여 **[BodyModelModel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel?view=azure-java-stable)** 개체를 생성합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>도우미 클래스 정의

다음으로, *ContentModeratorQuickstart.java* 파일에서 다음 클래스 정의를 **ContentModeratorQuickstart** 클래스 내에 추가합니다. 이 내부 클래스는 이미지 조정 프로세스에서 사용됩니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


### <a name="analyze-content"></a>콘텐츠 분석
이 코드 줄은 지정된 URL의 이미지에서 성인 또는 외설 콘텐츠를 확인합니다. 이러한 용어에 대한 자세한 내용은 이미지 조정 개념 가이드를 참조하세요.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>텍스트 확인
이 코드 줄은 이미지에서 표시되는 텍스트를 확인합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>얼굴 확인
이 코드 줄은 이미지에서 사람의 얼굴을 확인합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

마지막으로, 반환된 정보를 `EvaluationData` 목록에 저장합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>결과 출력

다음 코드를 `while` 루프 뒤에 추가합니다. 이 코드는 결과를 콘솔 및 *src/main/resources/ModerationOutput.json* 출력 파일에 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

`try` 문을 닫고 메서드를 완료하는 `catch` 문을 추가합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="moderate-text"></a>텍스트 조정

### <a name="set-up-sample-text"></a>샘플 텍스트 설정

**ContentModeratorQuickstart** 클래스 상단에 있는 로컬 텍스트 파일에 대한 참조를 정의합니다. 프로젝트 디렉터리에 .txt 파일을 추가하고 분석할 텍스트를 입력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>텍스트 분석

.txt 파일을 읽고 각 줄에 있는 **screenText** 메서드를 호출하는 새 메서드를 만듭니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>텍스트 조정 결과 출력

다음 코드를 추가하여 프로젝트 디렉터리의 .json 파일에 중재 결과를 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

메서드를 완료하려면 `try` 및 `catch` 문을 닫습니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


## <a name="run-the-application"></a>애플리케이션 실행

다음을 사용하여 앱을 빌드할 수 있습니다.

```console
gradle build
```

`gradle run` 명령을 사용하여 애플리케이션을 실행합니다.

```console
gradle run
```

그런 다음, *src/main/resources/ModerationOutput.json* 파일로 이동하여 콘텐츠 조정의 결과를 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Content Moderator Java 라이브러리를 사용하여 조정 작업을 수행하는 방법을 알아보았습니다. 다음으로, 개념 가이드를 참조하여 이미지 또는 다른 미디어의 조정에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [이미지 조정 개념](../../image-moderation-api.md)

* [Azure Content Moderator란?](../../overview.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java)에서 확인할 수 있습니다.