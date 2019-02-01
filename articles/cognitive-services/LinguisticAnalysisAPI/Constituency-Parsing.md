---
title: 구성 구문 분석 - Linguistic Analysis API
titlesuffix: Azure Cognitive Services
description: “구 구조 구문 분석”이라고도 하는 팀원 구문 분석이 텍스트에서 구를 식별하는 방법에 대해 알아봅니다.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 778005ee321c981fec1d1271cee54229bb3a522f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214180"
---
# <a name="constituency-parsing"></a>팀원 구문 분석

> [!IMPORTANT]
> Linguistic Analysis 미리 보기는 2018년 8월 9일부로 서비스 해제되었습니다. 텍스트 처리 및 분석에는 [Azure Machine Learning 텍스트 분석 모듈](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)을 사용하는 것이 좋습니다.

팀원 구문 분석(“구 구조 구문 분석”이라고도 함)의 목표는 텍스트에서 구를 식별하는 것입니다.
이는 텍스트에서 정보를 추출하는 경우에 유용할 수 있습니다.
고객은 텍스트의 큰 본문에서 기능 이름 또는 키 구를 찾고, 이러한 각 구를 둘러싼 한정자와 작업을 보고자 할 수 있습니다.

## <a name="phrases"></a>구

언어학자에게 *구*는 연속적인 단어 이상입니다.
구가 되려면 단어들의 그룹이 한 데로 모여 문장에서 특정 역할을 수행해야 합니다.
그러한 단어들의 그룹은 함께 이동하거나 전체적으로 변경될 수 있으며, 문장은 유창하며 문법에 맞아야 합니다.

문장 고려

> I want to find a new hybrid automobile with Bluetooth. (Bluetooth 기능이 있는 새로운 하이브리드 자동차를 찾고 싶다.)

이 문장에는 “a new hybrid automobile with Bluetooth”라는 명사구가 포함되어 있습니다.
이것이 구라는 것을 어떻게 알 수 있을까요?
구 전체를 앞으로 이동해도 문장을 다시 작성할 수 있습니다(다소 시적으로).

> A new hybrid automobile with Bluetooth I want to find. (나는 찾고 싶다, Bluetooth 기능이 있는 새로운 하이브리드 자동차를.)

또는 해당 구를 “a fancy new car”와 같이 유사한 기능 및 의미가 있는 구로 바꿀 수도 있습니다.

> I want to find a fancy new car. (멋진 새 차를 찾고 싶다.)

대신 단어의 다른 하위 집합을 선택한 경우 이러한 대체 작업은 이상하거나 읽을 수 없는 문장을 만들 수도 있습니다.
“find a new”를 앞으로 이동하면 어떤지 살펴보겠습니다.

> Find a new I want to hybrid automobile with Bluetooth. (새로운 것을 찾는다. Bluetooth 기능이 있는 하이브리드 자동차를 원한다.)

결과 문장은 읽고 이해하기가 매우 어렵습니다.

파서의 목표는 이러한 모든 구를 찾는 것입니다.
흥미롭게도 자연어에서 구는 서로 중첩될 경향이 있습니다.
다음과 같이 이러한 구의 자연적인 표현이 트리입니다.

![트리](./Images/tree.png)

이 트리에서 “NP”라고 표시된 분기는 명사구입니다.
이러한 문구는 *I*, *a new hybrid automobile*, *Bluetooth* 및 *a new hybrid automobile with Bluetooth*와 같이 여러 가지가 있습니다.

## <a name="phrase-types"></a>구 형식

| 레이블 | 설명 | 예 |
|-------|-------------|---------|
|ADJP   | 형용사구 | “so rude” |
|ADVP   | 부사구 | “clear through” |
|CONJP  | 접속사구 | “as well as” |
|FRAG   | 불완전하거나 단편적인 입력에 사용되는 단편 | “Highly recommended...” |
|INTJ   | 감탄사 | “Hooray” |
|LST    | 문장 부호를 포함하는 목록 표식 | “#4)” |
|NAC    | Not A Constituent(구성 요소가 아님), 구성 요소가 아닌 구의 범위를 나타내는 데 사용됨 |  “you get things and for a good deal”에서 “and for a good deal” |
|NP | 명사구 | “a tasty potato pancake” |
|NX | 구의 중심 단어를 표시하기 위해 복잡한 특정 NP 내에서 사용됨| |
|PP | 전치사구| “in the pool” |
|PRN    | 괄호| “(so called)” |
|PRT    | 소사| “ripped out”에서 “out” |
|QP | 명사구 내 수량구(즉, 복잡한 측정값/양)| “around $75” |
|RRC    | 축소된 관계사절| “many issues still unresolved”에서 “still unresolved” |
|S  | 문장 또는 절 | “This is a sentence.”
|SBAR   | 종종 종속된 절로 시작되는 종속절 | “I looked around as I left.”에서 “as I left”|
|SBARQ  | wh-어나 wh-구로 시작하는 직접 질문 | “What was the point?” |
|SINV   | 도치된 평서문 | “At no time were they aware.” (원래 주어 “they”가 어떻게 동사 “were” 뒤로 이동했는지 주의) |
|SQ | 도치된 yes/no 질문 또는 wh-질문의 주요 절 | “Did they get the car?” |
|UCP    | 상이한 등위구| “small and with bugs” (형용사와 전치사구가 어떻게 “and”와 결합되는지 확인)|
|VP | 동사구 | “ran into the woods” |
|WHADJP | wh-형용사구 | “how uncomfortable” |
|WHADVP | wh-부사구| “when” |
|WHNP   | wh-명사구| “which potato”, “how much soup”|
|WHPP   | wh-전치사구| “in which country”|
|X  | 알 수 없는, 불확실한 또는 unbracketable.| “the... the soup”에서 첫 번째 “the” |


## <a name="specification"></a>사양

여기에 나온 트리는 [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42)의 S 표현식을 사용합니다.
