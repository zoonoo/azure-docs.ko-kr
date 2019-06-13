---
title: Azure Cosmos DB의 인덱싱 정책 관리
description: Azure Cosmos DB의 인덱싱 정책 관리 방법을 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 05fd369cfebba03c814507f82755fa6cb6a89400
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386793"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB의 인덱싱 정책 관리

Azure Cosmos DB에서 데이터는 각 컨테이너에 대해 정의된 [인덱싱 정책](index-policy.md)에 따라 인덱싱됩니다. 새로 만든 컨테이너에 대한 기본 인덱싱 정책은 임의 문자열 또는 숫자에 대해 범위 인덱스를 적용하며, Point 형식의 GeoJSON에 대해서는 공간 인덱스를 적용합니다. 이 정책을 다음과 같은 방법으로 재정의할 수 있습니다.

- Azure Portal에서
- Azure CLI를 사용하여
- SDK 중 하나를 사용하여

[인덱싱 정책 업데이트](index-policy.md#modifying-the-indexing-policy)는 인덱스 변환을 트리거합니다. 이 변환의 진행률을 SDK에서 추적할 수도 있습니다.

> [!NOTE]
> SDK 및 포털 업그레이드의 일환으로 새 컨테이너에 배포된 새 인덱스 레이아웃에 맞게 인덱스 정책을 개선하는 중입니다. 이 새 레이아웃을 사용하면 모든 기본 데이터 형식이 전체 자릿수(-1)의 범위로 인덱싱됩니다. 따라서 인덱스 종류 및 전체 자릿수가 사용자에게 더 이상 공개되지 않습니다. 나중에 사용자는 includedPaths 섹션에 경로를 추가하고 indexKinds 및 전체 자릿수를 무시하기만 하면 됩니다. 이러한 변경 사항은 성능에 영향을 주지 않으며, 동일한 구문을 사용하여 인덱싱 정책을 계속 업데이트할 수 있습니다. 기존 문서의 모든 샘플을 계속 사용하여 인덱스 정책을 업데이트할 수 있습니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

Azure Cosmos 컨테이너는 자체의 인덱싱 정책을 Azure Portal에서 직접 편집할 수 있는 JSON 문서로 저장합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 새 Azure Cosmos 계정을 만들거나 기존 계정을 선택합니다.

1. **Data Explorer** 창을 열고 작업할 컨테이너를 선택합니다.

1. **크기 조정 및 설정**을 클릭합니다.

1. 인덱싱 정책 JSON 문서를 수정합니다([아래](#indexing-policy-examples) 예제 참조).

1. 완료되면 **저장**을 클릭합니다.

![Azure Portal을 사용하여 인덱싱 관리](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Azure CLI 사용

Azure CLI의 [az cosmosdb collection update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) 명령을 사용하여 컨테이너의 인덱싱 정책에 대한 JSON 정의를 바꿀 수 있습니다.

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>.NET SDK V2 사용

[.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)(해당 사용법에 관한 [이 빠른 시작](create-sql-api-dotnet.md) 참조)의 `DocumentCollection` 개체는 `IndexingMode`를 변경하고 `IncludedPaths` 및 `ExcludedPaths`를 추가 또는 제거할 수 있는 `IndexingPolicy` 속성을 표시합니다.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// set the indexing mode to Consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/headquarters/employees/?" });
// update the container with our changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

인덱스 변환 진행률을 추적하려면 `PopulateQuotaInfo` 속성을 `true`로 설정하는 `RequestOptions` 개체를 전달합니다.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Java SDK 사용

[Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)(해당 사용법에 관한 [이 빠른 시작](create-sql-api-java.md) 참조)의 `DocumentCollection` 개체는 `getIndexingPolicy()` 및 `setIndexingPolicy()` 메서드를 표시합니다. 해당 메서드가 조작하는 `IndexingPolicy` 개체를 사용하여 인덱싱 모드를 변경하고 포함된 경로 및 제외된 경로를 추가 또는 제거할 수 있습니다.

```java
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
    DocumentCollection container = result.getResource();
    IndexingPolicy indexingPolicy = container.getIndexingPolicy();
    // set the indexing mode to Consistent
    indexingPolicy.setIndexingMode(IndexingMode.Consistent);
    Collection<ExcludedPath> excludedPaths = new ArrayList<>();
    ExcludedPath excludedPath = new ExcludedPath();
    excludedPath.setPath("/*");
    // add an excluded path
    excludedPaths.add(excludedPath);
    indexingPolicy.setExcludedPaths(excludedPaths);
    // update the container with our changes
    client.replaceCollection(container, null);
});
```

컨테이너에 대한 인덱스 변환 진행률을 추적하려면 채워야 할 할당량 정보를 요청하는 `RequestOptions` 개체를 전달한 다음, `x-ms-documentdb-collection-index-transformation-progress` 응답 헤더에서 값을 검색합니다.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Node.js SDK 사용

[Node.js SDK](https://www.npmjs.com/package/@azure/cosmos)(해당 사용법에 관한 [이 빠른 시작](create-sql-api-nodejs.md) 참조)의 `ContainerDefinition` 인터페이스는 `indexingMode`를 변경하고 `includedPaths` 및 `excludedPaths`를 제거할 수 있는 `indexingPolicy` 개체를 표시합니다.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read();
// set the indexing mode to Consistent
containerResponse.body.indexingPolicy.indexingMode = "consistent";
// add an excluded path
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/headquarters/employees/?' });
// update the container with our changes
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

컨테이너에 대한 인덱스 변환 진행률을 추적하려면 `populateQuotaInfo` 속성을 `true`로 설정하는 `RequestOptions` 개체를 전달한 다음, `x-ms-documentdb-collection-index-transformation-progress` 응답 헤더에서 값을 검색합니다.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Python SDK 사용

[Python SDK](https://pypi.org/project/azure-cosmos/)(해당 사용법에 관한 [이 빠른 시작](create-sql-api-python.md) 참조)를 사용하는 경우 컨테이너 구성은 사전으로 관리됩니다. 이 사전에서 인덱싱 정책 및 해당 정책의 모든 특성에 액세스할 수 있습니다.

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [{"path" : "/headquarters/employees/?"}]
# update the container with our changes
response = client.ReplaceContainer(containerPath, container)
```

## <a name="indexing-policy-examples"></a>인덱싱 정책 예제

다음은 JSON 형식(정책이 Azure Portal에 표시되는 방법)으로 표시한 인덱싱 정책의 몇몇 예입니다. Azure CLI 또는 임의의 SDK를 통해 같은 매개 변수를 설정할 수 있습니다.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>일부 속성 경로를 선택적으로 제외하는 옵트아웃 정책
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>일부 속성 경로를 선택적으로 포함하는 옵트인 정책
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

참고: 일반적으로 **옵트아웃** 인덱싱 정책을 사용하여 Azure Cosmos DB가 모델에 추가할 수 있는 새 속성을 사전에 인덱싱하도록 하는 방법이 권장됩니다.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>특정 속성 경로에 대해서만 공간 인덱스 사용
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/geojson/property/?",
                "indexes": [
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": []
    }
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>모든 속성 경로를 제외하되 인덱싱을 활성 상태로 유지

이 정책은 [TTL(Time-to-Live) 기능](time-to-live.md)이 활성 상태이지만 보조 인덱스가 필요한 상황(Azure Cosmos DB를 순수 키 값 저장소로 사용하기 위해)에 사용할 수 있습니다.
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>인덱싱 안 함
```
    {
        "indexingMode": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>복합 인덱싱 정책 예제

개별 속성에 대한 경로를 포함 또는 제외하는 것 외에, 복합 인덱스를 지정할 수도 있습니다. 여러 속성에 대해 `ORDER BY` 절이 있는 쿼리를 수행하려면 해당 속성에 [복합 인덱스](index-policy.md#composite-indexes)가 필요합니다.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>(name asc, age desc)에 대해 정의된 복합 인덱스:
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

이 복합 인덱스는 다음 두 쿼리를 지원할 수 있습니다.

쿼리 #1:
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

쿼리 #2:
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>(name asc, age asc) 및 (name asc, age desc)에 대해 정의된 복합 인덱스:

동일한 인덱싱 정책 내에서 여러 다른 복합 인덱스를 정의할 수 있습니다. 
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ]
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>(name asc, age asc)에 대해 정의된 복합 인덱스:

순서를 지정하는 선택 사항입니다. 지정하지 않으면 순서는 오름차순입니다.
```
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

- [인덱싱 개요](index-overview.md)
- [인덱싱 정책](index-policy.md)
