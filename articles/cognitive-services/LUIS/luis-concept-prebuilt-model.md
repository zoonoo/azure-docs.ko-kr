---
title: 미리 작성된 모델
titleSuffix: Language Understanding - Azure Cognitive Services
description: 미리 빌드된 모델은 도메인, 의도, 발언 및 엔터티를 제공합니다. 미리 빌드된 도메인을 사용하여 앱을 시작할 수도 있고, 나중에 관련 도메인을 앱에 추가할 수도 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 5d2ea9d971eff22ddeed4122c9697ca3096697b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813888"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>미리 빌드된 도메인, 의도 및 엔터티 모델

미리 빌드된 모델은 도메인, 의도, 발언 및 엔터티를 제공합니다. 미리 빌드된 도메인을 사용하여 앱을 시작할 수도 있고, 나중에 관련 도메인을 앱에 추가할 수도 있습니다. 

## <a name="types-of-prebuilt-models"></a>미리 빌드된 모델 형식

LUIS는 3가지 형식의 미리 빌드된 모델을 제공합니다. 각 모델을 언제든지 앱에 추가할 수 있습니다. 

|모델 형식|포함|
|--|--|
|Domain|의도, 발언, 엔터티|
|의도|의도, 발언|
|엔터티|엔터티만| 

## <a name="prebuilt-domains"></a>미리 빌드된 도메인

LUIS(Language Understanding)는 클라이언트 애플리케이션의 일반 범주 또는 도메인에 함께 사용되는 [의도](luis-how-to-add-intents.md) 및 [엔터티](luis-concept-entity-types.md)의 미리 빌드된 집합인 ‘미리 빌드된 도메인’을 제공합니다.  

미리 빌드된 도메인은 LUIS 앱에 추가되도록 교육 및 준비됩니다. 미리 빌드된 도메인의 의도와 엔터티를 앱에 추가한 후 완전히 사용자 지정할 수 있습니다. 

전체 미리 빌드된 도메인을 사용자 지정하는 것부터 시작할 경우에는 앱에서 사용할 필요가 없는 의도와 엔터티를 삭제합니다. 이미 미리 빌드된 도메인이 제공하는 집합에 일부 의도나 엔터티를 추가할 수도 있습니다. 예를 들어, 스포츠 이벤트 앱에 대해 **Events** 미리 빌드된 도메인을 사용하는 경우에는 스포츠 팀에 대한 엔터티를 추가할 수 있습니다. LUIS에 [발화 제공](luis-how-to-add-example-utterances.md)을 시작할 경우에는 앱에 관련된 조건을 포함합니다. LUIS는 미리 빌드된 도메인의 의도와 엔터티를 인식하고 앱의 요구 사항에 맞게 조정하도록 학습됩니다. 

> [!TIP]
> 미리 빌드된 도메인의 의도와 엔터티는 함께 사용할 때 가장 성능이 좋습니다. 가능하면 동일한 도메인의 의도와 엔터티를 결합하는 것이 좋습니다.
> 유틸리티 미리 빌드된 도메인은 모든 도메인에 사용하도록 사용자 지정할 수 있는 의도를 제공합니다. 예를 들어, `Utilities.Repeat`를 앱에 추가하고 사용자가 애플리케이션에서 반복하려는 모든 작업을 인식하도록 학습시킬 수 있습니다. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>미리 빌드된 도메인 의도의 동작 변경

미리 빌드된 도메인에 포함되는 의도가 LUIS 앱에 포함하려는 의도와 유사함을 알 수 있지만 해당 의도가 다르게 동작하도록 합니다. 예를 들어, **Places** 미리 빌드된 도메인은 음식점을 예약하기 위한 `MakeReservation` 의도를 제공하지만, 앱에서는 해당 의도를 사용하여 호텔을 예약하도록 합니다. 이 경우 호텔 예약에 대한 발화를 LUIS에 제공하고 `MakeReservation` 의도로 레이블을 지정하여 해당 의도의 동작을 수정할 수 있으므로, LUIS는 호텔 예약 요청에서 `MakeReservation` 의도를 인식하도록 다시 학습될 수 있습니다.

[미리 빌드된 도메인 참조](./luis-reference-prebuilt-domains.md)에서 미리 빌드된 도메인의 전체 목록을 찾을 수 있습니다.

## <a name="prebuilt-intents"></a>미리 빌드된 의도

LUIS는 미리 빌드된 의도 및 발언을 제공합니다. 전체 도메인을 추가하지 않고 의도를 추가할 수 있습니다. 의도 추가는 의도 및 해당 발언을 추가하는 프로세스입니다. 의도 이름과 발언 목록 둘 다 수정할 수 있습니다.  

## <a name="prebuilt-entities"></a>미리 빌드된 엔터티

LUIS에는 날짜, 시간, 숫자, 측정값 및 통화 등, 일반적인 정보 유형을 인식하기 위한 미리 빌드된 엔터티 집합이 포함되어 있습니다. 미리 빌드된 엔터티 지원은 LUIS 앱의 문화권에 따라 다릅니다. 문화권별 지원을 비롯하여 LUIS에서 지원하는 미리 빌드된 엔터티의 전체 목록에 대해서는 [미리 빌드된 엔터티 참조](./luis-reference-prebuilt-entities.md)를 참조하세요.

미리 빌드된 엔터티가 애플리케이션에 포함되어 있으면, 게시된 애플리케이션에 해당 예측이 포함됩니다. 미리 빌드된 엔터티의 동작은 미리 학습되며 수정할 수 **없습니다**. 다음 단계에 따라 미리 빌드된 엔터티가 작동하는 방식을 확인하세요.

> [!NOTE]
> **builtin.datetime**은 더 이상 사용되지 않습니다. 이 엔터티는 모호한 날짜 및 시간의 인식을 개선하고 날짜 및 시간 범위를 인식할 수 있도록 하는 [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md)로 대체됩니다.

## <a name="next-steps"></a>다음 단계

[미리 빌드된 엔터티를 앱에 추가](luis-prebuilt-entities.md)하는 방법을 알아봅니다.
