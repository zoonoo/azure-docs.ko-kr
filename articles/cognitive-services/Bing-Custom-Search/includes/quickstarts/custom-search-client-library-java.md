---
title: Bing Custom Search Java 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: e871edf1f16c1c73a3c3b16649e5aeb622397c8d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87374973"
---
Java용 Bing Custom Search 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Bing Custom Search API를 사용하면 관심 있는 토픽에 대한 맞춤형 광고 없는 검색 경험을 만들 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch)에서 확인할 수 있습니다.

Java용 Bing Custom Search 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* Bing Custom Search 인스턴스에서 웹의 검색 결과를 찾습니다.

[참조 설명서](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [아티팩트(Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)의 현재 버전
* [Gradle 빌드 도구](https://gradle.org/install/) 또는 다른 종속성 관리자
* Bing Custom Search 인스턴스 [빠른 시작: 처음으로 Bing Custom Search 인스턴스 만들기](../../quick-start.md)에서 자세한 내용을 참조하세요.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

리소스에서 키를 가져온 후 `AZURE_BING_CUSTOM_SEARCH_API_KEY`라는 키에 대해 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

### <a name="create-a-new-gradle-project"></a>새 Gradle 프로젝트 만들기

> [!TIP]
> Gradle을 사용하지 않는 경우 다른 종속성 관리자에 대한 클라이언트 라이브러리 세부 정보는 [Maven 중앙 리포지토리](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)에서 찾을 수 있습니다.

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다.

```console
mkdir myapp && cd myapp
```

작업 디렉터리에서 `gradle init` 명령을 실행합니다. 이 명령은 *build.gradle.kts* 파일을 포함하여 런타임에 애플리케이션을 구성하는 데 사용되는 Gradle용 필수 빌드 파일을 만듭니다.

```console
gradle init --type basic
```

**DSL**을 선택하라는 메시지가 표시되면 **Kotlin**을 선택합니다.

## <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

*build.gradle.kts*를 찾고, 원하는 IDE 또는 텍스트 편집기에서 엽니다. 그런 다음, 이 빌드 구성에서 복사합니다. `dependencies` 아래에 클라이언트 라이브러리를 포함해야 합니다.

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

샘플 앱에 대한 폴더를 만듭니다. 작업 디렉터리에서 다음 명령을 실행합니다.

```console
mkdir src/main/java
```

새 폴더로 이동하여 *BingCustomSearchSample.java*라는 파일을 만듭니다. 파일을 열고 다음 `import` 명령문을 추가합니다.


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

`BingCustomSearchSample`이라는 클래스를 만듭니다.

```java
public class BingCustomSearchSample {
}
```

클래스에서 리소스의 키에 대한 `main` 메서드 및 변수를 만듭니다. 애플리케이션을 시작한 후에 환경 변수를 만든 경우 해당 변수에 액세스하려면 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 엽니다. 메서드는 나중에 정의합니다.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>개체 모델

Bing Custom Search 클라이언트는 [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) 개체의 [authenticate()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate) 메서드에서 만든 [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) 개체입니다. 클라이언트의 [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) 메서드를 사용하여 검색 요청을 보낼 수 있습니다.

API 응답은 검색 쿼리에 대한 정보와 검색 결과를 포함하는 [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) 개체입니다.

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Java용 Bing Custom Search 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [사용자 지정 검색 인스턴스에서 검색 결과 가져오기](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>클라이언트 인증

주 메서드는 키를 사용하고, 해당 `authenticate()`를 호출하는 [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) 개체를 포함해야 합니다.

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>사용자 지정 검색 인스턴스에서 검색 결과 가져오기

클라이언트의 [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) 함수를 사용하여 사용자 지정 인스턴스에 검색 쿼리를 보냅니다. `withCustomConfig`를 사용자 지정 구성 ID로 설정하거나 기본값을 `1`로 설정합니다. API에서 응답을 가져온 후 검색 결과가 발견되었는지 확인합니다. 그렇다면 응답의 `webPages().value().get()` 함수를 호출하여 첫 번째 검색 결과를 가져오고 결과의 이름과 URL을 출력합니다.

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>애플리케이션 실행

프로젝트의 주 디렉터리에서 다음 명령을 사용하여 앱을 빌드합니다.

```console
gradle build
```

`run` 목표를 사용하여 애플리케이션을 실행합니다.

```console
gradle run
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Custom Search 웹앱 빌드](../../tutorials/custom-search-web-page.md)
