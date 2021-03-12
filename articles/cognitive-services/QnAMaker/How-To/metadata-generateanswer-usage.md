---
title: GenerateAnswer API와 메타데이터 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker를 사용 하면 키/값 쌍의 형태로 메타 데이터를 질문/답변 쌍에 추가할 수 있습니다. 사용자 쿼리를 사용 하 여 결과를 필터링 하 고 추가 정보를 저장할 수 있습니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 7e8d1b13dfd802df820bea4015e411dbb85540ba
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011428"
---
# <a name="get-an-answer-with-the-generateanswer-api"></a>GenerateAnswer API를 사용 하 여 답변 받기

사용자의 질문에 대 한 예측 답변을 얻으려면 GenerateAnswer API를 사용 합니다. 기술 자료를 게시할 때 **게시** 페이지에서이 API를 사용 하는 방법에 대 한 정보를 볼 수 있습니다. 메타 데이터 태그를 기준으로 응답을 필터링 하도록 API를 구성 하 고 테스트 쿼리 문자열 매개 변수를 사용 하 여 끝점에서 기술 자료를 테스트할 수도 있습니다.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>GenerateAnswer API를 사용 하 여 답변 예측 가져오기

봇 또는 응용 프로그램에서 [GENERATEANSWER API](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) 를 사용 하 여 사용자 질문을 통해 기술 자료를 쿼리하고 질문 및 답변 쌍에서 가장 일치 하는 항목을 가져옵니다.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>GenerateAnswer 끝점 가져오기에 게시

[QnA Maker 포털](https://www.qnamaker.ai)에서 또는 [API](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)를 사용 하 여 기술 자료를 게시 한 후에는 generateanswer 끝점의 세부 정보를 가져올 수 있습니다.

엔드포인트 세부 정보를 가져오려면 다음을 수행합니다.
1. [https://www.qnamaker.ai](https://www.qnamaker.ai)에 로그인합니다.
1. **내 기술 자료** 에서 기술 자료에 대 한 **코드 보기** 를 선택 합니다.
    ![내 기술 자료의 스크린샷](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. GenerateAnswer 엔드포인트 세부 정보를 가져옵니다.

    # <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

    ![끝점 세부 정보 스크린샷](../media/qnamaker-how-to-metadata-usage/view-code.png)

    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리형(미리 보기 릴리스)](#tab/v2)

    ![관리 되는 끝점 세부 정보 스크린샷](../media/qnamaker-how-to-metadata-usage/view-code-managed.png)

    ---

기술 자료의 **설정** 탭에서 엔드포인트 세부 정보를 가져올 수도 있습니다.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 요청 구성

HTTP POST 요청을 사용하여 GenerateAnswer를 호출합니다. GenerateAnswer를 호출하는 방법을 보여주는 샘플 코드는 [빠른 시작](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)을 참조하세요.

POST 요청은 다음을 사용 합니다.

* 필수 [URI 매개 변수](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* 보안을 위해 필요한 헤더 속성 `Authorization`
* 필수 [본문 속성](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)입니다.

GenerateAnswer URL의 형식은 다음과 같습니다.

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

의 HTTP 헤더 속성은 `Authorization` 후행 공백이 있는 문자열의 값과 `EndpointKey` **설정** 페이지에 있는 끝점 키를 사용 하 여 설정 해야 합니다.

예제 JSON 본문은 다음과 같습니다.

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

[RankerType](../concepts/best-practices.md#choosing-ranker-type)에 대해 자세히 알아보세요.

이전 JSON은 임계값 점수 보다 30% 이상인 답만 요청 했습니다.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer 응답 속성

[응답](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) 은 응답을 표시 하는 데 필요한 모든 정보를 포함 하는 JSON 개체 이며, 사용 가능한 경우에는 다음 번에 대화를 진행 합니다.

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

이전 JSON은 점수가 38.5% 인 답변으로 응답 했습니다.

## <a name="match-questions-only-by-text"></a>텍스트를 기준으로 질문과 대답을 찾습니다.

기본적으로 QnA Maker는 질문과 대답을 검색 합니다. 질문에 대해서만 검색 하려는 경우 대답을 생성 하려면 `RankerType=QuestionOnly` generateanswer 요청의 게시 본문에서를 사용 합니다.

을 사용 하 여 게시 된 kb,를 사용 하 여 `isTest=false` 또는 테스트 kb에서 검색할 수 있습니다 `isTest=true` .

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}

```
## <a name="use-qna-maker-with-a-bot-in-c"></a>C에서 봇과 QnA Maker 사용 #

Bot framework는 [Getanswer API](/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)를 사용 하 여 QnA Maker의 속성에 대 한 액세스를 제공 합니다.

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

이전 JSON은 임계값 점수 보다 30% 이상인 답만 요청 했습니다.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Node.js에서 봇과 QnA Maker 사용

Bot framework는 [Getanswer API](/javascript/api/botbuilder-ai/qnamaker?preserve-view=true&view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)를 사용 하 여 QnA Maker의 속성에 대 한 액세스를 제공 합니다.

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

이전 JSON은 임계값 점수 보다 30% 이상인 답만 요청 했습니다.

## <a name="return-precise-answers"></a>정확한 답변 반환

### <a name="generate-answer-api"></a>응답 API 생성 

사용자는 관리 되는 QnA Maker 리소스를 사용할 때 [정확한 답변](../reference-precise-answering.md) 을 사용할 수 있습니다. AnswerSpanRequest 매개 변수는 동일한에 대해 업데이트 되어야 합니다.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3,
    "answerSpanRequest": {
        "enable": true,
        "topAnswersWithSpan": 1
    }
}
```

마찬가지로 사용자는 answerSpanRequest 매개 변수를 설정 하지 않고 정확한 답변을 사용 하지 않도록 선택할 수 있습니다.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3
}
```
### <a name="bot-settings"></a>봇 설정

Bot service에 대 한 정확한 응답 설정을 구성 하려면 bot App service 리소스로 이동 합니다. 그런 다음, 다음 설정을 추가 하 여 구성을 업데이트 해야 합니다.

- EnablePreciseAnswer
- DisplayPreciseAnswerOnly

|디스플레이 구성|EnablePreciseAnswer|DisplayPreciseAnswerOnly|
|:--|--|--|
|정확한 답변만|true|true|
|긴 대답|false|false|
|길고 정확한 답변 모두|true|false|

## <a name="common-http-errors"></a>일반 HTTP 오류

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

**게시** 페이지에는 postman 또는 말아의 [대답을 생성](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) 하는 정보도 제공 됩니다.

> [!div class="nextstepaction"]
> [기술 자료에 대한 분석 가져오기](../how-to/get-analytics-knowledge-base.md)
> [!div class="nextstepaction"]
> [신뢰 점수](../Concepts/confidence-score.md)
