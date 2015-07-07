<properties 
	pageTitle="Order By를 사용하여 DocumentDB 데이터 정렬 | Azure" 
	description="LINQ 및 SQL의 DocumentDB 쿼리에서 ORDER BY를 사용하는 방법 및 ORDER BY 쿼리에 대한 인덱싱 정책을 지정하는 방법에 대해 알아봅니다." 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="arramac"/>

# Order By를 사용하여 DocumentDB 데이터 정렬
Microsoft Azure DocumentDB는 JSON 문서에 대해 SQL을 사용한 문서 쿼리를 지원합니다. SQL 쿼리 문에서 ORDER BY 절을 사용하여 쿼리 결과를 주문할 수 있습니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

- 어떻게 Order By로 쿼리합니까?
- Order By에 대한 색인 정책 구성 방법
- 다음 단계

[샘플](#samples) 및 [FAQ](#faq)도 제공됩니다.

SQL 쿼리에 대한 전체 참조는 [DocumentDB 쿼리 자습서](documentdb-sql-query.md)를 참조하세요.

## Order By로 쿼리하는 방법
ANSI-SQL에서와 마찬가지로, 이제 DocumentDB를 쿼리할 때 SQL문에 선택적 Order By절을 포함할 수 있습니다. 절은 선택적 ASC/DESC 인수를 포함하여 결과를 검색해야 하는 순서를 지정할 수 있습니다.

### SQL을 사용하여 순서 지정
예를 들어, 다음은 PublishTimestamp의 내림차순으로 설명서를 검색하는 쿼리입니다.

    SELECT * 
    FROM Books 
    ORDER BY Books.PublishTimestamp DESC

### 필터링된 SQL을 사용하여 순서 지정
Books.ShippingDetails.Weight와 같은 문서 내에서 중첩된 모든 속성을 사용하여 주문할 수 있으며, 이 예제에서와 같이 Order By와 함께 WHERE절에서 추가 필터를 지정할 수 있습니다.

    SELECT * 
    FROM Books 
	WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### .NET용 LINQ 공급자를 사용하여 순서 지정
.NET SDK 버전 1.2.0 이상을 사용하여 이예제에서와 같이 LINQ 쿼리 내에서 OrderBy() 또는 OrderByDescending() 절을 사용할 수도 있습니다.

    foreach (Book book in client.CreateDocumentQuery<Book>(booksCollection.SelfLink)
        .OrderBy(b => b.PublishTimestamp)) 
    {
        // Iterate through books
    }

### .NET SDK에서 페이징을 사용하여 순서 지정
DocumentDB SDK 내에서 네이티브 페이징 지원을 사용하여, 다음.NET 코드 조각에서와 같이 한 번에 한 페이지에서 결과를 검색할 수 있습니다. 여기에서 FeedOptions.MaxItemCount 및 IDocumentQuery 인터페이스를 사용하여 한 번에 최대 10개까지 결과를 가져옵니다.

    var booksQuery = client.CreateDocumentQuery<Book>(
        booksCollection.SelfLink,
        "SELECT * FROM Books ORDER BY Books.PublishTimestamp DESC"
        new FeedOptions { MaxItemCount = 10 })
      .AsDocumentQuery();
            
    while (booksQuery.HasMoreResults) 
    {
        foreach(Book book in await booksQuery.ExecuteNextAsync<Book>())
        {
            // Iterate through books
        }
    }

DocumentDB는 숫자 형식 정렬(문자열이 아닌) 및 기능의 이 미리 보기에서 쿼리 당 단일 Order By 속성만 지원합니다. 자세한 내용은 [다음 단계](#Whats_coming_next)를 참조하세요.

## Order By로 색인 정책 구성
Order By 쿼리를 실행하려면 다음을 입력해야 합니다.

- 최대 전체 자릿수를 사용하여 문서 내의 특정 경로 인덱스(또는) 
- 최대 자릿수를 사용하여 재귀적으로 전체 컬렉션에 대해 *모든* 경로 인덱스 

전체 자릿수(JSON config에서-1의 자릿수로 표시됨)은 인덱싱되는 값에 따라 바이트의 가변 개수를 이용합니다. 따라서

- epoch 타임 스탬프와 같은 큰 숫자 값의 속성, 최대 자릿수에는 높은 인덱스 오버 헤드가 있습니다. 
- 더 작은 숫자 값이 있는 속성(열거형, 0, 우편번호, 연령 등)에 낮은 인덱스 오버 헤드가 있습니다.

자세한 내용은 [DocumentDB 색인 정책](documentdb-indexing-policies.md)을 참조하세요.

### 모든 숫자 속성에 대한 Order By의 인덱싱
다음은 모든(숫자) 속성에 대해 인덱스가 Order By인 컬렉션을 만들 수 있는 방법입니다.
                   

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            IndexType = IndexType.Range, 
            Path = "/",
            NumericPrecision = -1 });

    await client.CreateDocumentCollectionAsync(databaseLink, 
        booksCollection);  

### 단일 속성에 대한 Order By의 인덱싱
다음은 PublishTimestamp 속성에 대해 인덱스가 Order By인 컬렉션을 만들 수 있는 방법입니다.

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            IndexType = IndexType.Range,
            Path = "/"PublishTimestamp"/?",
            NumericPrecision = -1
        });

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            Path = "/"
        });


## 샘플
정책 인덱싱 정책 만들기 및 Order By를 사용한 페이징을 포함하여 Order By를 사용하는 방법을 나타내는 이 [Github 샘플 프로젝트](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)을 살펴 보세요. 샘플은 오픈 소스이며, 다른 DocumentDB 개발자에게 도움이 되는 정보와 함께 끌어오기 요청을 제출하는 것이 좋습니다. 참여하는 방법에 대한 지침은 [참여 지침](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md)을 참조하세요.

## 다음 단계

향후 서비스 업데이트는 여기에 도입된 Order By 지원에 맞게 확장됩니다. 다음 추가 기능에 대해 작업하고 사용자 의견에 따라 이 개선점 릴리스의 우선순위를 지정합니다.

- 동적 인덱싱 정책: 컬렉션을 만든 후 인덱싱 정책 수정 지원
- 문자열 범위 인덱스: 문자열 값에 대해 범위 쿼리(>, <, >=, <=)를 지원하는 인덱스 이를 지원하기 위해 인덱싱 정책에 대한 다양한 새 스키마를 도입합니다.
- DocumentDB 쿼리에서 문자열 Order By 지원
- Azure Preview 포털을 사용하여 인덱싱 정책을 업데이트할 수 있습니다.
- 보다 효율적인 Order By 및 여러 속성에 대한 Order By에 대해 복합 인덱스를 지원합니다.

## FAQ

**SDK가 순서 지정을 지원하는 플랫폼/버전**

Order By가 서버쪽 업데이트이기 때문에이 기능을 사용하려면 SDK의 새 버전을 다운로드할 필요가 없습니다. 서버쪽 JavaScript SDK를 포함하여 모든 플랫폼 및 SDK의 버전은 SQL 쿼리 문자열을 사용한 Order By를 사용할 수 있습니다. LINQ를 사용하는 경우 버전 1.2.0 또는 새로운 버전을 Nuget에서 다운로드 해야 합니다.

**Order By 쿼리의 예상된 요청 단위(RU) 소비**

Order By는 조회에 대한 DocumentDB 인덱스를 사용하므로 Order By 쿼리에서 사용되는 요청 단위의 수는 Order By 없이 해당 쿼리와 유사합니다. DocumentDB에서의 다른 모든 작업과 마찬가지로, 요청 단위 수는 문서의 크기/모양 및 쿼리의 복잡성에 따라 달라집니다.


**Order By에 대한 예상 인덱싱 오버헤드**

인덱싱 저장소 오버헤드는 숫자 속성의 수에 비례됩니다. 최악의 경우, 인덱스 오버헤드는 데이터의 100%가 됩니다. 범위/Order By 인덱싱 및 기본 해시 인덱싱 사이의 처리량(요청 단위) 오버헤드에는 차이가 없습니다.

**이 변경 내용은 Order By 없이 쿼리에 영향을 미칩니까?**

오늘날 Order By 없이 쿼리가 작동하는 방식에 변경된 내용은 없습니다. 이 기능은 출시되기 전에 모든 DocumentDB 쿼리가 ResourceId (_rid) 순서로 결과를 반환했습니다. Order By를 사용하면 쿼리는 자연스럽게 지정된 값의 순서대로 반환됩니다. Order By 쿼리에서 _같은 값으로 반환되는 여러 문서가 있는 경우 rid는 경우 보조 정렬 순서로 사용될 수 있습니다.

**Order By를 사용하여 DocumentDB에서 기존 내 데이터를 쿼리하는 방법**

[다음 단계](what's-coming-next) 섹션에 언급된 동적 인덱싱 정책 개선의 가용성으로 지원됩니다. 오늘날 이 작업을 수행하기 위해, 데이터를 내보내고 인덱스별 범위/주문으로 만든 새 DocumentDB 컬렉션으로 다시 가져와야 합니다. DocumentDB 가져오기 도구는 컬렉션 간 데이터를 마이그레이하는 데 사용될 수 있습니다.

**Order By의 현재 제한 사항은 무엇입니까?**

Order By는 숫자 속성 및 최대 자릿수(-1) 인덱싱으로 인덱스된 범위인 경우에만 지정될 수 있습니다. Order By는 문서 컬렉션에 대해서만 지원됩니다.

다음을 수행할 수 없습니다.
 
- 문자열 속성으로 Order By(서비스 예정).
- Id, _rid 및 _self(서비스 예정)와 같은 내부 문자열 속성으로 Order By- 문서 간 조인의 결과로 파생된 속성으로 Order By(서비스 예정).
- 다중 속성으로 Order By(서비스 예정).
- 표현식 또는 UDF/내장 함수의 결과와 같은 계산된 속성으로 Order By.
- 데이터베이스, 컬렉션, 사용자, 사용 권한 또는 첨부 파일에 대한 쿼리로 Order By.

## 다음 단계

[Github 샘플 프로젝트](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)를 분기하고 주문 데이터를 시작합니다.

## 참조
* [DocumentDB 쿼리 참조](documentdb-sql-query.md)
* [DocumentDB 인덱싱 정책 참조](documentdb-indexing-policies.md)
* [DocumentDB SQL 참조(영문)](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [DocumentDB Order By 샘플](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)
 

<!---HONumber=62-->