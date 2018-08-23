---
title: C#을 사용하여 엔드포인트 호출 - Bing Custom Search - Microsoft Cognitive Services
description: 이 빠른 시작에서는 C#으로 Bing Custom Search 엔드포인트를 호출하여 사용자 지정 검색 인스턴스의 검색 결과를 요청하는 방법을 보여 줍니다.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 87970e1c5e8487f9afca2acc680bdfeb610dc89f
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42093807"
---
# <a name="call-bing-custom-search-endpoint-c"></a>Bing Custom Search 엔드포인트 호출(C#)

이 빠른 시작에서는 C#으로 Bing Custom Search 엔드포인트를 호출하여 사용자 지정 검색 인스턴스의 검색 결과를 요청하는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 조건

-  바로 사용할 수 있는 사용자 지정 검색 인스턴스. [처음으로 Bing Custom Search 인스턴스 만들기](quick-start.md)를 참조하세요.
-  [.Net Core](https://www.microsoft.com/net/download/core)가 설치됨
- [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 및 **Bing Search API**. 이 빠른 시작에는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)이면 충분합니다. 평가판을 활성화할 때 제공된 액세스 키가 필요하며, Azure 대시보드에서 유료 구독 키를 사용해도 됩니다.  

  >[!NOTE]  
  >2017년 10월 15일 또는 그 이전에 프로비전된 미리 보기 키를 보유한 기존 Bing Custom Search 고객은 2017년 11월 30일까지 또는 허용되는 최대 쿼리 수를 모두 사용할 때까지 해당 키를 사용할 수 있습니다. 그 후에는 Azure의 일반 공급 버전으로 마이그레이션해야 합니다. 
 
## <a name="run-the-code"></a>코드 실행

이 예제를 실행하려면 다음 단계를 수행합니다.

1. 코드 폴더를 만듭니다.
2. 명령 프롬프트 또는 터미널에서, 방금 만든 폴더로 이동합니다.
3. 다음 명령을 실행합니다.
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
   ```

4. Program.cs에 다음 코드를 복사합니다.
5. **YOUR-SUBSCRIPTION-KEY** 및 **YOUR-CUSTOM-CONFIG-ID**를 해당 키 및 구성 ID로 바꿉니다.

    ``` CSharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    
    namespace bing_custom_search_example_dotnet
    {
        class Program
        {
            static void Main(string[] args)
            {
                var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
                var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
                var searchTerm = args.Length > 0 ? args[0]: "microsoft";            
    
                var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                    "q=" + searchTerm +
                    "&customconfig=" + customConfigId;
    
                var client = new HttpClient();
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                var httpResponseMessage = client.GetAsync(url).Result;
                var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
                BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
                
                for(int i = 0; i < response.webPages.value.Length; i++)
                {                
                    var webPage = response.webPages.value[i];
                    
                    Console.WriteLine("name: " + webPage.name);
                    Console.WriteLine("url: " + webPage.url);                
                    Console.WriteLine("displayUrl: " + webPage.displayUrl);
                    Console.WriteLine("snippet: " + webPage.snippet);
                    Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
                    Console.WriteLine();
                }            
            }
        }
    
        public class BingCustomSearchResponse
        {        
            public string _type{ get; set; }            
            public WebPages webPages { get; set; }
        }
    
        public class WebPages
        {
            public string webSearchUrl { get; set; }
            public int totalEstimatedMatches { get; set; }
            public WebPage[] value { get; set; }        
        }
    
        public class WebPage
        {
            public string name { get; set; }
            public string url { get; set; }
            public string displayUrl { get; set; }
            public string snippet { get; set; }
            public DateTime dateLastCrawled { get; set; }
            public string cachedPageUrl { get; set; }
            public OpenGraphImage openGraphImage { get; set; }        
        }
        
        public class OpenGraphImage
        {
            public string contentUrl { get; set; }
            public int width { get; set; }
            public int height { get; set; }
        }
    }
    ```
6. 다음 명령을 사용하여 응용 프로그램을 빌드합니다. 명령 출력에서 참조되는 dll 경로를 적어 둡니다.

    <pre>
    dotnet build 
    </pre>
7. 다음 명령을 사용하여 응용 프로그램을 실행합니다. **PATH TO OUTPUT**을 빌드 단계에서 참조되는 경로로 바꿉니다.

    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>다음 단계
- [호스트된 UI 환경 구성](./hosted-ui.md)
- [장식 표식을 사용하여 텍스트를 강조 표시](./hit-highlighting.md)
- [웹 페이지 페이징](./page-webpages.md)
