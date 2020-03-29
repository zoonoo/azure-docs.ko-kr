---
title: 예측 점수 - LUIS
titleSuffix: Azure Cognitive Services
description: 예측 점수는 사용자 발언을 기반으로 LUIS API 서비스가 예측 결과에 대해 가지고 있는 신뢰도를 나타냅니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: b360bc82b80e834492b524acc5c4535b0409eda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280819"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>예측 점수는 의도 및 엔터티에 대한 예측 정확도를 나타냅니다.

예측 점수는 LUIS가 사용자 발언의 예측 결과에 대해 가지고 있는 신뢰도를 나타냅니다.

예측 점수는 0(영)과 1(일) 사이입니다. 신뢰도가 높은 LUIS 점수의 예는 0.99입니다. 신뢰도 점수의 예는 0.01입니다. 

|점수 값|신뢰도|
|--|--|
|1|확실한 일치|
|0.99|높은 신뢰도|
|0.01|낮은 신뢰도|
|0|확실한 불일치|

## <a name="top-scoring-intent"></a>상위 점수 의도

모든 발화 예측은 상위 점수 의도를 반환합니다. 이 예측은 예측 점수의 수치 비교입니다. 

## <a name="proximity-of-scores-to-each-other"></a>서로 점수의 근접성

상위 2개의 점수는 그(것)들 사이 아주 작은 다름이 있을 수 있습니다. LUIS는 최고 점수를 반환하는 것 이외에 는 이 근접성을 나타내지 않습니다.  

## <a name="return-prediction-score-for-all-intents"></a>모든 의도의 예측 점수 반환

테스트 또는 엔드포인트 결과에는 모든 의도가 포함될 수 있습니다. 이 구성은 올바른 쿼리 문자열 이름/값 쌍을 사용하여 끝점에서 설정됩니다.

|예측 API|쿼리 문자열 이름|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>유사한 점수를 가진 의도 검토

모든 의도에 대한 점수를 검토하면 올바른 의도가 식별될 뿐만 아니라 다음에 식별된 의도의 점수가 발언에 대해 현저히 낮고 일관되게 낮은지 확인하는 좋은 방법입니다.

여러 의도의 예측 점수가 가까운 경우에는 발화 컨텍스트에 따라 LUIS가 의도 간에 전환할 수 있습니다. 이 상황을 해결 하려면 다양 한 컨텍스트 차이 각 의도 발언을 계속 추가 하거나 채팅 봇 등 클라이언트 응용 프로그램을 가질 수 있습니다., 2 상위 의도를 처리 하는 방법에 대 한 프로그래밍 방식으로 선택.

너무 밀접하게 득점된 2개의 의도는 **비결정적 교육으로**인해 반전될 수 있습니다. 최고 점수가 두 번째로 높은 점수가 될 수 있고 두 번째로 높은 점수가 최고 점수가 될 수 있습니다. 이러한 상황을 방지하기 위해 2개의 의도를 구별하는 단어 선택 및 컨텍스트를 사용하여 해당 발언에 대한 상위 두 의도 각각에 예제 발언을 추가합니다. 두 가지 의도에는 동일한 수의 예제 발언이 있어야 합니다. 학습으로 인한 반전을 방지할 수 있는 일반적인 분리 기준은 15%의 점수 차이입니다.

[모든 데이터로 학습하여](luis-how-to-train.md#train-with-all-data) **비결정적 교육을** 끌 수 있습니다.

## <a name="differences-with-predictions-between-different-training-sessions"></a>서로 다른 교육 세션 간의 예측과의 차이

다른 앱에서 동일한 모델을 학습하고 점수가 동일하지 않은 경우 이러한 차이는 **비결정적** 교육(임의성 요소)이 있기 때문입니다. 두 번째로, 둘 이상 의도의 발화가 겹치는 것은 동일 발화의 상위 의도가 학습에 따라 변경될 수 있음을 의미합니다.

챗봇에서 의도에 대한 자신감을 나타내기 위해 특정 LUIS 점수가 필요한 경우 상위 두 의도 간의 점수 차이를 사용해야 합니다. 이러한 상황은 교육의 변형에 대한 유연성을 제공합니다.

[모든 데이터로 학습하여](luis-how-to-train.md#train-with-all-data) **비결정적 교육을** 끌 수 있습니다.

## <a name="e-exponent-notation"></a>E(지수) 표기법

예측 점수는 `9.910309E-07`과 같이 0~1 범위 위에 ‘나타나는’ 지수 표기법을 사용합니다.__ 이 점수는 매우 **작은** 수를 나타냅니다.

|E 표기법 점수 |실제 점수|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>문장 부호

문장 부호를 사용하거나 무시하는 방법에 대해 [자세히 알아보세요.](luis-concept-utterance.md#punctuation-marks) 

## <a name="next-steps"></a>다음 단계

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.
