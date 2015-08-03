<properties 
    pageTitle="DocumentDB 인덱싱 정책 | Azure" 
    description="인덱싱이 DocumentDB에서 작동하는 방식을 이해하고 인덱싱 정책 구성 및 변경하는 방법을 알아봅니다." 
    services="documentdb" 
    documentationCenter="" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="07/19/2015" 
    ms.author="mimig"/>


# DocumentDB 인덱싱 정책

DocumentDB는 진정한 스키마 없는 데이터베이스입니다. 인덱싱하는 JSON 문서에 대해 스키마를 가정하거나 요구하지 않습니다. 따라서 신속하게 응용 프로그램 데이터 모델을 정의하여 응용 프로그램 데이터 모델에 대해 반복할 수 있습니다. 컬렉션에 문서를 추가하면 DocumentDB에서 자동으로 모든 문서 속성을 인덱싱하므로 쿼리에 이러한 속성을 사용할 수 있습니다. 자동 인덱싱을 통해 다른 유형의 문서를 저장할 수도 있습니다.

문서의 자동 인덱싱은 쓰기 최적화, 잠금 없는 로그 구조 인덱스 유지 관리 기술로 가능합니다. DocumentDB는 일관성 있는 쿼리를 제공하는 동시에 빠른 쓰기의 지속적인 볼륨을 지원합니다.

DocumentDB 인덱싱 하위 시스템은 다음을 지원하도록 설계되었습니다.

-  스키마 또는 인덱스 정의가 없는 효율적이고 풍부한 계층적 및 관계형 쿼리
-  지속적인 쓰기 볼륨을 처리하는 동안 일관성 있는 쿼리 결과 일관성 있는 쿼리와 더불어 높은 쓰기 처리량 작업을 위해 지속적인 쓰기 볼륨을 처리하는 동안 인덱스가 온라인에서 효율적으로 증분 업데이트됩니다.
- 효율적인 저장소 비용 효율성을 위해 인덱스의 디스크에 있는 저장소 오버헤드가 제한되고 예측 가능합니다.
- 다중 테넌트 지원 인덱스 업데이트가 DocumentDB 컬렉션당 할당된 시스템 리소스의 예산 내에서 수행됩니다. 

대부분의 응용 프로그램에 대해 기본 자동 인덱싱 정책을 사용할 수 있습니다. 기본 자동 인덱싱 정책만으로도 최대 유연성이 보장되고 성능과 저장소 효율성 간의 균형을 잘 유지할 수 있기 때문입니다. 반면에 사용자 지정 인덱싱 정책을 지정하면 쿼리 성능, 쓰기 성능 및 인덱스 저장소 오버헤드 간의 균형을 좀 더 세분화하여 조정할 수 있습니다.

예를 들어 특정 문서나 문서 내의 경로를 인덱싱에서 제외하여 인덱싱에 사용되는 저장소 공간을 줄일 수 있을 뿐만 아니라 인덱스 유지 관리를 위한 삽입 시간 비용도 줄일 수 있습니다. 범위 쿼리에 더 적합하도록 인덱스 유형을 변경하거나 쿼리 성능을 향상하기 위해 인덱스 자릿수(바이트)를 늘릴 수 있습니다. 이 문서에서는 DocumentDB에서 사용할 수 있는 다양한 인덱싱 구성 옵션과 작업에 맞게 인덱싱 정책을 사용자 지정하는 방법을 설명합니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

- DocumentDB는 어떻게 기본적으로 모든 속성의 인덱싱을 지원하나요?
- 인덱싱에서 포함하거나 제외할 속성을 어떻게 재정의하나요?
- 최종 업데이트에 대한 인덱스를 어떻게 구성하나요?
- Order By 또는 범위 쿼리를 수행하는 인덱싱을 어떻게 구성하나요?

## DocumentDB 인덱싱 작동 방법

DocumentDB의 인덱싱은 JSON 문법에서 문서를 **트리로 표현**할 수 있다는 사실을 이용합니다. JSON 문서를 트리로 표현하려면 문서 아래에 있는 나머지 실제 노드의 상위 항목이 되는 더미 루트 노드를 만들어야 합니다. JSON 문서의 배열 인덱스를 포함하는 각 레이블은 트리의 노드가 됩니다. 아래 그림은 예제 JSON 문서 및 해당 트리 표현을 보여 줍니다.

![인덱싱 정책](media/documentdb-indexing-policies/image001.png)

예를 들어 위의 예에서 JSON 속성 `{"headquarters": "Belgium"}` 속성은 경로 `/headquarters/Belgium`에 해당합니다. JSON 배열 `{"exports": [{"city": “Moscow"}, {"city": Athens"}]}` 배열은 경로 `/exports/[]/city/Moscow` 및 `/exports/[]/city/Athens`에 해당합니다.

>[AZURE.NOTE]경로 표현은 문서의 구조/스키마와 인스턴스 값 간의 경계를 모호하게 하여 DocumentDB를 진정한 스키마 없는 데이터베이스로 만들어 줍니다.

DocumentDB에서 문서는 SQL을 사용하여 쿼리될 수 있거나 단일 트랜잭션 범위 내에서 처리될 수 있는 컬렉션으로 구성됩니다. 각 컬렉션은 경로를 기준으로 표현된 고유한 인덱싱 정책으로 구성할 수 있습니다. 다음 섹션에서는 DocumentDB 컬렉션의 인덱싱 동작 구성하는 방법을 살펴보겠습니다.

## 컬렉션의 인덱싱 정책 구성

모든 DocumentDB 컬렉션에 대해 다음 옵션을 구성할 수 있습니다.

- 인덱싱 모드: **일관성**, **지연**(비동기 업데이트의 경우) 또는 **없음**("id" 기반 액세스 경우에만)
- 경로 포함 및 제외: 포함하거나 제외할 JSON 내의 경로를 선택합니다.
- 인덱스 종류: **해시**(같음 쿼리), **범위**(같음, 범위 및 저장소의 오버헤드가 높은 경우 Order By 쿼리)
- 인덱스 전체 자릿수: 1-8 또는 저장소와 성능 간에 적절한 균형을 유지하려면 최대 전체 자릿수(-1)
- 자동: **true** 또는 **false**를 사용하도록 설정 또는 **manual**(삽입할 때마다 옵트인(opt in))

다음 .NET 코드 조각에서는 컬렉션을 만드는 동안 사용자 지정 인덱싱 정책을 설정하는 방법을 보여 줍니다. 여기에서 최대 전체 자릿수의 문자열 및 숫자에 대한 범위 인덱스를 사용하여 정책을 설정합니다. 이 정책을 통해 문자열에 대한 Order By 쿼리를 실행할 수 있습니다.

    var collection = new DocumentCollection { Id = "myCollection" };
    
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, collection);   


>[AZURE.NOTE]인덱싱 정책에 대한 JSON 스키마는 REST API 버전 2015-06-03의 릴리스로 변경되어 문자열에 대한 범위 인덱스를 지원합니다. .NET SDK 1.2.0과 Java, Python, 및 Node.js SDK 1.1.0은 새 정책 스키마를 지원합니다. 이전 SDK는 REST API 버전 2015-04-08을 사용하고 인덱싱 정책의 이전 스키마를 지원합니다.
>
>기본적으로 DocumentDB는 해시 인덱스를 사용하여 문서 내의 모든 문자열 속성을 일관되게 인덱싱하고, 범위 인덱스를 사용하여 숫자 속성을 인덱싱합니다.

### 인덱싱 모드

동기(**일관성**), 비동기(**지연**) 및 없음(**없음**) 인덱스 업데이트 간에 선택할 수 있습니다. 기본적으로 컬렉션의 문서를 삽입하거나 바꾸거나 삭제할 때마다 인덱스가 동기적으로 업데이트됩니다. 따라서 쿼리가 인덱스가 따라잡을 때까지의 지연 없이 문서 읽기의 일관성 수준과 동일한 일관성 수준을 적용할 수 있습니다.

DocumentDB는 쓰기 최적화되며 동기 인덱스 유지 관리와 함께 문서 쓰기의 지속적인 볼륨을 지원하지만 인덱스를 지연 업데이트하도록 특정 컬렉션을 구성할 수 있습니다. 지연 인덱싱은 데이터가 갑자기 작성되는 시나리오에 적합하며, 오랜 시간 동안 콘텐츠를 인덱싱하는 데 필요한 작업을 분할하고자 합니다. 그러면 프로비전된 처리량을 효과적으로 사용하고 최대 사용 시간에 최소 대기 시간으로 쓰기 요청을 처리할 수 있습니다. 지연 인덱싱을 설정한 경우 쿼리 결과는 데이터베이스 계정에 대해 구성된 일관성 수준과 관계없이 결국 일관성을 지니게 됩니다.

다음 샘플은 모든 문서 삽입에 일관된 자동 인덱싱을 사용하여 .NET SDK로 DocumentDB 컬렉션을 만드는 방법을 보여 줍니다.


     // Default collection creates a hash index for all string and numeric    
     // fields. Hash indexes are compact and offer efficient
     // performance for equality queries.
     
     var collection = new DocumentCollection { Id ="defaultCollection" };
     
     // Optional. Override Automatic to false for opt-in indexing of documents.
     collection.IndexingPolicy.Automatic = true;
     
     // Optional. Set IndexingMode to Lazy for bulk import/read heavy        
     // collections. Queries might return stale results with Lazy indexing.
     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
     
     collection = await client.CreateDocumentCollectionAsync(database.SelfLink, collection);

### 인덱스 경로

문서 내에서 인덱싱에 포함하거나 인덱싱에서 제외할 경로를 선택할 수 있습니다. 따라서 쿼리 패턴을 사전에 알고 있는 경우 시나리오의 쓰기 성능이 향상되고 인덱스 저장소를 줄일 수 있습니다.

인덱스 경로는 루트(/)로 시작하며, 일반적으로 ? 와일드카드 연산자로 끝나 접두사에 대해 가능한 값이 여러 개 있음을 나타냅니다. 예를 들어 SELECT * FROM Families F WHERE F.familyName = "Andersen"을 처리하려면 컬렉션의 인덱스 정책에 /familyName/?의 인덱스 경로를 포함해야 합니다.

또한 인덱스 경로에 * 와일드카드 연산자를 사용하여 접두사 아래에 재귀적으로 경로에 대한 동작을 지정할 수 있습니다. 예를 들어 /payload/*를 사용하여 payload 속성 아래의 모든 항목을 인덱싱에서 제외할 수 있습니다.

인덱스 경로를 지정하는 일반적인 패턴은 다음과 같습니다.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Path</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>설명/사용 사례</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /*
                </p>
            </td>
            <td valign="top">
                <p>
                    컬렉션의 기본 경로입니다. 재귀적이며 전체 문서 트리에 적용됩니다.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /prop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    인덱스 경로는 다음과 같은 쿼리를 처리하는 데 필요합니다(각각 해시, 범위 유형이 포함되어 있음).
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop > 5
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
                </p>                
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /prop/*
                </p>
            </td>
            <td valign="top">
                <p>
                    지정된 레이블 아래의 모든 경로의 인덱스 경로입니다. 다음 쿼리로 작동
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop.nextprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/?
                </p>
            </td>
            <td valign="top">
                <p>
                    ["a", "b", "c"]와 같은 스칼라의 배열에 대한 반복 및 JOIN 쿼리를 제공하는 데 필요한 인덱스 경로입니다.
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    [{subprop: "a"}, {subprop: "b"}]와 같은 개체의 배열에 대한 반복 및 JOIN 쿼리를 제공하는 데 필요한 인덱스 경로입니다.
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"
                </p>
            </td>
        </tr>        
        <tr>
            <td valign="top">
                <p>
                    /prop/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    인덱스 경로는 다음과 같은 쿼리를 처리하는 데 필요합니다(각각 해시 또는 범위 유형이 포함되어 있음).
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop.subprop
                </p>                
            </td>
        </tr>
    </tbody>
</table>

>[AZURE.NOTE]사용자 지정 인덱스 경로를 설정하는 동안 특수 경로 "/*”로 지정된 전체 문서 트리에 대한 기본 인덱싱 규칙을 지정해야 합니다.

다음 예제에서는 범위 인덱싱을 사용하고 사용자 지정 전체 자릿수 값이 20바이트인 특정 경로를 구성합니다.

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });
        
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);

### 인덱스 데이터 형식, 종류 및 전체 자릿수

이제 경로를 지정하는 방법을 살펴보았으므로 경로에 대한 인덱싱 정책을 구성하는 데 사용할 수 있는 옵션에 대해 살펴보겠습니다. 모든 경로에 대해 하나 이상의 인덱싱 정의를 지정할 수 있습니다.

- 데이터 형식: **문자열** 또는 **숫자**(경로별로 데이터 형식당 하나만 포함할 수 있음)
- 인덱스 종류: **해시**(같음 쿼리) 또는 **범위**(같음, 범위 또는 Order By 쿼리)
- 전체 자릿수: 숫자의 경우 1-8 또는 -1(최대 전체 자릿수), 문자열의 경우 1-100(최대 전체 자릿수)

#### 인덱스 종류

DocumentDB는 모든 경로와 데이터 형식 쌍에 대해 두 가지 인덱스 종류를 지원합니다.

- **해시**는 효율적인 같음 쿼리를 지원합니다. 대부분의 사용 사례에서는 기본값 3바이트보다 큰 자릿수의 해시 인덱스가 불필요합니다.
- **범위**는 효율적인 같음 쿼리, 범위 쿼리(>, <, >=, <=, != 사용) 및 Order By 쿼리를 지원합니다. 또한 Order By 쿼리에는 기본적으로 최대 인덱스 전체 자릿수(-1)가 필요합니다.

#### 인덱스 전체 자릿수

인덱스 전체 자릿수를 사용하면 인덱스 저장소 오버헤드와 쿼리 성능 간에 적절한 균형을 유지할 수 있습니다. 숫자의 경우 기본 전체 자릿수 구성인 -1을 사용하는 것이 좋습니다. 숫자는 JSON에서 8바이트이므로 8바이트 구성과 같습니다. 1-7과 같이 낮은 전체 자릿수 값을 선택하면 일부 범위 내의 값이 같은 인덱스 항목에 매핑됩니다. 따라서 인덱스 저장소 공간은 줄어들지만 쿼리 실행은 더 많은 문서를 처리하고 결과적으로 더 많은 처리량(즉, 요청 단위)을 소모해야 할 수 있습니다.

인덱스 전체 자릿수 구성은 문자열 범위와 함께 사용할 때 실질적으로 더 유용합니다. 문자열은 임의의 길이일 수 있으므로 인덱스 전체 자릿수 선택은 문자열 범위 쿼리의 성능에 영향을 줄 수 있으며 필요한 인덱스 저장소 공간의 크기에 영향을 줄 수 있습니다. 문자열 범위 인덱스는 1-100 또는 최대 전체 자릿수 값(-1)으로 구성할 수 있습니다. 문자열에서 Order By가 필요하면 지정된 경로(-1)를 통해 지정해야 합니다.

다음 예제에서는 .NET SDK를 사용하여 컬렉션의 범위 인덱스 자릿수를 늘리는 방법을 보여 줍니다. 여기에서는 기본 경로 "/*"를 사용합니다.

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };
    
    rangeDefault.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


> [AZURE.NOTE]DocumentDB는 쿼리가 Order By를 사용하지만 최대 전체 자릿수로 쿼리된 경로에 대한 범위 인덱스가 없는 경우 오류를 반환합니다.
>
> 범위 인덱스(모든 전체 자릿수)가 없는 경우 >=와 같은 범위 연산자를 사용하여 쿼리에 오류가 반환되지만 인덱스에서 제공될 수 있는 다른 필터가 있는 경우 제공될 수 있습니다.
> 
> REST API의 x-ms-documentdb-enable-scans 헤더를 사용하거나 .NET SDK의 EnableScanInQuery 요청 옵션을 사용하여 범위 인덱스 없이 범위 쿼리를 수행할 수 있습니다.

마찬가지로 경로는 인덱싱에서 완전히 제외될 수 있습니다. 다음 예제는 "*" 와일드카드를 사용하여 인덱싱에서 문서의 전체 섹션(하위 트리라고도 함)을 제외하는 방법을 보여 줍니다.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, excluded);


### 자동 인덱싱

컬렉션에서 모든 문서를 자동으로 인덱싱하도록 할지 여부를 선택할 수 있습니다. 기본적으로 모든 문서는 자동으로 인덱싱되지만 이 기능을 해제하도록 선택할 수 있습니다. 인덱싱을 해제하면 자체 링크를 통해서나 ID를 사용한 쿼리로만 문서에 액세스할 수 있습니다.

자동 인덱싱이 해제된 상태에서도 특정 문서만 선택적으로 인덱스에 추가할 수 있습니다. 반대로, 자동 인덱싱을 설정된 상태로 두고 특정 문서만 선택적으로 제외할 수도 있습니다. 인덱싱 설정/해제 구성은 문서의 하위 집합만 쿼리해야 할 경우에 유용합니다.

예를 들어 다음 샘플은 [DocumentDB .NET SDK](https://github.com/Azure/azure-documentdb-java) 및 [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) 속성을 사용하여 문서를 명시적으로 포함하는 방법을 보여 줍니다.

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(defaultCollection.SelfLink,
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## 성능 튜닝

DocumentDB API는 사용된 인덱스 저장소와 같은 성능 메트릭에 대한 정보와 모든 작업에 대한 처리량 비용(요청 단위)을 제공합니다. 이 정보는 다양한 인덱싱 정책 및 성능 튜닝을 비교하는 데 사용할 수 있습니다.

컬렉션의 저장소 할당량 및 사용량을 확인하려면 컬렉션 리소스에 대해 HEAD 또는 GET 요청을 실행하고 x-ms-request-quota 및 x-ms-request-usage 헤더를 검사합니다. .NET SDK에서는 [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx)의 [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) 및 [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) 속성에 이에 해당하는 값이 포함되어 있습니다.

     // Measure the document size usage (which includes the index size) against   
     // different policies.        
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


각 쓰기 작업(만들기, 업데이트 또는 삭제)에 대한 인덱싱의 오버헤드를 측정하려면 x-ms-request-charge 헤더(또는 .NET SDK의 [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx)에 있는 [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) 속성)를 검사하여 이러한 작업에 사용된 요청 단위 수를 측정합니다.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
     
     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                  
     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }
     
     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## 인덱싱 정책 사양의 변경 내용
인덱싱 정책의 스키마에 대한 변경 내용이 2015년 7월 7일에 REST API 버전 2015-06-03에서 도입되었습니다. SDK 버전에서 해당 클래스는 스키마와 일치하도록 새로 구현됩니다.

다음과 같은 변경 내용은 JSON 사양에서 구현됩니다.

- 정책 인덱싱이 문자열에 대한 범위 인덱스 지원
- 각 경로는 각 데이터 형식에 하나씩 여러 인덱스 정의가 존재
- 인덱싱 정확도가 숫자의 경우 1-8, 문자열의 경우 1-100 및 -1(최대 전체 자릿수) 지원
- 경로 세그먼트는 각 경로에서 벗어나기 위해 큰따옴표를 사용할 필요가 없습니다. 예를 들어 /”title”/? 대신 /title/?에 대한 경로를 추가할 수 있습니다.
- "모든 경로"를 나타내는 루트 경로는 /*(및 /)로 표시

버전 1.1.0의 .NET SDK 또는 이전 버전으로 작성된 사용자 지정 인덱싱 정책을 사용하여 컬렉션을 프로비전하는 코드가 있으면 SDK 버전 1.2.0으로 이동하기 위해서 이러한 변경 내용을 처리하도록 응용 프로그램 코드를 변경해야 합니다. 인덱싱 정책을 구성하는 코드 또는 이전 SDK 버전을 사용하려는 계획이 없는 경우 변경하지 않아도 됩니다.

다음은 실용적인 비교를 위해 REST API 버전 2015-06-03과 이전 버전 2015-04-08을 사용하여 작성한 사용자 지정 인덱싱 정책에 대한 예제입니다.

**이전 인덱싱 정책 JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/"nonIndexedContent"/*"
       ]
    }

**현재 인덱싱 정책 JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }

## 다음 단계

인덱스 정책 관리 샘플 및 DocumentDB의 쿼리 언어에 대한 자세한 내용을 보려면 아래 링크를 참조하세요.

1.	[DocumentDB .NET 인덱스 관리 코드 샘플](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2.	[DocumentDB REST API 컬렉션 작업](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3.	[DocumentDB SQL을 사용한 쿼리](documentdb-sql-query.md)

 

<!---HONumber=July15_HO4-->