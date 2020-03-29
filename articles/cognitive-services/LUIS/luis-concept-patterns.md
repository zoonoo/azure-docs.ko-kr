---
title: 패턴 도움말 예측 - LUIS
titleSuffix: Azure Cognitive Services
description: 패턴를 통해 더 많은 발화를 제공하지 않고도 의도에 대한 더 높은 정확성을 얻을 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6c1b548de25369c162b4a08dfa20fce62c17f99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75890279"
---
# <a name="patterns-improve-prediction-accuracy"></a>패턴을 통해 예측 정확도 개선
패턴은 여러 발언이 매우 유사할 경우 정확도를 향상시키도록 디자인됩니다.  패턴를 통해 더 많은 발화를 제공하지 않고도 의도에 대한 더 높은 정확성을 얻을 수 있습니다. 

## <a name="patterns-solve-low-intent-confidence"></a>패턴을 통해 낮은 의도 신뢰도 해결
직원과 관련해서 조직도에 보고하는 Human Resources 앱을 가정해 봅니다. 직원의 이름 및 관계가 제공될 경우 LUIS는 관련 직원을 반환합니다. 관리자 이름은 Alice이고 Michael, Rebecca 및 Carl이 부하 직원으로 팀에 소속되어 있는 Tom이라는 직원이 있다고 가정합니다.

![조직도 이미지](./media/luis-concept-patterns/org-chart.png)

|발언|예측된 의도|의도 점수|
|--|--|--|
|Tom의 부하 직원은 누구인가요?|GetOrgChart|.30|
|어떤 직원이 Tom의 부하 직원인가요?|GetOrgChart|.30|

앱이 문장 길이가 다르고, 단어 순서가 다르고, 사용되는 단어(“부하 직원” “관리자”, “직속 부하”)도 다른 10~20개 발언을 포함하는 경우 LUIS는 낮은 신뢰도 점수를 반환할 수 있습니다. LUIS가 단어 순서의 중요성을 이해하는 데 도움이 되는 패턴을 만듭니다. 

패턴은 다음과 같은 상황을 해결합니다. 

* 의도 점수가 낮습니다.
* 올바른 의도는 최고 점수가 아니라 최고 점수에 너무 가깝습니다. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>패턴은 의도를 보장하지 않음
패턴은 혼합된 예측 기술을 사용합니다. 템플릿 발언에 대한 의도를 패턴으로 설정해도 의도 예측이 제대로 이루어질 것이라고 보장할 수 없지만 강력한 신호가 될 수 있습니다. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>패턴이 기계 학습 엔터티 검색을 개선하지 않음

패턴은 주로 의도와 역할을 예측하는 데 도움을 주기 위한 것입니다. _pattern.any_ 엔터티는 자유 형식 엔터티를 추출하는 데 사용됩니다. 패턴은 엔터티를 사용하지만 패턴은 기계 학습 엔터티를 검색하는 데 도움이 되지 않습니다.  

여러 발언을 단일 패턴으로 축소하는 경우에는 향상된 엔터티 예측을 기대하지 못합니다. 간단한 엔터티가 발생하려면 발언을 추가하거나 패턴이 발사되지 않는 목록 엔터티를 사용해야 합니다.

## <a name="patterns-use-entity-roles"></a>패턴에 엔터티 역할 사용
패턴의 둘 이상의 엔터티가 문맥상 관련이 있는 경우 패턴은 엔터티 [역할](luis-concept-roles.md)을 사용하여 엔터티에 대한 컨텍스트 정보를 추출합니다.  

## <a name="prediction-scores-with-and-without-patterns"></a>패턴이 있거나 없는 예측 점수
충분한 예제 발언이 제공될 경우, LUIS는 패턴 없이 예측 신뢰도를 높일 수 있습니다. 패턴은 많은 발언을 제공할 필요 없이, 신뢰도 점수를 높입니다.  

## <a name="pattern-matching"></a>패턴 일치
먼저 패턴 내 엔터티를 검색된 후, 패턴의 나머지 단어 및 단어 순서가 유효한지 검사됩니다. 패턴이 일치하려면 패턴에 엔터티가 필요합니다. 패턴은 문자 수준이 아닌 토큰 수준에서 적용됩니다. 

## <a name="pattern-only-apps"></a>패턴 전용 앱
각 의도에 대한 패턴이 있는 한 예제 발언이 없는 의도로 앱을 빌드할 수 있습니다. 패턴 전용 앱의 경우 예제 발언이 필요하므로 패턴에 기계 학습 엔터티가 포함되어서는 안 됩니다. 

## <a name="best-practices"></a>모범 사례
[모범 사례](luis-concept-best-practices.md)를 알아봅니다.

## <a name="pattern-syntax"></a>패턴 구문

[패턴 구문 참조에서 패턴 구문을](reference-pattern-syntax.md)알아봅니다. 

## <a name="next-steps"></a>다음 단계

패턴에 대해 자세히 알아보기:

* [패턴을 추가하는 방법](luis-how-to-model-intent-pattern.md)
* [pattern.any 엔터티를 추가하는 방법](luis-how-to-add-entities.md#add-a-patternany-entity)
* [패턴 구문](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [이 자습서의 패턴을 구현하는 방법 알아보기](luis-tutorial-pattern.md)
