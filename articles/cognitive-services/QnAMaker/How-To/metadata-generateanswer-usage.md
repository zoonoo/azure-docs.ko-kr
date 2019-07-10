---
title: GenerateAnswer API와 메타데이터 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker는 메타데이터를 키/값 쌍의 형태로 질문/응답 집합에 추가할 수 있습니다. 사용자 쿼리 결과 필터링 하 고 후속 대화에 사용할 수 있는 추가 정보를 저장할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/27/2019
ms.author: diberry
ms.openlocfilehash: b691d447f51165ea3cb56410da9cd2d4d00ce913
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490195"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>GenerateAnswer API 및 메타 데이터를 사용 하 여 답변을 가져오려면

사용자의 질문에 대 한 예측된 답변을 얻으려면 GenerateAnswer API를 사용 합니다. 이 API를 사용 하는 방법에 대 한 정보를 볼 수 있는 기술 자료를 게시할 때 합니다 **게시** 페이지입니다. 답변 메타 데이터 태그를 기준으로 필터링 하는 API를 구성 하 고 테스트 쿼리 문자열 매개 변수를 사용 하 여 끝점에서 기술 자료를 테스트할 수도 있습니다.

QnA Maker의 질문 및 답변 사용자 집합에 키 / 값 쌍의 형태로 메타 데이터를 추가할 수 있습니다. 그런 다음 사용자 쿼리 결과 필터링 하 고 후속 대화에 사용할 수 있는 추가 정보를 저장 합니다.이 정보를 사용할 수 있습니다. 자세한 내용은 [기술 자료](../Concepts/knowledge-base.md)를 참조하세요.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>질문 및 답변 QnA 엔터티를 사용 하 여 저장 합니다.

QnA Maker 질문 및 답변 데이터를 저장 하는 방법을 이해 하는 것이 반드시 합니다. 다음 일러스트레이션은 QnA 엔터티를 보여줍니다.

![QnA 엔터티의 그림](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

QnA 엔터티마다 고유한 영구 ID가 있습니다. 특정 QnA 엔터티를 업데이트 하려면 ID를 사용할 수 있습니다.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>GenerateAnswer API를 사용 하 여 답변 예측

사용 된 [GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) 봇 또는 사용자 질문을 사용 하 여 기술 자료를 쿼리 하는 응용 프로그램에서 설정에 가장 일치 하는 질문 및 답변에서 가져오려고 합니다.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>GenerateAnswer 끝점에 게시 

기술 자료에 게시 한 후 합니다 [QnA Maker 포털](https://www.qnamaker.ai), 또는 사용 하 여 합니다 [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), GenerateAnswer 끝점의 세부 정보를 가져올 수 있습니다.

엔드포인트 세부 정보를 가져오려면 다음을 수행합니다.
1. [https://www.qnamaker.ai](https://www.qnamaker.ai)에 로그인합니다.
1. **내 기술 자료**를 선택 **코드 보기** 기술 자료에 대 한 합니다.
    ![기술 자료를 내 스크린 샷](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. GenerateAnswer 엔드포인트 세부 정보를 가져옵니다.

    ![끝점 세부 정보 스크린샷](../media/qnamaker-how-to-metadata-usage/view-code.png)

기술 자료의 **설정** 탭에서 엔드포인트 세부 정보를 가져올 수도 있습니다.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 요청 구성

HTTP POST 요청을 사용하여 GenerateAnswer를 호출합니다. GenerateAnswer를 호출하는 방법을 보여주는 샘플 코드는 [빠른 시작](../quickstarts/csharp.md)을 참조하세요. 

POST 요청을 사용합니다.

* 필요한 [URI 매개 변수](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* 필요한 [헤더 속성](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer), `Authorization`, 보안
* 필요한 [본문 속성](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)합니다. 

GenerateAnswer URL의 형식은: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

HTTP 헤더 속성을 설정 해야 `Authorization` 문자열의 값을 사용 하 여 `EndpointKey` 는 후행 공백 다음에서 찾을 끝점 키를 **설정** 페이지입니다.

예제 JSON 본문 다음과 같습니다.

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer 응답 속성

합니다 [응답](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful_query) 사용 가능한 경우 답변 및 다음 표시 하는 데 필요한 정보를 대화의 설정 모두를 포함 하 여 JSON 개체입니다.

```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

## <a name="use-qna-maker-with-a-bot-in-c"></a>에 봇과 QnA Maker를 사용 합니다.C#

Bot framework QnA Maker의 속성에 대 한 액세스를 제공 합니다.

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

지원 봇이 [예로](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) 이 코드로 변경 합니다.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Node.js에서 봇과 QnA Maker를 사용 합니다.

Bot framework QnA Maker의 속성에 대 한 액세스를 제공 합니다.

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.03,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

지원 봇이 [예로](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) 이 코드로 변경 합니다.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>메타 데이터를 사용 하 여 답변 사용자 지정 메타 데이터 태그로 필터링

메타 데이터를 추가 합니다. 이러한 메타 데이터 태그로 답을 필터링 할 수 있습니다. 메타 데이터 열을 추가 합니다 **보기 옵션** 메뉴. 메타 데이터를 선택 하 여 메타 데이터 기술 자료에 추가할 **+** 아이콘을 메타 데이터 쌍을 추가 합니다. 이 쌍 하나 키 한 개와 값으로 구성 됩니다.

![메타 데이터를 추가 하는 스크린샷](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>메타데이터 태그에 대한 strictFilters로 결과 필터링

"경우에이 호텔을 닫고?", 사용자 질문을 고려해 여기서 의도 것을 의미 있는 식당 "환상의"로 지정 합니다.

결과 "환상의" 음식점에 대해서만 필요 하기 때문에 "음식점 Name" 메타 데이터에 GenerateAnswer 호출에서 필터를 설정할 수 있습니다. 다음 예제에서는이 보여 줍니다.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>질문 및 답변 결과 사용 하 여 대화 컨텍스트를 유지 하려면

GenerateAnswer에 대 한 응답에는 일치 하는 질문 및 답변 집합의 해당 메타 데이터 정보를 포함합니다. 뒷부분에 나오는 대화에 사용 하기 위해 이전 대화의 컨텍스트를 저장 하려면 클라이언트 응용 프로그램에서이 정보를 사용할 수 있습니다. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>텍스트에서 질문에만 일치

기본적으로 QnA Maker 질문 및 답변을 통해 검색합니다. 질문만 검색 하려는 경우 생성 하려면 답변을 사용 합니다 `RankerType=QuestionOnly` GenerateAnswer 요청 POST 본문에 있습니다.

게시 된 kb를 통해 검색할 수 있습니다 사용 하 여 `isTest=false`, 또는 사용 하 여 테스트 kb `isTest=true`합니다.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>다음 단계

**게시** 페이지에는 또한 사용 하 여 답변을 생성 하는 정보 제공 [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) 하 고 [cURL](../Quickstarts/get-answer-from-kb-using-curl.md)합니다. 

> [!div class="nextstepaction"]
> [기술 자료 만들기](./create-knowledge-base.md)
