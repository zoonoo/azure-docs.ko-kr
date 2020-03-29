---
title: 기술 자료 쿼리 - QnA 메이커
description: 기술 자료는 게시해야 합니다. 게시되면 기술 자료는 generateAnswer API를 사용하여 런타임 예측 끝점에서 쿼리됩니다.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221462"
---
# <a name="query-the-knowledge-base-for-answers"></a>답변에 대한 기술 자료 쿼리

기술 자료는 게시해야 합니다. 게시되면 기술 자료는 generateAnswer API를 사용하여 런타임 예측 끝점에서 쿼리됩니다. QnA Maker가 답변에 가장 적합한 일치를 선택할 수 있도록 질문 텍스트 및 기타 설정이 쿼리에 포함됩니다.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker가 사용자 쿼리를 처리하여 최상의 답변을 선택하는 방법

학습되고 [게시된](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker 기술 자료는 [생성응답 API에서](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)봇 또는 다른 클라이언트 응용 프로그램에서 사용자 쿼리를 수신합니다. 다음 다이어그램은 사용자 쿼리가 수신되는 프로세스를 보여 줍니다.

![사용자 쿼리의 순위 모델 프로세스](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>랭커 프로세스

프로세스는 다음 표에 설명되어 있습니다.

|단계|목적|
|--|--|
|1|클라이언트 응용 프로그램은 [생성응답 API로](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)사용자 쿼리를 보냅니다.|
|2|QnA Maker는 언어 검색, 맞춤법 검사기 및 단어 차단기를 통해 사용자 쿼리를 미리 처리합니다.|
|3|이 전처리는 최상의 검색 결과에 대한 사용자 쿼리를 변경하기 위해 수행됩니다.|
|4|이 변경된 쿼리는 결과 수를 받는 Azure `top` 인지 검색 인덱스로 전송됩니다. 정답이 이러한 결과에 없는 경우 약간 값을 `top` 늘립니다. 일반적으로 쿼리의 90%에서 작업하는 경우 `top` 10의 값입니다.|
|5|QnA Maker는 구문 및 의미 체계 기반 위화기를 사용하여 사용자 쿼리와 가져온 QnA 결과 간의 유사성을 확인합니다.|
|6|기계 학습 랭커 모델은 5단계부터 다양한 기능을 사용하여 신뢰도 점수와 새 순위 순서를 결정합니다.|
|7|새 결과는 순위 순서로 클라이언트 응용 프로그램에 반환됩니다.|
|||

사용되는 기능에는 단어 수준 의미 체계, 코퍼스의 용어 수준 중요도 및 두 텍스트 문자열 간의 유사성 및 관련성을 결정하기 위해 심층 학습된 의미 체계 모델이 포함되나 이에 국한되지 않습니다.

## <a name="http-request-and-response-with-endpoint"></a>엔드포인트가 있는 HTTP 요청 및 응답
기술 자료가 게시되면 서비스는 응용 프로그램에 통합할 수 있는 REST 기반 HTTP 끝점(일반적으로 채팅 봇)을 만듭니다.

### <a name="the-user-query-request-to-generate-an-answer"></a>응답을 생성하는 사용자 쿼리 요청

사용자 쿼리는 최종 사용자가 기술 자료에 대해 묻는 `How do I add a collaborator to my app?`질문입니다. 쿼리는 종종 자연어 형식또는 질문을 나타내는 몇 가지 키워드(예: )로 되어 있습니다. `help with collaborators` 쿼리는 클라이언트 응용 프로그램의 HTTP 요청에서 기술 자료로 전송됩니다.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

[scoreThreshold,](./confidence-score.md#choose-a-score-threshold) [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)및 [strictFilters와](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)같은 속성을 설정하여 응답을 제어합니다.

멀티 턴 [기능과](../how-to/multiturn-conversation.md) [대화 컨텍스트를](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) 사용하여 대화가 질문과 답변을 구체화하고 정답을 찾을 수 있도록 합니다.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>응답을 생성하는 호출의 응답

HTTP 응답은 지정된 사용자 쿼리에 가장 적합한 일치를 기반으로 기술 자료에서 검색된 답변입니다. 응답에는 응답 및 예측 점수가 포함됩니다. `top` 속성에 두 개 이상의 최고 답변을 요청 하는 경우, 당신은 하나 이상의 최고 답변을 얻을, 점수각.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [신뢰도 점수](./confidence-score.md)
