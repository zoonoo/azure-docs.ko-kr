---
title: 미리 빌드된 모델-LUIS
titleSuffix: Azure Cognitive Services
description: 미리 빌드된 모델은 도메인, 의도, 발언 및 엔터티를 제공합니다. 미리 빌드된 도메인을 사용하여 앱을 시작할 수도 있고, 나중에 관련 도메인을 앱에 추가할 수도 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: e0b5b95a7524b60a7c3367035a15a7158fa7908a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280834"
---
# <a name="prebuilt-models"></a>미리 작성된 모델

미리 빌드된 모델은 도메인, 의도, 발언 및 엔터티를 제공합니다. 미리 빌드된 모델을 사용 하 여 앱을 시작 하거나 나중에 앱에 관련 모델을 추가할 수 있습니다. 

## <a name="types-of-prebuilt-models"></a>미리 빌드된 모델 형식

LUIS는 세 가지 유형의 미리 작성 된 모델을 제공 합니다. 각 모델을 언제든지 앱에 추가할 수 있습니다. 

|모델 형식|포함|
|--|--|
|[도메인](luis-reference-prebuilt-domains.md)|의도, 발언, 엔터티|
|의도|의도, 발언|
|[엔터티](luis-reference-prebuilt-entities.md)|엔터티만| 

## <a name="prebuilt-domains"></a>미리 빌드된 도메인

LUIS (Language Understanding)는 미리 학습 된 [의도](luis-how-to-add-intents.md) 된 모델 및 도메인 또는 클라이언트 응용 프로그램의 공통 범주에 대해 함께 작동 하는 [엔터티의](luis-concept-entity-types.md) 미리 학습 된 *도메인*을 제공 합니다. 

미리 빌드된 도메인은 LUIS 앱에 추가되도록 교육 및 준비됩니다. 미리 작성 된 도메인의 의도 및 엔터티는 앱에 추가 된 후에는 완전히 사용자 지정할 수 있습니다. 

> [!TIP]
> 미리 빌드된 도메인의 의도와 엔터티는 함께 사용할 때 가장 성능이 좋습니다. 가능하면 동일한 도메인의 의도와 엔터티를 결합하는 것이 좋습니다.
> 유틸리티 미리 빌드된 도메인은 모든 도메인에 사용하도록 사용자 지정할 수 있는 의도를 제공합니다. 예를 들어, `Utilities.Repeat`를 앱에 추가하고 사용자가 애플리케이션에서 반복하려는 모든 작업을 인식하도록 학습시킬 수 있습니다. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>미리 빌드된 도메인 의도의 동작 변경

미리 빌드된 도메인에 포함되는 의도가 LUIS 앱에 포함하려는 의도와 유사함을 알 수 있지만 해당 의도가 다르게 동작하도록 합니다. 예를 들어, **미리 작성 된 도메인** 은 식당 예약을 만들기 위한 `MakeReservation` 의도를 제공 하지만 앱이 해당 의도를 사용 하 여 호텔 예약을 수행 하 게 하려고 합니다. 이 경우 호텔 예약 만들기에 대 한 길이 발언 예제를 추가 하 고 앱을 다시 학습 하 여 의도의 동작을 수정할 수 있습니다. 

[미리 빌드된 도메인 참조](./luis-reference-prebuilt-domains.md)에서 미리 빌드된 도메인의 전체 목록을 찾을 수 있습니다.

## <a name="prebuilt-intents"></a>미리 빌드된 의도

LUIS는 미리 작성 된 각 도메인에 대해 미리 작성 된 의도 및 해당 길이 발언을 제공 합니다. 전체 도메인을 추가하지 않고 의도를 추가할 수 있습니다. 의도를 추가 하는 것은 의도와 해당 길이 발언를 앱에 추가 하는 프로세스입니다. 의도 이름과 발언 목록 둘 다 수정할 수 있습니다.  

## <a name="prebuilt-entities"></a>미리 작성된 엔터티

LUIS에는 날짜, 시간, 숫자, 측정값 및 통화 등, 일반적인 정보 유형을 인식하기 위한 미리 빌드된 엔터티 집합이 포함되어 있습니다. 미리 빌드된 엔터티 지원은 LUIS 앱의 문화권에 따라 다릅니다. 문화권별 지원을 비롯하여 LUIS에서 지원하는 미리 빌드된 엔터티의 전체 목록에 대해서는 [미리 빌드된 엔터티 참조](./luis-reference-prebuilt-entities.md)를 참조하세요.

미리 빌드된 엔터티가 애플리케이션에 포함되어 있으면, 게시된 애플리케이션에 해당 예측이 포함됩니다. 미리 빌드된 엔터티의 동작은 미리 학습되며 수정할 수 **없습니다**. 

> [!NOTE]
> **builtin.datetime**은 더 이상 사용되지 않습니다. 이 엔터티는 모호한 날짜 및 시간의 인식을 개선하고 날짜 및 시간 범위를 인식할 수 있도록 하는 [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md)로 대체됩니다.

## <a name="next-steps"></a>다음 단계

[미리 빌드된 엔터티를 앱에 추가](luis-prebuilt-entities.md)하는 방법을 알아봅니다.
