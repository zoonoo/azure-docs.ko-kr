---
title: 문서 번역 시작
description: 'C #, Go, Java, Node.js 또는 Python 프로그래밍 언어 및 플랫폼을 사용 하 여 문서 번역 서비스를 만드는 방법'
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 886889ef9a42e358fca22a9d86955a23c5419dfa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738160"
---
# <a name="get-started-with-document-translation-preview"></a>문서 번역 시작 (미리 보기)

 이 문서에서는 HTTP REST API 메서드를 사용 하 여 문서 번역을 사용 하는 방법을 배웁니다. 문서 변환은 [Azure Translator](../translator-info-overview.md) 서비스의 클라우드 기반 기능입니다.  문서 변환 API를 사용 하면 소스 문서 구조와 텍스트 서식을 유지 하면서 전체 문서를 변환할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

시작 하려면 다음이 필요 합니다.

* 활성 [**Azure 계정**](https://azure.microsoft.com/free/cognitive-services/).  계정이 없는 경우 [**무료 계정을 만들**](https://azure.microsoft.com/free/)수 있습니다.

* Cognitive Services 리소스가 **아니라** [**변환기**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) 서비스 리소스입니다. 

* [**Azure blob storage 계정**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Azure Storage에 대한 모든 액세스는 스토리지 계정을 통해 수행됩니다.

* Azure 구독이 새 문서 번역 기능을 사용할 수 있도록 하는 완성 된 [**문서 번역 (미리 보기) 양식**](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-riVR3Xj0tOnIRdZOALbM9UOEE4UVdFQVBRQVBWWDBRQUM3WjYxUEpUTC4u) 입니다.

> [!NOTE]
> 문서 변환은 현재 Cognitive Services (다중 서비스) 리소스가 **아니라** 변환기 (단일 서비스) 리소스 에서만 지원 됩니다.

## <a name="get-your-custom-domain-name-and-subscription-key"></a>사용자 지정 도메인 이름 및 구독 키 가져오기

> [!IMPORTANT]
>
> * 문서 변환에 대 한 HTTP 요청을 수행 하려면 Azure Portal 리소스 _키와 끝점_ 페이지 또는 전역 변환기 끝점 ()에 있는 끝점을 사용할 수 없습니다 `api.cognitive.microsofttranslator.com` .
> * **문서 변환 서비스에 대 한 모든 API 요청에는 사용자 지정 도메인 끝점이 필요** 합니다.

### <a name="what-is-the-custom-domain-endpoint"></a>사용자 지정 도메인 끝점은 무엇 인가요? 

사용자 지정 도메인 끝점은 리소스 이름, 호스트 이름 및 번역기 하위 디렉터리로 형식이 지정 된 URL입니다.

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1
```

### <a name="find-your-custom-domain-name"></a>사용자 지정 도메인 이름 찾기

*사용자 지정 도메인 이름 (사용자 지정 도메인 이름* 이 라고도 함) 매개 **변수는** 사용자가 번역기 리소스를 만들 때 **이름** 필드에 입력 한 값입니다.

:::image type="content" source="../media/instance-details.png" alt-text="Azure Portal, 리소스 만들기, 인스턴트 세부 정보, 이름 필드의 이미지입니다.":::

### <a name="get-your-subscription-key"></a>구독 키 가져오기

Translator 서비스에 대 한 요청에는 액세스 인증을 위한 읽기 전용 키가 필요 합니다.

1. 새 리소스를 만든 경우 배포한 후 **리소스로 이동** 을 선택 합니다. 기존 문서 번역 리소스가 있는 경우 리소스 페이지로 직접 이동 합니다.
1. 왼쪽 레일의 *리소스 관리* 에서 **키 및 끝점** 을 선택 합니다.
1. *Microsoft 메모장과* 같은 편리한 위치에 구독 키를 복사 하 여 붙여 넣습니다.
1. 문서 변환 서비스에 대 한 요청을 인증 하는 아래 코드에 붙여 넣습니다.

:::image type="content" source="../media/translator-keys.png" alt-text="Azure Portal에서 구독 키 가져오기 필드의 이미지입니다.":::

## <a name="create-your-azure-blob-storage-containers"></a>Azure blob storage 컨테이너 만들기

원본, 대상 및 선택적 용어집 파일에 대해 [**Azure blob storage 계정**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) 에 [**컨테이너를 만들어야**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) 합니다.

* **원본 컨테이너**. 이 컨테이너는 번역을 위해 파일을 업로드 하는 위치입니다 (필수).
* **대상 컨테이너** 입니다. 이 컨테이너는 번역 된 파일이 저장 되는 위치입니다 (필수).  
* **용어집 컨테이너**. 이 컨테이너는 용어집 파일 (옵션)을 업로드 합니다.  

**문서 변환에 대 한 SAS 액세스 토큰 만들기를** *참조 하세요* .

`sourceUrl`, `targetUrl` 및 옵션은 `glossaryUrl` 쿼리 문자열로 추가 된 SAS (공유 액세스 서명) 토큰을 포함 해야 합니다. 토큰을 컨테이너 또는 특정 blob에 할당할 수 있습니다.

* **원본** 컨테이너 또는 blob에 **읽기** 및 **목록** 액세스를 지정 해야 합니다.
* **대상** 컨테이너 또는 blob에는 지정 된 **쓰기** 및 **목록** 액세스 권한이 있어야 합니다.
* **용어집** 컨테이너 또는 blob에서 **읽기** 및 **목록** 액세스를 지정 해야 합니다.

> [!TIP]
>
> * 작업에서 **여러** 파일 (blob)을 변환 하는 경우 **컨테이너 수준에서 SAS 액세스를 위임** 합니다.  
> * 작업에서 **단일** 파일 (blob)을 변환 하는 경우 **BLOB 수준에서 SAS 액세스를 위임** 합니다.  
>

## <a name="set-up-your-coding-platform"></a>코딩 플랫폼 설정

### <a name="c"></a>[C#](#tab/csharp)

* 새 프로젝트를 만듭니다.
* Program.cs를 아래에 표시된 C# 코드로 바꿉니다.
* 끝점을 설정 합니다. Program.cs의 구독 키 및 컨테이너 URL 값입니다.
* JSON 데이터를 처리 하려면 [ .NET CLI를 사용 하 여 패키지에Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json/)를 추가 합니다.
* 프로젝트 디렉터리에서 프로그램을 실행 합니다.

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

* 새 Node.js 프로젝트를 만듭니다.
* 를 사용 하 여 Axios 라이브러리를 설치 `npm i axios` 합니다.
* 아래 코드를 복사 하 여 프로젝트에 붙여넣습니다.
* 끝점, 구독 키 및 컨테이너 URL 값을 설정 합니다.
* 프로그램을 실행합니다.

### <a name="python"></a>[Python](#tab/python)  

* 새 프로젝트를 만듭니다.
* 샘플 중 하나에서 코드를 복사 하 여 프로젝트에 붙여넣습니다.
* 끝점, 구독 키 및 컨테이너 URL 값을 설정 합니다.
* 프로그램을 실행합니다. 예: `python translate.py`

### <a name="java"></a>[Java](#tab/java)

* 프로젝트에 대한 작업 디렉터리를 만듭니다. 다음은 그 예입니다. 

```powershell
mkdir sample-project
```

* 프로젝트 디렉터리에서 다음 하위 디렉터리 구조를 만듭니다.  

  src</br>
&emsp; └ 주</br>
&emsp;&emsp;&emsp;└ java

```powershell
mkdir -p src/main/java/
```

**참고**: java 원본 파일 (예: _java_)은 src/main/**Java** 에서 라이브입니다.

* 루트 디렉터리 (예: *샘플 프로젝트*)에서 Gradle를 사용 하 여 프로젝트를 초기화 합니다.

```powershell
gradle init --type basic
```

* **DSL** 을 선택하라는 메시지가 표시되면 **Kotlin** 을 선택합니다.

* 파일을 업데이트 `build.gradle.kts`  합니다. 샘플에 따라를 업데이트 해야 한다는 점에 유의 하세요 `mainClassName` .

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* **Java** 디렉터리에서 java 파일을 만들고 제공 된 샘플에서 코드를 복사 하 여 붙여넣습니다. 구독 키와 끝점을 추가 하는 것을 잊지 마세요.

* **루트 디렉터리에서 샘플을 빌드하고 실행 합니다**.

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Go](#tab/go)  

* 새 Go 프로젝트를 만듭니다.
* 아래 제공된 코드를 추가합니다.
* 끝점, 구독 키 및 컨테이너 URL 값을 설정 합니다.
* '.go' 확장명으로 파일을 저장합니다.
* Go가 설치된 컴퓨터에서 명령 프롬프트를 엽니다.
* 파일을 빌드합니다(예: 'go build example-code.go').
* 파일을 실행합니다(예: 'example-code').

 ---

## <a name="make-document-translation-requests"></a>문서 번역 요청 만들기

Batch 문서 번역 요청은 POST 요청을 통해 Translator 서비스 끝점으로 전송 됩니다. 성공 하면 POST 메서드에서 응답 코드를 반환 `202 Accepted`  하 고 서비스에서 일괄 처리 요청을 만듭니다.

### <a name="http-headers"></a>HTTP 헤더

각 문서 변환기 API 요청에는 다음과 같은 헤더가 포함 됩니다.

|HTTP 헤더|Description|
|---|--|
|Ocp-Apim-Subscription-Key|**필수**: 값은 Translator 또는 Cognitive Services 리소스에 대 한 Azure 구독 키입니다.|
|콘텐츠 형식|**필수**: 페이로드의 콘텐츠 형식을 지정 합니다. 허용 되는 값은 application/json 또는 charset = u t f-8입니다.|
|Content-Length|**필수**: 요청 본문의 길이입니다.|

### <a name="post-request-body-properties"></a>POST 요청 본문 속성

* POST 요청 본문은 라는 JSON 개체입니다 `inputs` .
* 개체에는 `inputs`  `sourceURL` `targetURL`  소스 및 대상 언어 쌍에 대 한 및 컨테이너 주소가 모두 포함 되며 선택적으로 컨테이너 주소를 포함할 수 있습니다 `glossaryURL` .
* `prefix`및 `suffix` 필드 (선택 사항)는 폴더를 포함 하 여 컨테이너에서 문서를 필터링 하는 데 사용 됩니다.
* 필드에 대 한 값  `glossaries`  (선택 사항)은 문서를 번역할 때 적용 됩니다.
* `targetUrl`각 대상 언어에 대 한은 고유 해야 합니다.

>[!NOTE]
> 이름이 같은 파일이 대상에 이미 있으면 해당 파일을 덮어씁니다.

## <a name="post-a-translation-request"></a>번역 요청 게시

<!-- markdownlint-disable MD024 -->
### <a name="post-request-body-without-optional-glossaryurl"></a>선택적 glossaryURL 없는 POST 요청 본문

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
                "storageSource": "AzureBlob",
                "filter": {
                    "prefix": "News",
                    "suffix": ".txt"
                },
                "language": "en"
            },
            "targets": [
                {
                    "targetUrl": "<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "de"
                }
            ]
        }
    ]
}
```

### <a name="post-request-body-with-optional-glossaryurl"></a>선택적 glossaryURL를 포함 하는 POST 요청 본문

```json
{
  "inputs":[
    {
      "source":{
        "sourceUrl":"<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
        "storageSource":"AzureBlob",
        "filter":{
          "prefix":"News",
          "suffix":".txt"
        },
        "language":"en"
      },
      "targets":[
        {
          "targetUrl":"<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
          "storageSource":"AzureBlob",
          "category":"general",
          "language":"de",
          "glossaries":[
            {
              "glossaryUrl":"<https://YOUR-GLOSSARY-URL-WITH-READ-LIST-ACCESS-SAS>",
              "format":"xliff",
              "version":"1.2"
            }
          ]
        }
      ]
    }
  ]
}
```

> [!IMPORTANT]
>
> 아래 코드 예제에서는 작업에 따라 다음 필드를 업데이트 해야 할 수 있습니다.
>>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id`  (작업 ID)
>>
> 값을 찾을 수 있는 위치 `id` :
> * `id`POST 메서드의 응답 헤더 URL 값에서 작업을 찾을 수 있습니다 `Operation-Location` . URL의 마지막 매개 변수는 작업의 작업 **`id`** 입니다.  
> * 작업 가져오기 요청을 사용 하 여 `id`  문서 변환 작업에 대 한 작업을 검색할 수도 있습니다.
>
> 아래 코드 샘플의 경우 키와 끝점이 표시 되 면 하드 코드 합니다. 작업이 완료 되 면 코드에서 키를 제거 하 고 공개적으로 게시 하지 마십시오.  
>
> 자격 증명을 안전 하 게 저장 하 고 액세스 하는 방법은 [Azure Cognitive Services 보안](/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp) 을 참조 하세요.

## <a name="_post-document-translation_-request"></a>_문서 변환 후_ 요청

번역 서비스에 batch 문서 번역 요청을 제출 합니다.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;
    

    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");
        
        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
            
                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;
                
                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:"); 
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="_get-file-formats_"></a>_파일 형식 가져오기_ 

지원 되는 파일 형식 목록을 검색 합니다. 성공 하면이 메서드는 `200 OK` 응답 코드를 반환 합니다.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-job-status_"></a>_작업 상태 가져오기_ 

단일 작업의 현재 상태와 문서 번역 요청에서 모든 작업의 요약을 가져옵니다. 성공 하면이 메서드는 `200 OK` 응답 코드를 반환 합니다.
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-document-status_"></a>_문서 상태 가져오기_

### <a name="brief-overview"></a>간략 한 개요

문서 번역 요청에서 특정 문서의 상태를 검색 합니다. 성공 하면이 메서드는 `200 OK` 응답 코드를 반환 합니다.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_delete-job_"></a>_작업 삭제_ 

### <a name="brief-overview"></a>간략 한 개요

현재 처리 중이거나 대기 중인 작업을 취소 합니다. 번역이 시작 되지 않은 문서만 취소 됩니다.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>콘텐츠 제한

다음 표에서는 문서 번역에 보내는 데이터의 제한을 나열 합니다.

|attribute | 제한|
|---|---|
|문서 크기| ≤ 40 |
|총 파일 수입니다.|≤ 1000 |
|일괄 처리의 총 콘텐츠 크기 | ≤ 250|
|일괄 처리의 대상 언어 수| ≤ 10 |
|변환 메모리 파일의 크기| ≤ 10MB|

> [!NOTE]
> 위의 콘텐츠 제한은 공개 릴리스 이전에 변경 될 수 있습니다.

## <a name="learn-more"></a>자세한 정보

* [Translator v3 API 참조](../reference/v3-0-reference.md)
* [언어 지원](../language-support.md)
* [Azure API Management의 구독](../../../api-management/api-management-subscriptions.md)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 번역기를 사용 하 여 사용자 지정 된 언어 시스템 만들기](../custom-translator/overview.md)
>
>