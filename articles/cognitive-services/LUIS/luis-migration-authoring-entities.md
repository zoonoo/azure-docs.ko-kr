---
title: V3 machine으로 마이그레이션-학습 엔터티
description: V3 제작은 기계 학습 엔터티와 응용 프로그램의 다른 엔터티 또는 기능에 관계를 추가 하는 기능과 함께 새로운 엔터티 형식인 기계 학습 엔터티를 제공 합니다.
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: aaa5472f25a5eca5ceadf979c57a83874ce4cb6e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684599"
---
# <a name="migrate-to-v3-authoring-entity"></a>V3 제작 엔터티로 마이그레이션

V3 제작은 기계 학습 엔터티와 응용 프로그램의 다른 엔터티 또는 기능에 관계를 추가 하는 기능과 함께 새로운 엔터티 형식인 기계 학습 엔터티를 제공 합니다.

## <a name="entities-are-decomposable-in-v3"></a>V3에서 엔터티 없습니다

[Api](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) 를 사용 하거나 포털에서 V3 authoring api를 사용 하 여 만든 엔터티를 사용 하면 부모 및 자식으로 계층화 된 엔터티 모델을 빌드할 수 있습니다. 부모는 **machine learning 엔터티** 로 알려져 있고 자식은 컴퓨터에서 배운 엔터티의 하위 **엔터티** 라고 합니다.

각 하위 엔터티는 기계 학습 엔터티 이지만 기능의 추가 구성 옵션이 있습니다.

* **필수 기능은** 기능과 일치할 때 엔터티를 추출할 수 있도록 보장 하는 규칙입니다. 규칙은 모델에 대 한 필수 기능에 의해 정의 됩니다.
    * [미리 빌드된 엔터티](luis-reference-prebuilt-entities.md)
    * [정규식 엔터티](reference-entity-regular-expression.md)
    * [엔터티를 나열](reference-entity-list.md)합니다.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>이러한 새 관계를 V2 작성과 비교 하는 방법

V2는 역할 및 기능과 함께 계층 구조 및 복합 엔터티를 제공 하 여 이와 동일한 작업을 수행 합니다. 엔터티, 기능 및 역할은 서로 명시적으로 관련 되지 않았기 때문에 예측 중에 LUIS에서 관계를 암시 하는 방법을 이해 하기 어렵습니다.

V3에서 관계는 명시적 이며 앱 작성자가 설계 합니다. 이를 통해 앱 작성자가 다음을 수행할 수 있습니다.

* LUIS에서 이러한 관계를 예측 하는 방법을 시각적으로 확인 하는 예제 길이 발언
* [대화형 테스트 창이](luis-interactive-test.md) 나 끝점을 사용 하 여 이러한 관계를 테스트 합니다.
* 잘 구성 된 이름의 중첩 된 [json 개체](reference-entity-machine-learned-entity.md) 를 통해 클라이언트 응용 프로그램에서 이러한 관계를 사용 합니다.

## <a name="planning"></a>계획

마이그레이션하는 경우 마이그레이션 계획에서 다음 사항을 고려 합니다.

* LUIS 앱을 백업 하 고 별도의 앱에서 마이그레이션을 수행 합니다. V2 및 V3 앱을 동시에 사용할 수 있으면 필요한 변경 내용과 예측 결과에 미치는 영향을 확인할 수 있습니다.
* 현재 예측 성공 메트릭 캡처
* 현재 대시보드 정보를 앱 상태의 스냅숏으로 캡처
* 기존 의도, 엔터티, 구 목록, 패턴 및 배치 테스트 검토
* 다음 요소는 **변경 하지 않고**마이그레이션할 수 있습니다.
    * 의도
    * 엔터티
        * 정규식 엔터티
        * 목록 엔터티
    * 기능
        * 구문 목록
* 다음 요소를 **변경 내용과 함께**마이그레이션해야 합니다.
    * 엔터티
        * 계층적 엔터티
        * 복합 엔터티
    * 역할-역할은 machine learning (부모) 엔터티에만 적용할 수 있습니다. 하위 엔터티에는 역할을 적용할 수 없습니다.
    * 계층 구조 및 복합 엔터티를 사용 하는 일괄 처리 테스트 및 패턴

마이그레이션 계획을 설계할 때 모든 계층 구조 및 복합 엔터티가 마이그레이션된 후 최종 기계 학습 엔터티를 검토 하는 시간을 남겨 둡니다. 직접 마이그레이션이 작동 하는 동안 일괄 처리 테스트 결과와 예측 JSON을 변경 하 고 검토 한 후에는 보다 통합 된 JSON으로 인해 클라이언트 쪽 앱에 제공 되는 최종 정보가 다르게 구성 될 수 있습니다. 이는 코드 리팩터링과 비슷하며 조직이 보유 하 고 있는 것과 동일한 검토 프로세스로 처리 되어야 합니다.

V2 모델에 대해 배치 테스트를 수행 하지 않고, 마이그레이션의 일부로 배치 테스트를 V3 모델로 마이그레이션하는 경우 마이그레이션이 끝점 예측 결과에 어떤 영향을 주는지 유효성을 검사할 수 없습니다.

## <a name="migrating-from-v2-entities"></a>V2 엔터티에서 마이그레이션

V3 제작 모델로 이동할 때 machine learning 엔터티 및 해당 하위 엔터티 및 기능으로 이동 하는 방법을 고려해 야 합니다.

다음 표에서는 v 2에서 V3 엔터티 디자인으로 마이그레이션하는 데 필요한 엔터티를 설명 합니다.

|V2 제작 엔터티 형식|V3 제작 엔터티 형식|예제|
|--|--|--|
|복합 엔터티|컴퓨터에서 배운 엔터티|[더 알아보세요](#migrate-v2-composite-entity)|
|계층적 엔터티|기계 학습 엔터티의 역할|[더 알아보세요](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>V2 복합 엔터티 마이그레이션

V2 복합의 각 자식은 V3 기계 학습 엔터티의 하위 엔터티로 표시 되어야 합니다. 복합 자식이 미리 작성 된 정규식 또는 목록 엔터티인 경우이 항목을 하위 엔터티에 필요한 기능으로 적용 해야 합니다.

복합 엔터티를 기계 학습 엔터티로 마이그레이션할 계획인 경우 고려 사항:
* 자식 엔터티는 패턴에서 사용할 수 없습니다.
* 하위 엔터티는 더 이상 공유 되지 않습니다.
* 컴퓨터를 사용 하지 않는 것으로 확인 되 면 자식 엔터티를 레이블 해야 합니다.

### <a name="existing-features"></a>기존 기능

복합 엔터티에서 단어를 승격 하는 데 사용 되는 모든 구 목록은 기계 학습 (부모) 엔터티, 하위 엔터티 (자식) 엔터티 또는 의도 (문구 목록이 한 의도에만 적용 되는 경우)에 기능으로 적용 되어야 합니다. 가장 현저 하 게 향상 되어야 하는 엔터티에 기능을 추가 하도록 계획 합니다. 하위 엔터티 (자식)의 예측이 크게 향상 되는 경우 일반적으로 기계 학습 (부모) 엔터티에이 기능을 추가 하지 마십시오.

### <a name="new-features"></a>새 기능

V3 제작에서 모든 엔터티 및 의도에 대 한 가능한 기능으로 엔터티를 평가 하는 계획 단계를 추가 합니다.

### <a name="example-entity"></a>예제 엔터티

이 엔터티는 예입니다. 사용자 고유의 엔터티 마이그레이션에는 다른 고려 사항이 필요할 수 있습니다.

에서 사용 하는 피자를 수정 하기 위해 V2 복합을 고려 합니다 `order` .
* 배달 시간에 대 한 미리 빌드된 datetimeV2
* 피자, 원형, crust 및 topping과 같은 특정 단어를 상승 시키기 위한 구 목록
* 토 핑 (예: 버섯, olives, pepperoni)를 검색 하는 엔터티를 나열 합니다.

이 엔터티에 대 한 예제 utterance는 다음과 같습니다.

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

다음 표에서는 마이그레이션을 보여 줍니다.

|V2 모델|V3 모델|
|--|--|
|부모-구성 요소 엔터티 이름`Order`|부모-컴퓨터-학습 엔터티 이름`Order`|
|자식-미리 작성 datetimeV2|* 미리 작성 된 엔터티를 새 앱으로 마이그레이션합니다.<br>* 미리 작성 한 datetimeV2에 대 한 필수 기능을 부모에 추가 합니다.|
|토 핑에 대 한 자식 목록 엔터티|* 목록 엔터티를 새 앱으로 마이그레이션<br>* 그런 다음 목록 엔터티의 부모에 필요한 기능을 추가 합니다.|


## <a name="migrate-v2-hierarchical-entity"></a>V2 계층적 엔터티 마이그레이션

V2 제작에서 계층 구조 엔터티는 LUIS의 기존 역할 이전에 제공 되었습니다. 둘 다 컨텍스트 사용을 기준으로 엔터티를 추출 하는 것과 동일한 목적으로 제공 됩니다. 계층 구조 엔터티가 있는 경우 역할을 사용 하는 단순 엔터티로 간주할 수 있습니다.

V3 제작에서:
* 역할은 machine learning (부모) 엔터티에 적용할 수 있습니다.
* 모든 하위 엔터티에 역할을 적용할 수 없습니다.

이 엔터티는 예입니다. 사용자 고유의 엔터티 마이그레이션에는 다른 고려 사항이 필요할 수 있습니다.

피자를 수정 하기 위한 V2 계층적 엔터티를 고려 합니다 `order` .
* 각 자식은 원래 topping 또는 final topping을 결정 합니다.

이 엔터티에 대 한 예제 utterance는 다음과 같습니다.

`Change the topping from mushrooms to olives`

다음 표에서는 마이그레이션을 보여 줍니다.

|V2 모델|V3 모델|
|--|--|
|부모-구성 요소 엔터티 이름`Order`|부모-컴퓨터-학습 엔터티 이름`Order`|
|원본 및 최종 피자를 사용 하는 자식 계층 구조 엔터티 topping|* `Order` 각 topping에 대 한 역할을에 추가 합니다.|

## <a name="api-change-constraint-replaced-with-required-feature"></a>API 변경 제약 조건이 필수 기능으로 바뀜

이 변경 사항은 빌드 회의에서 2020 년 5 월에 적용 되었으며, 앱이 제한 된 기능을 사용 하는 경우에만 v3 authoring Api에 적용 됩니다. V2 제작에서 v3 authoring로 마이그레이션하거나 v3 제한 기능을 사용 하지 않은 경우이 섹션을 건너뜁니다.

**기능** -다른 모델에 대 한 기능으로 기존 엔터티를 요구 하 고, 엔터티가 검색 된 경우에만 해당 모델을 추출할 수 있습니다. 기능이 변경 되지 않았지만 API와 용어가 변경 되었습니다.

|이전 용어|새 용어|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>자동 마이그레이션

**6 월 19 2020**부터이 기능을 제공 하는 이전 authoring API를 사용 하 여 프로그래밍 방식으로 제약 조건을 만들 수 없습니다.

모든 기존 제약 조건 기능은 필수 기능 플래그로 자동으로 마이그레이션됩니다. 예측 API에는 프로그래밍 방식으로 변경할 필요가 없으며 예측 정확도의 품질에 대 한 변경 사항은 없습니다.

#### <a name="luis-portal-changes"></a>LUIS 포털 변경

LUIS preview 포털은이 기능을 **제약 조건**으로 참조 했습니다. 현재 LUIS 포털은이 기능을 **필수 기능**으로 지정 합니다.

#### <a name="previous-authoring-api"></a>이전 authoring API

이 기능은 엔터티 자식의 속성을 사용 하 여 엔터티 정의의 일부로 미리 보기 제작 **[만들기 엔터티 자식 API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)** 에 적용 되었습니다 `instanceOf` .

```json
{
    "name" : "dayOfWeek",
    "instanceOf": "datetimeV2",
    "children": [
        {
           "name": "dayNumber",
           "instanceOf": "number",
           "children": []
        }
    ]
}
```

#### <a name="new-authoring-api"></a>새 제작 API

이 기능은 이제 및 속성을 사용 하 여 **[엔터티 추가 기능 관계 API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26)** 와 함께 적용 됩니다 `featureName` `isRequired` . 속성의 값은 `featureName` 모델의 이름입니다.

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>다음 단계

* [개발자 리소스](developer-reference-resource.md)
