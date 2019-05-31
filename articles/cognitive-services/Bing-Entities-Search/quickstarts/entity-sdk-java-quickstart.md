---
title: '빠른 시작: Java용 Bing Entity Search SDK를 사용하여 엔터티 검색'
titlesuffix: Azure Cognitive Services
description: Java용 Bing Entity Search SDK를 사용하여 엔터티를 검색하려면 이 빠른 시작을 사용하세요.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 15958033ef305a44f9c5254409fa111acd9d1eb5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65813712"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>빠른 시작: Java용 Bing Entity Search SDK를 사용하여 검색 요청 보내기

Java용 Bing Entity Search SDK를 사용하여 엔터티 검색을 시작하려면 이 빠른 시작을 사용하세요. Bing Entity Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있는 반면, SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/)

* Java용 Bing Entity Search SDK

Maven, Gradle 또는 기타 종속성 관리 시스템을 사용하여 Bing Entity Search SDK 종속성을 설치합니다. Maven POM 파일에는 다음 선언이 필요합니다.

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>프로젝트 만들기 및 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 Java 프로젝트를 만들고 다음 라이브러리를 가져옵니다.

    ```java
    import com.microsoft.azure.cognitiveservices.entitysearch.*;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.EntitySearchAPIImpl;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.SearchResponseInner;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List;
    ```

2. 구독 키에 대한 변수 만들기

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>검색 클라이언트 만들기

1. `dominantEntityLookup` 클라이언트를 구현합니다. 그러려면 API 엔드포인트와 `ServiceClientCredentials` 클래스의 인스턴스가 필요합니다.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    `ServiceClientCredentials`를 구현하려면 다음 단계를 수행합니다.

   1. `OkHttpClient.Builder` 개체를 매개 변수로 사용하여 `applyCredentialsFilter()` 함수를 재정의합니다. 
        
       ```java
       //...
       new ServiceClientCredentials() {
               @Override
               public void applyCredentialsFilter(OkHttpClient.Builder builder) {
               //...
               }
       //...
       ```
    
   2. `applyCredentialsFilter()` 내에서 `builder.addNetworkInterceptor()`를 호출합니다. 새 `Interceptor` 개체를 만들고, `Chain` 인터셉터 개체를 사용하도록 `intercept()` 메서드를 재정의합니다.

       ```java
       //...
       builder.addNetworkInterceptor(
           new Interceptor() {
               @Override
               public Response intercept(Chain chain) throws IOException {
               //...    
               }
           });
       ///...
       ```

   3. `intercept` 함수 내에서 요청에 대한 변수를 만듭니다. `Request.Builder()`를 사용하여 요청을 빌드합니다. `Ocp-Apim-Subscription-Key` 헤더에 구독 키를 추가하고, 요청 개체에서 `chain.proceed()`를 반환합니다.
            
       ```java
       //...
       public Response intercept(Chain chain) throws IOException {
           Request request = null;
           Request original = chain.request();
           Request.Builder requestBuilder = original.newBuilder()
                   .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
           request = requestBuilder.build();
           return chain.proceed(request);
       }
       //...
       ```
      ## <a name="send-a-request-and-receive-a-response"></a>요청을 보내고 응답 수신

1. 구독 키를 사용하여 검색 클라이언트의 새 인스턴스를 만듭니다. `client.entities().search()`를 사용하여 검색 쿼리 `satya nadella`에 대한 검색 요청을 보내고, 응답을 받습니다. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. 엔터티가 반환되면 목록으로 변환합니다. 이 절차를 반복하고, 기준 엔터티를 인쇄합니다.

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entrys = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entrys) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 빌드](../tutorial-bing-entities-search-single-page-app.md)

* [Bing Entity Search API란?](../overview.md )
