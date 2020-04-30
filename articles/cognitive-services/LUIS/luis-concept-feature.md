---
title: 기능-LUIS
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81531901"
---
# <a name="machine-learned-features"></a>컴퓨터에서 학습 한 기능

기계 학습에서 _기능은_ 시스템이 학습 & 학습 하는 데이터의 특징 또는 특성입니다. LUIS (Language Understanding)에서 기능은 사용자의 의도 및 엔터티에 대해 중요 한 사항을 설명 하 고 설명 합니다.

[PREVIEW LUIS 포털](https://preview.luis.ai)에서 기능은 의도 또는 엔터티를 _설명_ 하는 데 사용 되기 때문에 _설명자_ 입니다.

## <a name="features-_descriptors_-in-language-understanding"></a>Language Understanding의 기능 (_설명자_)

설명자 라고도 하는 기능에서는 예제 길이 발언 Language Understanding 식별 하는 데 도움이 되는 단서를 설명 합니다. 기능은 다음과 같습니다.

* 의도 또는 엔터티에 대 한 기능으로 서의 문구 목록
* 의도 또는 엔터티에 대 한 기능으로 서의 엔터티

모델 분해를 위해 기능을 스키마의 필수 부분으로 간주 해야 합니다.

## <a name="what-is-a-phrase-list"></a>구 목록 이란?

구 목록은 식별 하려는 개념을 식별 하는 데 도움이 되는 단어, 구, 숫자 또는 기타 문자 목록입니다. 목록에서 대/소문자를 구분 하지 않습니다.

## <a name="when-to-use-a-phrase-list"></a>구 목록을 사용 해야 하는 경우

LUIS는 구 목록과 일치 하는 항목을 식별 하는 데 컨텍스트와 일반화을 고려 합니다. LUIS 앱이 새 항목을 일반화 하 고 식별할 수 있도록 요구 하는 경우 구 목록을 사용 합니다.

새 연락처의 이름을 인식 해야 하는 모임 스케줄러, 새 제품을 인식 해야 하는 인벤토리 앱 등 새 인스턴스를 인식할 수 있도록 하려면 컴퓨터에서 학습 한 엔터티를 사용 하 여 시작 합니다. 그런 다음 비슷한 의미로 단어를 찾는 데 도움이 되는 구 목록을 LUIS. 이 문구 목록 가이드는 이러한 단어의 값에 중요도를 추가 하 여 예제를 LUIS 합니다.

구문 목록은 의도 및 엔터티 모두의 이해 품질을 개선하도록 도와주는 도메인별 어휘와 비슷합니다.

## <a name="considerations-when-using-a-phrase-list"></a>구 목록 사용 시 고려 사항

기본적으로 구문 목록이 앱의 모든 모델에 적용 됩니다. 모든 의도 및 엔터티를 교차할 수 있는 구 목록에 적용 됩니다. Decomposability의 경우 관련 된 모델에만 구 목록을 적용 해야 합니다.

기본적으로 전역적으로 만들어진 구 목록을 만든 다음 나중에 특정 모델에 대 한 설명자 (기능)로 적용 하면 다른 모델에서 제거 됩니다. 이 제거는 적용 되는 모델에 대 한 구 목록과 관련성을 추가 하 여 모델에서 제공 하는 정확도를 향상 시킵니다.

플래그 `enabledForAllModels` 는 API의이 모델 범위를 제어 합니다.

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>구 목록을 사용 하는 방법

의도 또는 엔터티에 다음과 같은 중요 한 단어나 구가 있는 경우 [구 목록을 만듭니다](luis-how-to-add-features.md) .

* 산업 용어
* 속어
* 약어
* 회사별 언어
* 다른 언어에서 왔지만 앱에서 자주 사용되는 언어
* 예제 발화의 핵심 단어 및 구문

가능한 단어나 구를 모두 추가 **하지** 마십시오. 대신, 한 번에 몇 개의 단어나 구를 추가 하 고 다시 학습 및 publish를 추가 합니다. 시간이 지남에 따라 목록이 증가 함에 따라 여러 가지 용어 (동의어)를 찾을 수 있습니다. 다른 목록으로 구분 합니다.

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>엔터티를 기능으로 사용 하는 경우

엔터티는 의도 또는 엔터티 수준에서 기능으로 추가할 수 있습니다.

### <a name="entity-as-a-feature-to-an-intent"></a>의도에 대 한 기능으로 서의 엔터티입니다.

해당 엔터티의 검색에서 의도에 중요 한 경우 엔터티를 설명자 (기능)로 추가 합니다.

예를 들어 비행을 예약 하기 위한 의도가 고 엔터티가 티켓 정보 (예: 좌석, 원본 및 대상 수) 인 경우 티켓 정보 엔터티를 찾는 것은 책 비행의 예측에 가중치를 추가 해야 합니다.

### <a name="entity-as-a-feature-to-another-entity"></a>다른 엔터티에 대 한 기능으로 서의 엔터티입니다.

엔터티 (a)의 검색이 엔터티 (B)의 예측에 중요 한 경우 엔터티 (A)를 다른 엔터티 (B)에 기능으로 추가 해야 합니다.

예를 들어 주소 엔터티 (A)가 검색 되 면 주소 (A)를 찾으면 배송 주소 엔터티 (B)에 대 한 예측에 가중치를 추가 합니다.

## <a name="best-practices"></a>모범 사례
[모범 사례](luis-concept-best-practices.md)를 알아봅니다.

## <a name="next-steps"></a>다음 단계

* 예측 런타임에서 앱 모델 [확장](schema-change-prediction-runtime.md)
* LUIS 앱에 기능을 추가하는 방법에 대해 자세히 알아보려면 [기능 추가](luis-how-to-add-features.md)를 참조하세요.
