---
title: '빠른 시작: QnA Maker 포털에서 질문 및 답변 추가'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: bf88928ca24a1205ec7a1ddd2fd20af0d0e91468
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422663"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>빠른 시작: QnA Maker 포털을 사용하여 질문 및 답변 추가

기술 자료가 만들어지면 사용자가 질문에 대한 올바른 답변을 찾을 수 있도록 메타데이터가 포함된 질문 및 답변 세트를 추가합니다.

올바른 답변은 단일 답변이지만, 고객이 단일 답변으로 이어지도록 질문할 수 있는 여러 가지 방법이 있을 수 있습니다.

예를 들어 다음 표의 질문은 Azure 서비스 제한에 대한 것이지만 각각 다른 Azure 서비스와 관련이 있습니다. 

<a name="qna-table"></a>


|설정|질문|Answer|Metadata|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

메타데이터가 질문 및 답변 세트에 추가되면 클라이언트 애플리케이션에서 다음을 수행할 수 있습니다.

* 특정 메타데이터와만 일치하는 답변을 요청합니다.
* 모든 답변을 받지만 각 답변의 메타데이터에 따라 해당 답변을 사후 처리합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="prerequisites"></a>필수 조건

* QnA Maker 서비스
* 해당 QnA Maker 서비스에서 만든 기술 자료

둘 모두는 [첫 번째 빠른 시작](../how-to/create-knowledge-base.md)에서 만들어졌습니다.

## <a name="sign-in-to-the-qna-maker-portal"></a>QnA Maker 포털에 로그인

1. [QnA Maker 포털](https://www.qnamaker.ai)에 로그인합니다.

1. 기존 기술 자료를 선택합니다. 기술 자료가 없으면 [이전 빠른 시작](../how-to/create-knowledge-base.md)으로 돌아가서 기술 자료를 만드는 단계를 완료합니다.

## <a name="add-additional-alternatively-phrased-questions"></a>대체 구문이 추가된 질문 추가 

[이전 빠른 시작](../how-to/create-knowledge-base.md)의 현재 기술 자료에는 QnA Maker 문제 해결 질문 및 답변 세트가 있습니다. 이러한 세트는 만들기 프로세스 중에 URL이 기술 자료에 추가될 때 만들어졌습니다. 

이 URL을 가져올 때 하나의 답변이 있는 하나의 질문만 만들어졌습니다. 

이 절차에서는 추가 질문을 추가합니다.

1. **편집** 페이지에서 질문 및 답변 세트 위에 있는 검색 텍스트 상자를 사용하여 `How large a knowledge base can I create?` 질문을 찾습니다.

1. **질문** 열에서 **+ 대체 구문 추가**를 선택한 다음, 다음 표에 제공된 각각의 새 구문을 추가합니다.

    |대체 구문|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`| 

1. **저장 및 학습**을 선택하여 기술 자료를 다시 학습시킵니다. 

1. **테스트**를 선택한 다음, 새 대체 구문 중 하나에 가깝지만 정확히 동일한 단어가 아닌 질문을 입력합니다.

    `What GB size can a knowledge base be?`

    markdown 형식의 올바른 답변이 반환됩니다. `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    반환된 답변 아래에서 **검사**를 선택하면 질문에 부합하지만 동일한 높은 수준으로 신뢰할 수 없는 더 많은 답변이 표시될 수 있습니다. 

    대체 구문의 가능한 모든 조합을 추가하지 않습니다. QnA Maker의 [활성 학습](../how-to/improve-knowledge-base.md)을 설정합니다. 그러면 기술 자료에서 사용자의 요구를 충족시키는 데 가장 적합한 대체 구문을 찾을 수 있습니다.

1. **테스트**를 다시 선택하여 테스트 창을 닫습니다.

## <a name="add-metadata-to-filter-the-answers"></a>답변을 필터링하기 위한 메타데이터 추가

메타데이터가 질문 및 답변 세트에 추가되면 클라이언트 애플리케이션에서 필터링된 답변을 요청할 수 있습니다. 이 필터는 [첫 번째 및 두 번째 순위 매기기](../concepts/knowledge-base.md#ranker-process)를 적용하기 전에 적용됩니다.

1. [이 빠른 시작의 첫 번째 테이블](#qna-table)에서 메타데이터 없이 두 번째 질문 및 답변 세트를 추가한 다음, 다음 단계를 계속 진행합니다. 

1. **보기 옵션**을 선택한 다음, **메타데이터 표시**를 선택합니다. 

1. 방금 추가한 질문 및 답변 세트에 대해 **메타데이터 태그 추가**를 선택한 다음, `service` 이름과 `search` 값을 추가합니다(`service:search`).

1. `link_in_answer` 이름 및 `false` 값의 다른 메타데이터 태그(`link_in_answer:false`)를 추가합니다.

1. 테이블에서 첫 번째 답변(`How large a knowledge base can I create?`)을 검색합니다. 
1. 동일한 두 메타데이터 태그에 대한 메타데이터 쌍을 추가합니다.

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    이제 서로 다른 값이 있는 동일한 메타데이터 태그가 포함된 두 개의 질문이 있습니다. 

1. **저장 및 학습**을 선택하여 기술 자료를 다시 학습시킵니다. 

1. 위쪽 메뉴에서 **게시**를 선택하여 게시 페이지로 이동합니다. 
1. **게시** 단추를 선택하여 현재 기술 자료를 쿼리 가능한 엔드포인트에 게시합니다. 
1. 기술 자료가 게시되면 **Curl** 탭을 선택하여 기술 자료에서 답변을 생성하는 데 사용되는 cURL 명령 예제를 확인합니다.
1. 명령을 편집할 수 있도록 메모장 또는 다른 편집 가능한 환경에 복사합니다. 사용자 고유의 리소스 이름, 기술 자료 ID 및 엔드포인트 키를 편집합니다.

    |Replace|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    질문이 한 단어(`size`)에 불과하며, 질문 및 답변 세트를 반환할 수 있습니다. `strictFilters` 배열은 `qna_maker` 답변만 줄이도록 응답에 지시합니다. 

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. 응답에는 필터 조건을 충족하는 답변만 포함됩니다. 

    다음 cURL 응답은 읽기 쉬운 형식으로 지정되었습니다.

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    검색 용어는 충족하지 않지만 필터는 충족하는 질문 및 답변 세트가 있으면 반환되지 않습니다. 대신 일반 `No good match found in KB.` 답변이 반환됩니다.

    메타데이터 이름 및 값 쌍은 필요한 제한 내에서 유지해야 합니다. 

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Postman 또는 cURL을 사용하여 답변 가져오기](get-answer-from-knowledge-base-using-url-tool.md)