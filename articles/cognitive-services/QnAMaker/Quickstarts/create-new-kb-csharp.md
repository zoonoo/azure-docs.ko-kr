---
title: '빠른 시작: 기술 자료 만들기 - REST, C# - QnA Maker'
titlesuffix: Azure Cognitive Services
description: 이 REST 기반 빠른 시작에서는 Cognitive Services API 계정의 Azure 대시보드에 표시될 QnA Maker 기술 자료 샘플을 프로그래밍 방식으로 만드는 방법을 안내합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: e1456cb0e7b7662cd460e51af3456fc496502798
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645071"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c"></a>빠른 시작: C#를 사용하여 QnA Maker 기술 자료 만들기

이 빠른 시작에서는 QnA Maker 기술 자료 샘플을 프로그래밍 방식으로 만드는 방법을 안내합니다. QnA Maker는 [데이터 원본](../Concepts/data-sources-supported.md)에서 반구조화된 콘텐츠(예: FAQ)의 질문과 답변을 자동으로 추출합니다. 기술 자료 모델은 API 요청 본문에 전송된 JSON에 정의됩니다. 

이 빠른 시작에서 호출하는 QnA Maker API는 다음과 같습니다.
* [KB 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [작업 세부 정보 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>필수 조건

* 최신 [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키를 검색하려면 대시보드의 **리소스 관리** 아래에서 **키**를 선택합니다. 

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-csharp-repo-note.md)]

## <a name="create-a-knowledge-base-project"></a>기술 자료 프로젝트 만들기

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>필수 종속성 추가

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-dependencies.md)]  

## <a name="add-the-required-constants"></a>필요한 상수 추가

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-constants.md)] 

## <a name="add-the-kb-definition"></a>KB 정의 추가

다음 KB 정의를 상수 뒤에 추가합니다.

```csharp
static string kb = @"
{
  'name': 'QnA Maker FAQ from quickstart',
  'qnaList': [
    {
      'id': 0,
      'answer': 'You can use our REST APIs to manage your knowledge base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa',
      'source': 'Custom Editorial',
      'questions': [
        'How do I programmatically update my knowledge base?'
      ],
      'metadata': [
        {
          'name': 'category',
          'value': 'api'
        }
      ]
    }
  ],
  'urls': [
    'https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs',
    'https://docs.microsoft.com/bot-framework/resources-bot-framework-faq'
  ],
  'files': []
}
";
```

## <a name="add-supporting-functions-and-structures"></a>지원하는 함수 및 구조 추가

[!INCLUDE [Add supporting functions and structures](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-support-functions.md)] 

## <a name="add-a-post-request-to-create-kb"></a>KB를 만들기 위한 POST 요청 추가

다음 코드에서는 KB를 만들도록 QnA Maker API에 HTTPS 요청을 하고 응답을 받습니다.

```csharp
async static Task<Response> PostCreateKB(string kb)
{
    // Builds the HTTP request URI.
    string uri = host + service + method;

    // Writes the HTTP request URI to the console, for display purposes.
    Console.WriteLine("Calling " + uri + ".");

    // Asynchronously invokes the Post(string, string) method, using the
    // HTTP request URI and the specified data source.
    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Content = new StringContent(kb, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

다음 API 호출은 작업 ID를 포함한 JSON 응답을 반환합니다. 작업 ID를 사용하여 KB가 성공적으로 만들어졌는지 결정합니다. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>만들기 상태를 확인하기 위한 GET 요청 추가

작업의 상태를 확인합니다.

```csharp
async static Task<Response> GetStatus(string operationID)
{
    // Builds the HTTP request URI.
    string uri = host + service + operationID;

    // Writes the HTTP request URI to the console, for display purposes.
    Console.WriteLine("Calling " + uri + ".");

    // Asynchronously invokes the Get(string) method, using the
    // HTTP request URI.
    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Get;
        request.RequestUri = new Uri(uri);
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

다음 API 호출은 작업 상태를 포함한 JSON 응답을 반환합니다. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

성공하거나 실패할 때까지 호출을 반복합니다. 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>CreateKB 메서드 추가

다음 메서드는 KB를 만들고 상태 확인을 반복합니다. KB를 만드는 데 시간이 걸릴 수 있으므로 상태가 성공 또는 실패일 때까지 상태를 확인하기 위한 호출을 반복해야 합니다.

```csharp
async static void CreateKB()
{
    try
    {
        // Starts the QnA Maker operation to create the knowledge base.
        var response = await PostCreateKB(kb);

        // Retrieves the operation ID, so the operation's status can be
        // checked periodically.
        var operation = response.headers.GetValues("Location").First();

        // Displays the JSON in the HTTP response returned by the 
        // PostCreateKB(string) method.
        Console.WriteLine(PrettyPrint(response.response));

        // Iteratively gets the state of the operation creating the
        // knowledge base. Once the operation state is set to something other
        // than "Running" or "NotStarted", the loop ends.
        var done = false;
        while (true != done)
        {
            // Gets the status of the operation.
            response = await GetStatus(operation);

            // Displays the JSON in the HTTP response returned by the
            // GetStatus(string) method.
            Console.WriteLine(PrettyPrint(response.response));

            // Deserialize the JSON into key-value pairs, to retrieve the
            // state of the operation.
            var fields = JsonConvert.DeserializeObject<Dictionary<string, string>>(response.response);

            // Gets and checks the state of the operation.
            String state = fields["operationState"];
            if (state.CompareTo("Running") == 0 || state.CompareTo("NotStarted") == 0)
            {
                // QnA Maker is still creating the knowledge base. The thread is 
                // paused for a number of seconds equal to the Retry-After header value,
                // and then the loop continues.
                var wait = response.headers.GetValues("Retry-After").First();
                Console.WriteLine("Waiting " + wait + " seconds...");
                Thread.Sleep(Int32.Parse(wait) * 1000);
            }
            else
            {
                // QnA Maker has completed creating the knowledge base. 
                done = true;
            }
        }
    }
    catch
    {
        // An error occurred while creating the knowledge base. Ensure that
        // you included your QnA Maker subscription key where directed in the sample.
        Console.WriteLine("An error occurred while creating the knowledge base.");
    }
    finally
    {
        Console.WriteLine("Press any key to continue.");
    }

}
``` 

## <a name="add-the-createkb-method-to-main"></a>Main에 CreateKB 메서드 추가

CreateKB 메서드를 호출하도록 Main 메서드를 변경합니다.

```csharp
static void Main(string[] args)
{
    // Call the CreateKB() method to create a knowledge base, periodically 
    // checking the status of the QnA Maker operation until the 
    // knowledge base is created.
    CreateKB();

    // The console waits for a key to be pressed before closing.
    Console.ReadLine();
}
```

## <a name="build-and-run-the-program"></a>프로그램 빌드 및 실행

프로그램을 빌드하고 실행합니다. 그러면 자동으로 KB를 만들기 위한 요청을 QnA Maker API에 보낸 다음, 30초마다 결과를 폴링합니다. 각 응답이 콘솔 창에 출력됩니다.

기술 자료가 생성되면 QnA Maker 포털의 [내 기술 자료](https://www.qnamaker.ai/Home/MyServices) 페이지에서 볼 수 있습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
