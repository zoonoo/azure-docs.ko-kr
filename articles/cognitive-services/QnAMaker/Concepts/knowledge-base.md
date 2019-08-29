---
title: 기술 자료 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 기술 자료는 질문/답변(QnA) 쌍, 각 QnA 쌍과 연결된 선택적 메타데이터의 집합으로 구성됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2b3e74f337cf8f57321c3a8d94f8191fc3ebb530
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093907"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>QnA Maker 기술 자료란?

QnA Maker 기술 자료는 질문/답변(QnA) 쌍, 각 QnA 쌍과 연결된 선택적 메타데이터의 집합으로 구성됩니다.

## <a name="key-knowledge-base-concepts"></a>주요 기술 자료 개념

* **질문** - 사용자 쿼리를 가장 잘 나타내는 텍스트를 포함하는 질문입니다. 
* **답변** - 답변은 사용자 쿼리가 관련 질문과 일치할 경우 반환되는 응답입니다.  
* **메타데이터** - 메타데이터는 QnA 쌍과 연결된 태그이며 키-값 쌍으로 표현됩니다. 메타데이터 태그는 QnA 쌍을 필터링하고 쿼리 매칭이 수행되는 집합을 제한하는 데 사용됩니다.

숫자 QnA ID로 표현되는 단일 QnA에는 모두 단일 답변에 매핑되는 질문의 여러 변형(대체 질문)이 있습니다. 또한 각 쌍에는 하나의 키와 하나의 값과 연결 된 메타 데이터 필드가 여러 개 있을 수 있습니다.

![QnA Maker 기술 자료](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>기술 자료 콘텐츠 형식

기술 자료에 풍부한 콘텐츠를 수집하면 QnA Maker가 콘텐츠를 markdown으로 변환하려고 합니다. 대부분의 채팅 클라이언트에서 이해할 수 있는 markdown 형식을 이해하려면 [이](https://aka.ms/qnamaker-docs-markdown-support) 블로그를 읽어 보세요.

메타데이터 필드는 콜론으로 구분된 키-값 쌍으로 이루어져 있습니다 **(Product:Shredder)** . 키와 값은 모두 텍스트로만 구성되어야 합니다. 메타데이터 키에는 공백이 포함되어서는 안 됩니다. 메타 데이터는 키 당 하나의 값만 지원 합니다.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker에서 사용자 쿼리를 처리 하 여 최상의 답을 선택 하는 방법

학습 및 [게시](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) 된 QnA Maker 기술 자료는 [generateanswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)에서 봇 또는 다른 클라이언트 응용 프로그램의 사용자 쿼리를 수신 합니다. 다음 다이어그램에서는 사용자 쿼리를 수신 하는 프로세스를 보여 줍니다.

![사용자 쿼리에 대 한 순위 지정 프로세스](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

프로세스는 다음 표에 설명 되어 있습니다.

|단계|용도|
|--|--|
|1|클라이언트 응용 프로그램이 사용자 쿼리를 [Generateanswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)로 보냅니다.|
|2|Qna Maker는 언어 검색, spellers 및 단어 분리기를 사용 하 여 사용자 쿼리를 전처리 합니다.|
|3|이러한 전처리는 최상의 검색 결과를 위해 사용자 쿼리를 변경 하는 데 사용 됩니다.|
|4|이 변경 된 쿼리는 결과 `top` 수를 수신 하는 Azure Search 인덱스에 전송 됩니다. 이러한 결과에 올바른 답변이 없는 경우 값 `top` 을 약간 늘립니다. 일반적으로에 대 한 `top` 값 10은 쿼리의 90%에서 작동 합니다.|
|5|QnA Maker 고급 기능화 적용 하 여 사용자 쿼리에 대해 인출 된 Azure Search 결과의 정확성을 확인 합니다. |
|6|학습 된 ranker 모델은 5 단계의 기능 점수를 사용 하 여 Azure Search 결과의 순위를 결정 합니다.|
|7|새 결과는 순서 대로 클라이언트 응용 프로그램에 반환 됩니다.|
|||

사용 되는 기능에는 단어 수준 의미 체계, 모음의 용어 수준 중요도 및 두 텍스트 문자열 간의 유사성과 연관성을 확인 하는 심층 학습 의미 체계 모델이 포함 되지만이에 국한 되지 않습니다.

## <a name="http-request-and-response-with-endpoint"></a>끝점을 사용 하 여 HTTP 요청 및 응답
기술 자료를 게시할 때 서비스는 응용 프로그램에 통합할 수 있는 REST 기반 HTTP **엔드포인트** (일반적으로 채팅 봇)를 만듭니다. 

### <a name="the-user-query-request-to-generate-an-answer"></a>응답을 생성 하는 사용자 쿼리 요청

**사용자 쿼리** 는 최종 사용자가와 `How do I add a collaborator to my app?`같은 기술 자료를 요청 하는 질문입니다. 쿼리는 대개 자연 언어 형식 이거나 질문을 나타내는 몇 가지 키워드 (예: `help with collaborators`)로 되어 있습니다. 쿼리가 클라이언트 응용 프로그램의 HTTP **요청** 에서 사용자의 정보로 전송 됩니다.

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

[ScoreThreshold](./confidence-score.md#choose-a-score-threshold), [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers), [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)등의 속성을 설정 하 여 응답을 제어 합니다.

대화 [컨텍스트](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) 를 [멀티 턴 기능과](../how-to/multiturn-conversation.md) 함께 사용 하 여 질문 및 답변을 구체화 하는 대화를 유지 하 고 정확한 답변 및 최종 답변을 찾을 수 있습니다.

### <a name="the-response-from-a-call-to-generate-answer"></a>호출에서 응답을 생성 하는 응답

HTTP **응답** 은 지정 된 사용자 쿼리와 가장 일치 하는 항목에 따라 기술 자료에서 검색 된 대답입니다. 응답에는 대답 및 예측 점수가 포함 됩니다. `top` 속성을 사용 하 여 둘 이상의 최상위 응답을 요청 하는 경우 각각 점수가 있는 두 개 이상의 최고 답을 얻을 수 있습니다. 

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

### <a name="test-and-production-knowledge-base"></a>테스트 및 프로덕션 기술 자료
기술 자료는 QnA Maker를 통해 생성, 유지 관리 및 사용되는 질문 및 답변의 리포지토리입니다. 각 QnA Maker 계층을 여러 기술 자료에 사용할 수 있습니다.

기술 자료에는 두 가지 상태(테스트 및 게시됨)가 있습니다. 

**테스트 기술 자료** 는 응답의 정확성 및 완전성을 위해 편집, 저장 및 테스트 중인 버전입니다. 테스트 기술 자료의 변경 내용은 애플리케이션/챗봇의 최종 사용자에게 영향을 주지 않습니다. 테스트 기술 자료는 HTTP 요청에서 `test` 로 알려져 있습니다. 

**게시 된 기술 자료** 는 채팅 봇/응용 프로그램에서 사용 되는 버전입니다. 기술 자료를 게시하는 작업은 테스트 기술 자료 콘텐츠를 게시된 기술 자료 버전에 넣습니다. 게시된 기술 자료는 애플리케이션이 엔드포인트를 통해 사용하는 버전이므로, 콘텐츠가 올바르고 잘 테스트되었는지 신중하게 확인해야 합니다. 게시 된 기술 자료를 HTTP 요청 `prod` 에서 라고 합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료의 개발 수명 주기](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>참고자료

[QnA Maker 개요](../Overview/overview.md)

다음을 사용 하 여 기술 자료 만들기 및 편집: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.Net SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

다음을 사용 하 여 답변 생성: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.Net SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
