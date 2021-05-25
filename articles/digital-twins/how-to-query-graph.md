---
title: 트윈 그래프 쿼리
titleSuffix: Azure Digital Twins
description: 자세한 내용은 Azure Digital Twins 트윈 그래프를 쿼리하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 3fd504ec36abae3f00cd2a7eb4e1f7b639be0cea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462680"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Azure Digital Twins 트윈 그래프 쿼리

이 문서에서는 **Azure Digital Twins 쿼리 언어** 를 사용하여 [트윈 그래프](concepts-twins-graph.md)에서 정보를 쿼리하는 방법에 대한 쿼리 예제와 자세한 지침을 제공합니다. (쿼리 언어에 대한 소개와 전체 기능 목록은 [*개념: 쿼리 언어*](concepts-query-language.md)를 참조하세요.)

이 문서는 쿼리 언어 구조와 디지털 트윈에 대한 일반적인 쿼리 작업을 설명하는 샘플 쿼리로 시작합니다. 그런 다음, 쿼리를 작성한 후 Azure Digital Twins [쿼리 API](/rest/api/digital-twins/dataplane/query) 또는 [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)를 사용하여 쿼리를 실행하는 방법을 설명합니다.

> [!NOTE]
> API 또는 SDK 호출로 아래 샘플 쿼리를 실행하는 경우 쿼리 텍스트를 한 줄로 압축해야 합니다.

## <a name="show-all-digital-twins"></a>모든 디지털 트윈 표시

다음은 인스턴스의 모든 디지털 트윈 목록을 반환하는 기본 쿼리입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="query-by-property"></a>속성별 쿼리

**속성**(ID 및 메타 데이터 포함)별로 디지털 트윈을 가져옵니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty1":::

위의 쿼리에 표시된 것처럼 디지털 트윈의 ID는 메타데이터 필드 `$dtId`를 사용하여 쿼리됩니다.

>[!TIP]
> Cloud Shell에서 `$`로 시작하는 메타데이터 필드를 사용하여 쿼리를 실행하는 경우, Cloud Shell에서 이 필드가 변수가 아니고 쿼리 텍스트에서 리터럴로 사용되어야 함을 인식할 수 있도록 `$`를 백틱으로 이스케이프해야 합니다.

또한 **특정 속성이 정의되었는지 여부** 에 따라 트윈을 가져올 수 있습니다. 다음은 정의된 *위치* 속성이 있는 트윈을 가져오는 쿼리입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty2":::

이렇게 하면 [디지털 트윈에 태그 추가](how-to-use-tags.md)에 설명된 대로 *태그* 속성별로 트윈을 가져오는 데 도움이 될 수 있습니다. 다음은 *적색* 으로 태그가 지정된 모든 트윈을 가져오는 쿼리입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

**속성의 형식** 에 따라 트윈을 가져올 수도 있습니다. 다음은 *온도* 속성이 숫자인 트윈을 가져오는 쿼리입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty3":::

>[!TIP]
> 속성이 `Map` 형식인 경우 다음과 같이 쿼리에서 직접 매핑 키와 값을 사용할 수 있습니다.
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty4":::

## <a name="query-by-model"></a>모델별 쿼리

`IS_OF_MODEL` 연산자는 트윈의 [**모델**](concepts-models.md)을 기준으로 필터링하는 데 사용할 수 있습니다.

[상속](concepts-models.md#model-inheritance) 및 모델 [버전 관리](how-to-manage-model.md#update-models)를 고려하고 트윈이 다음 조건 중 하나를 충족하는 경우 주어진 트윈에 대해 **true** 로 평가됩니다.

* 트윈은 `IS_OF_MODEL()`에 제공된 모델을 직접 구현하고, 트윈에 있는 모델의 버전 번호는 제공된 모델의 버전 번호보다 *크거나 같습니다*.
* 트윈은 `IS_OF_MODEL()`에 제공된 모델로 *확장* 하는 모델을 구현하고, 트윈의 확장 모델 버전 번호는 제공된 모델의 버전 번호보다 *크거나 같습니다*.

예를 들어 모델 `dtmi:example:widget;4`의 트윈을 쿼리하면 쿼리가 **버전 4 이상의** **위젯** 모델을 기반으로 하는 모든 트윈을 반환하고, **위젯에서 상속된되는 모든 모델** 의 버전 **4 이상** 을 기반으로 하는 트윈도 반환합니다.

`IS_OF_MODEL`은 여러 다른 매개 변수를 사용할 수 있으며 이 섹션의 나머지 부분은 다양한 오버로드 옵션만을 위한 것입니다.

`IS_OF_MODEL`을 가장 간단하게 사용할 때는 `twinTypeName` 매개 변수만 사용합니다(`IS_OF_MODEL(twinTypeName)`).
다음은 이 매개 변수의 값을 전달하는 쿼리 예입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel1":::

둘 이상의 컬렉션이 있을 때(예: `JOIN`이 사용되는 경우) 검색할 트윈 컬렉션을 지정하려면 `twinCollection` 매개 변수를 추가합니다(`IS_OF_MODEL(twinCollection, twinTypeName)`).
다음은 이 매개 변수의 값을 추가하는 쿼리 예입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel2":::

정확히 일치시키려면 `exact` 매개 변수를 추가합니다(`IS_OF_MODEL(twinTypeName, exact)`).
다음은 이 매개 변수의 값을 추가하는 쿼리 예입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel3":::

세 가지 인수를 모두 함께 전달할 수도 있습니다(`IS_OF_MODEL(twinCollection, twinTypeName, exact)`).
다음은 세 매개 변수의 값을 모두 지정하는 하는 쿼리 예입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel4":::

## <a name="query-by-relationship"></a>관계별 쿼리

디지털 트윈의 **관계** 를 기반으로 쿼리하는 경우 Azure Digital Twins 쿼리 언어에 특수 구문이 사용됩니다.

관계는 `FROM` 절의 쿼리 범위로 가져옵니다. "클래식" SQL 유형 언어와의 중요한 차이점은 이 `FROM` 절의 각 표현식이 테이블이 아니라, 오히려 `FROM` 절은 엔터티간 관계 순회를 나타내며 `JOIN`의 Azure Digital Twins 버전으로 작성됩니다.

Azure Digital Twins [모델](concepts-models.md) 기능을 사용하면 관계가 트윈과 별개로 존재하지 않습니다. 즉, Azure Digital Twins 쿼리 언어의 `JOIN`에서는 관계를 독립적으로 쿼리할 수 없고 트윈에 연결되어야 하기 때문에 일반 SQL `JOIN`과 약간 다릅니다.
이러한 차이점을 통합하기 위해 `JOIN` 절에서 `RELATED` 키워드를 사용하여 트윈의 관계 세트를 참조합니다.

다음 섹션에서는 이처럼 표시되는 몇 가지 예를 제공합니다.

> [!TIP]
> 개념적으로 이 기능은 CosmosDB의 문서 중심 기능을 모방합니다. 여기서 `JOIN`은 문서 내의 자식 개체에 대해 수행할 수 있습니다. CosmosDB는 `IN` 키워드를 사용하여 `JOIN`이 현재 컨텍스트 문서 내의 배열 요소를 반복하도록 의도되었음을 나타냅니다.

### <a name="relationship-based-query-examples"></a>관계 기반 쿼리 예

관계를 포함하는 데이터 세트를 가져오려면 단일 `FROM` 문과 N개의 `JOIN` 문을 차례로 사용합니다. 여기서 `JOIN` 문은 이전 `FROM` 또는 `JOIN` 문의 결과에 관계를 나타냅니다.

다음은 관계 기반 샘플 쿼리입니다. 이 코드 조각은 *ID* 속성이 'ABC'인 모든 디지털 트윈을 선택하고 *포함* 관계를 통해 이러한 디지털 트윈과 관련된 모든 디지털 트윈을 선택합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship1":::

> [!NOTE]
> 개발자는 이 `JOIN`를을 `WHERE` 절의 키 값과 연관시킬 필요가 없습니다(또는 `JOIN` 정의와 함께 키 값을 인라인으로 지정할 필요 없음). 관계 속성 자체가 대상 엔터티를 식별하기 때문에 이 상관 관계는 시스템에서 자동으로 컴퓨팅됩니다.

### <a name="query-the-properties-of-a-relationship"></a>관계의 속성 쿼리

디지털 트윈이 DTDL을 통해 설명한 속성을 갖는 방식과 마찬가지로 관계에도 속성이 있을 수 있습니다. **해당 관계의 속성을 기반으로** 트윈을 쿼리할 수 있습니다.
Azure Digital Twins 쿼리 언어는 `JOIN` 절 내에서 관계에 별칭을 할당하여 관계의 필터링 및 프로젝션을 허용합니다.

예를 들어 *reportedCondition* 속성이 있는 *servicedBy* 관계를 고려합니다. 아래 쿼리에서는 속성을 참조하기 위해 이 관계에 이라는 별칭 'R'을 지정합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship2":::

위의 예에서 *reportedCondition* 이 어떻게 *servicedBy* 관계 자체의 속성인지 확인합니다(*servicedBy* 관계가 있는 일부 디지털 트윈의 속성은 아님).

### <a name="query-with-multiple-joins"></a>여러 JOIN을 사용하여 쿼리

단일 쿼리에서는 최대 5개의 `JOIN`이 지원됩니다. 이렇게 하면 한 번에 여러 수준의 관계를 이동할 수 있습니다.

다음은 방 1과 2의 조명 패널에 포함된 모든 전구를 가져오는 다중 조인 쿼리의 예입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship3":::

## <a name="count-items"></a>항목 계산

`Select COUNT` 절을 사용하여 결과 집합의 항목 수를 계산할 수 있습니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount1":::

`WHERE` 절을 추가하여 특정 조건을 충족하는 항목 수를 계산합니다. 다음은 트윈 모델 유형에 따라 적용된 필터로 계산하는 몇 가지 예입니다(이 구문에 대한 자세한 내용은 아래 [*모델별 쿼리*](#query-by-model) 참조).

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount2":::

`COUNT`를 `JOIN` 절과 함께 사용할 수도 있습니다. 다음은 방 1과 2의 조명 패널에 포함된 모든 전구를 계산하는 쿼리입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount3":::

## <a name="filter-results-select-top-items"></a>결과 필터링: 상위 항목 선택

`Select TOP` 절을 사용하여 쿼리의 여러 "최상위" 항목을 선택할 수 있습니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectTop":::

## <a name="filter-results-specify-return-set-with-projections"></a>결과 필터링: 프로젝션을 사용하여 반환 집합 지정

`SELECT` 문에서 프로젝션을 사용하여 쿼리가 반환할 열을 선택할 수 있습니다.

>[!NOTE]
>현재 복잡한 속성은 지원되지 않습니다. 프로젝션 특성이 유효한지 확인하려면 프로젝션을 `IS_PRIMITIVE` 검사와 결합합니다.

다음은 프로젝션을 사용하여 트윈과 관계를 반환하는 쿼리의 예입니다. 다음 쿼리는 ID가 *ABC* 인 *Factory* 가 *Factory.Customer* 의 관계를 통해 *Consumer* 와 관련이 있는 시나리오에서 *Consumer*, *Factory* 및 *Edge* 를 프로젝션하고, 그 관계는 *Edge* 로 표시됩니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections1":::

프로젝션을 사용하여 트윈의 속성을 반환할 수도 있습니다. 다음 쿼리는 *Factory.customer* 의 관계를 통해 ID가 *ABC* 인 *Factory* 와 관련된 *Consumer* 의 *Name* 속성을 프로젝션합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections2":::

프로젝션을 사용하여 관계의 속성을 반환할 수도 있습니다. 이전 예와 마찬가지로 다음 쿼리는 *Factory.customer* 의 관계를 통해 ID가 *ABC* 인 *Factory* 와 관련된 *Consumers* 의 *Name* 속성을 프로젝션하지만, 이제 해당 관계의 두 속성인 *prop1* 과 *prop2* 도 반환합니다. 이는 *Edge* 관계 이름을 지정하고 해당 속성을 수집하여 수행합니다.  

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections3":::

별칭을 사용하여 프로젝션으로 쿼리를 단순화할 수도 있습니다.

다음 쿼리는 이전 예와 동일한 작업을 수행하지만 속성 이름의 별칭을 `consumerName`, `first`, `second` 및 `factoryArea`로 지정합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections4":::

다음은 위와 동일한 집합을 쿼리하지만 *Consumer.name* 속성만 `consumerName`로 프로젝션하고 전체 *Factory* 를 트윈으로 프로젝션하는 유사한 쿼리입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections5":::

## <a name="build-efficient-queries-with-the-in-operator"></a>IN 연산자를 사용하여 효율적인 쿼리 빌드

트윈 배열을 빌드하고 `IN` 연산자로 쿼리하여 필요한 쿼리 수를 크게 줄일 수 있습니다. 

예를 들어 *Buildings* 에 *Floors* 가 포함되고 *Floors* 에 *Rooms* 가 포함되는 시나리오를 생각해보겠습니다. 건물 내에서 더운 방을 검색하려면 다음 단계를 따릅니다.

1. `contains` 관계를 기반으로 건물의 층을 찾습니다.

    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWithout":::

2. 방을 찾으려면 Floors을 하나씩 고려하고 `JOIN` 쿼리를 실행하여 각 방을 찾는 대신 건물의 층 컬렉션(아래 쿼리에서 *Floor* 로 이름 지정)으로 쿼리할 수 있습니다.

    클라이언트 앱에서:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    쿼리에서:
    
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWith":::

## <a name="other-compound-query-examples"></a>기타 복합 쿼리 예

조합 연산자를 사용하여 위의 쿼리 유형을 **결합** 하여 단일 쿼리에 더 많은 세부 정보를 포함할 수 있습니다. 다음은 한 번에 둘 이상의 트윈 설명자를 쿼리하는 복합 쿼리의 몇 가지 추가 예입니다.

* *Room 123* 에 있는 디바이스에서 운영자 역할을 수행하는 MxChip 디바이스를 반환합니다.
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples1":::
* ID가 *id1* 인 다른 트윈과 *Contains* 이라는 관계가 있는 트윈을 가져옵니다.
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples2":::
* *floor11* 에 포함된 이 방 모델의 모든 방을 가져옵니다.
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples3":::

## <a name="run-queries-with-the-api"></a>API를 사용하여 쿼리 실행

쿼리 문자열을 결정한 후에는 [**쿼리 API**](/rest/api/digital-twins/dataplane/query)를 호출하여 실행합니다.

API를 직접 호출하거나 Azure Digital Twins에 사용할 수 있는 [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) 중 하나를 사용할 수 있습니다.

다음 코드 조각은 클라이언트 앱에서 [.NET(C#) SDK](/dotnet/api/overview/azure/digitaltwins/client) 호출을 보여줍니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="RunQuery":::

이 호출에 사용된 쿼리는 디지털 트윈 목록을 반환합니다. 위의 예는 [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin) 개체를 나타냅니다. 각 쿼리에 대한 데이터 반환 형식은 `SELECT` 문을 사용하여 지정하는 용어에 따라 달라집니다.
* `SELECT * FROM ...`으로 시작하는 쿼리는 디지털 트윈 목록을 반환합니다(`BasicDigitalTwin` 개체 또는 사용자가 만든 기타 사용자 정의 디지털 트윈 형식으로 직렬화될 수 있음).
* `SELECT <A>, <B>, <C> FROM ...` 형식으로 시작하는 쿼리는 `<A>`, `<B>` 및 `<C>` 키가 있는 사전을 반환합니다.
* 사용자 지정 데이터를 반환하도록 다른 형식의 `SELECT` 문을 만들 수 있습니다. 매우 사용자 정의된 결과 집합을 처리하기 위해 고유한 클래스를 만드는 것을 고려할 수 있습니다. 

### <a name="query-with-paging"></a>페이징으로 쿼리

쿼리 호출은 페이징을 지원합니다. 다음은 오류 처리 및 페이징과 함께 `BasicDigitalTwin`를 쿼리 결과 유형으로 사용하는 전체 예입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

## <a name="next-steps"></a>다음 단계

이 문서에서 쿼리를 실행하는 데 사용되는 쿼리 API를 포함하여 [ Azure Digital Twins API 및 SDK ](how-to-use-apis-sdks.md)에 대해 자세히 알아보세요.
