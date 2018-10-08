---
title: 신뢰도 점수 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 신뢰도 점수는 사용자 질문과 반환된 응답 간의 일치 정도를 나타냅니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 33da5cf5724b8314ce813f12eb077d9a15ec1b2a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041526"
---
# <a name="confidence-score"></a>신뢰도 점수

신뢰도 점수는 사용자 질문과 반환된 응답 간의 일치 정도를 나타냅니다.

사용자 쿼리가 기술 자료 콘텐츠와 일치하면 둘 이상의 응답이 반환될 수 있습니다. 응답은 신뢰도 점수의 내림차순으로 반환됩니다.

신뢰도 점수는 0에서 100 사이입니다.

|점수 값|신뢰도|
|--|--|
|100|사용자 쿼리와 KB 질문이 정확히 일치합니다.|
|90|신뢰도 높음 - 대부분의 단어가 일치합니다.|
|40-60|신뢰도 양호 - 중요 단어가 일치합니다.|
|10|신뢰도 낮음 - 중요 단어가 일치하지 않습니다.|
|0|일치하는 단어가 없습니다.|


## <a name="similar-confidence-scores"></a>유사한 신뢰도 점수
여러 응답의 신뢰도 점수가 유사한 경우 쿼리가 너무 일반적이어서 여러 답변과 동일한 가능성으로 일치할 수 있습니다. 모든 QnA 엔터티에 고유한 의도가 있도록 QnA를 더 효과적으로 구성합니다.


## <a name="improving-confidence-scores"></a>신뢰도 점수 개선
사용자 쿼리에 대한 특정 응답의 신뢰도 점수를 향상하기 위해 사용자 쿼리를 해당 응답의 대체 질문으로 기술 자료에 추가할 수 있습니다.
   
## <a name="confidence-score-differences"></a>신뢰도 점수 차이
콘텐츠가 동일하더라도 기술 자료의 테스트 버전과 게시된 버전 간에 답변의 신뢰도 점수가 사소하게 변경될 수 있습니다. 이는 테스트 및 게시된 기술 자료 콘텐츠가 서로 다른 Azure Search 인덱스에 있기 때문입니다.

[게시](../How-To/publish-knowledge-base.md) 작업의 작동 방식을 보려면 여기를 참조하세요.


## <a name="no-match-found"></a>일치하는 항목 없음
순위 지정에서 적절한 일치 항목을 찾지 못하면 신뢰도 점수 0.0 또는 “없음”이 반환되고 기본 응답은 “No good match found in the KB”(KB에 적절한 일치 항목이 없습니다.)입니다. 엔드포인트를 호출하는 봇 또는 응용 프로그램 코드에서 이 기본 응답을 재정의할 수 있습니다. 또는 Azure에서 재정의 응답을 설정할 수도 있으며, 이 경우 특정 QnA Maker 서비스에 배포된 모든 기술 자료의 기본값이 변경됩니다.

1. [Azure Portal](http://portal.azure.com)로 이동한 다음, 직접 만든 QnA Maker 서비스를 나타내는 리소스 그룹으로 이동합니다.

2. **App Service**를 클릭하여 엽니다.

    ![App Service 액세스](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. **응용 프로그램 설정**을 클릭하고 **DefaultAnswer** 필드를 원하는 기본 응답으로 편집합니다. **저장**을 클릭합니다.

    ![기본 응답 변경](../media/qnamaker-concepts-confidencescore/change-response.png)

4. App Service 다시 시작

    ![QnA Maker appservice 다시 시작](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [지원되는 데이터 원본](./data-sources-supported.md)

## <a name="see-also"></a>참고 항목 

[QnA Maker 개요](../Overview/overview.md)
