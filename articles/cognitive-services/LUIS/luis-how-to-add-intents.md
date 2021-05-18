---
title: 의도 추가 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 앱에 의도를 추가하여 동일한 의도가 포함된 질문 또는 명령 그룹을 식별합니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.service: cognitive-services
ms.openlocfilehash: f2401c032f0fc90024e0049fad5f696b8a184544
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018942"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>의도를 추가하여 발화에 대한 사용자 의도 결정

LUIS 앱에 [의도](luis-concept-intent.md)를 추가하여 동일한 의도가 포함된 질문 또는 명령 그룹을 식별합니다.

LUIS 포털에서 의도는 맨 위 탐색 모음의 **빌드** 섹션에서 관리된 다음, 왼쪽 패널의 **의도** 에서 관리됩니다.

## <a name="add-an-intent-to-your-app"></a>앱에 의도 추가

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. **의도** 페이지에서 **+ 만들기** 를 선택합니다.
1. **새 의도 만들기** 대화 상자에서 의도 이름을 입력하고(예: `ModifyOrder`) **완료** 를 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![의도 추가](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    게시된 예측 엔드포인트에서 발화를 예측하려면 의도에 [예제 발화](luis-concept-utterance.md)가 필요합니다.

## <a name="add-an-example-utterance"></a>예제 발화 추가

예제 발언은 사용자 질문 또는 명령의 텍스트 예제입니다. LUIS(Language Understanding)를 학습시키기 위해 이 의도를 예측하려면 의도에 예제 발화를 추가해야 합니다. LUIS는 의도를 이해하기 시작하는 데 15~30개의 예제 발화가 필요합니다. 예제 발화를 대량으로 추가하지 않습니다. 각 발화는 이미 의도에 있는 예와 다른 방식으로 신중하게 선택해야 합니다.

1. 의도 세부 정보 페이지에서 의도 이름 아래의 텍스트 상자에 사용자로부터 예상하는 관련 발화(예: `Deliver a large cheese pizza`)를 입력하고 Enter 키를 누릅니다.

    > [!div class="mx-imgBorder"]
    > ![발언이 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    LUIS는 모든 발화를 소문자로 변환하고 하이픈과 같은 [토큰](luis-language-support.md#tokenization) 주위에 공백을 추가합니다.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>의도 예측 오류

의도 예측 오류는 의도에 대해 학습된 앱으로 발화가 예측되지 않을 때 결정됩니다.

1. 발화 예측 오류를 찾아 수정하려면 부정확하고 명확하지 않은 항목에 대한 **필터** 옵션을 사용합니다.

    > [!div class="mx-imgBorder"]
    > ![발화 예측 오류를 찾아 수정하려면 필터 옵션을 사용합니다.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

1. 의도 세부 정보 페이지에서 점수 값을 표시하려면 **보기** 옵션 메뉴에서 **자세한 의도 점수 표시** 를 선택합니다.

    필터와 보기가 적용되고 오류가 있는 예제 발화가 있는 경우 예제 발화 목록에 발화와 문제가 표시됩니다.

각 행에 예제 발화에 대한 현재 학습의 예측 점수, 가장 가까운 경쟁 상대의 점수와 이러한 두 점수의 차이가 표시됩니다.

### <a name="fixing-intents"></a>의도 수정

의도 예측 오류를 수정하는 방법을 알아보려면 [요약 대시보드](luis-how-to-use-dashboard.md)를 사용합니다. 요약 대시보드는 활성 버전의 마지막 학습에 대한 분석을 제공하고 모델을 수정할 수 있는 최고의 제안을 제공합니다.

## <a name="using-the-contextual-toolbar"></a>상황에 맞는 도구 모음 사용

상황에 맞는 도구 모음에서 다른 작업을 제공합니다.

* 예제 발화 편집 또는 삭제
* 다른 의도에 예제 발화를 다시 할당
* 필터링 및 보기: 필터링된 엔터티를 포함하는 발화만 표시하거나 선택적 세부 정보를 표시합니다.
* 예제 발화 검색

## <a name="train-your-app-after-changing-model-with-intents"></a>의도가 포함된 모델을 변경한 후 앱 학습

의도를 추가, 편집 또는 제거한 후 변경 내용이 엔드포인트 쿼리에 적용되도록 앱을 [학습](luis-how-to-train.md) 및 [게시](luis-how-to-publish-app.md)합니다. 변경할 때마다 학습하지는 않습니다. 변경 그룹을 수행한 후 학습합니다.

## <a name="next-steps"></a>다음 단계

엔터티와 함께 [예제 발화](./luis-how-to-add-entities.md)를 추가하는 방법을 자세히 알아봅니다.