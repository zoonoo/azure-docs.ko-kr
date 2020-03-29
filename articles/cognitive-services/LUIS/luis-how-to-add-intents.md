---
title: 의도 추가 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 앱에 의도를 추가하여 동일한 의도가 포함된 질문 또는 명령 그룹을 식별합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 66a3350dee60772ce706af8995179dcd8c485b64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904292"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>의도를 추가하여 발화에 대한 사용자 의도 결정

LUIS 앱에 [의도](luis-concept-intent.md)를 추가하여 동일한 의도가 포함된 질문 또는 명령 그룹을 식별합니다. 

의도는 맨 위 탐색 모음의 **빌드** 섹션에서 관리된 다음, 왼쪽 패널의 **의도**에서 관리됩니다. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>의도 추가

1. LUIS [미리 보기 포털에서](https://preview.luis.ai)의도를 보려면 **빌드를** 선택합니다. 
1. 의도 페이지에서 **+ 만들기를** **선택합니다.**
1. 새 의도 대화 상자 **만들기에서** 의도 이름을 `ModifyOrder`입력하고 완료 를 **선택합니다.**

    > [!div class="mx-imgBorder"]
    > ![의도 추가](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    의도는 예제 발언이 필요합니다.

## <a name="add-an-example-utterance"></a>예제 발화 추가

예제 발언은 사용자 질문 또는 명령의 텍스트 예제입니다. 이 의도를 예측할 때 언어 이해(LUIS)를 가르치려면 의도에 예제 발언을 추가해야 합니다. LUIS는 의도를 이해하기 위해 15~30개의 예제 발언 범위가 필요합니다. 예제 발언을 일괄으로 추가하지 마십시오. 각 발언은 의도에 이미 있는 예제와 어떻게 다른지 신중하게 선택해야 합니다. 

1. 의도 세부 정보 페이지에서 의도 이름 아래의 텍스트 상자에 `Deliver a large cheese pizza` 사용자가 기대하는 관련 발언을 입력한 다음 Enter를 누릅니다.
 
    > [!div class="mx-imgBorder"]
    > ![발언이 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS는 모든 발언을 소문자로 변환하고 하이픈과 같은 토큰 주위에 [공백을 추가합니다.](luis-language-support.md#tokenization)

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>의도 예측 오류 

의도의 예제 발언에는 예제 발언이 현재 사용 중인 의도와 학습 중에 결정된 의도 사이에 의도 예측 오류가 있을 수 있습니다. 

발화 예측 오류를 찾아 수정하려면 잘못 및 불분명의 **필터** 옵션을 **상세 뷰의** **보기** 옵션과 함께 사용합니다. 

![발언 예측 오류를 찾아 수정하려면 필터 옵션을 사용합니다.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

필터와 뷰가 적용되고 오류가 있는 예제 발언이 있는 경우 예제 발언 목록에는 발언및 문제가 표시됩니다.

> [!div class="mx-imgBorder"]
> ![! [필터와 뷰가 적용되고 오류가 있는 예제 발언이 있는 경우 예제 발언 목록에는 발언과 문제가 표시됩니다.] (./미디어/루이스-투-추가 의도/오류-인-발언.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

각 행은 예제 발언에 대한 현재 교육의 예측 점수, 가장 가까운 라이벌의 점수를 보여 주며, 이는 이 두 점수의 차이입니다. 

### <a name="fixing-intents"></a>의도 수정

의도 예측 오류를 수정하는 방법을 알아보려면 [요약 대시보드를](luis-how-to-use-dashboard.md)사용합니다. 요약 대시보드는 활성 버전의 마지막 학습에 대한 분석을 제공하고 모델을 수정하기 위한 주요 제안을 제공합니다.  

## <a name="using-the-contextual-toolbar"></a>상황에 맞는 도구 모음 사용

컨텍스트 도구 모음은 다음과 같은 다른 작업을 제공합니다.

* 예제 발화 편집 또는 삭제
* 예제 발언을 다른 의도로 재할당
* 필터 및 뷰: 필터링된 엔터티가 포함된 발언만 표시하거나 선택적 세부 정보 보기
* 예제 발언을 통한 검색

## <a name="train-your-app-after-changing-model-with-intents"></a>의도가 포함된 모델을 변경한 후 앱 학습

의도를 추가, 편집 또는 제거한 후 변경 내용이 엔드포인트 쿼리에 적용되도록 앱을 [학습](luis-how-to-train.md) 및 [게시](luis-how-to-publish-app.md)합니다. 모든 변경 후 훈련하지 마십시오. 변경 그룹 후 훈련. 

## <a name="next-steps"></a>다음 단계

엔터티와 함께 [예제 발화](luis-how-to-add-example-utterances.md)를 추가하는 방법을 자세히 알아봅니다. 
