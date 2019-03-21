---
title: '빠른 시작: Java를 사용하여 Bing Custom Search 엔드포인트 호출 | Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Java로 Bing Custom Search 인스턴스의 검색 결과를 요청할 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: fc1c7d2730d68fb8e3b51a39e0cb89b4098418d6
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337025"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>빠른 시작: Java를 사용하여 Bing Custom Search 엔드포인트 호출

이 빠른 시작을 사용하여 Bing Custom Search 인스턴스의 검색 결과를 요청할 수 있습니다. 이 애플리케이션은 Java로 작성되지만 Bing Custom Search API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java)에 제공됩니다.

## <a name="prerequisites"></a>필수 조건

- Bing Custom Search 인스턴스 [빠른 시작: 처음으로 Bing Custom Search 인스턴스 만들기](quick-start.md)에서 자세한 내용을 참조하세요.

- 최신 [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html)  

- [Gson 라이브러리](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 Java 프로젝트를 만들고 다음 라이브러리를 가져옵니다.

    ```java
    import java.io.InputStream;
    import java.net.URL;
    import java.net.URLEncoder;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    import java.util.Scanner;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. `CustomSrchJava`라는 클래스를 만들고 구독 키, 사용자 지정 검색 엔드포인트 및 검색 인스턴스의 사용자 지정 구성 ID에 대한 변수를 만듭니다. 
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. `SearchResults`라는 또 다른 클래스를 만들고 Bing Custom Search 인스턴스의 응답을 포함합니다.

    ```csharp
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

4. `prettify()`라는 함수를 만들어서 Bing Custom Search API의 JSON 응답 형식을 지정합니다.

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>검색 요청 보내고 받기 

1. 요청을 보내고 `SearchResults` 개체를 반환하는 `SearchWeb()`라는 함수를 만듭니다. 사용자 지정 구성 ID, 쿼리 및 엔드포인트 정보를 결합하여 요청 URL을 만듭니다. `Ocp-Apim-Subscription-Key` 헤더에 구독 키를 추가합니다.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct the URL for your search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ...
    ```

2. 스트림을 만들고 JSON 응답을 `SearchResults` 개체에 저장합니다.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            ...
            // receive the JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();
        
            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);
            
            stream.close();
            return results;
        }
    ```

3. 애플리케이션의 Main 메서드에서 검색 용어를 사용하여 `SearchWeb()`을 호출하고, 

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. 프로그램을 실행합니다.
    
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Custom Search 웹앱 빌드](./tutorials/custom-search-web-page.md)
