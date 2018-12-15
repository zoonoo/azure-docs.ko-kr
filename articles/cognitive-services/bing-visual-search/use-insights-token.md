---
title: 인사이트 토큰 사용 - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: Bing Visual Search API에서 이미지의 인사이트 토큰을 사용하여 이미지에 대한 중요 정보를 얻는 방법을 보여줍니다.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: conceptual
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 76fb61f0f1dd3c1f72c4c6132d1bfdc8b516477a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52955550"
---
# <a name="using-an-insights-token-to-get-insights-about-an-image"></a>인사이트 토큰을 사용하여 이미지에 대한 중요 정보 얻기

Bing Visual Search API는 사용자가 제공하는 이미지에 대한 정보를 반환합니다. 이미지의 URL, 인사이트 토큰을 사용하거나 이미지를 업로드하여 이미지를 제공할 수 있습니다. 이러한 옵션에 대한 내용은 [Bing Visual Search API란?](overview.md)을 참조하세요. 이 문서에서는 인사이트 토큰 사용을 보여 줍니다. 이미지를 업로드하여 중요 정보를 얻는 방법을 보여 주는 예제에 대해서는 빠른 시작([C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [Node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md))을 참조하세요.


Visual Search에 이미지 토큰 또는 URL을 전송하는 경우 다음은 POST 본문에 포함해야 하는 양식 데이터를 보여 줍니다. 양식 데이터에는 Content-Disposition 헤더를 포함해야 하고, 해당 `name` 매개 변수는 "knowledgeRequest"로 설정해야 합니다. `imageInfo` 개체에 대한 자세한 내용은 [요청](#the-request)을 참조하세요.

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

이 문서의 예제에서는 인사이트 토큰을 사용하는 방법을 보여 줍니다. /Images/search API 응답의 Image 개체에서 인사이트 토큰을 가져옵니다. 인사이트 토큰을 가져오는 방법에 대한 내용은 [Bing Image Search API](../Bing-Image-Search/overview.md)를 참조하세요.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFEDF3D40...",
    }
}

--boundary_1234-abcd--
```


인사이트 토큰을 사용하는 예제를 보려면 [C#](#using-csharp) | [Java](#using-java) | [Node.js](#using-nodejs) | [Python](#using-python)을 참조하세요.

<a name="using-csharp" />

## <a name="using-c"></a>C# 사용

### <a name="prerequisites"></a>필수 조건

Windows에서 실행되는 이 코드를 가져오려면 [Visual Studio 2017](https://www.visualstudio.com/downloads/)이 필요합니다. (무료 Community Edition이 작동됩니다.)

이 빠른 시작에서는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) 구독 키 또는 유료 구독 키를 사용할 수 있습니다.

## <a name="running-the-application"></a>응용 프로그램 실행

이 응용 프로그램을 실행하려면 다음 단계를 따릅니다.

1. Visual Studio에서 새 콘솔 솔루션을 만듭니다.
1. `Program.cs`의 내용을 이 빠른 시작에 표시되는 코드로 바꿉니다.
2. `accessKey` 값을 구독 키로 바꿉니다.
2. `insightsToken` 값을 /images/search 응답의 인사이트 토큰으로 바꿉니다.
3. 프로그램을 실행합니다.

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using System.Threading;

namespace VisualSearchInsightsToken
{

    class Program
    {
        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Update with an insights token from an image object that the /images/search API endpoint returns.
        static string insightsToken = @"ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

        static string BoundaryTemplate = "batch_{0}";

        static void Main(string[] args)
        {
            try { 
                Console.WriteLine("Getting image insights using insights token: " + insightsToken);

                var knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";
                var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());

                var json = BingVisualSearch(knowledgeRequest, boundary, uriBase, accessKey);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(json));

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

        }


        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response with insights.
        /// </summary>
        static string BingVisualSearch(string knowledgeRequest, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new StringContent(knowledgeRequest, Encoding.UTF8, "application/json"), "knowledgeRequest");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }


            return json;
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

<a name="using-java" />

## <a name="using-java"></a>Java 사용

### <a name="prerequisites"></a>필수 조건

이 코드를 컴파일하고 실행하려면 [JDK 7 또는 8](https://aka.ms/azure-jdks)이 필요합니다. 즐겨찾기가 있는 경우 Java IDE를 사용할 수 있지만 텍스트 편집기로 충분합니다.

이 빠른 시작에서는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) 구독 키 또는 유료 구독 키를 사용할 수 있습니다.

## <a name="running-the-application"></a>응용 프로그램 실행

이 응용 프로그램을 실행하려면 다음 단계를 따릅니다.

1. [gson 라이브러리](https://github.com/google/gson)를 다운로드하거나 설치합니다. Maven을 통해 얻을 수도 있습니다.
2. 즐겨 찾는 IDE 또는 편집기에서 새 Java 프로젝트를 만듭니다.
3. `VisualSearch.java`라는 파일에 제공되는 코드를 추가합니다.
4. `subscriptionKey` 값을 구독 키로 바꿉니다.
5. 프로그램을 실행합니다.

```java
package insightstoken;

import java.util.*;
import java.io.*;



/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// http://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class InsightsToken {

    
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere>";

    // To get an insights, call the /images/search endpoint. Get the token from
    // the imageInsightsToken field in the Image object.
    static String insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        String knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";

        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addTextBody("knowledgeRequest", knowledgeRequest, ContentType.create("application/json"))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    
}
```


<a name="using-nodejs" />

## <a name="using-nodejs"></a>Node.js 사용

### <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Node.js 6](https://nodejs.org/en/download/)이 필요합니다.

이 빠른 시작에서는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) 구독 키 또는 유료 구독 키를 사용할 수 있습니다.

## <a name="running-the-application"></a>응용 프로그램 실행

이 응용 프로그램을 실행하려면 다음 단계를 따릅니다.

1. 프로젝트에 대한 폴더를 만듭니다(또는 즐겨찾는 IDE 또는 편집기 사용).
2. 명령 프롬프트 또는 터미널에서, 방금 만든 폴더로 이동합니다.
3. 요청 모듈을 설치합니다.  
  ```  
  npm install request  
  ```  
3. 다음과 같이 form-data 모듈을 설치합니다.  
  ```  
  npm install form-data  
  ```  
4. GetVisualInsights.js라는 파일을 만들고 다음 코드를 추가합니다.
5. `subscriptionKey` 값을 구독 키로 바꿉니다.
7. 프로그램을 실행합니다.  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';

// To get an insights, call the /images/search endpoint. Get the token from
// the imageInsightsToken field in the Image object.
var insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

var knowledgeRequest = {
    "imageInfo" : {
        "imageInsightsToken" : insightsToken
    }
};

var form = new FormData();
form.append('knowledgeRequest', JSON.stringify(knowledgeRequest));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


<a name="using-python" />

## <a name="using-python"></a>Python 사용


### <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Python 3](https://www.python.org/)가 필요합니다.

이 빠른 시작에서는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) 구독 키 또는 유료 구독 키를 사용할 수 있습니다.

## <a name="running-the-walkthrough"></a>연습 실행

이 응용 프로그램을 실행하려면 다음 단계를 따릅니다.

1. 즐겨 찾는 IDE 또는 편집기에서 새 Python 프로젝트를 만듭니다.
2. visualsearch.py라는 파일을 만들고, 이 빠른 시작에 표시되는 코드를 추가합니다.
3. `SUBSCRIPTION_KEY` 값을 구독 키로 바꿉니다.
4. 프로그램을 실행합니다.


```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from
# the imageInsightsToken field in the Image object.
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest' : (None, formData)}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>다음 단계

[Bing Visual Search 단일 페이지 앱 자습서](tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual Search 개요](overview.md)  
[사용해보기](https://aka.ms/bingvisualsearchtryforfree)  
[평가판 액세스 키 받기](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Search API 참조](https://aka.ms/bingvisualsearchreferencedoc)
