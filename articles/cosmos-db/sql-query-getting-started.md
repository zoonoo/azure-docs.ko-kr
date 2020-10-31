---
title: Azure Cosmos DB에서 SQL 쿼리 시작
description: SQL 쿼리를 사용 하 여 Azure Cosmos DB에서 데이터를 쿼리 하는 방법에 대해 알아봅니다. Azure Cosmos DB의 컨테이너에 샘플 데이터를 업로드 하 고 쿼리할 수 있습니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: tisande
ms.openlocfilehash: ee38e076e76111f4f4e6bf3b8fa8dde8c3debcfc
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100816"
---
# <a name="getting-started-with-sql-queries"></a>SQL 쿼리 시작
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

SQL API 계정 Azure Cosmos DB에서는 두 가지 방법으로 데이터를 읽을 수 있습니다.

**Point reads** -단일 *항목 ID* 및 파티션 키에 대 한 키/값 조회를 수행할 수 있습니다. *항목 ID* 와 파티션 키 조합은 키 이며 항목 자체는 값입니다. 1kb 문서의 경우 지점은 일반적으로 10 밀리초 미만의 대기 시간으로 비용 1 [요청 단위](request-units.md) 를 읽습니다. Point reads는 단일 항목을 반환 합니다.

**Sql 쿼리** -JSON 쿼리 언어로 구조적 쿼리 언어 (sql)를 사용 하 여 쿼리를 작성 하 여 데이터를 쿼리할 수 있습니다. 쿼리는 항상 최소 2.3 요청 단위를 비용으로 청구 하며, 일반적으로 지점 읽기 보다 더 높은 가변 대기 시간을 갖습니다. 쿼리에서 많은 항목을 반환할 수 있습니다.

Azure Cosmos DB에 대 한 대부분의 읽기 작업은 지점 읽기와 SQL 쿼리의 조합을 사용 합니다. 단일 항목을 읽어야 하는 경우 쿼리 보다 더 저렴 하 고 더 빠른 지점 읽기가 있습니다. Point reads는 쿼리 엔진을 사용 하 여 데이터에 액세스할 필요가 없으며 데이터를 직접 읽을 수 있습니다. 물론, 모든 작업에서 지점 읽기를 사용 하 여 데이터를 독점적으로 읽을 수 있는 것은 아니므로 SQL을 쿼리 언어로 지원 하 고 [스키마에 관계 없는 인덱싱을](index-overview.md) 사용 하 여 데이터에 보다 유연 하 게 액세스할 수 있습니다.

다음은 각 SDK를 사용 하 여 요소 읽기를 수행 하는 방법에 대 한 몇 가지 예입니다.

- [.NET SDK](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?preserve-view=true&view=azure-dotnet)
- [Java SDK](/java/api/com.azure.cosmos.cosmoscontainer.readitem?preserve-view=true&view=azure-java-stable#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](/javascript/api/@azure/cosmos/item?preserve-view=true&view=azure-node-latest#read-requestoptions-)
- [Python SDK](/python/api/azure-cosmos/azure.cosmos.containerproxy?preserve-view=true&view=azure-python#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

이 문서의 나머지 부분에서는 Azure Cosmos DB에서 SQL 쿼리 작성을 시작 하는 방법을 보여 줍니다. SQL 쿼리는 SDK 또는 Azure Portal를 통해 실행할 수 있습니다.

## <a name="upload-sample-data"></a>샘플 데이터 업로드

SQL API Cosmos DB 계정에서 라는 컨테이너를 만듭니다 `Families` . 컨테이너에 두 개의 간단한 JSON 항목을 만듭니다. 이 데이터 집합을 사용 하 여 Azure Cosmos DB 쿼리 설명서에서 대부분의 샘플 쿼리를 실행할 수 있습니다.

### <a name="create-json-items"></a>JSON 항목 만들기

다음 코드는 패밀리에 대해 두 개의 간단한 JSON 항목을 만듭니다. Andersen 및 Wakefield 제품군에 대 한 간단한 JSON 항목에는 부모, 자식, 애완 동물, 주소 및 등록 정보가 포함 됩니다. 첫 번째 항목에는 문자열, 숫자, 부울, 배열 및 중첩 속성이 있습니다.

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

두 번째 항목은 `givenName` 및 대신 및를 사용 `familyName` `firstName` `lastName` 합니다.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>JSON 항목 쿼리

JSON 데이터에 대 한 몇 가지 쿼리를 수행 하 여 Azure Cosmos DB의 SQL 쿼리 언어에 대 한 몇 가지 주요 측면을 이해 합니다.

다음 쿼리는 필드가 일치 하는 항목을 반환 합니다 `id` `AndersenFamily` . 쿼리 이기 때문에 `SELECT *` 쿼리 출력은 전체 JSON 항목입니다. SELECT 구문에 대 한 자세한 내용은 [select 문](sql-query-select.md)을 참조 하십시오.

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

쿼리 결과는 다음과 같습니다.

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

다음 쿼리는 JSON 출력을 다른 모양으로 다시 포맷 합니다. 이 쿼리는 `Family` 두 개의 선택 된 필드인 `Name` 및 `City` 주소 도시가 상태와 같은 새 JSON 개체를 프로젝션 합니다. "서 면"은 이러한 경우와 일치 합니다.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

쿼리 결과는 다음과 같습니다.

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

다음 쿼리는 `id` 도시별로 정렬 된 일치 항목의 모든 자식을 포함 하는 이름을 반환 합니다 `WakefieldFamily` .

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

결과는 다음과 같습니다.

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>설명

위의 예에서는 Cosmos DB 쿼리 언어의 몇 가지 측면을 보여 줍니다.  

* SQL API는 JSON 값에 대해 작동 하므로 행과 열 대신 트리 모양의 엔터티를 처리 합니다. `Node1.Node2.Node3…..Nodem`ANSI SQL의 두 부분으로 된 참조와 마찬가지로와 같이 임의의 깊이에서 트리 노드를 참조할 수 있습니다 `<table>.<column>` .

* 쿼리 언어는 스키마가 있는 데이터와 함께 작동 하므로 유형 시스템을 동적으로 바인딩해야 합니다. 항목에 따라 동일한 식이 다른 형식을 생성할 수 있습니다. 쿼리 결과는 유효한 JSON 값 이지만 고정 스키마가 아닐 수도 있습니다.  

* Azure Cosmos DB는 엄격한 JSON 항목만 지원합니다. 형식 시스템과 식이 JSON 형식만 처리하도록 제한됩니다. 자세한 내용은 [JSON 사양](https://www.json.org/)을 참조하세요.  

* Cosmos 컨테이너는 JSON 항목의 스키마 없는 컬렉션입니다. 컨테이너 항목 간의 관계는 기본 키 및 외래 키 관계가 아닌 포함을 통해 암시적으로 캡처됩니다. 이 기능은이 문서의 뒷부분에서 설명 하는 항목 내 조인에 중요 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT 절](sql-query-select.md)