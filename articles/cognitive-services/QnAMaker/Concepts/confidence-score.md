---
title: 신뢰도 점수 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 신뢰도 점수는 응답이 지정된 사용자 쿼리에 올바르게 일치한다는 신뢰도를 나타냅니다.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/05/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 97c44c9285ec7a29827361111599db37bc6a86f3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59282579"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>QnA Maker 기술 자료의 신뢰도 점수
사용자 쿼리가 기술 자료와 일치하면 QnA Maker는 신뢰도 점수와 함께 관련 응답을 반환합니다. 이 점수는 응답이 지정된 사용자 쿼리에 올바르게 일치한다는 신뢰도를 나타냅니다. 

신뢰도 점수는 0과 100 사이의 숫자입니다. 점수가 100이면 정확히 일치할 가능성이 높지만 점수가 0이면 일치하는 응답을 찾지 못한 것입니다. 이 점수가 높을수록 응답의 신뢰도가 높습니다. 지정된 쿼리에 대해 여러 응답이 반환될 수 있습니다. 이 경우 응답은 높은 신뢰도 점수부터 반환됩니다.

아래 예제에서는 2개 질문을 포함하는 QnA 엔터티 1개를 볼 수 있습니다. 


![샘플 QnA 쌍](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

위의 예제에서는 다른 유형의 사용자 쿼리에 대해 아래의 샘플 점수 범위와 같은 점수를 예측할 수 있습니다.


![순위 지정 점수 범위](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


다음 표에서는 지정된 점수와 연결된 일반적인 신뢰도를 보여 줍니다.

|점수 값|점수 의미|예제 쿼리|
|--|--|--|
|90 - 100|사용자 쿼리와 KB 질문이 거의 정확히 일치합니다.|“내 변경 내용이 게시 후에 KB에서 업데이트되지 않았습니다.”|
|> 70|높은 신뢰도 - 일반적으로 사용자의 쿼리에 완벽하게 좋은 응답|“내 기술 자료를 게시했지만 업데이트되지 않았습니다.”|
|50 - 70|중간 신뢰도 - 일반적으로 사용자 쿼리의 기본 의도에 적절하게 맞는 응답|“내 기술 자료를 게시하기 전에 업데이트를 저장해야 하나요?”|
|30 - 50|낮은 신뢰도 - 일반적으로 사용자의 의도에 부분적으로 부합되는 관련된 응답|“저장 및 학습이란 무엇인가요?”|
|< 30|매우 낮은 신뢰도 - 일반적으로 사용자 쿼리에 대한 응답이 아니며 일치하는 일부 단어나 구 포함 |“내 기술 자료에서 동의어를 추가할 수 있는 위치는 어디인가요?”|
|0|일치하는 부분이 없으므로 응답은 반환되지 않습니다.|“서비스 비용은 얼마나 되나요?”|

## <a name="choose-a-score-threshold"></a>점수 임계값 선택
위의 표에서는 대부분의 기술 자료에서 예상되는 점수를 보여 줍니다. 그러나 모든 기술 자료가 다르며 단어 유형, 의도 및 목적도 다르므로, 테스트를 통해 본인에게 가장 적합한 임계값을 선택하는 것이 좋습니다. 대부분의 기술 자료에 적합한 기본 및 권장 임계값은 **50**입니다.

임계값을 선택할 때 정확도와 적용 범위 간에 균형을 맞추고 요구 사항에 맞게 임계값을 조정해야 합니다.

- 사용자 시나리오에서 **정확도**(또는 정밀도)가 더 중요한 경우 임계값을 늘립니다. 이러한 방식으로 진행하면 응답을 반환할 때마다 훨씬 더 신뢰할 수 있는 사례가 되고, 사용자가 원하는 응답이 될 가능성이 높아집니다. 이 경우 더 많은 질문에 응답이 제공되지 않을 수 있습니다. *예제:* 임계값 **70**을 지정하면 “저장 및 학습이란 무엇인가요?”와 같은 모호한 예제는 누락될 수 있습니다.

- **적용 범위**(또는 재현율)가 좀 더 중요하고 가능한 한 많은 질문에 응답하려는 경우(사용자 질문에 부분적으로만 연관되어 있더라도) 임계값을 낮춥니다. 응답이 사용자의 실제 쿼리에 대한 응답이 아니지만 관련된 다른 응답을 제공하는 경우가 더 많을 수 있습니다. *예제:* 임계값 **30**을 지정하면 “내 기술 자료를 어디에서 편집할 수 있나요?”와 같은 쿼리에 대해 관련성이 높은 응답을 제공하지 못할 수 있으며 위와 같이 응답할 수 있습니다.

> [!NOTE]
> QnA Maker의 최신 버전에는 점수 매기기 논리에 대한 개선 사항이 포함되며 임계값에 영향을 줄 수 있습니다. 언제든지 서비스를 업데이트하여 필요한 경우 임계값을 테스트하고 조정해야 합니다. [여기](https://www.qnamaker.ai/UserSettings)에서 QnA 서비스 버전을 확인할 수 있습니다. 최신 업데이트를 가져오는 방법은 [여기](../How-To/troubleshooting-runtime.md)를 참조하세요.

## <a name="improve-confidence-scores"></a>신뢰도 점수 개선
사용자 쿼리에 대한 특정 응답의 신뢰도 점수를 향상하기 위해 사용자 쿼리를 해당 응답의 대체 질문으로 기술 자료에 추가할 수 있습니다. 대/소문자를 구분하지 않는 [단어 변경](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd)을 사용하여 KB의 키워드에 동의어를 추가할 수도 있습니다.


## <a name="similar-confidence-scores"></a>유사한 신뢰도 점수
여러 응답의 신뢰도 점수가 유사한 경우 쿼리가 너무 일반적이어서 여러 답변과 동일한 가능성으로 일치할 수 있습니다. 모든 QnA 엔터티에 고유한 의도가 있도록 QnA를 더 효과적으로 구성합니다.


## <a name="confidence-score-differences"></a>신뢰도 점수 차이
콘텐츠가 동일하더라도 기술 자료의 테스트 버전과 게시된 버전 간에 답변의 신뢰도 점수가 사소하게 변경될 수 있습니다. 이는 테스트 및 게시된 기술 자료 콘텐츠가 서로 다른 Azure Search 인덱스에 있기 때문입니다. 기술 자료를 게시하면 기술 자료의 QnA 콘텐츠가 테스트 인덱스에서 Azure Search의 프로덕션 인덱스로 이동합니다. [게시](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) 작업의 작동 방식을 알아보세요.

다른 Azure 지역에 기술 자료가 있는 경우 각 Azure 지역에서는 자체 Azure Search 인덱스를 사용합니다. 다른 인덱스가 사용되므로 점수가 정확하게 일치하지 않습니다. 


## <a name="no-match-found"></a>일치하는 항목 없음
순위 지정에서 적절한 일치 항목을 찾지 못하면 신뢰도 점수 0.0 또는 “없음”이 반환되고 기본 응답은 “No good match found in the KB”(KB에 적절한 일치 항목이 없습니다.)입니다. 이 설정을 재정의할 수 있습니다 [기본 응답](#change-default-answer) 봇 또는 응용 프로그램 코드는 끝점을 호출 합니다. 또는 Azure에서 재정의 응답을 설정할 수도 있으며, 이 경우 특정 QnA Maker 서비스에 배포된 모든 기술 자료의 기본값이 변경됩니다.

## <a name="change-default-answer"></a>기본 답변 변경

1. [Azure Portal](https://portal.azure.com)로 이동한 다음, 직접 만든 QnA Maker 서비스를 나타내는 리소스 그룹으로 이동합니다.

2. **App Service**를 클릭하여 엽니다.

    ![Azure Portal에서 QnA Maker에 대한 App service에 액세스](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. **애플리케이션 설정**을 클릭하고 **DefaultAnswer** 필드를 원하는 기본 응답으로 편집합니다. **저장**을 클릭합니다.

    ![애플리케이션 설정을 선택한 다음, QnA Maker에 대한 DefaultAnswer 편집](../media/qnamaker-concepts-confidencescore/change-response.png)

4. App Service 다시 시작

    ![DefaultAnswer를 변경한 후 QnA Maker appservice를 다시 시작](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [지원되는 데이터 원본](./data-sources-supported.md)

