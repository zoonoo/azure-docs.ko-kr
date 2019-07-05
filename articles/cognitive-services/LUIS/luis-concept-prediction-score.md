---
title: 예측 점수
titleSuffix: Language Understanding - Azure Cognitive Services
description: 예측 점수를 나타내는 LUIS API 서비스 예측 결과 대 한에 신뢰 수준의 사용자 utterance에 기반 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 383ce4c4248f7e21f745f503c74a29cb613983e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814005"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>예측 점수는 의도 및 엔터티에 대한 예측 정확도를 나타냅니다.

예측 점수를 나타내는 LUIS에 예측 결과 대 한 신뢰 수준을 기반으로 사용자 utterance 합니다.

예측 점수는 0(영)과 1(일) 사이입니다. 신뢰도가 높은 LUIS 점수의 예는 0.99입니다. 신뢰도 점수의 예는 0.01입니다. 

|점수 값|신뢰도|
|--|--|
|1|확실한 일치|
|0.99|높은 신뢰도|
|0.01|낮은 신뢰도|
|0|확실한 불일치|

발언의 신뢰도 점수가 낮으면 LUIS는 [LUIS](luis-reference-regions.md) 웹 사이트 **의도** 페이지에서 빨간색 윤곽선이 있는 식별된 **레이블이 지정된 의도**를 사용하여 해당 발언을 강조 표시합니다.

![점수 불일치](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>상위 점수 의도

모든 발화 예측은 상위 점수 의도를 반환합니다. 이 예측은 예측 점수의 숫자를 비교 합니다. 최고 2 점수는 매우 작은 차이점을 가질 수 있습니다. LUIS는 상위 점수를 반환 합니다. 이외의이 근접성을 나타내지 않습니다.  

## <a name="return-prediction-score-for-all-intents"></a>모든 의도의 예측 점수 반환

테스트 또는 엔드포인트 결과에는 모든 의도가 포함될 수 있습니다. 이 구성은 [엔드포인트](https://aka.ms/v1-endpoint-api-docs)에서 `verbose=true` 쿼리 문자열 이름/값 쌍을 사용하여 설정됩니다.

## <a name="review-intents-with-similar-scores"></a>유사한 점수를 가진 의도 검토

올바른 의도가 식별되는지, 다음 식별된 의도의 점수가 여러 발화에 일관되게 상당히 더 낮은지 확인하려면 모든 의도의 점수를 검토하는 것이 좋습니다.

여러 의도의 예측 점수가 가까운 경우에는 발화 컨텍스트에 따라 LUIS가 의도 간에 전환할 수 있습니다. 이 문제를 해결 하려면 계속 보다 다양 한 상황에 맞는 차이 사용 하 여 각 의도 길이 발언을 추가 하거나 채팅 봇 같은 클라이언트 응용 프로그램을 2 상위 의도 처리 하는 방법에 대 한 프로그래밍 방식으로 선택할 수 있습니다.

점수가 매겨진 너무-밀접 하 게 되는 2 인 텐트 비결 교육 인해 반전 될 수 있습니다. 최고 점수가 두 번째로 높은 점수가 될 수 있고 두 번째로 높은 점수가 최고 점수가 될 수 있습니다. 이 상황을 방지 하기 위해 각 단어 선택 및 2 의도 구분할 수 있는 컨텍스트를 사용 하 여 해당 utterance에 대 한 상위 두 의도에 예제 발언을 추가 합니다. 두 가지 의도에는 동일한 수의 예제 발언이 있어야 합니다. 학습으로 인한 반전을 방지할 수 있는 일반적인 분리 기준은 15%의 점수 차이입니다.

비 결정적인 교육을 해제할 수 있습니다 [모든 데이터를 사용 하 여 학습](luis-how-to-train.md#train-with-all-data)합니다.

## <a name="differences-with-predictions-between-different-training-sessions"></a>예측 다양 한 교육 세션 간 차이점

다른 앱에서 동일한 모델을 학습 하 고 점수를 동일 하지 않은 경우 비 결정적인 교육 (의 임의성 훼손 이라는 요소) 이므로이 차이점은입니다. 두 번째로, 둘 이상 의도의 발화가 겹치는 것은 동일 발화의 상위 의도가 학습에 따라 변경될 수 있음을 의미합니다.

채팅 봇 의도에 대 한 신뢰도 나타내는 특정 LUIS 점수에 필요한 경우 점수에 대 한 차이 상위 두 의도 사용 해야 합니다. 이 이런 교육에는 변형에 대 한 유연성을 제공합니다.

## <a name="e-exponent-notation"></a>E(지수) 표기법

예측 점수는 `9.910309E-07`과 같이 0~1 범위 위에 ‘나타나는’ 지수 표기법을 사용합니다.  이 점수는 매우 **작은** 수를 나타냅니다.

|E 표기법 점수 |실제 점수|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>문장 부호

문장 부호는 LUIS에서 별도 토큰입니다. 끝에 마침표를 포함 하지 않는 utterance 및 부분에 마침표를 포함 하는 utterance 별도 두 표현 되며 두 개의 다른 예측을 가져올 수 있습니다. 모델이 [예제 발언](luis-concept-utterance.md)(문장 부호를 포함하거나 포함하지 않음) 또는 [패턴](luis-concept-patterns.md)(`I am applying for the {Job} position[.]` 특수 구문을 사용하여 문장 부호를 무시하는 것이 더 쉬움)에서 문장 부호를 처리하는지 확인합니다.

## <a name="next-steps"></a>다음 단계

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.
