---
title: 기술 자료에 GenerateAnswer API와 함께 메타데이터 사용 | Microsoft Docs
description: GenerateAnswer API와 함께 메타데이터 사용
services: cognitive-services
author: pchoudhari
manager: rsrikan
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/18/2018
ms.author: pchoudh
ms.openlocfilehash: 94e3632884d7033971ff1c45b455afb9a09ee798
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "35383237"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>메타데이터 및 GenerateAnswer API 사용

QnA Maker는 메타데이터를 키/값 쌍의 형태로 질문/응답 집합에 추가할 수 있습니다. 이 정보는 결과를 사용자 쿼리로 필터링하고, 특정 결과를 부스팅하고, 후속 대화에 사용할 수 있는 추가 정보를 저장하는 등의 다양한 방법으로 사용할 수 있습니다. 자세한 내용은 [기술 자료](../Concepts/knowledge-base.md)를 참조하세요.

## <a name="qna-entity"></a>QnA 엔터티

먼저 QnA Maker가 질문/응답 데이터를 저장하는 방법을 이해하는 것이 중요합니다. 다음 일러스트레이션은 QnA 엔터티를 보여줍니다.

![QnA 엔터티](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

QnA 엔터티마다 고유한 영구 ID가 있습니다. 이 ID는 특정 QnA 엔터티를 업데이트하는 데 사용할 수 있습니다.

## <a name="generateanswer-api"></a>GenerateAnswer API

봇 또는 응용 프로그램에서 GenerateAnswer API를 사용하여 사용자 질문으로 기술 자료를 쿼리하면 질문/응답 집합에서 최적의 일치 항목을 가져올 수 있습니다.

### <a name="generateanswer-endpoint"></a>GenerateAnswer 엔드포인트

기술 자료를 게시한 후에는 [QnA Maker 포털](https://www.qnamaker.ai)에서 또는 [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)를 사용하여 GenerateAnswer 엔드포인트 세부 정보를 가져올 수 있습니다.

엔드포인트 세부 정보를 가져오려면 다음을 수행합니다.
1. [https://www.qnamaker.ai](https://www.qnamaker.ai)에 로그인합니다.
2. **내 기술 자료**에서 기술 자료에 대한 **코드 보기**를 클릭합니다.
![내 기술 자료](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
3. GenerateAnswer 엔드포인트 세부 정보를 가져옵니다.

![엔드포인트 세부 정보](../media/qnamaker-how-to-metadata-usage/view-code.png)

기술 자료의 **설정** 탭에서 엔드포인트 세부 정보를 가져올 수도 있습니다.

### <a name="generateanswer-request"></a>GenerateAnswer 요청

HTTP POST 요청을 사용하여 GenerateAnswer를 호출합니다. GenerateAnswer를 호출하는 방법을 보여주는 샘플 코드는 [빠른 시작](../quickstarts/csharp.md)을 참조하세요.

- **요청 URL**: https://{QnA Maker endpoint}/knowledgebases/{기술 자료 ID}/generateAnswer

- **요청 매개 변수**: 
    - **기술 자료 ID**(문자열): 기술 자료의 GUID입니다.
    - **QnAMaker 엔드포인트**(문자열): Azure 구독에 배포된 엔드포인트의 호스트 이름입니다.
- **요청 헤더**
    - **콘텐츠 형식**(문자열): API로 전송되는 본문의 미디어 형식입니다.
    - **권한 부여**(문자열): 엔드포인트 키입니다.
- **요청 본문**
    - **질문**(문자열): 기술 자료에 대해 쿼리할 사용자 질문입니다.
    - **top**(선택 사항, 정수): 출력에 포함할 순위에 오른 결과의 수입니다. 기본값은 1입니다.
    - **userId**(선택 사항, 문자열): 사용자를 식별하는 고유 ID입니다. 이 ID는 채팅 로그에 기록됩니다.
    - **strictFilters**(선택 사항, 문자열): 지정할 경우 지정된 메타데이터가 있는 답변만 반환하라고 QnA Maker에 지시합니다. 자세한 내용은 아래를 참조하세요.
    ```json
    {
        "question": "qna maker and luis",
        "top": 6,
        "strictFilters": [
        {
            "name": "category",
            "value": "api"
        }],
        "userId": "sd53lsY="
    }
    ```

### <a name="generateanswer-response"></a>GenerateAnswer 응답

- **Response 200** - 호출이 성공하면 질문 결과를 반환합니다. 응답에는 다음 필드가 포함됩니다.
    - **답변** - 사용자 쿼리에 대한 답변 목록으로, 순위 점수 기준 내림차순으로 정렬됩니다.
        - **점수**: 0~100 사이의 순위 점수입니다.
        - **질문**: 사용자가 제공한 질문입니다.
        - **원본**: 기술 자료에서 답변을 추출하거나 저장한 원본 이름입니다.
        - **메타데이터**: 답변과 연결된 메타데이터입니다.
            - 이름: 메타데이터 이름입니다. (문자열, 최대 길이: 100자, 필수)
            - 값: 메타데이터 값입니다. (문자열, 최대 길이: 100자, 필수)
        - **Id**: 답변에 할당된 고유 ID입니다.
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

## <a name="metadata-example"></a>메타데이터 예제

하이데라바드의 레스토랑에 대한 아래 FAQ 데이터를 잘 살펴봅니다. 기어 아이콘을 클릭하여 기술 자료에 메타데이터를 추가합니다.

![메타데이터 추가](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters"></a>strictFilters를 사용하여 결과 필터링

"이 호텔의 영업 종료 시간은 언제입니까?"라는 사용자 질문을 음미합니다. 이 질문의 의도는 "Paradise" 레스토랑입니다.

결과는 "Paradise" 레스토랑에만 필요하므로 "Restaurant Name" 메타데이터에 대한 GenerateAnswer 호출의 필터를 다음과 같이 설정할 수 있습니다.

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

### <a name="keep-context"></a>컨텍스트 유지
GenerateAnswer에 대한 응답에는 다음과 같이 일치하는 질문/답변 집합의 해당 메타데이터 정보가 포함됩니다.

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

이 정보를 사용하여 이전 대화의 컨텍스트를 기록해 두었다가 나중에 대화에 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 만들기](./create-knowledge-base.md)
