---
title: '빠른 시작: Java용 Bing Web Search SDK 사용'
titleSuffix: Azure Cognitive Services
description: Bing Web Search SDK를 사용하면 Bing Web Search를 Java 애플리케이션에 쉽게 통합할 수 있습니다. 이 빠른 시작에서는 요청을 보내고, JSON 응답을 받고, 결과를 필터링 및 구문 분석하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 7209df902f03a7055e142dcbbb7743b6832958b6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863783"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-java"></a>빠른 시작: Java용 Bing Web Search SDK 사용

Bing Web Search SDK를 사용하면 Bing Web Search를 Java 애플리케이션에 쉽게 통합할 수 있습니다. 이 빠른 시작에서는 요청을 보내고, JSON 응답을 받고, 결과를 필터링 및 구문 분석하는 방법에 대해 알아봅니다.

지금 코드를 보시겠나요? [Java용 Bing Web Search SDK 샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/)은 GitHub에서 사용할 수 있습니다.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

[Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 실행하기 전에 필요한 몇 가지 조건은 다음과 같습니다.

* [JDK 7 또는 8](https://aka.ms/azure-jdks)
* [Apache Maven](https://maven.apache.org/download.cgi) 또는 즐겨찾는 빌드 자동화 도구
* 구독 키

## <a name="create-a-project-and-set-up-your-pom-file"></a>프로젝트 만들기 및 POM 파일 설정

Maven 또는 즐겨찾는 빌드 자동화 도구를 사용하여 새 Java 프로젝트를 만듭니다. Maven을 사용한다고 가정하는 경우 [POM(Project Object Model)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) 파일에 다음 줄을 추가합니다. `mainClass`의 모든 인스턴스를 애플리케이션으로 바꿉니다.

```xml
<build>
    <plugins>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.4.0</version>
        <configuration>
          <!--Your comment
            Replace the mainClass with the path to your java application.
            It should begin with com and doesn't require the .java extension.
            For example: com.bingwebsearch.app.BingWebSearchSample. This maps to
            The following directory structure:
            src/main/java/com/bingwebsearch/app/BingWebSearchSample.java.
          -->
          <mainClass>com.path.to.your.app.APP_NAME</mainClass>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.0</version>
        <configuration>
          <source>1.7</source>
          <target>1.7</target>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>attached</goal>
            </goals>
            <configuration>
              <descriptorRefs>
                <descriptorRef>jar-with-dependencies</descriptorRef>
              </descriptorRefs>
              <archive>
                <manifest>
                  <!--Your comment
                    Replace the mainClass with the path to your java application.
                    For example: com.bingwebsearch.app.BingWebSearchSample.java.
                    This maps to the following directory structure:
                    src/main/java/com/bingwebsearch/app/BingWebSearchSample.java.
                  -->
                  <mainClass>com.path.to.your.app.APP_NAME.java</mainClass>
                </manifest>
              </archive>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.9.0</version>
    </dependency>
    <dependency>
      <groupId>commons-net</groupId>
      <artifactId>commons-net</artifactId>
      <version>3.3</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-websearch</artifactId>
      <version>1.0.1</version>
    </dependency>
  </dependencies>
```

## <a name="declare-dependencies"></a>종속성 선언

즐겨찾는 IDE 또는 편집기에서 프로젝트를 열고, 다음과 같은 종속성을 가져옵니다.

```java
import com.microsoft.azure.cognitiveservices.search.websearch.BingWebSearchAPI;
import com.microsoft.azure.cognitiveservices.search.websearch.BingWebSearchManager;
import com.microsoft.azure.cognitiveservices.search.websearch.models.ImageObject;
import com.microsoft.azure.cognitiveservices.search.websearch.models.NewsArticle;
import com.microsoft.azure.cognitiveservices.search.websearch.models.SearchResponse;
import com.microsoft.azure.cognitiveservices.search.websearch.models.VideoObject;
import com.microsoft.azure.cognitiveservices.search.websearch.models.WebPage;
```

Maven을 사용하여 프로젝트를 만든 경우 패키지가 이미 선언되어 있습니다. 그렇지 않은 경우 지금 패키지를 선언합니다. 예를 들면 다음과 같습니다.

```java
package com.bingwebsearch.app
```

## <a name="declare-the-bingwebsearchsample-class"></a>BingWebSearchSample 클래스 선언

`BingWebSearchSample` 클래스를 선언합니다. `main` 메서드를 포함한 대부분의 코드가 포함됩니다.  

```java
public class BingWebSearchSample {

// The code in the following sections goes here.

}
```

## <a name="construct-a-request"></a>요청 구성

`BingWebSearchSample` 클래스에 있는 `runSample` 메서드는 요청을 구성합니다. 이 코드를 애플리케이션에 복사합니다.

```java
public static boolean runSample(BingWebSearchAPI client) {
    /*
     * This function performs the search.
     *
     * @param client instance of the Bing Web Search API client
     * @return true if sample runs successfully
     */
    try {
        /*
         * Performs a search based on the .withQuery and prints the name and
         * url for the first web pages, image, news, and video result
         * included in the response.
         */
        System.out.println("Searched Web for \"Xbox\"");
        // Construct the request.
        SearchResponse webData = client.bingWebs().search()
            .withQuery("Xbox")
            .withMarket("en-us")
            .withCount(10)
            .execute();

// Code continues in the next section...
```

## <a name="handle-the-response"></a>응답 처리

다음으로, 응답을 구문 분석하고 결과를 출력하는 코드를 추가해 보겠습니다. 첫 번째 웹 페이지, 이미지, 뉴스 기사 및 비디오에 대한 `name` 및 `url`이 응답 개체에 포함되면 출력됩니다.

```java
/*
* WebPages
* If the search response has web pages, the first result's name
* and url are printed.
*/
if (webData != null && webData.webPages() != null && webData.webPages().value() != null &&
        webData.webPages().value().size() > 0) {
    // find the first web page
    WebPage firstWebPagesResult = webData.webPages().value().get(0);

    if (firstWebPagesResult != null) {
        System.out.println(String.format("Webpage Results#%d", webData.webPages().value().size()));
        System.out.println(String.format("First web page name: %s ", firstWebPagesResult.name()));
        System.out.println(String.format("First web page URL: %s ", firstWebPagesResult.url()));
    } else {
        System.out.println("Couldn't find the first web result!");
    }
} else {
    System.out.println("Didn't find any web pages...");
}
/*
 * Images
 * If the search response has images, the first result's name
 * and url are printed.
 */
if (webData != null && webData.images() != null && webData.images().value() != null &&
        webData.images().value().size() > 0) {
    // find the first image result
    ImageObject firstImageResult = webData.images().value().get(0);

    if (firstImageResult != null) {
        System.out.println(String.format("Image Results#%d", webData.images().value().size()));
        System.out.println(String.format("First Image result name: %s ", firstImageResult.name()));
        System.out.println(String.format("First Image result URL: %s ", firstImageResult.contentUrl()));
    } else {
        System.out.println("Couldn't find the first image result!");
    }
} else {
    System.out.println("Didn't find any images...");
}
/*
 * News
 * If the search response has news articles, the first result's name
 * and url are printed.
 */
if (webData != null && webData.news() != null && webData.news().value() != null &&
        webData.news().value().size() > 0) {
    // find the first news result
    NewsArticle firstNewsResult = webData.news().value().get(0);
    if (firstNewsResult != null) {
        System.out.println(String.format("News Results#%d", webData.news().value().size()));
        System.out.println(String.format("First news result name: %s ", firstNewsResult.name()));
        System.out.println(String.format("First news result URL: %s ", firstNewsResult.url()));
    } else {
        System.out.println("Couldn't find the first news result!");
    }
} else {
    System.out.println("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response has videos, the first result's name
 * and url are printed.
 */
if (webData != null && webData.videos() != null && webData.videos().value() != null &&
        webData.videos().value().size() > 0) {
    // find the first video result
    VideoObject firstVideoResult = webData.videos().value().get(0);

    if (firstVideoResult != null) {
        System.out.println(String.format("Video Results#%s", webData.videos().value().size()));
        System.out.println(String.format("First Video result name: %s ", firstVideoResult.name()));
        System.out.println(String.format("First Video result URL: %s ", firstVideoResult.contentUrl()));
    } else {
        System.out.println("Couldn't find the first video result!");
    }
} else {
    System.out.println("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>main 메서드 선언

이 애플리케이션에서 main 메서드는 클라이언트를 인스턴스화하고, `subscriptionKey`의 유효성을 검사하고, `runSample`를 호출하는 코드를 포함합니다. 계속하기 전에 Azure 계정에 유효한 구독 키를 입력했는지 확인합니다.

```java
public static void main(String[] args) {
    try {
        // Enter a valid subscription key for your account.
        final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
        // Instantiate the client.
        BingWebSearchAPI client = BingWebSearchManager.authenticate(subscriptionKey);
        // Make a call to the Bing Web Search API.
        runSample(client);
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }
}
```

## <a name="run-the-program"></a>프로그램 실행

마지막 단계는 프로그램을 실행하는 것입니다!

```console
mvn compile exec:java
```

## <a name="clean-up-resources"></a>리소스 정리

이 프로젝트가 완료되면 프로그램의 코드에서 구독 키를 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Cognitive Services Java SDK 샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch)

## <a name="see-also"></a>참고 항목

* [Azure Java SDK 참조](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/websearch)