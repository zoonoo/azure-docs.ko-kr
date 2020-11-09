---
title: 기술 자료 쿼리-QnA Maker
description: 기술 자료가 게시 되어야 합니다. 게시 된 후에는 generateAnswer API를 사용 하 여 런타임 예측 끝점에서 기술 자료가 쿼리 됩니다.
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e8dd056a7b6357b8342d3059e17baa88db92b404
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376729"
---
# <a name="query-the-knowledge-base-for-answers"></a>기술 자료에서 답변을 쿼리 합니다.

기술 자료가 게시 되어야 합니다. 게시 된 후에는 generateAnswer API를 사용 하 여 런타임 예측 끝점에서 기술 자료가 쿼리 됩니다. 쿼리에는 질문 텍스트 및 기타 설정이 포함 되어 QnA Maker 대답에 가장 적합 한 일치 항목을 선택할 수 있습니다.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker에서 사용자 쿼리를 처리 하 여 최상의 답을 선택 하는 방법

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (안정적인 릴리스)](#tab/v1)

학습 및 [게시](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) 된 QnA Maker 기술 자료는 [generateanswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)에서 봇 또는 다른 클라이언트 응용 프로그램의 사용자 쿼리를 수신 합니다. 다음 다이어그램에서는 사용자 쿼리를 수신 하는 프로세스를 보여 줍니다.

![사용자 쿼리에 대 한 순위 모델 프로세스](../media/qnamaker-concepts-knowledgebase/ranker-v1.png)

### <a name="ranker-process"></a>Ranker 프로세스

프로세스는 다음 표에 설명 되어 있습니다.

|단계|목적|
|--|--|
|1|클라이언트 응용 프로그램이 사용자 쿼리를 [Generateanswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)로 보냅니다.|
|2|QnA Maker 언어 검색, spellers 및 단어 분리기를 사용 하 여 사용자 쿼리를 전처리 합니다.|
|3|이러한 전처리는 최상의 검색 결과에 대 한 사용자 쿼리를 변경 하는 데 사용 됩니다.|
|4|이 변경 된 쿼리는 결과 수를 수신 하는 Azure Cognitive Search 인덱스에 전송 됩니다 `top` . 이러한 결과에 올바른 답변이 없는 경우 값을 `top` 약간 늘립니다. 일반적으로에 대 한 값 10은 `top` 쿼리의 90%에서 작동 합니다.|
|5|QnA Maker는 구문 및 의미 체계 기반 기능화을 사용 하 여 사용자 쿼리와 인출 된 QnA 결과 간의 유사성을 결정 합니다.|
|6|컴퓨터에서 학습 한 ranker 모델은 5 단계에서 제공 하는 다양 한 기능을 사용 하 여 신뢰도 점수와 새 순위를 결정 합니다.|
|7|새 결과는 순서 대로 클라이언트 응용 프로그램에 반환 됩니다.|
|||

사용 되는 기능에는 단어 수준 의미 체계, 모음의 용어 수준 중요도 및 두 텍스트 문자열 간의 유사성과 연관성을 확인 하는 심층 학습 의미 체계 모델이 포함 되지만이에 국한 되지 않습니다.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리 (미리 보기 릴리스)](#tab/v2)

학습 및 [게시](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) 된 QnA Maker 기술 자료는 [generateanswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)에서 봇 또는 다른 클라이언트 응용 프로그램의 사용자 쿼리를 수신 합니다. 다음 다이어그램에서는 사용자 쿼리를 수신 하는 프로세스를 보여 줍니다.

![사용자 쿼리 미리 보기에 대 한 순위 모델 프로세스](../media/qnamaker-concepts-knowledgebase/ranker-v2.png)

### <a name="ranker-process"></a>Ranker 프로세스

프로세스는 다음 표에 설명 되어 있습니다.

|단계|목적|
|--|--|
|1|클라이언트 응용 프로그램이 사용자 쿼리를 [Generateanswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)로 보냅니다.|
|2|QnA Maker 언어 검색, spellers 및 단어 분리기를 사용 하 여 사용자 쿼리를 전처리 합니다.|
|3|이러한 전처리는 최상의 검색 결과에 대 한 사용자 쿼리를 변경 하는 데 사용 됩니다.|
|4|이 변경 된 쿼리는 결과 수를 수신 하는 Azure Cognitive Search 인덱스에 전송 됩니다 `top` . 이러한 결과에 올바른 답변이 없는 경우 값을 `top` 약간 늘립니다. 일반적으로에 대 한 값 10은 `top` 쿼리의 90%에서 작동 합니다.|
|5|QnA Maker는 최신 변환기 기반 모델을 사용 하 여 Azure Cognitive Search에서 가져온 후보 QnA 결과와 사용자 쿼리 간의 유사성을 결정 합니다. 변환기 기반 모델은 모든 언어에서 신뢰도 점수와 새 순위를 결정 하는 데 가로로 작동 하는 심층 학습 다국어 모델입니다.|
|6|새 결과는 순서 대로 클라이언트 응용 프로그램에 반환 됩니다.|
|||

Ranker은 모든 대체 질문과 대답에서 작동 하 여 사용자 쿼리에 가장 일치 하는 QnA 쌍을 찾습니다. 사용자는 ranker만 ranker 하도록 유연성을 구성할 수 있습니다. 

---

## <a name="http-request-and-response-with-endpoint"></a>끝점을 사용 하 여 HTTP 요청 및 응답
기술 자료를 게시할 때 서비스는 응용 프로그램에 통합할 수 있는 REST 기반 HTTP 엔드포인트 (일반적으로 채팅 봇)를 만듭니다.

### <a name="the-user-query-request-to-generate-an-answer"></a>응답을 생성 하는 사용자 쿼리 요청

사용자 쿼리는 최종 사용자가와 같은 기술 자료를 요청 하는 질문입니다 `How do I add a collaborator to my app?` . 쿼리는 대개 자연 언어 형식 이거나 질문을 나타내는 몇 가지 키워드 (예:)로 되어 `help with collaborators` 있습니다. 클라이언트 응용 프로그램의 HTTP 요청에서 기술 자료로 쿼리를 보냅니다.

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

[ScoreThreshold](./confidence-score.md#choose-a-score-threshold), [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers), [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)등의 속성을 설정 하 여 응답을 제어 합니다.

대화 [컨텍스트](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) 를 [멀티 턴 기능과](../how-to/multiturn-conversation.md) 함께 사용 하 여 질문 및 답변을 구체화 하는 대화를 유지 하 고 정확한 답변 및 최종 답변을 찾을 수 있습니다.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>에 대 한 호출에서 응답을 생성 합니다.

HTTP 응답은 지정 된 사용자 쿼리와 가장 일치 하는 항목에 따라 기술 자료에서 검색 된 대답입니다. 응답에는 대답 및 예측 점수가 포함 됩니다. 속성을 사용 하 여 둘 이상의 최고 답을 요청 하는 경우 `top` 각각 점수가 있는 두 개 이상의 최고 답을 얻을 수 있습니다.

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