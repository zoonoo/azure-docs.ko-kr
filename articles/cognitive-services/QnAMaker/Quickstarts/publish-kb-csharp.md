---
title: '빠른 시작: 기술 자료 게시 - REST, C# -  QnA Maker'
titleSuffix: Azure Cognitive Services
description: 이 REST 기반 빠른 시작에서는 테스트된 최신 버전의 기술 자료를 게시된 기술 자료를 나타내는 Azure Search 인덱스에 푸시하는 방법(KB 게시)을 안내합니다. 또한 응용 프로그램 또는 챗봇에서 호출할 수 있는 엔드포인트를 만듭니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: ce027abb75423d0174a7175c3bbafe5c0fb3e157
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646265"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>빠른 시작: C#를 사용하여 QnA Maker 기술 자료 게시

이 빠른 시작에서는 KB(기술 자료)를 프로그래밍 방식으로 게시하는 방법을 안내합니다. 게시는 최신 버전의 기술 자료를 전용 Azure Search 인덱스에 푸시하고, 응용 프로그램 또는 챗봇에서 호출할 수 있는 엔드포인트를 만듭니다.

이 빠른 시작에서 호출하는 QnA Maker API는 다음과 같습니다.
* [게시](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) - 이 API는 요청 본문에 어떤 정보도 요구하지 않습니다.

## <a name="prerequisites"></a>필수 조건

* 최신 [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키를 검색하려면 대시보드의 **리소스 관리** 아래에서 **키**를 선택합니다. 
* QnA Maker KB(기술 자료) ID는 아래와 같이 kbid 쿼리 문자열 매개 변수의 URL에 있습니다.

    ![QnA Maker 기술 자료 ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

아직 기술 자료가 없는 경우 샘플을 만들어서 빠른 시작: [새 기술 자료 만들기](create-new-kb-csharp.md)에서 사용하면 됩니다.

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-csharp-repo-note.md)]

## <a name="create-knowledge-base-project"></a>기술 자료 프로젝트 만들기

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-required-dependencies"></a>필요한 종속성 추가

[!INCLUDE [Add required dependencies to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-dependencies.md)] 

## <a name="add-required-constants"></a>필요한 상수 추가

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-constants.md)]  

## <a name="add-knowledge-base-id"></a>기술 자료 ID 추가

[!INCLUDE [Add knowledge base ID as constant](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-kb-id.md)] 

## <a name="add-supporting-functions-and-structures"></a>지원하는 함수 및 구조 추가

Program 클래스 내에 다음 코드 블록을 추가합니다.

```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="add-post-request-to-publish-kb"></a>KB를 게시하기 위한 POST 요청 추가

다음 코드에서는 KB를 게시하도록 QnA Maker API에 HTTPS 요청을 하고 응답을 받습니다.

```csharp
async static void PublishKB()
{
    string responseText;

    var uri = host + service + method + kb;
    Console.WriteLine("Calling " + uri + ".");
    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);

        // successful status doesn't return an JSON so create one
        if (response.IsSuccessStatusCode)
        {
            responseText = "{'result' : 'Success.'}";
        }
        else
        {
            responseText =  await response.Content.ReadAsStringAsync();
        }
    }
    Console.WriteLine(PrettyPrint(responseText));
    Console.WriteLine("Press any key to continue.");
}
```

API 호출은 성공적인 게시에 대해 응답 본문에 내용이 없는 204 상태를 반환합니다. 이 코드는 204 응답에 대한 내용을 추가합니다.

다른 응답의 경우 해당 응답은 변경되지 않은 채 반환됩니다.
 
## <a name="add-the-publishkb-method-to-main"></a>Main에 PublishKB 메서드 추가

CreateKB 메서드를 호출하도록 Main 메서드를 변경합니다.

```csharp
static void Main(string[] args)
{

    // Call the PublishKB() method to publish a knowledge base.
    PublishKB();

    // The console waits for a key to be pressed before closing.
    Console.ReadLine();
}
```

## <a name="build-and-run-the-program"></a>프로그램 빌드 및 실행

프로그램을 빌드하고 실행합니다. 그러면 자동으로 KB를 게시하기 위한 요청을 QnA Maker API에 보낸 다음, 응답이 콘솔 창에 출력됩니다.

기술 자료가 게시되면 클라이언트 응용 프로그램 또는 챗봇을 사용하여 엔드포인트에서 쿼리할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
