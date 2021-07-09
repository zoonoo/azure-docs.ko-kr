---
title: V3 기계 학습 엔터티로 마이그레이션
description: V3 작성은 기계 학습 엔터티 및 기타 엔터티 또는 애플리케이션의 기능에 관계를 추가하는 기능과 함께 새로운 엔터티 형식인 기계 학습 엔터티를 제공합니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/28/2021
ms.openlocfilehash: 6808c5d84a1be6a2e9b262eab00010d4a962c259
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110701171"
---
# <a name="migrate-to-v3-authoring-entity"></a>V3 제작 엔터티로 마이그레이션

V3 작성은 기계 학습 엔터티 및 기타 엔터티 또는 애플리케이션의 기능에 관계를 추가하는 기능과 함께 새로운 엔터티 형식인 기계 학습 엔터티를 제공합니다. 현재 마이그레이션을 완료해야 하는 날짜는 없습니다.

## <a name="entities-are-decomposable-in-v3"></a>엔터티는 V3에서 분해 가능

[API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)를 사용하거나 포털에서 V3 작성 API로 만든 엔터티를 통해 부모 및 자식으로 계층화된 엔터티 모델을 빌드할 수 있습니다. 부모는 **기계 학습 엔터티** 로 알려져 있고 자식은 기계 학습 엔터티의 **하위 엔터티** 로 알려져 있습니다.

각 하위 엔터티는 기계 학습 엔터티이지만 기능 구성 옵션이 추가되어 있습니다.

* **필수 기능** 은 기능과 일치할 때 엔터티를 추출할 수 있도록 보장하는 규칙입니다. 규칙은 모델에 대한 필수 기능에 의해 정의됩니다.
    * [미리 빌드된 엔터티](luis-reference-prebuilt-entities.md)
    * [정규식 엔터티](reference-entity-regular-expression.md)
    * [목록 엔터티](reference-entity-list.md)입니다.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>새 관계를 V2 작성과 비교하는 방법

V2는 역할 및 기능과 함께 계층 구조 및 복합 엔터티를 제공하여 이와 동일한 작업을 수행합니다. 엔터티, 기능 및 역할은 서로 명시적으로 관련되지 않아 예측 중에 LUIS에서 관계를 암시하는 방법을 이해하기 어렵습니다.

V3에서 관계는 명시적이며 앱 작성자가 설계합니다. 이를 통해 앱 작성자가 다음을 수행할 수 있습니다.

* 예제 발화에서 LUIS가 이러한 관계를 예측하는 방법을 시각적으로 확인
* [대화형 테스트 창](luis-interactive-test.md) 또는 엔드포인트를 사용하여 이러한 관계를 테스트
* 잘 구조화되고 명명된 중첩 [.json 개체](reference-entity-machine-learned-entity.md)를 통해 클라이언트 애플리케이션에서 이러한 관계를 사용

## <a name="planning"></a>계획

마이그레이션할 때 마이그레이션 계획에서 다음 사항을 고려합니다.

* LUIS 앱을 백업하고 별도의 앱에서 마이그레이션을 수행합니다. V2 및 V3 앱을 동시에 사용할 수 있으면 필요한 변경 내용과 예측 결과에 미치는 영향을 확인할 수 있습니다.
* 현재 예측 성공 메트릭 캡처
* 현재 대시보드 정보를 앱 상태의 스냅샷으로 캡처
* 기존 의도, 엔터티, 구문 목록, 패턴 및 일괄 테스트 검토
* 다음 요소는 **변경하지 않고** 마이그레이션할 수 있습니다.
    * 의도
    * 엔터티
        * 정규식 엔터티
        * 목록 엔터티
    * 기능
        * 구문 목록
* 다음 요소는 **변경 내용과 함께** 마이그레이션해야 합니다.
    * 엔터티
        * 계층적 엔터티
        * 복합 엔터티
    * 역할 - 역할은 기계 학습(부모) 엔터티에만 적용할 수 있습니다. 하위 엔터티에는 역할을 적용할 수 없습니다.
    * 계층 구조 및 복합 엔터티를 사용하는 일괄 테스트 및 패턴

마이그레이션 계획을 설계할 때 모든 계층 구조 및 복합 엔터티가 마이그레이션된 후 최종 기계 학습 엔터티를 검토합니다. 직접 마이그레이션이 작동하는 동안 일괄 테스트 결과와 예측 JSON을 변경 및 검토한 후에는 보다 통합된 JSON으로 인해 클라이언트 쪽 앱에 제공되는 최종 정보가 다르게 구성될 수 있습니다. 이는 코드 리팩터링과 비슷하며 조직이 보유하고 있는 동일한 검토 프로세스로 처리되어야 합니다.

V2 모델에 대해 일괄 테스트를 수행하지 않고, 마이그레이션의 일부로 일괄 테스트를 V3 모델로 마이그레이션하는 경우 마이그레이션이 엔드포인트 예측 결과에 어떤 영향을 주는지 검사할 수 없습니다.

## <a name="migrating-from-v2-entities"></a>V2 엔터티에서 마이그레이션

V3 작성 모델로 이동할 때 기계 학습 엔터티와 해당 하위 엔터티 및 기능으로 이동하는 방법을 고려해야 합니다.

다음 표에서는 V2에서 V3 엔터티 디자인으로 마이그레이션하는 데 필요한 엔터티를 설명합니다.

|V2 작성 엔터티 형식|V3 작성 엔터티 형식|예제|
|--|--|--|
|복합 엔터티|기계 학습된 엔터티|[자세한 정보](#migrate-v2-composite-entity)|
|계층적 엔터티|기계 학습 엔터티의 역할|[자세한 정보](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>V2 복합 엔터티 마이그레이션

V2 복합 엔터티의 각 자식은 V3 기계 학습 엔터티의 하위 엔터티로 표시되어야 합니다. 복합 자식이 미리 빌드된 정규식 또는 목록 엔터티인 경우 이 항목을 하위 엔터티의 필수 기능으로 적용해야 합니다.

복합 엔터티를 기계 학습 엔터티로 마이그레이션할 계획인 경우 고려 사항:
* 자식 엔터티는 패턴에서 사용할 수 없음
* 하위 엔터티는 더 이상 공유되지 않음
* 자식 엔터티가 비 기계 학습 엔터티로 사용되는 경우 레이블을 지정해야 함

### <a name="existing-features"></a>기존 기능

복합 엔터티에서 단어를 개선하는 데 사용되는 모든 구문 목록은 기계 학습 엔터티(부모), 하위 엔터티(자식) 또는 의도에 기능으로 적용되어야 합니다(구문 목록이 한 의도에만 적용되는 경우). 가장 현저하게 향상되어야 하는 엔터티에 기능을 추가하도록 계획합니다. 하위 엔터티(자식)의 예측이 가장 크게 향상되는 경우 일반적으로 기계 학습 엔터티(부모)에 이 기능을 추가하지 않습니다.

### <a name="new-features"></a>새로운 기능

V3 작성에서 모든 엔터티 및 의도에 대한 가능한 기능으로 엔터티를 평가하는 계획 단계를 추가합니다.

### <a name="example-entity"></a>엔터티 예

이 엔터티는 예로 든 것일 뿐입니다. 사용자 고유의 엔터티 마이그레이션에는 다른 고려 사항이 필요할 수 있습니다.

다음을 사용하는 피자 `order`를 수정하기 위해 V2 복합을 고려합니다.
* 배달 시간에 대한 미리 빌드된 datetimeV2
* 피자, 파이, 크러스트 및 토핑과 같은 특정 단어를 개선하기 위한 구문 목록
* 버섯, 올리브, 페퍼로니 토핑을 검색하기 위한 목록 엔터티

이 엔터티에 대한 예제 발화는 다음과 같습니다.

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

다음 표에서는 마이그레이션을 보여 줍니다.

|V2 모델|V3 모델|
|--|--|
|부모 - 이름이 `Order`인 복합 엔터티|부모 - 이름이 `Order`인 기계 학습 엔터티|
|자식 - 미리 빌드된 datetimeV2|* 미리 빌드된 엔터티를 새 앱으로 마이그레이션합니다.<br>* 미리 빌드된 datetimeV2의 부모에 필수 기능을 추가합니다.|
|자식 - 토핑에 대한 목록 엔터티|* 목록 엔터티를 새 앱으로 마이그레이션합니다.<br>* 그런 다음 목록 엔터티의 부모에 필수 기능을 추가합니다.|


## <a name="migrate-v2-hierarchical-entity"></a>V2 계층적 엔터티 마이그레이션

V2 작성에서 계층적 엔터티는 LUIS의 기존 역할 이전에 제공되었습니다. 둘 다 컨텍스트 사용을 기준으로 엔터티를 추출하는 동일한 목적으로 제공됩니다. 계층적 엔터티가 있는 경우 역할을 사용하는 단순 엔터티로 간주할 수 있습니다.

V3 작성에서:
* 역할은 기계 학습(부모) 엔터티에 적용할 수 있습니다.
* 모든 하위 엔터티에 역할을 적용할 수는 없습니다.

이 엔터티는 예로 든 것일 뿐입니다. 사용자 고유의 엔터티 마이그레이션에는 다른 고려 사항이 필요할 수 있습니다.

피자 `order`를 수정하기 위해 V2 계층적 엔터티를 고려합니다.
* 각 자식은 원래 토핑 또는 최종 토핑을 결정합니다.

이 엔터티에 대한 예제 발화는 다음과 같습니다.

`Change the topping from mushrooms to olives`

다음 표에서는 마이그레이션을 보여 줍니다.

|V2 모델|V3 모델|
|--|--|
|부모 - 이름이 `Order`인 복합 엔터티|부모 - 이름이 `Order`인 기계 학습 엔터티|
|자식 - 원래 및 최종 피자 토핑을 사용한 계층적 엔터티|* 각 토핑에 대해 역할을 `Order`에 추가합니다.|

## <a name="api-change-constraint-replaced-with-required-feature"></a>API 변경 제약 조건이 필수 기능으로 바뀜

이 변경 사항은 2020년 5월에 빌드 회의에서 적용되었으며, 앱이 제한된 기능을 사용하는 경우에만 v3 작성 API에 적용됩니다. V2 작성에서 v3 작성으로 마이그레이션하거나 v3 제한 기능을 사용하지 않은 경우 이 섹션을 건너뜁니다.

**기능** - 다른 모델에 대한 기능으로 기존 엔터티를 요구하고, 엔터티가 검색된 경우에만 해당 모델을 추출할 수 있습니다. 기능은 변경되지 않았지만 API와 용어가 변경되었습니다.

|이전 용어|새 용어|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>자동 마이그레이션

**2020년 6월 19일** 부터 이 기능을 제공하는 이전 작성 API를 사용하여 프로그래밍 방식으로 제약 조건을 만들 수 없습니다.

모든 기존 제약 조건 기능은 필수 기능 플래그로 자동으로 마이그레이션됩니다. 예측 API를 프로그래밍 방식으로 변경할 필요가 없으며 예측 정확도의 품질에 대한 변경 사항은 없습니다.

#### <a name="luis-portal-changes"></a>LUIS 포털 변경

LUIS 미리 보기 포털은 이 기능을 **제약 조건** 으로 참조했습니다. 현재 LUIS 포털은 이 기능을 **필수 기능** 으로 지정합니다.

#### <a name="previous-authoring-api"></a>이전 작성 API

이 기능은 엔터티 자식의 `instanceOf` 속성을 사용하여 엔터티 정의의 일부로 미리 보기 작성 **[엔터티 자식 API 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)** 에 적용되었습니다.

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

#### <a name="new-authoring-api"></a>새 작성 API

이제 이 기능은 `featureName` 및 `isRequired` 속성을 사용하여 **[엔터티 기능 관계 API 추가](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26)** 에 적용됩니다. `featureName` 속성의 값은 모델의 이름입니다.

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>다음 단계

* [개발자 리소스](developer-reference-resource.md)
