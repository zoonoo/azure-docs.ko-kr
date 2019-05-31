---
title: 만들기, 게시, 답변
titleSuffix: QnA Maker - Azure Cognitive Services
description: 이 REST 기반 자습서에서는 프로그래매틱 방식으로 기술 자료를 생성 및 게시한 후 기술 자료의 질문에 답변하는 방법을 안내합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: a14bc14e6115c1bc25582c3de71382ae17d7debd
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792291"
---
# <a name="tutorial-using-c-create-knowledge-base-then-answer-question"></a>자습서: C#을 사용하여 기술 자료를 만든 후 질문에 답변하기

이 자습서에서는 프로그래매틱 방식으로 기술 자료(KB)를 생성 및 게시한 후 기술 자료의 고객 질문에 답변하는 방법을 안내합니다. 

> [!div class="checklist"]
> * 기술 자료 만들기 
> * 만들기 상태 확인
> * 기술 자료 학습 및 게시
> * 엔드포인트 정보 가져오기
> * Curl을 사용하여 기술 자료 쿼리


이 빠른 시작에서 호출하는 QnA Maker API는 다음과 같습니다.

* [기술 자료(kb) 만들기](https://go.microsoft.com/fwlink/?linkid=2092179)
* [작업 세부 정보 가져오기](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)
* [기술 자료 세부 정보 가져오기](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/getdetails) 
* [기술 자료 엔드포인트 가져오기](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/getkeys)
* [게시](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) 

## <a name="prerequisites"></a>필수 조건

* 최신 [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키를 검색하려면 대시보드의 **리소스 관리**에서 **키**를 선택합니다. 

> [!NOTE] 
> 전체 솔루션 파일은 [**Azure-Samples/cognitive-services-qnamaker-csharp** GitHub 리포지토리](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base)에서 사용할 수 있습니다.

## <a name="create-a-knowledge-base-project"></a>기술 자료 프로젝트 만들기

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>필수 종속성 추가

Program.cs의 맨 위에서 단일 _using_ 문을 다음 줄로 바꾸어 프로젝트에 필요한 종속성을 추가합니다.

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-a-kbdetails-class"></a>KBDetails 클래스 추가
Namespace 대괄호 안에 이 KBDetails 클래스를 추가합니다. 이 클래스는 NewtonSoft 라이브러리가 JSON 응답을 C# 클래스로 역직렬화하게 해줍니다.

[!code-csharp[Add a KBDetails class](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=15-26 "Add a KBDetails class")]

## <a name="add-the-required-constants"></a>필요한 상수 추가

프로그램 클래스의 맨 위에서 QnA Maker에 액세스하기 위한 다음 상수를 추가합니다.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=30-57 "Add the required constants")]

## <a name="add-the-kb-definition"></a>KB 정의 추가

다음 KB 모델 정의를 상수 뒤에 추가합니다.

[!code-csharp[Add the KB definition](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=59-85 "Add the KB definition")]

## <a name="add-supporting-functions-and-structures"></a>지원하는 함수 및 구조 추가
프로그램 클래스 안에 다음 코드 블록을 추가합니다.

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=87-123 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>KB를 만들기 위한 POST 요청 추가

다음 코드에서는 KB를 만들도록 QnA Maker API에 HTTPS 요청을 하고 응답을 받습니다.

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=124-141 "Add a POST request to create KB")]

다음 API 호출은 작업 ID를 포함한 JSON 응답을 반환합니다. 나중에 이 프로그램은 작업 ID를 사용하여 KB가 성공적으로 만들어졌는지 결정합니다. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>만들기 상태를 확인하기 위한 GET 요청 추가

생성 작업의 상태를 확인합니다.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=142-151 "Add GET request to determine creation status")]

다음 API 호출은 작업 상태를 포함한 JSON 응답을 반환합니다. 

```JSON
{
  "operationState": "Running",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
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
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>CreateKB 메서드 추가

다음 메서드는 KB를 만들고 상태를 확인하는 호출을 캡슐화합니다.  POST 응답 헤더 필드 중 **Location**에 _create_ **Operation ID**가 반환되며, GET 요청시 경로의 일부에 사용됩니다. KB를 만드는 데 시간이 걸릴 수 있으므로 상태가 성공 또는 실패 할 때까지, 상태 확인을 위해 호출을 반복해야 합니다. 작업이 성공하면 KB ID가 **resourceLocation**으로 반환됩니다. 

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=152-227 "Add GET request to determine creation status")]

## <a name="add-publish-method"></a>게시 방법 추가

기술 자료가 성공적으로 만들어지면 KB를 게시하세요. 학습 API에 대한 호출을 예상했을 수 있습니다. 하지만 이 버전에는 필요 없습니다. 

다음 코드에서는 KB를 게시하도록 QnA Maker API에 HTTPS 요청을 하고 응답을 받습니다.

[!code-csharp[Add publish method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=228-259 "Add publish method")]

API 호출은 성공적인 게시에 대해 응답 본문에 내용이 없는 204 상태를 반환합니다. 빠른 시작 코드는 204 응답에 대한 텍스트를 추가하므로 결과를 볼 수 있습니다.

다른 응답의 경우 해당 응답은 변경되지 않은 채 반환됩니다.

## <a name="generating-an-answer"></a>답변 생성
이 프로그램이 KB에 액세스하여 질문을 전송하고 최적의 답변을 받으려면 KB 세부 정보 API의 _엔드포인트 호스트_와 Endpoints API의 _기본 엔드포인트 키_가 필요합니다. 이러한 메서드는 다음 섹션에 답변 생성 메서드와 함께 제공됩니다. 

다음 표에서는 데이터를 사용하여 URI를 생성하는 방법을 보여줍니다.

|답변 URI 템플릿 생성|
|--|
|https://**HOSTNAME**.azurewebsites.net/qnamaker/knowledgebases/**KBID**/generateAnswer|

_기본 엔드포인트_는 답변 생성 요청을 인증하는 헤더로 전달됩니다.

|헤더 이름|헤더 값|
|--|--|
|권한 부여|`Endpoint` + **기본 엔드포인트**<br>예제: `Endpoint xxxxxxx`<br>`Endpoint` 텍스트와 기본 엔드포인트 값 사이의 공백에 주목하세요. 

요청 본문은 적절한 JSON을 전달해야 합니다.

```JSON
{
    question: "What languages does QnA Maker support?"
}
```

## <a name="get-kb-details"></a>KB 세부 정보 가져오기
KB 세부 정보를 가져오려면 다음 메서드를 추가합니다. 이러한 세부 정보에는 KB의 호스트 이름이 포함됩니다. 호스트 이름은 QnA Maker 리소스를 만들 때 입력한 QnA Maker Azure 웹 서비스의 이름입니다. 

[!code-csharp[Get KB Details](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=260-273 "Add publish method")]

이 API 호출은 JSON 응답을 반환합니다. 

```JSON
{
  "id": "ZZZ31e19-cba7-48d1-8594-5c4297ecc9c1",
  "hostName": "https://qnamaker-s0-s.azurewebsites.net",
  "lastAccessedTimestamp": "2018-10-11T18:10:05Z",
  "lastChangedTimestamp": "2018-10-11T18:09:37Z",
  "lastPublishedTimestamp": "2018-10-11T18:10:15Z",
  "name": "QnA Maker FAQ from quickstart",
  "userId": "AAAc3841df0b42cdb00f53a49d51a89c",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

## <a name="get-kb-endpoints"></a>KB 엔드포인트 가져오기
QnA Maker의 기본 엔드포인트를 가져오려면 다음 메서드를 추가합니다. 이러한 엔드포인트는 KB에 연결되지 않으며, Azure Portal의 QnA Maker 리소스 키와 연결된 모든 KB에 유효합니다.  

[!code-csharp[Get KB Endpoints](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=274-289 "Get KB Endpoints")]

이 API 호출은 JSON 응답을 반환합니다. 

```JSON
{
  "primaryEndpointKey": "AAAb5719-e2f7-4a33-937d-7a3b4736a1be",
  "secondaryEndpointKey": "BBBcba78-c1d2-4166-b98f-c77255aefaba",
  "installedVersion": "4.2.0",
  "lastStableVersion": "4.2.0"
}
```

## <a name="get-an-answer"></a>답변 가져오기
사용자의 질문에 대한 답변을 가져오려면 다음 메서드를 추가합니다. 

[!code-csharp[Get Answer](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=290-315 "Get Answer")]

이 API 호출은 JSON 응답을 반환합니다. 

```JSON
{
  "answers": [
    {
      "questions": [
        "Does QnA Maker support non-English languages?"
      ],
      "answer": "See more details about [supported languages](https://docs.microsoft.com/azure/cognitive-services/qnamaker/overview/languages-supported).\n\n\nIf you have content from multiple languages, be sure to create a separate service for each language.",
      "score": 82.19,
      "id": 11,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```

## <a name="main-method"></a>기본 메서드
기본 메서드는 답변을 만들고, 게시하고, 생성하는 동기 호출을 보여줍니다. 

[!code-csharp[Main method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=316-337 "Main method")]

## <a name="build-and-run-the-program"></a>프로그램 빌드 및 실행

프로그램을 빌드하고 실행합니다. 

기술 자료가 생성되면 QnA Maker 포털의 [내 기술 자료](https://www.qnamaker.ai/Home/MyServices) 페이지에서 볼 수 있습니다. 답변 생성 API를 사용하는 방법을 알고 나면 모든 언어나 HTTP 요청 프레임워크에서 해당 API를 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://go.microsoft.com/fwlink/?linkid=2092179)
