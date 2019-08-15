---
title: GenerateAnswer API와 메타데이터 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker는 메타데이터를 키/값 쌍의 형태로 질문/응답 집합에 추가할 수 있습니다. 사용자 쿼리를 사용 하 여 결과를 필터링 하 고 추가 정보를 저장할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: diberry
ms.openlocfilehash: dbfa7aaccd513ffcf8ba3907911d8c49275b2ea6
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967649"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>GenerateAnswer API 및 메타 데이터를 사용 하 여 답변 받기

사용자의 질문에 대 한 예측 답변을 얻으려면 GenerateAnswer API를 사용 합니다. 기술 자료를 게시할 때 **게시** 페이지에서이 API를 사용 하는 방법에 대 한 정보를 볼 수 있습니다. 메타 데이터 태그를 기준으로 응답을 필터링 하도록 API를 구성 하 고 테스트 쿼리 문자열 매개 변수를 사용 하 여 끝점에서 기술 자료를 테스트할 수도 있습니다.

QnA Maker를 사용 하면 질문 및 답변 집합에 키 및 값 쌍의 형태로 메타 데이터를 추가할 수 있습니다. 그런 다음이 정보를 사용 하 여 사용자 쿼리에 대 한 결과를 필터링 하 고 추가 정보를 저장할 수 있습니다. 자세한 내용은 [기술 자료](../Concepts/knowledge-base.md)를 참조하세요.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>QnA 엔터티를 사용 하 여 질문 및 답변 저장

QnA Maker 질문과 대답 데이터를 저장 하는 방법을 이해 하는 것이 중요 합니다. 다음 일러스트레이션은 QnA 엔터티를 보여줍니다.

![QnA 엔터티 그림](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

QnA 엔터티마다 고유한 영구 ID가 있습니다. ID를 사용 하 여 특정 QnA 엔터티를 업데이트할 수 있습니다.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>GenerateAnswer API를 사용 하 여 답변 예측 가져오기

봇 또는 응용 프로그램에서 [GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) 를 사용 하 여 사용자 질문을 통해 기술 자료를 쿼리하고 질문 및 답변 집합에서 가장 일치 하는 항목을 가져옵니다.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>GenerateAnswer 끝점 가져오기에 게시 

[QnA Maker 포털](https://www.qnamaker.ai)에서 또는 [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)를 사용 하 여 기술 자료를 게시 한 후에는 generateanswer 끝점의 세부 정보를 가져올 수 있습니다.

엔드포인트 세부 정보를 가져오려면 다음을 수행합니다.
1. [https://www.qnamaker.ai](https://www.qnamaker.ai)에 로그인합니다.
1. **내 기술 자료**에서 기술 자료에 대 한 **코드 보기** 를 선택 합니다.
    ![내 기술 자료의 스크린샷](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. GenerateAnswer 엔드포인트 세부 정보를 가져옵니다.

    ![끝점 세부 정보 스크린샷](../media/qnamaker-how-to-metadata-usage/view-code.png)

기술 자료의 **설정** 탭에서 엔드포인트 세부 정보를 가져올 수도 있습니다.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 요청 구성

HTTP POST 요청을 사용하여 GenerateAnswer를 호출합니다. GenerateAnswer를 호출하는 방법을 보여주는 샘플 코드는 [빠른 시작](../quickstarts/csharp.md)을 참조하세요. 

POST 요청은 다음을 사용 합니다.

* 필수 [URI 매개 변수](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* 보안을 위해 필요한 `Authorization` [헤더 속성](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer)
* 필수 [본문 속성](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)입니다. 

GenerateAnswer URL의 형식은 다음과 같습니다. 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

의 `Authorization` HTTP 헤더 속성은 후행 공백이 있는 문자열 `EndpointKey` 의 값과 **설정** 페이지에 있는 끝점 키를 사용 하 여 설정 해야 합니다.

예제 JSON 본문은 다음과 같습니다.

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

[응답](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) 은 응답을 표시 하는 데 필요한 모든 정보를 포함 하는 JSON 개체 이며, 사용 가능한 경우에는 다음 번에 대화를 진행 합니다.

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

## <a name="use-qna-maker-with-a-bot-in-c"></a>에서 봇과 함께 QnA Maker 사용C#

Bot 프레임 워크는 QnA Maker의 속성에 대 한 액세스를 제공 합니다.

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

지원 봇에는이 코드가 포함 된 [예제가](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) 있습니다.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Node.js에서 봇과 함께 QnA Maker 사용

Bot 프레임 워크는 QnA Maker의 속성에 대 한 액세스를 제공 합니다.

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

지원 봇에는이 코드가 포함 된 [예제가](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) 있습니다.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>메타 데이터를 사용 하 여 사용자 지정 메타 데이터 태그로 답변 필터링

메타 데이터를 추가 하면 이러한 메타 데이터 태그로 답변을 필터링 할 수 있습니다. **보기 옵션** 메뉴에서 메타 데이터 열을 추가 합니다. 메타 데이터 **+** 아이콘을 선택 하 여 메타 데이터 쌍을 추가 하 여 기술 자료에 메타 데이터를 추가 합니다. 이 쌍은 하나의 키와 하나의 값으로 구성 됩니다.

![메타 데이터 추가 스크린샷](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>메타데이터 태그에 대한 strictFilters로 결과 필터링

"Paradise" 라는 사용자 질문에 대 한 의도는 "이 호텔에 근접 하는 경우"를 참조 하세요.

식당 "Paradise"에 대 한 결과만 필요 하므로, 메타 데이터 "식당 이름"에 대 한 GenerateAnswer 호출에서 필터를 설정할 수 있습니다. 다음 예제에서는이를 보여 줍니다.

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>질문 및 답변 결과를 사용 하 여 대화 컨텍스트 유지

GenerateAnswer에 대 한 응답에는 일치 하는 질문 및 답변 집합에 해당 하는 메타 데이터 정보가 포함 됩니다. 클라이언트 응용 프로그램에서이 정보를 사용 하 여 이후 대화에서 사용할 이전 대화의 컨텍스트를 저장할 수 있습니다. 

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

## <a name="match-questions-only-by-text"></a>텍스트를 기준으로 질문과 대답을 찾습니다.

기본적으로 QnA Maker는 질문과 대답을 검색 합니다. 질문에 대해서만 검색 하려는 경우 대답을 생성 하려면 generateanswer 요청의 게시 본문 `RankerType=QuestionOnly` 에서를 사용 합니다.

을 사용 하 여 `isTest=false` `isTest=true`게시 된 kb,를 사용 하 여 또는 테스트 kb에서 검색할 수 있습니다.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>다음 단계

**게시** 페이지에는 [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) 및 [말아](../Quickstarts/get-answer-from-kb-using-curl.md)의 대답을 생성 하는 정보도 제공 됩니다. 

> [!div class="nextstepaction"]
> [기술 자료 만들기](./create-knowledge-base.md)
