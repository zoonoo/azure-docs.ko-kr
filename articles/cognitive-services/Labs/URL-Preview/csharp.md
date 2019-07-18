---
title: '빠른 시작: Project URL Preview, C#'
titlesuffix: Azure Cognitive Services
description: C#을 통해 Project URL Preview를 사용하여 시작합니다.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 2cb3f1de803b8549a88866da1042f4f32609a601
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827158"
---
# <a name="quickstart-url-preview-query-in-c"></a>빠른 시작: C#의 URL 미리 보기 쿼리

다음 C# 예제에서는 SwiftKey 웹 사이트(https://swiftkey.com/en)에 대한 URL 미리 보기를 만듭니다.

## <a name="prerequisites"></a>필수 조건

Windows에서 이 코드를 실행하려면 [Visual Studio 2017 이상](https://www.visualstudio.com/downloads/)이 필요합니다. 체험 Community Edition을 사용해도 됩니다.

[Cognitive Services Labs](https://aka.ms/answersearchsubscription) 평가판에 대한 액세스 키 가져오기

## <a name="code-scenario"></a>코드 시나리오

다음 C# 코드에서는 SwiftKey 웹 사이트(https://swiftkey.com/en)의 URL 미리 보기를 만듭니다. 

다음 단계로 구현됩니다.
1. 미리 보기에 대한 엔드포인트 및 쿼리 URL을 지정하려면 변수를 선언합니다.  
2. 요청 만들기
3. *Ocp-Apim-Subscription-Key* 헤더를 추가합니다. 
4. 웹 요청을 비동기적으로 실행합니다. 
5. 요청을 읽습니다.
6. 헤더 및 JSON 결과를 콘솔에 인쇄합니다.

**소스 코드**

```
using System;
using System.IO;
using System.Net;
using System.Text;

namespace UrlPrevCshp
{
    class Program
    {
        static void Main(string[] args)
        {
            String uriBase = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search";
            String searchQuery = "https://swiftkey.com/en"; 
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

            // Do the Web request and get response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR-SUBSCRIPTION-KEY";
            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            Console.WriteLine("\nHTTP Headers:\n");
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    Console.WriteLine(header + ": " + response.Headers[header]);
            }

            Console.WriteLine(JsonPrettyPrint(json));
            

            Console.ReadKey();
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
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
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
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
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
- [Java 빠른 시작](java-quickstart.md)
- [JavaScript 빠른 시작](javascript.md)
- [Node 빠른 시작](node-quickstart.md)
- [Python 빠른 시작](python-quickstart.md)
