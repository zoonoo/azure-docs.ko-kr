---
title: 엔터티 형식-LUIS
titleSuffix: Azure Cognitive Services
description: 엔터티는 utterance에서 데이터를 추출 합니다. 엔터티 형식은 데이터의 예측 가능한 추출을 제공 합니다. 두 가지 유형의 엔터티가 있습니다. 컴퓨터를 학습 하 고 컴퓨터를 인식 하지 못합니다. 길이 발언에서 작업 하는 엔터티의 유형을 파악 하는 것이 중요 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9dc26e50e1c0f43e816e422f0fee91a246ea04a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487607"
---
# <a name="entities-and-their-purpose-in-luis"></a>LUIS에서 엔터티 및 해당 용도

엔터티의 주요 목적은 클라이언트 응용 프로그램에서 데이터를 예측 가능한 방식으로 추출할 수 있도록 하는 것입니다. _선택적인_보조 목적은 설명자를 사용 하 여 의도의 예측을 높이는 것입니다. 

엔터티는 다음과 같은 두 가지 유형이 있습니다. 

* 컴퓨터에서 학습-컨텍스트
* 비 기계어 학습-정확히 일치 하는 텍스트

가장 넓은 범위의 데이터 추출 선택 항목을 제공 하므로 항상 컴퓨터에서 얻은 엔터티로 시작 합니다.

## <a name="entity-compared-to-intent"></a>엔터티 및 의도 비교

엔터티는 추출 하려는 utterance 내부의 데이터 개념을 나타냅니다. 

다음 3 길이 발언을 고려 합니다.

|발언|추출 된 데이터|설명|
|--|--|--|
|`Help`|-|추출할 항목이 없습니다.|
|`Send Bob a present`|Bob, 있음|Bob은 작업을 완료 하는 데 매우 중요 합니다. 이 경우에는 충분 한 정보를 제공 하거나 봇에서 추가 작업을 수행 하는 것이 무엇 인지 명확 하 게 파악할 수 있습니다.|
|`Send Bob a box of chocolates.`|두 가지 중요 한 데이터 인 Bob과 빠졌습니다의 상자는 사용자의 요청을 완료 하는 데 중요 합니다.|

발화에는 여러 엔터티가 포함되거나 전혀 포함되지 않을 수 있습니다. 클라이언트 응용 프로그램에서 작업을 수행 하는 데 엔터티가 필요할 _수 있습니다_ . 

비교 하 여 utterance에 대 한 의도의 예측은 _필수_ 이며 전체 utterance를 나타냅니다. LUIS에는 예제 길이 발언가 의도에 포함 되어 있어야 합니다. Utterance의 기본 의도가 클라이언트 응용 프로그램에서 중요 하지 않은 경우 모든 길이 발언를 없음 의도에 추가 합니다. 

앱 수명 주기에서 나중에 길이 발언를 중단 하려는 경우이 작업을 쉽게 수행할 수 있습니다. 이는 제작 하는 동안 길이 발언를 구성 하거나 클라이언트 응용 프로그램에서 예측 된 의도를 사용 하는 데 사용할 수 있습니다. 

클라이언트 응용 프로그램에서 예측 의도를 사용 하기 위한 요구 사항은 없지만 예측 끝점 응답의 일부로 반환 됩니다.

## <a name="entities-represent-data"></a>데이터를 나타내는 엔터티

엔터티는 발화에서 가져오려는 데이터입니다. 이름, 날짜, 제품 이름 또는 모든 단어 그룹이 될 수 있습니다. 

|발언|엔터티|Data|
|--|--|--|
|Buy 3 tickets to New York|미리 빌드된 숫자<br>Location.Destination|3<br>뉴욕|
|Buy a ticket from New York to London on March 5|Location.Origin<br>Location.Destination<br>미리 빌드된 datetimeV2|뉴욕<br>런던<br>March 5, 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>선택 사항이지만 많이 권장되는 엔터티

의도는 필수인 반면 엔터티는 선택 사항입니다. 앱의 모든 개념에 대해 엔터티를 만들 필요는 없지만 클라이언트 애플리케이션이 작업을 수행하는 데 필요한 개념에 대해서는 만들어야 합니다. 

발화에 봇이 계속 수행해야 하는 세부 정보가 없는 경우, 추가할 필요가 없습니다. 앱이 완성되고 나중에 추가할 수 있습니다. 

정보를 사용하는 방법을 잘 모르는 경우, [datetimeV2](luis-reference-prebuilt-datetimev2.md), [서수](luis-reference-prebuilt-ordinal.md), [이메일](luis-reference-prebuilt-email.md) 및 [전화 번호](luis-reference-prebuilt-phonenumber.md)와 같은 몇 가지 일반적인 미리 빌드된 엔터티를 추가합니다.

## <a name="design-entities-for-decomposition"></a>분해를 위한 엔터티 디자인

컴퓨터에서 학습 한 엔터티를 사용 하 여 엔터티 디자인을 시작 합니다. 이를 통해 시간에 따라 엔터티를 쉽게 디자인 하 고 변경할 수 있습니다. **제약 조건** 및 **설명자** 를 사용 하 여 **하위 구성 요소** (자식 엔터티)를 추가 하 여 엔터티 디자인을 완료 합니다. 

분해를 위한 디자인을 통해 LUIS은 클라이언트 응용 프로그램에 깊이 있는 엔터티 해상도를 반환할 수 있습니다. 이렇게 하면 클라이언트 응용 프로그램이 비즈니스 규칙에 집중 하 고 데이터 확인을 LUIS로 유지할 수 있습니다.

### <a name="machine-learned-entities-are-primary-data-collections"></a>컴퓨터에서 학습 한 엔터티는 주 데이터 컬렉션입니다.

컴퓨터에서 배운 엔터티는 최상위 데이터 단위입니다. 하위 구성 요소는 컴퓨터에서 학습 한 엔터티의 자식 엔터티입니다. 

**제약 조건은** 데이터를 식별 하 고 추출 하는 규칙을 적용 하는 정확한 텍스트 일치 엔터티입니다. **설명자** 는 예측이 적용 되는 단어 또는 구의 관련성을 높이기 위해 적용 되는 기능입니다.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>엔터티의 형식

데이터를 추출해야 하는 방법 및 추출된 후에 데이터를 표시하는 방법에 따라 엔터티를 선택합니다.

|엔터티 형식|목적|
|--|--|
|[**컴퓨터-학습**](#composite-entity)|엔터티 형식에 관계 없이 엔터티의 부모 그룹입니다. 컴퓨터에서 배운 엔터티는 utterance의 컨텍스트에서 학습 합니다. 이렇게 하면 예 길이 발언에서 배치의 변형이 중요 합니다. |
|[**목록**](#list-entity)|**정확히 일치**하는 텍스트를 사용 하 여 추출 된 항목 및 해당 동의어의 목록입니다.|
|[**Pattern.any**](#patternany-entity)|엔터티의 끝을 확인하기 어려운 엔터티 |
|[**미리 빌드됨**](#prebuilt-entity)|URL, 전자 메일 등의 특정 종류의 데이터를 추출 하도록 이미 학습 되었습니다. 이러한 미리 빌드된 엔터티 중 일부는 오픈 소스 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 프로젝트에 정의되어 있습니다. 특정 문화권이나 엔터티가 현재 지원되지 않은 경우 프로젝트에 적용됩니다.|
|[**정규식**](#regular-expression-entity)|**정확히 일치**하는 텍스트를 위해 정규식을 사용 합니다.|

### <a name="entity-role-defines-context"></a>엔터티 역할이 컨텍스트를 정의 합니다.

엔터티의 역할은 utterance 내의 컨텍스트에 따라 명명 된 별칭입니다. 예는 원본 및 대상의 두 위치를 포함 하는 비행을 예약 하기 위한 utterance입니다.

`Book a flight from Seattle to Cairo`

`location` 엔터티의 두 예제를 추출 해야 합니다. 클라이언트 응용 프로그램은 티켓 구매를 완료 하기 위해 각각의 위치 유형을 알고 있어야 합니다. `location` 엔터티에는 `origin` 및 `destination`의 두 역할이 필요 하며, 둘 다 예 길이 발언에 표시 되어야 합니다. 

LUIS가 `location`를 찾았지만 역할을 확인할 수 없는 경우에도 위치 엔터티가 반환 됩니다. 클라이언트 응용 프로그램은 사용자가 지정 하는 위치 유형을 확인 하기 위해 질문을 해야 합니다. 

여러 엔터티가 utterance에 존재할 수 있으며 역할을 사용 하지 않고 추출할 수 있습니다. 문장의 컨텍스트가 엔터티 값을 나타내는 경우에는 역할을 사용 해야 합니다.

Utterance에 `I want to travel to Seattle, Cairo, and London.`위치 목록이 포함 된 경우이 목록은 각 항목에 추가 의미가 없는 목록입니다. 

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>최대 엔터티 수보다 더 많이 필요한 경우 

제한 보다 더 많이 필요한 경우 지원 담당자에 게 문의 하세요. 이렇게 하려면 시스템에 대한 자세한 정보를 수집하고 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트로 이동한 다음, **지원**을 선택하세요. Azure 구독에 지원 서비스가 포함된 경우, [Azure 기술 지원](https://azure.microsoft.com/support/options/)에 문의하세요. 

## <a name="entity-prediction-status"></a>엔터티 예측 상태

LUIS 포털에서는 utterance 예의 엔터티에 선택한 엔터티와 다른 엔터티 예측이 있는 경우를 보여 줍니다. 이 다른 점수는 현재 학습 된 모델을 기반으로 합니다. 

## <a name="next-steps"></a>다음 단계

좋은 [발화](luis-concept-utterance.md)에 대한 개념을 알아보세요. 

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.
