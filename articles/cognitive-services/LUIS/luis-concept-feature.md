---
title: 특징 - 루이스
titleSuffix: Azure Cognitive Services
description: 언어 모델에 기능을 추가하여 레이블을 지정하거나 분류하려는 입력을 인식하는 방법에 대한 힌트를 제공합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 5b8257e24cf52d01be8065d97db17fd685aa316d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531901"
---
# <a name="machine-learned-features"></a>기계 학습 기능

기계 학습에서 _기능은_ 시스템에서 학습하는 & 관찰하는 데이터의 구별되는 특성 또는 속성입니다. LUIS(언어 이해)에서 기능은 의도 및 엔터티에 대해 중요한 사항을 설명하고 설명합니다.

LUIS [미리 보기 포털에서](https://preview.luis.ai)기능은 의도 또는 엔터티를 _설명하는_ 데 사용되기 때문에 _설명자입니다._

## <a name="features-_descriptors_-in-language-understanding"></a>언어 이해의_특징(설명자)_

설명자라고도 하는 기능은 언어 이해가 예제 발언을 식별하는 데 도움이 되는 단서를 설명합니다. 기능은 다음과 같습니다.

* 의도 또는 엔터티에 대한 기능으로 구 목록
* 의도 또는 엔터티에 대한 기능으로 엔터티

피처는 모델 분해를 위해 스키마의 필수 부분으로 간주되어야 합니다.

## <a name="what-is-a-phrase-list"></a>구 목록이란 무엇입니까?

구 목록은 식별하려는 개념을 식별하는 데 도움이 되는 단어, 구, 숫자 또는 기타 문자 목록입니다. 이 목록은 대/소문자를 구분하지 않습니다.

## <a name="when-to-use-a-phrase-list"></a>구 목록을 사용하는 경우

LUIS는 구 문맥을 고려하고 일반화하여 텍스트일치와 유사하지만 일치하지 않는 항목을 식별합니다. LUIS 앱이 새 항목을 일반화하고 식별할 수 있어야 하는 경우 구 목록을 사용합니다.

새 연락처의 이름을 인식해야 하는 모임 스케줄러 나 새 제품을 인식해야 하는 인벤토리 앱과 같은 새 인스턴스를 인식하려면 기계 학습 엔터티로 시작합니다. 그런 다음 LUIS에서 비슷한 의미를 가진 단어를 찾는 데 도움이 되는 구 목록을 만듭니다. 이 구 목록은 LUIS가 해당 단어의 값에 추가적인 의미를 추가하여 예제를 인식하도록 안내합니다.

구문 목록은 의도 및 엔터티 모두의 이해 품질을 개선하도록 도와주는 도메인별 어휘와 비슷합니다.

## <a name="considerations-when-using-a-phrase-list"></a>구 목록을 사용할 때 고려해야 할 사항

기본적으로 구 목록은 앱의 모든 모델에 적용됩니다. 이렇게 하면 모든 의도와 엔터티를 교차할 수 있는 구 목록에서 작동합니다. 분해성을 위해 구 목록은 관련 모델에만 적용해야 합니다.

기본적으로 전역적으로 생성된 구 목록을 만든 다음 나중에 특정 모델에 설명자(피처)로 적용하면 다른 모델에서 제거됩니다. 이 제거는 적용된 모델의 구 목록과 관련성을 추가하여 모델에서 제공하는 정확도를 개선하는 데 도움이 됩니다.

플래그는 `enabledForAllModels` API에서 이 모델 범위를 제어합니다.

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>구 목록 사용 방법

의도 또는 엔터티에 다음과 같은 중요한 단어 나 구가 있는 경우 [구 목록을 만듭니다.](luis-how-to-add-features.md)

* 산업 용어
* 속어
* 약어
* 회사별 언어
* 다른 언어에서 왔지만 앱에서 자주 사용되는 언어
* 예제 발화의 핵심 단어 및 구문

가능한 모든 단어나 구를 **추가하지** 마십시오. 대신 한 번에 몇 단어나 구를 추가한 다음 다시 학습하고 게시합니다. 목록이 시간이 지남에 따라 증가함에 따라 일부 용어에는 여러 형태(동의어)가 있을 수 있습니다. 다른 목록으로 나누기.

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>엔터티를 피처로 사용하는 경우

엔터티는 의도 또는 엔터티 수준에서 기능으로 추가할 수 있습니다.

### <a name="entity-as-a-feature-to-an-intent"></a>의도에 대한 기능으로서의 엔티티

의도에 대해 해당 엔터티의 검색이 중요한 경우 의도에 엔터티를 설명자(기능)로 추가합니다.

예를 들어 항공편 예약 의향이 항공권 예약이고 엔티티가 항공권 정보(예: 좌석 수, 출발지 및 도착지)인 경우 항공권 정보 엔터티를 찾는 것은 예약 항공편 의도예측에 가중치를 더해야 합니다.

### <a name="entity-as-a-feature-to-another-entity"></a>다른 엔터티에 대한 기능으로 엔터티

엔터티(A)는 엔터티(B)의 예측에 대해 해당 엔터티(A)의 검색이 중요한 경우 다른 엔터티(B)에 기능으로 추가되어야 합니다.

예를 들어, 도로 주소 엔터티(A)가 감지되면 도로 주소(A)를 찾으면 배송 주소 엔터티(B)에 대한 예측에 가중치가 추가됩니다.

## <a name="best-practices"></a>모범 사례
[모범 사례](luis-concept-best-practices.md)를 알아봅니다.

## <a name="next-steps"></a>다음 단계

* 예측 런타임시 앱 모델 [확장](schema-change-prediction-runtime.md)
* LUIS 앱에 기능을 추가하는 방법에 대해 자세히 알아보려면 [기능 추가](luis-how-to-add-features.md)를 참조하세요.
