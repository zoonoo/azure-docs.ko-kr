---
title: '빠른 시작: 뉴스 검색 수행 - Java용 Bing News Search SDK'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Java용 Bing News Search SDK를 통해 뉴스를 검색하고 응답을 처리합니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 49356d79ce9f8c4efdd27dc946f7373c3efe59cd
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264566"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>빠른 시작: Java용 Bing News Search SDK로 뉴스 검색

Java용 Bing News Search SDK를 사용하여 뉴스 검색을 시작하려면 이 빠른 시작을 사용합니다. Bing News Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있는 반면, SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Maven, Gradle 또는 기타 종속성 관리 시스템을 사용하여 Bing News Search SDK 종속성을 설치합니다. Maven POM 파일에는 다음 선언이 필요합니다.

```xml
    <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
    </dependencies>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>프로젝트 만들기 및 초기화

즐겨 찾는 IDE 또는 편집기에서 새 Java 프로젝트를 만들고 다음 라이브러리를 가져옵니다.

```java
import com.microsoft.azure.cognitiveservices.newssearch.*;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsInner;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsSearchAPIImpl;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.TrendingTopicsInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import java.io.IOException;
```

## <a name="create-a-search-client-and-store-credentials"></a>검색 클라이언트 및 저장소 자격 증명 만들기

1. 새 `NewsSearchAPIImpl` 검색 클라이언트를 반환하는 `getClient()`라는 메서드를 만듭니다. 새 `NewsSearchAPIImpl` 개체 및 새 `ServiceClientCredentials` 개체에 대해 첫 번째 매개 변수로 엔드포인트를 추가하여 자격 증명을 저장합니다.

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. `ServiceClientCredentials` 개체를 만들려면 `applyCredentialsFilter()` 함수를 재정의합니다. 메서드에 `OkHttpClient.Builder`를 전달하고, 작성기의 `addNetworkInterceptor()` 메서드를 사용하여 SDK 호출에 대한 자격 증명을 만듭니다.

    ```java
    new ServiceClientCredentials() {
        @Override
        public void applyCredentialsFilter(OkHttpClient.Builder builder) {
            builder.addNetworkInterceptor(
                    new Interceptor() {
                        @Override
                        public Response intercept(Chain chain) throws IOException {
                            Request request = null;
                            Request original = chain.request();
                            // Request customization: add request headers.
                            Request.Builder requestBuilder = original.newBuilder()
                                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                            request = requestBuilder.build();
                            return chain.proceed(request);
                        }
                    });
        }
    });
    ```

## <a name="send-and-receive-a-search-request"></a>검색 요청 보내고 받기

1. `getClient()`를 호출하는 메서드를 만들고 Bing News Search 서비스에 검색 요청을 보냅니다. *market* 및 *count* 매개 변수로 검색을 필터링한 다음, 첫 번째 뉴스 결과의 이름, URL, 게시 날짜, 설명, 공급 기업 이름 및 검색에 대한 예상되는 일치 항목의 총 수를 비롯한 정보를 출력합니다.

    ```java
    public static void newsSearch(String subscriptionKey)
    {
        NewsSearchAPIImpl client = getClient(subscriptionKey);
        String searchTerm = "Quantum Computing";
    
        NewsInner newsResults = client.searchs().list(searchTerm, null, null, null,
                null, null, 100, null, "en-us",
                null, null, null, null, null,
                null, null);
    
        if (newsResults.value().size() > 0)
        {
            NewsArticle firstNewsResult = newsResults.value().get(0);
    
            System.out.println(String.format("TotalEstimatedMatches value: %d", newsResults.totalEstimatedMatches()));
            System.out.println(String.format("News result count: %d", newsResults.value().size()));
            System.out.println(String.format("First news name: %s", firstNewsResult.name()));
            System.out.println(String.format("First news url: %s", firstNewsResult.url()));
            System.out.println(String.format("First news description: %s", firstNewsResult.description()));
            System.out.println(String.format("First news published time: %s", firstNewsResult.datePublished()));
            System.out.println(String.format("First news provider: %s", firstNewsResult.provider().get(0).name()));
        }
        else
        {
            System.out.println("Couldn't find news results!");
        }
    
    }
    
    ```

2. `main()` 메서드에 검색 메서드를 추가하여 코드를 실행합니다.

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
[단일 페이지 웹앱 만들기](tutorial-bing-news-search-single-page-app.md)
