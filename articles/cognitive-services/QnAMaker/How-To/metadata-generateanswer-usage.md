---
title: GenerateAnswer API와 메타데이터 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker는 메타데이터를 키/값 쌍의 형태로 질문/응답 집합에 추가할 수 있습니다. 결과를 사용자 쿼리로 필터링하고 후속 대화에 사용할 수 있는 추가 정보를 저장할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 6a8cbabfd4e47c50d2c2e6f4a23c50a931e645a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221448"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>생성답변 API 및 메타데이터로 답변 받기

사용자 질문에 대한 예측된 답변을 얻으려면 생성응답 API를 사용합니다. 기술 보고서를 게시하면 **게시** 페이지에서 이 API를 사용하는 방법에 대한 정보를 볼 수 있습니다. API를 구성하여 메타데이터 태그를 기반으로 답변을 필터링하고 테스트 쿼리 문자열 매개 변수를 사용하여 끝점에서 기술 기반을 테스트할 수도 있습니다.

QnA Maker를 사용하면 키 및 값 쌍의 형태로 메타데이터를 질문과 답변 집합에 추가할 수 있습니다. 그런 다음 이 정보를 사용하여 결과를 사용자 쿼리로 필터링하고 후속 대화에 사용할 수 있는 추가 정보를 저장할 수 있습니다. 자세한 내용은 [기술 자료](../Concepts/knowledge-base.md)를 참조하세요.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>QnA 엔터티로 질문 및 답변 저장

QnA Maker가 질문과 답변 데이터를 저장하는 방법을 이해하는 것이 중요합니다. 다음 일러스트레이션은 QnA 엔터티를 보여줍니다.

![QnA 엔터티 그림](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

QnA 엔터티마다 고유한 영구 ID가 있습니다. ID를 사용하여 특정 QnA 엔터티를 업데이트할 수 있습니다.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>생성답변 API를 통해 답변 예측 받기

봇 또는 응용 프로그램에서 [생성Answer API를](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) 사용하여 사용자 질문으로 기술 기반을 쿼리하여 질문 및 답변 집합에서 최상의 일치를 얻을 수 있습니다.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>생성 답변 끝점을 얻으려면 게시

[QnA Maker 포털에서](https://www.qnamaker.ai)또는 [API를](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)사용하여 기술 자료를 게시한 후 생성Answer 끝점의 세부 정보를 얻을 수 있습니다.

엔드포인트 세부 정보를 가져오려면 다음을 수행합니다.
1. 에 로그인합니다. [https://www.qnamaker.ai](https://www.qnamaker.ai)
1. **내 기술 기반에서**기술 자료에 대한 **코드 보기를** 선택합니다.
    ![내 지식 자료의 스크린샷](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. GenerateAnswer 엔드포인트 세부 정보를 가져옵니다.

    ![끝점 세부 정보의 스크린샷](../media/qnamaker-how-to-metadata-usage/view-code.png)

기술 자료의 **설정** 탭에서 엔드포인트 세부 정보를 가져올 수도 있습니다.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>생성응답 요청 구성

HTTP POST 요청을 사용하여 GenerateAnswer를 호출합니다. GenerateAnswer를 호출하는 방법을 보여주는 샘플 코드는 [빠른 시작](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)을 참조하세요.

POST 요청은 다음을 사용합니다.

* 필수 [URI 매개 변수](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* 필수 헤더 `Authorization`속성, 보안
* 필수 [바디 속성](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

생성답변 URL에는 다음과 같은 형식이 있습니다.

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

이후 공백이 있는 문자열 `Authorization` `EndpointKey` 값을 사용하여 HTTP 헤더 속성을 설정한 **페이지에** 있는 끝점 키로 설정해야 합니다.

JSON 본문이 다음과 같은 예입니다.

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

[랭커Type에](../concepts/best-practices.md#choosing-ranker-type)대해 자세히 알아보십시오.

이전 JSON은 임계값 점수가 30% 이상인 답변만 요청했습니다.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>응답 응답 속성 생성

[응답은](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) 답변을 표시하는 데 필요한 모든 정보와 사용 가능한 경우 대화의 다음 턴을 포함하는 JSON 개체입니다.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
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

이전 JSON은 38.5%의 점수로 응답했습니다.

## <a name="use-qna-maker-with-a-bot-in-c"></a>C의 봇과 함께 QnA 메이커 사용 #

봇 프레임워크는 [getAnswer API를](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)사용하여 QnA Maker의 속성에 대한 액세스를 제공합니다.

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

이전 JSON은 임계값 점수가 30% 이상인 답변만 요청했습니다.

지원 봇에는 이 [코드의 예가](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) 있습니다.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Node.js의 봇과 함께 QnA 메이커 사용

봇 프레임워크는 [getAnswer API를](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)사용하여 QnA Maker의 속성에 대한 액세스를 제공합니다.

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

이전 JSON은 임계값 점수가 30% 이상인 답변만 요청했습니다.

지원 봇에는 이 [코드의 예가](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) 있습니다.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>메타데이터를 사용하여 사용자 지정 메타데이터 태그로 답변 필터링

메타데이터를 추가하면 이러한 메타데이터 태그로 답변을 필터링할 수 있습니다. 옵션 보기 메뉴에서 메타데이터 열을 **추가합니다.** 메타데이터 쌍을 추가할 메타데이터 **+** 아이콘을 선택하여 기술 자료에 메타데이터를 추가합니다. 이 쌍은 하나의 키와 하나의 값으로 구성됩니다.

![메타데이터 추가 스크린샷](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>메타데이터 태그에 대한 strictFilters로 결과 필터링

"이 호텔은 언제 문을 닫습니까?"라는 사용자 질문을 고려해 보십시오.

결과는 레스토랑 "파라다이스"에만 필요하므로 메타데이터 "레스토랑 이름"의 생성 응답 호출에서 필터를 설정할 수 있습니다. 다음 예제에서는 다음을 보여 주며 다음과 같은 것을 보여 주며 다음과 같은 것을 보여 주며

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>질문 및 답변 결과를 사용하여 대화 컨텍스트 유지

생성응답에 대한 응답에는 일치하는 질문 및 답변 집합의 해당 메타데이터 정보가 포함됩니다. 클라이언트 응용 프로그램에서 이 정보를 사용하여 이후 대화에서 사용할 수 있도록 이전 대화의 컨텍스트를 저장할 수 있습니다.

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

## <a name="match-questions-only-by-text"></a>질문 일치

기본적으로 QnA Maker는 질문과 답변을 검색합니다. 질문을 통해서만 검색하려면 답변을 생성하려면 생성응답 요청의 POST `RankerType=QuestionOnly` 본문에서 를 사용합니다.

을 사용하여 게시된 kb를 `isTest=false`검색하거나 에서 를 `isTest=true`사용하여 테스트 kb를 검색할 수 있습니다.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>일반적인 HTTP 오류

|코드|설명|
|:--|--|
|2xx|Success|
|400|요청 매개 변수가 잘못되었습니다. 필수 매개 변수가 누락되었거나, 형식이 잘못되었거나, 너무 큽니다.|
|400|요청 본문이 잘못되었습니다. JSON이 누락되었거나, 형식이 잘못되었거나, 너무 큽니다.|
|401|잘못된 키|
|403|사용 권한 없음 - 올바른 사용 권한이 없음|
|404|기술 자료가 존재하지 않음|
|410|이 API는 더 이상 사용되지 않으며, 더 이상 사용할 수 없습니다.|

## <a name="next-steps"></a>다음 단계

또한 **게시** 페이지는 Postman 또는 cURL을 사용하여 [답변을 생성하는](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) 정보를 제공합니다.

> [!div class="nextstepaction"]
> [기술 자료 봇 만들기](../tutorials/integrate-qnamaker-luis.md)
