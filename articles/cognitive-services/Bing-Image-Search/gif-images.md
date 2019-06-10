---
title: Bing Image Search API를 사용하여 GIF 이미지 검색
titleSuffix: Azure Cognitive Services
description: Bing Image Search API를 사용하여 웹에서 .gif 이미지를 검색합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 04/24/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 0a2040424aa70f30831e214ce0b05d21414ff45c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389402"
---
# <a name="search-for-gif-images"></a>GIF 이미지 검색 

Bing Image Search API를 사용하면 관련성이 가장 높은 .gif 이미지에 대해 전체 웹을 검색할 수도 있습니다.  개발자는 다양한 대화 시나리오에 관련된 gif를 통합할 수 있습니다. 

다음 URL은 애니메이션된 .gif 이미지에 대한 쿼리입니다.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
```
[q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) 매개 변수는 검색 단어를 지정합니다.  또한 이전 쿼리는 [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) 필터 매개 변수를 사용하여 `animatedGif`를 지정합니다.

결과의 예를 보려면 다음 URL을 사용하여 bing.com을 검색합니다.
```
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

```
## <a name="query-parameters"></a>쿼리 매개 변수

쿼리 매개 변수 및 옵션에 대한 자세한 내용은 [Image Search API 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query-parameters)를 참조하세요. 예제는 [Java를 사용하여 애니메이션된 gif 예제 검색](#gifExample) 헤더 아래에서 수행됩니다.

## <a name="tips-and-suggestions"></a>팁 및 제안 사항

- [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) 및 [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) 매개 변수를 지정할 수 있습니다. 인덱스에서 대부분의 gif가 2MB 미만이므로 maxFileSize=2000000으로 설정하는 것이 좋습니다.  그러면 대역폭이 모바일 셀룰러 시나리오와 같은 문제가 있는 경우 데이터 크기를 제어할 수 있습니다.
- 인식된 성능을 향상시키려면 원본 URL을 로드하기 전에 먼저 썸네일을 로드합니다.  
- 사용자 쿼리가 아직 없는 첫 번째 실행 또는 방문 페이지 환경의 경우 추세 gif 검색을 사용하여 [추세 이미지 API](trending-images.md)에서 도움을 줍니다.
- [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) 매개 변수에 대한 세 가지 설정이 있습니다.  `strict` 옵션은 성인 등급 콘텐츠를 차단합니다.
- 지원되는 언어 및 위치의 전체 목록은 [mkt](supported-countries-markets.md)를 참조하세요.
- *AnimatedGifHttps*는 https 주소에서 생성되고 애니메이션된 gif 이미지만을 반환합니다. 보안상 대부분의 애플리케이션은 https를 통해 외부 웹 링크에 연결되어야 합니다. 예를 들어 Apple 앱 스토어는 HTTPS를 통해 웹 서비스에 연결되어야 합니다. 그러면 전송 중에 사용자 데이터를 안전하게 암호화합니다.

<a name="gifExample" />

## <a name="example-search-for-animated-gif-using-java"></a>Java를 사용하는 애니메이션된 gif 검색 예제

다음 URL은 애니메이션된 .gif 이미지를 검색합니다. `q=interesting`
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

```
다음 예제에서 볼 수 있듯이 URL 쿼리에는 [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#headers) 헤더가 필요합니다.

다음 Java 예제에서는 요청을 빌드하고 전송합니다.

```
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

        // receive JSON body
        InputStream stream = connection.getInputStream();
        String response = new Scanner(stream).useDelimiter("\\A").next();

        // construct result object for return
        SearchResults results = new SearchResults(new HashMap<String, String>(), response);

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
    }

    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        if (subscriptionKey.length() != 32) {
            System.out.println("Invalid Bing Search API subscription key!");
            System.out.println("Please paste yours into the source code.");
            System.exit(1);
        }

        try {
            System.out.println("Searching the Web for: " + searchTerm);

            SearchResults result = SearchImages(searchTerm);

            System.out.println("\nRelevant HTTP Headers:\n");
            for (String header : result.relevantHeaders.keySet())
                System.out.println(header + ": " + result.relevantHeaders.get(header));

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(result.jsonResponse));
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }

}

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

```

## <a name="results"></a>결과
코드는 다음과 같은 결과를 JSON 개체로 가져옵니다.

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>다음 단계
- [C# 빠른 시작](quickstarts/csharp.md)
- [자습서 Image Search 단일 페이지 애플리케이션](tutorial-bing-image-search-single-page-app.md)
