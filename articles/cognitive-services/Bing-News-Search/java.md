---
title: '빠른 시작: C#을 사용하여 웹 검색 수행 - Bing Web Search REST API'
titlesuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Java를 통해 Bing News Search REST API로 요청을 보내고 JSON 응답을 받습니다.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 1/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: f8a98133d68cb73958664dd04bb2d959c97195cf
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263852"
---
# <a name="quickstart-perform-a-news-search-using-java-and-the-bing-news-search-rest-api"></a>빠른 시작: Java 및 Bing News Search REST API를 사용하여 뉴스 검색 수행

이 빠른 시작을 사용하여 Bing News Search API를 처음 호출하고 JSON 응답을 봅니다. 이 간단한 Java 애플리케이션은 뉴스 검색 쿼리를 API에 보내고, 응답을 표시합니다.

이 애플리케이션은 Java에서 작성되지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

이 샘플의 소스 코드는 [GitHub에](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingNewsSearchv7.java) 제공됩니다. 

## <a name="prerequisites"></a>필수 조건

* [JDK(Java Development Kit) 7 또는 8](https://aka.ms/azure-jdks)

* [Gson 라이브러리](https://github.com/google/gson)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

[Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요.

## <a name="create-and-initialize-a-project"></a>프로젝트 만들기 및 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 Java 프로젝트를 만들고 다음 라이브러리를 가져옵니다.

    ```java
    import java.net.*;
    import java.util.*;
    import java.io.*;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. API 엔드포인트, 구독 키 및 검색 용어에 대한 변수를 사용하여 새 클래스를 만듭니다.

    ```java
    public static SearchResults SearchNews (String searchQuery) throws Exception {
        static String subscriptionKey = "enter key here";
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/news/search";
        static String searchTerm = "Microsoft";
    //...
    }
    ```

## <a name="construct-the-search-request-and-recieve-a-json-response"></a>검색 요청 생성 및 JSON 응답 수신

1. 마지막 단계에서 변수를 사용하여 API 요청에 대한 검색 URL의 형식을 지정합니다. 검색 용어는 요청에 추가되기 전에 URL로 인코딩되어야 합니다.

    ```java
    public static SearchResults SearchNews (String searchQuery) throws Exception {
        // construct the search request URL (in the form of URL + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    }
    ```

2. Bing News Search API에서 JSON 응답을 수신하고, 결과 개체를 생성합니다.

    ```java
    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();
    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
    ```

## <a name="process-the-json-response"></a>JSON 응답 처리

1. JSON 본문에서 Bing 관련 HTTP 헤더를 구분한 다음, 스트림을 닫고 API 응답을 반환합니다.
    ```java
    // extract Bing-related HTTP headers
    Map<String, List<String>> headers = connection.getHeaderFields();
    for (String header : headers.keySet()) {
        if (header == null) continue;      // may have null key
        if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
            results.relevantHeaders.put(header, headers.get(header).get(0));
        }
    }
    stream.close();
    return results;
    ```

2. 메서드를 만들어 JSON 구문 분석 및 다시 직렬화
    ```java
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

3. 애플리케이션의 main 메서드에서 검색 메서드를 호출하고, 결과를 표시합니다.
    ```csharp
   public static void main (String[] args) {
       System.out.println("Searching the Web for: " + searchTerm);
       SearchResults result = SearchNews(searchTerm);
    
       System.out.println("\nRelevant HTTP Headers:\n");
       for (String header : result.relevantHeaders.keySet())
             System.out.println(header + ": " + result.relevantHeaders.get(header));  
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    }
    ```

## <a name="json-response"></a>JSON 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
{
   "_type": "News",
   "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft",
   "totalEstimatedMatches": 36,
   "sort": [
      {
         "name": "Best match",
         "id": "relevance",
         "isSelected": true,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft"
      },
      {
         "name": "Most recent",
         "id": "date",
         "isSelected": false,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft&sortby=date"
      }
   ],
   "value": [
      {
         "name": "Microsoft to open flagship London brick-and-mortar retail store",
         "url": "http:\/\/www.contoso.com\/article\/microsoft-to-open-flagshi...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.F9E4A49EC010417...",
               "width": 220,
               "height": 146
            }
         },
         "description": "After years of rumors about Microsoft opening a brick-and-mortar...", 
         "about": [
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entiti...", 
             "name": "Microsoft"
           }, 
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entit...", 
             "name": "London"
           }
         ], 
         "provider": [
           {
             "_type": "Organization", 
             "name": "Contoso"
           }
         ], 
          "datePublished": "2017-09-21T21:16:00.0000000Z", 
          "category": "ScienceAndTechnology"
      }, 

      . . .
      
      {
         "name": "Microsoft adds Availability Zones to its Azure cloud platform",
         "url": "https:\/\/contoso.com\/2017\/09\/21\/microsoft-adds-availability...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.0AE7595B9720...",
               "width": 700,
               "height": 466
            }
         },
         "description": "Microsoft has begun adding Availability Zones to its...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/a093e9b...",
               "name": "Microsoft"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/cf3abf7d-e379-...",
               "name": "Windows Azure"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/9cdd061c-1fae-d0...",
               "name": "Cloud"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Contoso"
            }
         ],
         "datePublished": "2017-09-21T09:01:00.0000000Z",
         "category": "ScienceAndTechnology"
      }
   ]
}
```


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](tutorial-bing-news-search-single-page-app.md)