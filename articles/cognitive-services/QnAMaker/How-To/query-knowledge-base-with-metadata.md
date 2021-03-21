---
title: 메타 데이터를 사용 하 여 필터 컨텍스트
titleSuffix: Azure Cognitive Services
description: QnA Maker QnA 쌍으로 메타 데이터를 필터링 합니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f65ca9386963824f0cb740f587de83c9dec7f78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103022141"
---
# <a name="filter-responses-with-metadata"></a>메타 데이터를 사용 하 여 응답 필터링

QnA Maker를 사용 하면 키/값 쌍의 형태로 메타 데이터를 질문 및 답변 쌍에 추가할 수 있습니다. 그런 다음이 정보를 사용 하 여 사용자 쿼리에 대 한 결과를 필터링 하 고 추가 정보를 저장할 수 있습니다.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>QnA 엔터티를 사용 하 여 질문 및 답변 저장

QnA Maker 질문과 대답 데이터를 저장 하는 방법을 이해 하는 것이 중요 합니다. 다음 일러스트레이션은 QnA 엔터티를 보여줍니다.

![QnA 엔터티 그림](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

QnA 엔터티마다 고유한 영구 ID가 있습니다. ID를 사용 하 여 특정 QnA 엔터티를 업데이트할 수 있습니다.

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>메타 데이터를 사용 하 여 사용자 지정 메타 데이터 태그로 답변 필터링

메타 데이터를 추가 하면 이러한 메타 데이터 태그로 답변을 필터링 할 수 있습니다. **보기 옵션** 메뉴에서 메타 데이터 열을 추가 합니다. 메타 데이터 아이콘을 선택 하 여 메타 데이터 쌍을 추가 하 여 기술 자료에 메타 데이터를 추가 **+** 합니다. 이 쌍은 하나의 키와 하나의 값으로 구성 됩니다.

![메타 데이터 추가 스크린샷](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>메타데이터 태그에 대한 strictFilters로 결과 필터링

"Paradise" 라는 사용자 질문에 대 한 의도는 "이 호텔에 근접 하는 경우"를 참조 하세요.

식당 "Paradise"에 대 한 결과만 필요 하므로, 메타 데이터 "식당 이름"에 대 한 GenerateAnswer 호출에서 필터를 설정할 수 있습니다. 다음 예제에서는이를 보여 줍니다.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>기본적으로 논리적 AND

쿼리에서 여러 메타 데이터 필터를 결합 하려면 속성의 배열에 메타 데이터 필터를 추가 합니다 `strictFilters` . 기본적으로 값은 논리적으로 결합 되어 있습니다 (및). 논리 조합은 답에서 쌍이 반환 되려면 모든 필터가 QnA 쌍과 일치 해야 합니다.

이는 `strictFiltersCompoundOperationType` 속성을의 값과 함께 사용 하는 것과 같습니다 `AND` .

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>Logical 또는 using strictFiltersCompoundOperationType 속성

여러 메타 데이터 필터를 결합할 때 일치 하는 필터 중 하나 또는 일부에만 관심이 있는 경우 `strictFiltersCompoundOperationType` 의 값으로 속성을 사용 `OR` 합니다.

이렇게 하면 모든 필터가 일치 하지만 메타 데이터가 없는 대답은 반환 하지 않을 때 기술 자료에서 답을 반환할 수 있습니다.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>퀵 스타트의 메타 데이터 예제

메타 데이터에 대 한 QnA Maker 포털 빠른 시작에서 메타 데이터에 대해 자세히 알아보세요.
* [제작 - QnA 쌍에 메타데이터 추가](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [쿼리 예측 - 메타데이터를 통한 응답 필터링](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>질문 및 답변 결과를 사용 하 여 대화 컨텍스트 유지

GenerateAnswer에 대 한 응답에는 일치 하는 질문 및 답변 쌍에 해당 하는 메타 데이터 정보가 포함 됩니다. 클라이언트 응용 프로그램에서이 정보를 사용 하 여 이후 대화에서 사용할 이전 대화의 컨텍스트를 저장할 수 있습니다.

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

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 분석](../How-to/get-analytics-knowledge-base.md)
