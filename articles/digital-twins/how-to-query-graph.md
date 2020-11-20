---
title: 쌍 그래프 쿼리
titleSuffix: Azure Digital Twins
description: 자세한 내용은 Azure Digital Twins 쌍 그래프를 쿼리 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperfq2
ms.openlocfilehash: 6533cbde10dfc924bd982357def859229eb1714a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963167"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Azure Digital Twins 쌍 그래프 쿼리

이 문서에서는 쿼리 예제와 **Azure Digital Twins 쿼리 언어** 를 사용 하 여 쌍 [그래프](concepts-twins-graph.md) 에서 정보를 쿼리 하는 방법에 대 한 자세한 지침을 제공 합니다. (쿼리 언어 및 해당 기능의 전체 목록에 대 한 소개는 [*개념: 쿼리 언어*](concepts-query-language.md)를 참조 하세요.)

이 문서는 디지털 쌍에 대 한 쿼리 언어 구조와 일반적인 쿼리 작업을 보여 주는 샘플 쿼리로 시작 합니다. 그런 다음 Azure Digital Twins [쿼리 API](/rest/api/digital-twins/dataplane/query) 또는 [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)를 사용 하 여 쿼리를 작성 한 후 쿼리를 실행 하는 방법을 설명 합니다.

> [!TIP]
> API 또는 SDK 호출을 사용 하 여 아래 샘플 쿼리를 실행 하는 경우 쿼리 텍스트를 한 줄로 축소 해야 합니다.

## <a name="show-all-digital-twins"></a>모든 디지털 쌍 표시

다음은 인스턴스의 모든 디지털 쌍 목록을 반환 하는 기본 쿼리입니다.

```sql
SELECT *
FROM DIGITALTWINS
```

## <a name="query-by-property"></a>속성으로 쿼리

**속성** (ID 및 메타 데이터 포함)으로 디지털 쌍을 가져옵니다.

```sql
SELECT  *
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> 디지털 쌍의 ID는 메타 데이터 필드를 사용 하 여 쿼리 됩니다 `$dtId` .

**특정 속성이 정의 되어 있는지 여부** 에 따라 쌍을 가져올 수도 있습니다. 다음은 정의 된 *Location* 속성을 가진 쌍를 가져오는 쿼리입니다.

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

그러면 태그를 [디지털 쌍에 추가](how-to-use-tags.md)에 설명 된 대로 *태그* 속성에 따라 쌍을 가져오는 데 도움이 될 수 있습니다. *Red* 로 태그가 지정 된 모든 쌍를 가져오는 쿼리는 다음과 같습니다.

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(tags.red)
```

**속성의 형식** 에 따라 쌍을 가져올 수도 있습니다. *온도* 속성이 숫자 인 쌍를 가져오는 쿼리는 다음과 같습니다.

```sql
SELECT * FROM DIGITALTWINS T WHERE IS_NUMBER(T.Temperature)
```

## <a name="query-by-model"></a>모델 별로 쿼리

`IS_OF_MODEL`연산자를 사용 하 여 쌍의 [**모델**](concepts-models.md)을 기준으로 필터링 할 수 있습니다.

[상속](concepts-models.md#model-inheritance) 및 모델 [버전 관리](how-to-manage-model.md#update-models)를 고려 하 고 쌍이 다음 조건 중 하나를 충족 하는 경우 지정 된 쌍에 대해 **true** 로 평가 됩니다.

* 쌍은에 제공 된 모델을 직접 구현 `IS_OF_MODEL()` 하 고 쌍에 있는 모델의 버전 번호는 제공 된 모델의 버전 번호 *보다 크거나 같습니다* .
* 쌍은에 제공 된 모델을 *확장* 하는 모델을 구현 하 `IS_OF_MODEL()` 고 쌍의 확장 된 모델 버전 번호는 제공 된 모델의 버전 번호 *보다 크거나 같습니다* .

예를 들어 모델의 쌍를 쿼리하면 `dtmi:example:widget;4` 쿼리가 **버전 4 이상의** **위젯** 모델을 기반으로 하는 모든 쌍을 반환 하 고, **위젯에 상속 된 모든 모델** 의 버전 **4 이상을** 기반으로 하는 쌍도 반환 합니다.

`IS_OF_MODEL` 는 여러 가지 매개 변수를 사용할 수 있으며이 섹션의 나머지 부분은 다른 오버 로드 옵션에만 사용 됩니다.

를 사용 하는 가장 간단한 방법은 매개 변수를 사용 하는 것 `IS_OF_MODEL` `twinTypeName` `IS_OF_MODEL(twinTypeName)` 입니다.
이 매개 변수에 값을 전달 하는 쿼리 예제는 다음과 같습니다.

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1')
```

가 사용 되는 경우와 같이 두 개 이상 있을 때 검색할 쌍 컬렉션을 지정 하려면 `JOIN` `twinCollection` 매개 변수를 추가 `IS_OF_MODEL(twinCollection, twinTypeName)` 합니다.
이 매개 변수에 대 한 값을 추가 하는 쿼리 예제는 다음과 같습니다.

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1')
```

정확히 일치 하는 항목을 수행 하려면 `exact` 매개 변수를 추가 `IS_OF_MODEL(twinTypeName, exact)` 합니다.
이 매개 변수에 대 한 값을 추가 하는 쿼리 예제는 다음과 같습니다.

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1', exact)
```

세 인수를 모두 함께 전달할 수도 있습니다 `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
세 매개 변수 모두에 대해 값을 지정 하는 쿼리 예제는 다음과 같습니다.

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1', exact)
```

## <a name="query-by-relationship"></a>관계로 쿼리

디지털 쌍의 **관계** 를 기반으로 쿼리 하는 경우 Azure Digital twins 쿼리 언어에는 특별 한 구문이 있습니다.

관계는 절의 쿼리 범위로 끌어옵니다 `FROM` . "기존" SQL 형식 언어와의 중요 한 차이점은이 절의 각 식이 테이블이 아니라는 것입니다 .이 `FROM` `FROM` 절은 엔터티 간 관계 순회를 표현 하 고 Azure Digital twins 버전의를 사용 하 여 작성 됩니다 `JOIN` .

Azure Digital Twins [모델](concepts-models.md) 기능을 사용 하는 경우 관계는 twins와 독립적으로 존재 하지 않습니다. 즉, 여기에서 관계는 독립적으로 쿼리할 수 없으며 쌍에 연결 되어야 하므로 Azure Digital Twins 쿼리 언어는 `JOIN` 일반 SQL과 약간 다릅니다 `JOIN` .
이러한 차이를 반영 하기 위해 키워드는 `RELATED` 절에서 쌍의 `JOIN` 관계 집합을 참조 하는 데 사용 됩니다.

다음 섹션에서는이 처럼 표시 되는 몇 가지 예를 제공 합니다.

> [!TIP]
> 개념적으로이 기능은 `JOIN` 문서 내의 자식 개체에 대해 수행할 수 있는 CosmosDB의 문서 중심 기능을 모방 합니다. CosmosDB는 키워드를 사용 하 여 `IN` `JOIN` 이 현재 컨텍스트 문서 내의 배열 요소를 반복 하도록 지정 합니다.

### <a name="relationship-based-query-examples"></a>관계 기반 쿼리 예제

관계를 포함 하는 데이터 집합을 가져오려면 단일 `FROM` 문 뒤에 N `JOIN` 문을 사용 `JOIN` 합니다. 여기서 문은 이전 또는 문의 결과에 대 한 관계를 표시 합니다 `FROM` `JOIN` .

다음은 관계 기반 쿼리 예제입니다. 이 코드 조각은 *ID* 속성이 ' ABC ' 인 모든 디지털 쌍을 선택 하 고, *포함* 관계를 통해 이러한 디지털 쌍과 관련 된 모든 digital 쌍를 선택 합니다.

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC'
```

> [!NOTE]
> 개발자는이를 `JOIN` 절의 키 값과 상관 관계를 지정 하지 않아도 `WHERE` 됩니다 (또는 정의를 사용 하 여 인라인 키 값 지정 `JOIN` ). 관계 속성 자체가 대상 엔터티를 식별 하므로이 상관 관계는 시스템에 의해 자동으로 계산 됩니다.

### <a name="query-the-properties-of-a-relationship"></a>관계 속성 쿼리

디지털 쌍이 DTDL을 통해 설명 하는 속성을 갖는 방식과 마찬가지로 관계에도 속성이 있을 수 있습니다. **해당 관계의 속성에 따라** 쌍를 쿼리할 수 있습니다.
Azure Digital Twins 쿼리 언어를 사용 하면 절 내의 관계에 별칭을 할당 하 여 관계를 필터링 하 고 투영할 수 있습니다 `JOIN` .

예를 들어 *reportedCondition* 속성이 있는 *servicedBy* relationship을 고려 합니다. 아래 쿼리에서는 속성을 참조 하기 위해이 관계에 ' R '의 별칭이 지정 됩니다.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC'
AND R.reportedCondition = 'clean'
```

위의 예제에서 *reportedCondition* 는 *servicedBy* 관계 자체의 속성 ( *servicedBy* 관계가 있는 일부 디지털 쌍이 아님)을 확인 합니다.

### <a name="query-with-multiple-joins"></a>여러 조인이 있는 쿼리

단일 쿼리에서 최대 5 개까지 `JOIN` 지원 됩니다. 이렇게 하면 여러 수준의 관계를 한 번에 트래버스할 수 있습니다.

다음은 대화방 1과 2의 라이트 패널에 포함 된 모든 라이트 전구을 가져오는 다중 조인 쿼리의 예입니다.

```sql
SELECT LightBulb
FROM DIGITALTWINS Room
JOIN LightPanel RELATED Room.contains
JOIN LightBulb RELATED LightPanel.contains
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="count-items"></a>항목 수 계산

절을 사용 하 여 결과 집합의 항목 수를 계산할 수 있습니다 `Select COUNT` .

```sql
SELECT COUNT()
FROM DIGITALTWINS
```

`WHERE`특정 조건을 충족 하는 항목 수를 계산 하는 절을 추가 합니다. 쌍 모델의 형식에 따라 적용 된 필터를 사용 하 여 계산 하는 몇 가지 예는 다음과 같습니다 .이 구문에 대 한 자세한 내용은 아래 [*모델용 쿼리*](#query-by-model) 를 참조 하세요.

```sql
SELECT COUNT()
FROM DIGITALTWINS
WHERE IS_OF_MODEL('dtmi:sample:Room;1')

SELECT COUNT()
FROM DIGITALTWINS c
WHERE IS_OF_MODEL('dtmi:sample:Room;1') AND c.Capacity > 20
```

`COUNT`절과 함께를 사용할 수도 있습니다 `JOIN` . 다음은 대화방 1과 2의 밝은 패널에 포함 된 모든 라이트 전구의 수를 계산 하는 쿼리입니다.

```sql
SELECT COUNT()  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="filter-results-select-top-items"></a>결과 필터링: 상위 항목 선택

절을 사용 하 여 쿼리에서 여러 "최상위" 항목을 선택할 수 있습니다 `Select TOP` .

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

## <a name="filter-results-specify-return-set-with-projections"></a>결과 필터링: 프로젝션을 사용 하 여 반환 집합 지정

문에 프로젝션을 사용 하 여 `SELECT` 쿼리에서 반환할 열을 선택할 수 있습니다.

>[!NOTE]
>지금은 복합 속성이 지원 되지 않습니다. 프로젝션 속성이 유효한 지 확인 하려면 프로젝션을 검사와 결합 합니다 `IS_PRIMITIVE` .

다음은 프로젝션을 사용 하 여 쌍 및 관계를 반환 하는 쿼리의 예입니다. 다음 쿼리는 ID가 *ABC* 인 *팩터리가* *팩터리* 와의 관계를 통해 *소비자* 와 관련 되는 시나리오에서 *소비자*, *팩터리* 및 *edge* 를 프로젝션 합니다. 이러한 관계는에 *지* 로 표시 됩니다.

```sql
SELECT Consumer, Factory, Edge
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
```

프로젝션을 사용 하 여 쌍의 속성을 반환할 수도 있습니다. 다음 쿼리는 *팩터리의* ID를 사용 하 여 *팩터리에* 관련 된 *소비자* 의 *Name* 속성을 *프로젝트의 관계를 통해 프로젝션* 합니다.

```sql
SELECT Consumer.name
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Consumer.name)
```

프로젝션을 사용 하 여 관계의 속성을 반환할 수도 있습니다. 이전 예제와 마찬가지로 다음 쿼리는 팩터리의 ID를 사용 하 여 *팩터리에* 관련 된 *소비자* 의 *Name* 속성을 투영 *합니다.* *ABC* 그러나 이제 해당 관계의 두 속성 ( *prop1* 및 *prop2*)도 반환 합니다. 관계에 *지* 의 이름을 지정 하 고 해당 속성을 수집 하 여이를 수행 합니다.  

```sql
SELECT Consumer.name, Edge.prop1, Edge.prop2, Factory.area
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

별칭을 사용 하 여 프로젝션을 사용 하 여 쿼리를 단순화할 수도 있습니다.

다음 쿼리는 이전 예제와 동일한 작업을 수행 하지만 속성 이름을,, 및로 별칭으로 `consumerName` 합니다 `first` `second` `factoryArea` .

```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)"
```

다음은 위와 동일한 집합을 쿼리 하지만 *Consumer.name* 속성만로 프로젝션 하 `consumerName` 고 전체 *팩터리* 를 쌍으로 프로젝션 하는 비슷한 쿼리입니다.

```sql
SELECT Consumer.name AS consumerName, Factory
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name)
```

## <a name="build-efficient-queries-with-the-in-operator"></a>IN 연산자를 사용 하 여 효율적인 쿼리 작성

쌍의 배열을 작성 하 고 연산자를 사용 하 여 쿼리 하는 데 필요한 쿼리 수를 크게 줄일 수 있습니다 `IN` . 

예를 들어 *건물* 에 *층* 이 포함 되어 있고 *층* 이 *방을* 포함 하는 시나리오를 가정해 보겠습니다. 핫의 건물 내에서 방을 검색 하기 위해 다음 단계를 수행 하는 것이 한 가지 방법입니다.

1. 관계에 따라 건물에서 층 찾기 `contains`

    ```sql
    SELECT Floor
    FROM DIGITALTWINS Building
    JOIN Floor RELATED Building.contains
    WHERE Building.$dtId = @buildingId
    ```

2. 한 층을 하나씩 고려 하 고 쿼리를 실행 `JOIN` 하 여 각 항목에 대 한 방을 찾는 대신 회의실을 찾으려면 빌딩에서 층의 컬렉션을 사용 하 여 쿼리할 수 있습니다 (아래 쿼리에서 *바닥* 이라고 명명).

    클라이언트 앱에서:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    쿼리:
    
    ```sql
    
    SELECT Room
    FROM DIGITALTWINS Floor
    JOIN Room RELATED Floor.contains
    WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
    AND Room. Temperature > 72
    AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
    
    ```

## <a name="other-compound-query-examples"></a>기타 복합 쿼리 예제

조합 연산자를 사용 하 여 위의 쿼리 유형을 조합 하 여 단일 쿼리에 자세한 정보를 **포함할 수 있습니다** . 다음은 한 번에 둘 이상의 쌍 설명자 유형을 쿼리 하는 복합 쿼리의 몇 가지 추가 예입니다.

| Description | 쿼리 |
| --- | --- |
| *공간 123* 가 있는 장치에서 운영자 역할을 하는 MxChip 장치를 반환 합니다. | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contoso:com:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| ID가 *id1* 인 다른 쌍이 *포함* 된 relationship 이라는 관계가 있는 쌍을 가져옵니다. | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| *Floor11* 에 포함 된이 대화방 모델의 모든 대화방 가져오기 | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contoso:com:DigitalTwins:Room;1')` |

## <a name="run-queries-with-the-api"></a>API를 사용 하 여 쿼리 실행

쿼리 문자열을 결정 했으면 [**쿼리 API**](/rest/api/digital-twins/dataplane/query)를 호출 하 여 실행 합니다.

API를 직접 호출 하거나 Azure Digital Twins에 사용할 수 있는 [sdk](how-to-use-apis-sdks.md#overview-data-plane-apis) 중 하나를 사용할 수 있습니다.

다음 코드 조각에서는 클라이언트 앱에서 [.net (c #) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 호출을 보여 줍니다.

```csharp
    string adtInstanceEndpoint = "https://<your-instance-hostname>";

    var credential = new DefaultAzureCredential();
    DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceEndpoint), credential);

    // Run a query for all twins   
    string query = "SELECT * FROM DIGITALTWINS";
    AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);
```

이 호출은 [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin?view=azure-dotnet&preserve-view=true) 개체 형식으로 쿼리 결과를 반환 합니다.

쿼리 호출은 페이징을 지원 합니다. 다음은 `BasicDigitalTwin` 오류 처리 및 페이징을 사용 하 여 쿼리 결과 형식으로를 사용 하는 전체 예제입니다.

```csharp
try
{
    await foreach(BasicDigitalTwin twin in result)
        {
            // You can include your own logic to print the result
            // The logic below prints the twin's ID and contents
            Console.WriteLine($"Twin ID: {twin.Id} \nTwin data");
            IDictionary<string, object> contents = twin.Contents;
            foreach (KeyValuePair<string, object> kvp in contents)
            {
                Console.WriteLine($"{kvp.Key}  {kvp.Value}");
            }
        }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"Error {e.Status}: {e.Message}");
    throw;
}
```

## <a name="next-steps"></a>다음 단계

이 문서의 쿼리를 실행 하는 데 사용 되는 쿼리 API를 포함 하 여 [Azure Digital Twins api 및 sdk](how-to-use-apis-sdks.md)에 대해 자세히 알아보세요.
