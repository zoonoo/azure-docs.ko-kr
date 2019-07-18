---
title: '빠른 시작: Project Answer Search, C#'
titlesuffix: Azure Cognitive Services
description: C#을 통해 Project Answer Search를 사용하여 시작하는 코드 샘플입니다.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: dce13636cce299098c988ccea3a059f0f0e12acd
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823269"
---
# <a name="quickstart-project-answer-search-query-in-c"></a>빠른 시작: C#에서 Project Answer Search 쿼리

다음 C# 예제에서는 미적분의 제3 법칙에 대한 정보의 쿼리를 만들고 보냅니다.

## <a name="prerequisites"></a>필수 조건

Windows에서 이 코드를 실행하려면 [Visual Studio 2017 이상](https://www.visualstudio.com/downloads/)이 필요합니다. 체험 Community Edition을 사용해도 됩니다.

[Cognitive Services Labs](https://aka.ms/answersearchsubscription) 평가판에 대한 액세스 키 가져오기

## <a name="code-scenario"></a>코드 시나리오

다음 C# 코드는 쿼리를 만들어 보냅니다. 

다음 단계에서 구현됩니다.
1. 미리 보기에 대한 엔드포인트 및 쿼리 URL을 지정하려면 변수를 선언합니다.  
2. 요청 만들기
3. *Ocp-Apim-Subscription-Key* 헤더를 추가합니다. 
4. 웹 요청을 비동기적으로 실행합니다. 
5. 요청을 읽습니다.
6. 헤더 및 JSON 결과를 콘솔에 인쇄합니다.

**소스 코드**

```
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.IO;

namespace Answers_csharp
{
    class Program
    {
        // Replace the accessKey string value with your valid access key.
        const string accessKey = "YOUR-SUBSCRIPTION-KEY";

        const string uriBase = "https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search"; 

        const string searchTerm = "third law of calculus"; 

        // Used to return news search results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("Searching locally for: " + searchTerm);

            SearchResult result = BingLocalSearch(searchTerm);

            Console.WriteLine("\nRelevant HTTP Headers:\n");
            foreach (var header in result.relevantHeaders)
                Console.WriteLine(header.Key + ": " + header.Value);

            Console.WriteLine("\nJSON Response:\n");
            Console.WriteLine(JsonPrettyPrint(result.jsonResult));

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Does Bing knowledge search and returns the results as a SearchResult.
        /// </summary>
        static SearchResult BingLocalSearch(string searchQuery)
        {
            // Construct the URI of the search request
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + "&mkt=en-us";

            // Send the Web request and get the response.
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            // Create result object for return
            var searchResult = new SearchResult();
            searchResult.jsonResult = json;
            searchResult.relevantHeaders = new Dictionary<String, String>();

            // Extract Bing HTTP headers
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    searchResult.relevantHeaders[header] = response.Headers[header];
            }

            return searchResult;
        }

        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }

    }
}

```
## <a name="running-the-application"></a>애플리케이션 실행

애플리케이션을 실행하려면

1. Visual Studio에서 새 콘솔 솔루션을 만듭니다.
2. `Program.cs`를 제공된 코드로 바꿉니다.
3. `YOUR-ACCESS-KEY` 값을 구독에 대해 유효한 액세스 키로 바꿉니다.
4. 프로그램을 실행합니다.

## <a name="next-steps"></a>다음 단계
[Java 빠른 시작](java-quickstart.md)
