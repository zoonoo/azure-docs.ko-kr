---
title: '빠른 시작: Java용 Bing Video Search SDK를 사용하여 비디오 검색'
titleSuffix: Azure Cognitive Services
description: Java용 Bing Video Search SDK로 비디오 검색 요청을 보내려면 이 빠른 시작을 사용합니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: 95112a4161c6a0c21b7e4a834658e5e7f130a8c3
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568007"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-java"></a>빠른 시작: Java용 Bing Video Search SDK를 사용하여 비디오 검색 수행

Java용 Bing Video Search SDK를 통해 뉴스 검색을 시작하려면 이 빠른 시작을 사용합니다. Bing Video Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있지만 SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 자세한 주석 및 Bing Video Search 기능이 포함된 [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* [Gson 라이브러리](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

Maven, Gradle 또는 기타 종속성 관리 시스템을 사용하여 Bing Video Search SDK 종속성을 설치합니다. Maven POM 파일에는 다음 선언이 필요합니다.

```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-videosearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies> 
```

## <a name="create-and-initalize-a-project"></a>프로젝트 만들기 및 초기화


즐겨 찾는 IDE 또는 편집기에서 새 Java 프로젝트를 만들고 다음 라이브러리를 가져옵니다.

    ```java
    import com.microsoft.azure.cognitiveservices.videosearch.*;
    import com.microsoft.azure.cognitiveservices.videosearch.VideoObject;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List; 
    ```

## <a name="create-a-search-client"></a>검색 클라이언트 만들기

2. `VideoSearchAPIImpl` 클라이언트를 구현합니다. 그러려면 API 엔드포인트와 `ServiceClientCredentials` 클래스의 인스턴스가 필요합니다.

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
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

## <a name="send-a-search-request-and-recieve-the-response"></a>검색 요청 전송 및 응답 수신 

1. 문자열로 구독 키를 사용하는 `VideoSearch()`라는 함수를 만듭니다. 이전에 만든 검색 클라이언트를 인스턴스화합니다.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. `VideoSearch()` 내에서 `SwiftKey`를 검색어로 하는 클라이언트를 사용하여 비디오 검색 요청을 보냅니다. Video Search API가 결과를 반환한 경우 첫 번째 결과를 가져와 해당 ID, 이름 및 URL을 반환된 비디오의 총 수와 함께 출력합니다. 
    
    ```java
    VideosInner videoResults = client.searchs().list("SwiftKey");

    if (videoResults == null){
        System.out.println("Didn't see any video result data..");
    }
    else{
        if (videoResults.value().size() > 0){
            VideoObject firstVideoResult = videoResults.value().get(0);

            System.out.println(String.format("Video result count: %d", videoResults.value().size()));
            System.out.println(String.format("First video id: %s", firstVideoResult.videoId()));
            System.out.println(String.format("First video name: %s", firstVideoResult.name()));
            System.out.println(String.format("First video url: %s", firstVideoResult.contentUrl()));
        }
        else{
            System.out.println("Couldn't find video results!");
        }
    }
    ```

3. 기본 메서드에서 검색 메서드를 호출합니다.

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>참고 항목 

* [Bing Video Search API란?](../overview.md)
* [Cognitive Services .NET SDK 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)