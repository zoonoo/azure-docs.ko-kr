---
title: '빠른 시작: Bing Visual Search REST API 및 Java를 사용하여 이미지 인사이트 가져오기'
titleSuffix: Azure Cognitive Services
description: 이미지를 Bing Visual Search API에 업로드하고 이미지에 대한 인사이트를 가져오는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 148d145dae01fffdf7a4c650a0e2b20f8387295a
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742187"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>빠른 시작: Bing Visual Search REST API 및 Java를 사용하여 이미지 인사이트 가져오기

이 빠른 시작을 사용하여 Bing Visual Search API를 처음 호출하고 검색 결과를 확인합니다. 이 간단한 C# 애플리케이션은 API에 이미지를 업로드하고, 이미지에 대한 반환된 정보를 표시합니다. 이 애플리케이션은 Java에서 작성되지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

로컬 이미지를 업로드하는 경우 양식 데이터에는 Content-Disposition 헤더가 포함되어야 합니다. 해당 `name` 매개 변수를 "image"로 설정해야 하고 `filename` 매개 변수를 임의의 문자열 매개 변수로 설정할 수 있습니다. 양식의 콘텐츠는 이미지의 이진입니다. 업로드할 수는 최대 이미지 크기는 1MB입니다.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>필수 조건

* [JDK(Java Development Kit) 7 또는 8](https://aka.ms/azure-jdks)
* [Gson 라이브러리](https://github.com/google/gson)
* [Apache HttpComponents](http://hc.apache.org/downloads.cgi)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>프로젝트 만들기 및 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 Java 프로젝트를 만들고 다음 라이브러리를 가져옵니다.

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. API 엔드포인트, 구독 키에 대한 변수 및 이미지에 대한 경로를 만듭니다. 

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>JSON 파서 만들기

`JsonParser`를 사용하여 API에서 JSON 응답을 쉽게 읽을 수 있도록 메서드를 만듭니다.

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>검색 요청 및 쿼리 생성

1. 애플리케이션의 기본 메서드에서 `HttpClientBuilder.create().build();`를 사용하여 Http 클라이언트를 만듭니다.

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. API에 이미지를 업로드하는 `HttpEntity`를 만듭니다.

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. 엔드포인트를 사용하여 `httpPost` 개체를 만들고, 구독 키를 사용하도록 헤더를 설정합니다.

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>JSON 응답 수신 및 처리

1. `HttpClient.execute()`를 사용하여 API에 요청을 보내고, `InputStream` 개체에 응답을 저장합니다.
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. JSON 문자열을 저장하고, 응답을 출력합니다.

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Custom Search 웹앱 빌드](../tutorial-bing-visual-search-single-page-app.md)