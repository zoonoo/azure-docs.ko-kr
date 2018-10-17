---
title: '빠른 시작: Java를 사용하여 엔드포인트 호출 - Bing Custom Search'
titlesuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Java로 Bing Custom Search 엔드포인트를 호출하여 사용자 지정 검색 인스턴스에서 검색 결과를 요청하는 방법을 보여줍니다.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: ba1eb9799bc95dede081c6f1a6d972896c126da6
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814120"
---
# <a name="quickstart-call-bing-custom-search-endpoint-java"></a>빠른 시작: Bing Custom Search 엔드포인트 호출(Java)

이 빠른 시작에서는 Java로 Bing Custom Search 엔드포인트를 호출하여 사용자 지정 검색 인스턴스에서 검색 결과를 요청하는 방법을 보여줍니다. 

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

- 바로 사용할 수 있는 사용자 지정 검색 인스턴스. [처음으로 Bing Custom Search 인스턴스 만들기](quick-start.md)를 참조하세요.
- 설치된 [Java](https://www.java.com).
- 구독 키 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)을 정품 인증하면 구독 키를 받을 수 있습니다. Azure 대시보드에서 유료 구독 키를 사용할 수도 있습니다([Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 참조).    

## <a name="run-the-code"></a>코드 실행

이 예제를 실행하려면 다음 단계를 수행합니다.

1. 원하는 Java IDE를 사용하여 패키지를 만듭니다.  
  
2. 패키지에 CustomSrchJava.java라는 파일을 만들고 다음 코드를 복사합니다. **YOUR-SUBSCRIPTION-KEY** 및 **YOUR-CUSTOM-CONFIG-ID**를 구독 키와 구성 ID로 바꿉니다.  
  
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
    
    public class CustomSrchJava {       
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";  
    
        static String searchTerm = "Microsoft";  // Replace with search term specific to your search scenario.
    
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
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
                System.out.println("Invalid Custom Search subscription key!");
                System.out.println("Please paste yours into the source code.");
                System.exit(1);
            }
    
            try {
                System.out.println("Searching your slice of the Web for: " + searchTerm);
    
                SearchResults result = SearchWeb(searchTerm);
    
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
    
    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    
    }
    ```  
  
4. 프로그램을 실행합니다.
    
## <a name="next-steps"></a>다음 단계
- [호스트된 UI 환경 구성](./hosted-ui.md)
- [장식 표식을 사용하여 텍스트를 강조 표시](./hit-highlighting.md)
- [웹 페이지 페이징](./page-webpages.md)