---
title: Personalizer란?
titleSuffix: Azure Cognitive Services
description: Personalizer는 사용자에게 표시할 최상의 환경을 선택할 수 있는 클라우드 기반 API 서비스로, 사용자의 실시간 동작을 통해 학습합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 01/21/2020
ms.author: diberry
ms.openlocfilehash: 850ab0ee89ee167886d8747a0c721bb643529e14
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80052052"
---
# <a name="what-is-personalizer"></a>Personalizer란?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Personalizer는 클라이언트 애플리케이션에서 각 사용자를 표시하는 데 가장 적합한 단일 _콘텐츠_ 항목을 선택할 수 있도록 도움을 주는 클라우드 기반 API 서비스입니다. 이 서비스는 콘텐츠 및 컨텍스트에 대해 제공하는 집합적 실시간 정보를 기준으로 콘텐츠 항목에서 가장 적합한 항목을 선택합니다.

콘텐츠 항목이 사용자에게 제공되면 시스템에서 사용자 동작을 모니터링하고 보상 점수를 Personalizer에 다시 보고하여 받은 컨텍스트 정보를 기준으로 가장 적합한 콘텐츠를 선택하는 기능을 향상시킵니다.

**콘텐츠**는 사용자에게 표시하기 위해 선택하려는 텍스트, 이미지, URL 또는 이메일과 같은 정보의 단위일 수 있습니다.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Personalizer에서 가장 적합한 콘텐츠 항목을 어떻게 선택하나요?

Personalizer는 **보충 학습**을 사용하여 모든 사용자의 집합적 동작 및 보상 점수에 따라 가장 적합한 항목(_작업_)을 선택합니다. 작업은 선택할 뉴스 기사, 특정 영화 또는 제품과 같은 콘텐츠 항목입니다.

**순위** 호출에서는 작업 기능과 함께 작업 항목 및 컨텍스트 기능을 사용하여 상위 작업 항목을 선택합니다.

* **기능이 포함된 작업** - 각 항목에 해당하는 특정 기능이 포함된 콘텐츠 항목
* **컨텍스트 기능** - 앱을 사용하는 경우 사용자, 해당 컨텍스트 또는 해당 환경의 기능

순위 호출은 **보상 작업 ID** 필드에서 사용자에게 표시할 콘텐츠 항목인 __작업__의 ID를 반환합니다.
사용자에게 표시되는 __작업__은 기계 학습 모델을 사용하여 선택되며, 시간이 지남에 따라 총 보상량을 최대화하려고 합니다.

몇 가지 예제 시나리오는 다음과 같습니다.

|내용 유형|**작업(기능 포함)**|**컨텍스트 기능**|반환되는 보상 작업 ID<br>(이 콘텐츠 표시)|
|--|--|--|--|
|뉴스 목록|a. `The president...`(국가별, 정치, [텍스트])<br>b. `Premier League ...`(글로벌, 스포츠, [텍스트, 이미지, 비디오])<br> 다. `Hurricane in the ...`(국가별, 날씨, [텍스트, 이미지]|뉴스를 읽는 디바이스<br>월 또는 계절<br>|a `The president...`|
|영화 목록|1. `Star Wars`(1977, [작업, 모험, 판타지], George Lucas)<br>2. `Hoop Dreams`(1994, [다큐멘터리, 스포츠], Steve James<br>3. `Casablanca`(1942, [사랑, 드라마, 전쟁], Michael Curtiz)|영화를 보는 디바이스<br>화면 크기<br>사용자 유형<br>|3. `Casablanca`|
|제품 목록|i. `Product A` (3Kg, $$$$, 24시간 내 배달)<br>ii. `Product B` (20Kg, $$, 세관에서 2주 배송)<br>iii. `Product C` (3Kg, $$$,48시간 내 배달)|쇼핑을 읽는 디바이스<br>사용자의 지출 계층<br>월 또는 계절|ii. `Product B`|

Personalizer는 보충 학습을 사용하여 다음과 같은 조합에 기반한 _보상 작업 ID_라고 하는 가장 적합한 단일 작업을 선택했습니다.
* 학습된 모델 - Personalizer 서비스에서 받은 과거 정보
* 현재 데이터 - 기능이 포함된 특정 작업 및 컨텍스트 기능

## <a name="when-to-call-personalizer"></a>Personalizer를 호출하는 경우

Personalizer의 **순위** [API](https://go.microsoft.com/fwlink/?linkid=2092082)는 _콘텐츠를 표시할 때마다_ 실시간으로 호출됩니다. 이를 **이벤트**라고 하며, _event ID_로 표시합니다.

Personalizer의 **보상** [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)는 실시간으로 호출하거나 인프라에 맞게 조정하기 위해 지연시킬 수 있습니다. 비즈니스 요구 사항에 따라 보상 점수를 결정합니다. 이는 양호한 경우 1, 양호하지 않은 경우 0 또는 비즈니스 목표와 메트릭을 고려하여 만든 알고리즘을 통해 생성된 숫자와 같은 단일 값일 수 있습니다.

## <a name="personalizer-content-requirements"></a>Personalizer 콘텐츠 요구 사항

콘텐츠가 다음과 같은 경우 Personalizer를 사용합니다.

* 선택할 수 있는 항목 세트가 제한되어 있습니다(최대 50개 항목). 더 큰 목록이 있는 경우 [추천 엔진을 사용](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution)하여 목록을 50개 항목으로 줄입니다.
* 순위를 지정하려는 콘텐츠를 설명하는 정보(_기능이 포함된 작업_ 및  _컨텍스트 기능_)가 있습니다.
* Personalizer가 효과적일 수 있도록 최소 1k/일의 콘텐츠 관련 이벤트가 있습니다. Personalizer가 필요한 최소 트래픽을 받지 못하는 경우 서비스에서 가장 적합한 단일 콘텐츠 항목을 결정하는 데 시간이 더 오래 걸립니다.

Personalizer는 그룹 정보를 거의 실시간으로 사용하여 가장 적합한 단일 콘텐츠 항목을 반환하므로 서비스에서 수행하지 않는 작업은 다음과 같습니다.
* 사용자 프로필 정보 유지 및 관리
* 개별 사용자의 기본 설정 또는 기록 로깅
* 정리된 콘텐츠 및 레이블이 지정된 콘텐츠 요구

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>클라이언트 애플리케이션에 대해 Personalizer를 설계 및 구현하는 방법

1. 콘텐츠, **_작업_** 및 **_컨텍스트_** 를 [설계](concepts-features.md)하고 계획합니다. **_보상_** 점수에 대한 보상 알고리즘을 결정합니다.
1. 만드는 각 [Personalizer 리소스](how-to-settings.md)는 하나의 학습 루프로 간주됩니다. 루프는 해당 콘텐츠 또는 사용자 환경에 대한 순위 및 보상 호출을 모두 받습니다.
1. Personalizer를 웹 사이트 또는 콘텐츠 시스템에 추가합니다.
    1. 콘텐츠가 사용자에게 표시되기 전에 애플리케이션, 웹 사이트 또는 시스템에서 **순위** 호출을 Personalizer에 추가하여 가장 적합한 단일 _콘텐츠_ 항목을 확인합니다.
    1. 반환되는 _보상 작업 ID_인 가장 적합한 단일 _콘텐츠_ 항목을 사용자에게 표시합니다.
    1. 다음과 같이 _알고리즘_을 사용자의 동작 방식에 대해 수집한 정보에 적용하여 **보상** 점수를 확인합니다.

        |동작|계산된 보상 점수|
        |--|--|
        |사용자가 가장 적합한 단일 _콘텐츠_ 항목(보상 작업 ID)을 선택함|**1**|
        |사용자가 다른 콘텐츠를 선택함|**0**|
        |가장 적합한 단일 _콘텐츠_ 항목(보상 작업 ID)을 선택하기 전에 사용자가 일시 중지하여 막연하게 스크롤함|**0.5**|

    1. 0과 1 사이의 보상 점수를 보내는 **보상** 호출을 추가합니다.
        * 콘텐츠를 표시한 직후
        * 또는 나중에 오프라인 시스템에서
    1. 사용 기간 후에 오프라인 평가를 사용하여 [루프를 평가](concepts-offline-evaluation.md)합니다. 오프라인 평가를 사용하면 코드를 변경하거나 사용자 환경에 영향을 주지 않고 Personalizer 서비스의 효율성을 테스트하고 평가할 수 있습니다.

## <a name="next-steps"></a>다음 단계


* [Personalizer의 작동 원리](how-personalizer-works.md)
* [보충 학습이란?](concepts-reinforcement-learning.md)
* [Rank 요청에 대한 기능 및 작업에 대해 알아보기](concepts-features.md)
* [Reward 요청 점수를 확인하는 방법 알아보기](concept-rewards.md)
* [빠른 시작](sdk-learning-loop.md)
* [자습서](tutorial-use-azure-notebook-generate-loop-data.md)
* [대화형 데모 사용](https://personalizationdemo.azurewebsites.net/)
