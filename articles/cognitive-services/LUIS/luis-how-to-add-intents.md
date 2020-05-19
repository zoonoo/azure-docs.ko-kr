---
title: 의도 추가-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 앱에 의도를 추가하여 동일한 의도가 포함된 질문 또는 명령 그룹을 식별합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: b2cb7494ae3d26fa14bef906b8f5222b9dbc70e1
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584976"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>의도를 추가하여 발화에 대한 사용자 의도 결정

LUIS 앱에 [의도](luis-concept-intent.md)를 추가하여 동일한 의도가 포함된 질문 또는 명령 그룹을 식별합니다.

의도는 맨 위 탐색 모음의 **빌드** 섹션에서 관리된 다음, 왼쪽 패널의 **의도**에서 관리됩니다.

## <a name="add-intent"></a>의도 추가

1. [LUIS 포털](https://www.luis.ai)에 로그인 하 고 **구독** 및 **제작 리소스** 를 선택 하 여 해당 제작 리소스에 할당 된 앱을 확인 합니다.
1. **내 앱** 페이지에서 해당 이름을 선택 하 여 앱을 엽니다.
1. **의도** 페이지에서 **+ 만들기**를 선택 합니다.
1. **새 용도 만들기** 대화 상자에서 의도 이름 (예:)을 입력 `ModifyOrder` 하 고 **완료**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![의도 추가](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    의도에 길이 발언 예제가 필요 합니다.

## <a name="add-an-example-utterance"></a>예제 발화 추가

예제 발언은 사용자 질문 또는 명령의 텍스트 예제입니다. 이러한 의도를 예측할 때 Language Understanding (LUIS)를 학습 하려면 길이 발언 예제를 의도에 추가 해야 합니다. LUIS는 길이 발언의 범위를 이해 하기 시작 하는 데 15 ~ 30의 예제를 요구 합니다. 예 길이 발언를 대량으로 추가 하지 마십시오. 각 utterance는 이미 의도 한 예제와 다른 방식으로 신중 하 게 선택 해야 합니다.

1. 의도 세부 정보 페이지에서 의도 이름 아래에 있는 텍스트 상자와 같이 사용자에 게 필요한 관련 utterance를 입력 한 `Deliver a large cheese pizza` 다음 enter 키를 누릅니다.

    > [!div class="mx-imgBorder"]
    > ![발언이 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    LUIS는 모든 길이 발언를 소문자로 변환 하 고 하이픈 등의 [토큰](luis-language-support.md#tokenization) 주위에 공백을 추가 합니다.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>의도 예측 오류

의도 한 utterance 예제에는 utterance가 현재 있는 것과 학습 중에 결정 된 의도 사이에 의도 예측 오류가 있을 수 있습니다.

Utterance 예측 오류를 찾아 해결 하려면 **자세히 보기**의 **보기** 옵션과 부정확 하 고 명확 하지 않은 **필터** 옵션을 사용 합니다.

![Utterance 예측 오류를 찾아 수정 하려면 필터 옵션을 사용 합니다.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

필터와 보기가 적용 되 고 오류가 있는 예 길이 발언가 있는 경우 예제 utterance 목록에 길이 발언와 문제가 표시 됩니다.

> [!div class="mx-imgBorder"]
> ![! [필터 및 보기가 적용 되 고 길이 발언 예제가 있는 경우 예제 utterance 목록에는 길이 발언와 문제가 표시 됩니다.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

각 행은 예 utterance의 현재 학습 예측 점수를 보여 줍니다. 가장 가까운 경쟁의 점수는 이러한 두 점수의 차이입니다.

### <a name="fixing-intents"></a>의도 수정

의도 예측 오류를 수정 하는 방법을 알아보려면 [요약 대시보드](luis-how-to-use-dashboard.md)를 사용 합니다. 요약 대시보드는 활성 버전의 마지막 학습에 대 한 분석을 제공 하 고 모델을 수정할 수 있는 최고의 제안을 제공 합니다.

## <a name="using-the-contextual-toolbar"></a>상황에 맞는 도구 모음 사용

상황에 맞는 도구 모음은 다른 작업을 제공 합니다.

* 편집 또는 삭제 예제 utterance
* 다른 의도에 utterance 예를 다시 할당 합니다.
* 필터 및 뷰: 필터링 된 엔터티를 포함 하는 길이 발언 표시 하거나 선택적 세부 정보를 표시 합니다.
* 예제 길이 발언 검색

## <a name="train-your-app-after-changing-model-with-intents"></a>의도가 포함된 모델을 변경한 후 앱 학습

의도를 추가, 편집 또는 제거한 후 변경 내용이 엔드포인트 쿼리에 적용되도록 앱을 [학습](luis-how-to-train.md) 및 [게시](luis-how-to-publish-app.md)합니다. 모든 단일 변경을 수행한 후에는 학습 하지 마십시오. 변경 그룹 후에 학습 합니다.

## <a name="next-steps"></a>다음 단계

엔터티와 함께 [예제 발화](luis-how-to-add-example-utterances.md)를 추가하는 방법을 자세히 알아봅니다.
