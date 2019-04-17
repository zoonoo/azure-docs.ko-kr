---
title: GenerateAnswer API와 메타데이터 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker는 메타데이터를 키/값 쌍의 형태로 질문/응답 집합에 추가할 수 있습니다. 이 정보는 사용자 쿼리에 대한 결과를 필터링하고 후속 대화에 사용할 수 있는 추가 정보를 저장하는 데 사용할 수 있습니다.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/21/2019
ms.author: tulasim
ms.openlocfilehash: d14e2897183a97da5e84a76b699def529f1d167e
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579413"
---
# <a name="get-a-knowledge-answer-with-the-generateanswer-api-and-metadata"></a>GenerateAnswer API 및 메타 데이터를 사용 하 여 기술 답변을 가져오려면

사용자의 질문에 대 한 예측된 답변을 얻으려면 GenerateAnswer API를 사용 합니다. 기술 자료를 게시할 때 게시 페이지에서이 API를 사용 하려면이 정보가 표시 됩니다. 답변 메타 데이터 태그를 기준으로 필터링 하는 API를 구성 하 고 테스트 쿼리 문자열 매개 변수를 사용 하 여 끝점에서 기술 자료를 테스트할 수도 있습니다.

QnA Maker는 메타데이터를 키 및 값 쌍의 형태로 질문/응답 집합에 추가할 수 있습니다. 이 정보는 사용자 쿼리에 대한 결과를 필터링하고 후속 대화에 사용할 수 있는 추가 정보를 저장하는 데 사용할 수 있습니다. 자세한 내용은 [기술 자료](../Concepts/knowledge-base.md)를 참조하세요.

<a name="qna-entity"></a>

## <a name="storing-questions-and-answers-with-a-qna-entity"></a>QnA 엔터티를 사용 하 여 질문 및 답변 저장

먼저 QnA Maker가 질문/응답 데이터를 저장하는 방법을 이해하는 것이 중요합니다. 다음 일러스트레이션은 QnA 엔터티를 보여줍니다.

![QnA 엔터티](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

QnA 엔터티마다 고유한 영구 ID가 있습니다. 이 ID는 특정 QnA 엔터티를 업데이트하는 데 사용할 수 있습니다.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>GenerateAnswer API를 사용 하 여 답변 예측

봇 또는 애플리케이션에서 GenerateAnswer API를 사용하여 사용자 질문으로 기술 자료를 쿼리하면 질문/응답 집합에서 최적의 일치 항목을 가져올 수 있습니다.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>GenerateAnswer 끝점에 게시 

기술 자료를 게시한 후에는 [QnA Maker 포털](https://www.qnamaker.ai)에서 또는 [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)를 사용하여 GenerateAnswer 엔드포인트 세부 정보를 가져올 수 있습니다.

엔드포인트 세부 정보를 가져오려면 다음을 수행합니다.
1. [https://www.qnamaker.ai](https://www.qnamaker.ai)에 로그인합니다.
1. **내 기술 자료**에서 기술 자료에 대한 **코드 보기**를 클릭합니다.
    ![내 기술 자료](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. GenerateAnswer 엔드포인트 세부 정보를 가져옵니다.

    ![엔드포인트 세부 정보](../media/qnamaker-how-to-metadata-usage/view-code.png)

기술 자료의 **설정** 탭에서 엔드포인트 세부 정보를 가져올 수도 있습니다.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 요청 구성

HTTP POST 요청을 사용하여 GenerateAnswer를 호출합니다. GenerateAnswer를 호출하는 방법을 보여주는 샘플 코드는 [빠른 시작](../quickstarts/csharp.md)을 참조하세요.

합니다 **요청 URL** 형식은 다음과 같습니다. 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer?isTest=true
```

|HTTP 요청 속성|name|Type|목적|
|--|--|--|--|
|URL 경로 매개 변수|기술 자료 ID|문자열|기술 자료를 위한 GUID입니다.|
|URL 경로 매개 변수|QnAMaker 끝점 호스트|문자열|Azure 구독에 배포된 엔드포인트의 호스트 이름입니다. 기술 자료를 게시 한 후 설정 페이지에서 제공 됩니다. |
|헤더|콘텐츠 형식|문자열|API로 전송되는 본문의 미디어 유형입니다. 기본값은: '|
|헤더|권한 부여|문자열|엔드포인트 키(EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)입니다.|
|Post 본문|JSON 개체|JSON|설정 사용 하 여 질문|


JSON 본문을 몇 가지 설정은 다음과 같습니다.

|JSON 본문 속성|필수|Type|목적|
|--|--|--|--|
|`question`|필수|문자열|기술 자료에 보낼 사용자 질문입니다.|
|`top`|선택 사항|정수|출력에 포함할 순위에 오른 결과의 수입니다. 기본값은 1입니다.|
|`userId`|선택 사항|문자열|사용자를 식별하는 고유 ID입니다. 이 ID는 채팅 로그에 기록됩니다.|
|`isTest`|선택 사항|부울|경우 true를 반환 결과로 `testkb` 게시 된 인덱스 대신 인덱스 검색.|
|`strictFilters`|선택 사항|문자열|지정할 경우 지정된 메타데이터가 있는 답변만 반환하라고 QnA Maker에 지시합니다.|

예제 JSON 본문 다음과 같습니다.

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
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

응답에 성공 하면 200 및 JSON 응답의 상태를 반환합니다. 

|답변 속성 (점수에 의해 정렬)|목적|
|--|--|
|score에 대한 임계값 수준 보기|0~100 사이의 순위 점수입니다.|
|Id|답변에 할당된 고유 ID입니다.|
|질문|사용자가 제공한 질문입니다.|
|대답|질문에 대한 답변입니다.|
|원본|기술 자료에서 답변을 추출하거나 저장한 원본 이름입니다.|
|metadata|답변과 연결된 메타데이터입니다.|
|metadata.name|메타데이터 이름입니다. (문자열, 최대 길이: 100자, 필수)|
|metadata.value: 메타데이터 값입니다. (문자열, 최대 길이: 100자, 필수)|


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

<a name="metadata-example"></a>

## <a name="using-metadata-allows-you-to-filter-answers-by-custom-metadata-tags"></a>사용자 지정 메타 데이터 태그로 답변을 필터링 할 수 메타 데이터를 사용 하 여

메타 데이터를 추가 합니다. 이러한 메타 데이터 태그로 답을 필터링 할 수 있습니다. 아래 FAQ 데이터를 고려하세요. 메타데이터 아이콘을 클릭하여 기술 자료에 메타데이터를 추가합니다.

![메타데이터 추가](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>메타데이터 태그에 대한 strictFilters로 결과 필터링

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

<name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>질문 및 답변 결과 사용 하 여 대화 컨텍스트를 유지 하려면

GenerateAnswer에 대 한 응답에는 일치 하는 질문/답변 집합의 해당 메타 데이터 정보를 포함합니다. 이 내용은 뒷부분에 나오는 대화에 사용 하기 위해 이전 대화의 컨텍스트를 저장 하려면 클라이언트 응용 프로그램에서 사용할 수 있습니다. 

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

게시 페이지에서는 [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) 및 [cURL](../Quickstarts/get-answer-from-kb-using-curl.md)과 함께 대답을 생성하기 위한 정보를 제공합니다. 

> [!div class="nextstepaction"]
> [기술 자료 만들기](./create-knowledge-base.md)
