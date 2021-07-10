---
title: '자습서: Azure Bot Service를 사용하여 여러 도메인에 대한 FAQ 봇 만들기'
description: 이 자습서에서는 QnA Maker 및 Azure Bot Service를 사용하여 프로덕션 사용 사례에 대한 코드 없는 FAQ 봇을 만듭니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.author: diagarw
ms.date: 03/31/2021
ms.openlocfilehash: d79eed22d441949810cfc1738f3af2c0f8703adc
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110116475"
---
# <a name="add-multiple-domains-to-your-faq-bot"></a>FAQ 봇에 여러 도메인 추가

FAQ 봇을 빌드하는 경우 여러 도메인에서 쿼리를 처리해야 하는 사용 사례가 발생할 수 있습니다. Microsoft의 마케팅 팀이 여러 Surface 제품에 대한 일반적인 사용자 쿼리에 답변하는 고객 지원 봇을 빌드한다고 가정합니다. 여기에서는 간단한 설명을 위해 [Surface 펜](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98) 및 [Surface 이어버드](https://support.microsoft.com/surface/use-surface-earbuds-aea108c3-9344-0f11-e5f5-6fc9f57b21f9)에서 각각 FAQ URL을 사용하여 기술 자료를 만듭니다.

QnA Maker를 사용하여 여러 도메인에서 쿼리를 처리하도록 봇을 설계하는 방법은 다음과 같습니다.

* 단일 기술 자료를 만들고 메타데이터를 사용하여 QnA 쌍을 별도의 도메인에 태깅합니다.
* 각 도메인에 대한 별도의 기술 자료를 만듭니다.
* 각 도메인에 대해 별도의 QnA Maker 리소스를 만듭니다.

## <a name="create-a-single-knowledge-base-and-tag-qna-pairs-into-distinct-domains-with-metadata"></a>단일 기술 자료를 만들고 메타데이터를 사용하여 QnA 쌍을 별도의 도메인에 태깅합니다.

콘텐츠 작성자는 문서를 사용하여 QnA를 추출하거나 기술 자료에 사용자 지정 QnA를 추가할 수 있습니다. 이러한 QnA를 특정 도메인 또는 범주로 그룹화하려면 QnA 쌍에 [메타데이터](../How-To/query-knowledge-base-with-metadata.md)를 추가합니다.

Surface 제품의 경우 다음 단계를 수행하여 두 제품 유형에 대한 쿼리에 답변하는 봇을 만들 수 있습니다.

1. KB 만들기 페이지의 3단계에서 Surface 제품에 대한 다음 FAQ URL을 추가하고 'KB 만들기'를 클릭합니다. 이러한 원본에서 QnA 쌍을 추출하면 새 기술 자료가 생성됩니다. 
   
   [Surface 펜 FAQ](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98)<br>[Surface 이어버드 FAQ](https://support.microsoft.com/surface/use-surface-earbuds-aea108c3-9344-0f11-e5f5-6fc9f57b21f9)
 
2. KB를 만든 후에 **보기 옵션** 으로 이동하여 **메타데이터 표시** 를 클릭합니다. 그러면 QnA에 대한 메타데이터 열이 열립니다.

   >[!div class="mx-imgBorder"]
   >[![메타데이터 표시]( ../media/qnamaker-tutorial-updates/show-metadata.png) ]( ../media/qnamaker-tutorial-updates/expand/show-metadata.png#lightbox)


3. 이 기술 자료에는 두 제품에 QnA가 있으며, 특정 제품에 대한 QnA에서 답변을 검색할 수 있도록 두 제품을 구분하려고 합니다. 이렇게 하려면 그에 맞게 QnA 쌍에 대한 메타데이터 필드를 업데이트해야 합니다. 

   아래 예에서 볼 수 있듯이 키는 **product** 이고, 값은 각각 **surface_pen** 또는 **surface_earbuds** 인 메타데이터를 추가했습니다. 이 예제를 확장하여 여러 제품에 대한 데이터를 추출하고 각 제품에 대한 다른 값을 추가할 수 있습니다.

   >[!div class="mx-imgBorder"]
   >[![메타데이터]( ../media/qnamaker-tutorial-updates/metadata-example-2.png) ]( ../media/qnamaker-tutorial-updates/expand/metadata-example-2.png#lightbox)

4. 이제 특정 제품에서 답변을 검색하도록 시스템을 제한하려면 해당 제품을 GenerateAnswer API에서 strict 필터로 전달해야 합니다.

    [GenerateAnswer API를 사용하는 방법](../How-To/metadata-generateanswer-usage.md)을 알아보세요. GenerateAnswer URL의 형식은 다음과 같습니다.
    ```
    https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
    ```

    API 호출의 JSON 본문에서 메타데이터 *product* 에 대한 값으로 *surface_pen* 을 전달했습니다. 그러면 시스템은 QnA 쌍 중에서 동일한 메타데이터가 있는 답변만 찾습니다. 

    ```json
    {
        "question": "What is the price?",
        "top": 6,
        "isTest": true,
        "scoreThreshold": 30,
        "rankerType": ""  // values: QuestionOnly
        "strictFilters": [
        {
            "name": "product",
            "value": "surface_pen"
        }],
        "userId": "sd53lsY="
    }
    ```

    사용자 입력을 기반으로 메타데이터 값을 확보할 수 있는 방법은 다음과 같습니다. 

    * 봇 클라이언트를 통해 사용자로부터 도메인을 입력으로 명시적으로 받습니다. 예를 들어 아래와 같이 대화가 시작되면 사용자로부터 제품 범주를 입력으로 받을 수 있습니다.

      ![메타데이터 입력 받기](../media/qnamaker-tutorial-updates/expand/explicit-metadata-input.png)

    * 봇 컨텍스트를 기반으로 도메인을 암시적으로 식별합니다. 예를 들어 이전 질문이 특정 Surface 제품에 관한 것이면 클라이언트에 의해 컨텍스트로 저장될 수 있습니다. 사용자가 다음 쿼리에서 제품을 명시하지 않으면 봇 컨텍스트를 메타데이터로 GenerateAnswer API에 전달할 수 있습니다.

      ![컨텍스트 전달]( ../media/qnamaker-tutorial-updates/expand/extract-metadata-from-context.png)

    * 사용자 쿼리에서 엔터티를 추출하여 메타데이터 필터에 사용할 도메인을 식별합니다. 엔터티 추출에는 Text Analytics 및 LUIS와 같은 Cognitive Services를 사용할 수 있습니다.

      ![쿼리에서 메타데이터 추출]( ../media/qnamaker-tutorial-updates/expand/extract-metadata-from-query.png)

### <a name="how-large-can-our-knowledge-bases-be"></a>기술 자료 크기는 얼마까지 가능한가요? 

단일 기술 자료에 QnA 쌍을 최대 50,000개까지 추가할 수 있습니다. 데이터의 QnA 쌍이 50,000개를 초과하는 경우 기술 자료를 분할하는 것이 좋습니다.

## <a name="create-a-separate-knowledge-base-for-each-domain"></a>각 도메인에 대한 별도의 기술 자료 만들기

각 도메인에 대해 별도의 기술 자료를 만들고 기술 자료를 별도로 유지 관리할 수도 있습니다. 모든 API는 사용자가 기술 자료 ID를 전달해야 기술 자료를 업데이트하거나 사용자의 질문에 대한 답변을 가져올 수 있습니다.  

서비스가 사용자 질문을 받으면 위에 표시된 GenerateAnswer 엔드포인트의 KB ID를 전달해야 관련 기술 자료에서 답변을 가져올 수 있습니다. 기술 자료 ID는 아래와 같이 **게시** 페이지 섹션에서 찾을 수 있습니다.

>[!div class="mx-imgBorder"]
>![KB ID 가져오기](../media/qnamaker-tutorial-updates/fetch-kb-id.png)

## <a name="create-a-separate-qna-maker-resource-for-each-domain"></a>각 도메인에 대해 별도의 QnA Maker 리소스를 만듭니다.

Microsoft의 마케팅 팀이 Surface 및 Xbox 제품에 대한 사용자 쿼리에 답변하는 고객 지원 봇을 빌드한다고 가정합니다. Surface 및 Xbox의 기술 자료에 액세스하는 별개의 팀을 할당하려고 합니다. 이런 경우 하나는 Surface용, 다른 하나는 Xbox용으로 두 개의 QnA Maker 리소스를 만드는 것이 좋습니다. 하지만 동일한 리소스에 액세스하는 사용자에 대해 별개의 역할을 정의할 수 있습니다. [역할 기반 액세스 제어](../How-To/manage-qna-maker-app.md)에 대해 자세히 알아보세요. 
