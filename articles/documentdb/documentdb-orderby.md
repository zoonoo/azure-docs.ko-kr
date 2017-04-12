---
redirect_url: https://azure.microsoft.com/services/documentdb/
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 0ca716857733290fad4278e3be5059408bb75393
ms.lasthandoff: 03/29/2017


---
# <a name="sorting-documentdb-data-using-order-by"></a>Order By를 사용하여 DocumentDB 데이터 정렬
Microsoft Azure DocumentDB는 JSON 문서에 대해 SQL을 사용한 문서 쿼리를 지원합니다. SQL 쿼리 문에서 ORDER BY 절을 사용하여 쿼리 결과를 주문할 수 있습니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다. 

* 어떻게 Order By로 쿼리합니까?
* Order By에 대한 색인 정책 구성 방법
* 다음 단계

[샘플](#samples) 및 [FAQ](#faq)도 제공됩니다.

SQL 쿼리에 대한 전체 참조는 [DocumentDB 쿼리 자습서](documentdb-sql-query.md)를 참조하세요.

## <a name="how-to-query-with-order-by"></a>Order By로 쿼리하는 방법
ANSI-SQL에서와 마찬가지로, 이제 DocumentDB를 쿼리할 때 SQL문에 선택적 Order By절을 포함할 수 있습니다. 절은 선택적 ASC/DESC 인수를 포함하여 결과를 검색해야 하는 순서를 지정할 수 있습니다. 

### <a name="ordering-using-sql"></a>SQL을 사용하여 순서 지정
예를 들어 다음은 해당 제목의 내림차순으로 상위 10개 설명서를 검색하는 쿼리입니다. 

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### <a name="ordering-using-sql-with-filtering"></a>필터링된 SQL을 사용하여 순서 지정
Books.ShippingDetails.Weight와 같은 문서 내에서 중첩된 모든 속성을 사용하여 주문할 수 있으며, 이 예제에서와 같이 Order By와 함께 WHERE절에서 추가 필터를 지정할 수 있습니다.

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### <a name="ordering-using-the-linq-provider-for-net"></a>.NET용 LINQ 공급자를 사용하여 순서 지정
.NET SDK 버전 1.2.0 이상을 사용하여 이예제에서와 같이 LINQ 쿼리 내에서 OrderBy() 또는 OrderByDescending() 절을 사용할 수도 있습니다.

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

DocumentDB는 곧 추가 쿼리 형식을 가진 단일 숫자, 문자열 또는 쿼리 당 부울 속성으로 순서를 지정하도록 지원합니다. 자세한 내용은 [다음 단계](#Whats_coming_next) 를 참조하세요.

## <a name="configure-an-indexing-policy-for-order-by"></a>Order By로 색인 정책 구성
DocumentDB는 두 종류의 인덱스(해시 및 범위)를 지원하며 이는 특정 경로/속성, 데이터 형식(문자열/숫자) 및 다른 전체 자릿수 값(최대 전체 자릿수 또는 고정된 전체 자릿수 값)에 대해 설정된다는 사실에 유의하십시오. DocumentDB가 기본적으로 인덱싱 해시를 사용하기 때문에 Order By를 사용하려면 숫자, 문자열 또는 둘 모두의 범위에 사용자 지정 인덱싱 정책을 사용하여 새 컬렉션을 만들어야 합니다. 

> [!NOTE]
> 문자열 범위 인덱스는 2015년 7월 7일에 REST API 버전 2015-06-03에서 도입되었습니다. 문자열에 대해 Order By을 위한 정책을 만들려면 NET SDK의 SDK 버전 1.2.0. 또는 버전 1.1.0 Python, Node.js 또는 Java SDK를 사용해야 합니다.
> 
> REST API 버전 2015-06-03 전에 기본 컬렉션 인덱싱 정책은 문자열과 숫자를 위한 해시였습니다. 문자열을 위한 해시 및 숫자에 대한 범위로 변경되었습니다. 
> 
> 

자세한 내용은 [DocumentDB 색인 정책](documentdb-indexing-policies.md)을 참조하세요.

### <a name="indexing-for-order-by-against-all-properties"></a>모든 속성에 대한 Order By의 인덱싱
다음은 JSON 문서 내에서 표시되는 임의/모든 숫자 또는 문자열 속성에 대해 Order By로 인덱싱된 "모든 범위"를 가지고 컬렉션을 만들 수 있는 방법입니다. 다음은 최대 전체 자릿수(-1)에서 범위에 대한 문자열 값의 기본 인덱스 유형을 무시합니다.

    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

> [!NOTE]
> 오직 Order By이 RangeIndex로 인덱싱되는 데이터 형식(문자열 및 숫자)의 결과를 반환합니다. 예를 들어 숫자에 RangeIndex가 있는 기본 인덱싱 정책이 있다면 문자열 값을 가진 경로에 대한 Order By는 어떤 문서도 반환하지 않습니다.
> 
> 

### <a name="indexing-for-order-by-for-a-single-property"></a>단일 속성에 대한 Order By의 인덱싱
다음은 문자열인 제목 속성에 대해 인덱스가 Order By인 컬렉션을 만들 수 있는 방법입니다. 범위 인덱싱을 가진 제목 속성("/Title/?")에 대한 경로와 기본 인덱싱 체계를 가진 다른 모든 속성에 대해 문자열에 대한 해시 및 숫자에 대한 범위인 경로 등 두 개의 경로가 있습니다.                    

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## <a name="samples"></a>샘플
정책 인덱싱 정책 만들기 및 Order By를 사용한 페이징을 포함하여 Order By를 사용하는 방법을 나타내는 이 [GitHub 샘플 프로젝트](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) 을 살펴 보세요. 샘플은 오픈 소스이며, 다른 DocumentDB 개발자에게 도움이 되는 정보와 함께 끌어오기 요청을 제출하는 것이 좋습니다. 참여하는 방법에 대한 지침은 [참여 지침](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) 을 참조하세요.  

## <a name="faq"></a>FAQ
**Order By 쿼리의 예상된 요청 단위(RU) 소비**

Order By는 조회에 대한 DocumentDB 인덱스를 사용하므로 Order By 쿼리에서 사용되는 요청 단위의 수는 Order By 없이 해당 쿼리와 유사합니다. DocumentDB에서의 다른 모든 작업과 마찬가지로, 요청 단위 수는 문서의 크기/모양 및 쿼리의 복잡성에 따라 달라집니다. 

**Order By에 대한 예상 인덱싱 오버헤드**

인덱싱 저장소 오버헤드는 속성의 수에 비례합니다. 최악의 경우, 인덱스 오버헤드는 데이터의 100%가 됩니다. 범위/Order By 인덱싱 및 기본 해시 인덱싱 사이의 처리량(요청 단위) 오버헤드에는 차이가 없습니다.

**Order By를 사용하여 DocumentDB에서 기존 내 데이터를 쿼리하는 방법**

Order By를 사용하여 쿼리 결과를 정렬하기 위해 컬렉션의 인덱싱 정책을 수정하여 정렬하는 데 사용된 속성에 대해 범위 인덱스 형식을 사용해야 합니다. [인덱싱 정책 수정](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection)을 참조하세요. 

**Order By의 현재 제한 사항은 무엇입니까?**

Order By는 속성, 숫자 또는 문자열에 대해 최대 자릿수(-1)로 인덱스된 범위인 경우에만 지정될 수 있습니다.

다음을 수행할 수 없습니다.

* id, _rid 및 _self(서비스 예정)와 같은 내부 문자열 속성으로 Order By
* 문서 간 조인의 결과로 파생된 속성으로 Order By(서비스 예정).
* 다중 속성으로 Order By(서비스 예정).
* 데이터베이스, 컬렉션, 사용자, 사용 권한 또는 첨부 파일에 대한 쿼리로 Order By.(서비스 예정)
* 표현식 또는 UDF/내장 함수의 결과와 같은 계산된 속성으로 Order By.

Order By는 Azure Portal에서 쿼리 탐색기를 사용하는 경우 파티션 간 쿼리에 현재 지원되지 않습니다.

## <a name="troubleshooting"></a>문제 해결
Order By가 지원되지 않는다는 오류가 발생하면 Order By를 지원하는 [SDK](documentdb-sdk-dotnet.md)의 버전을 사용하는지 확인합니다. 

## <a name="next-steps"></a>다음 단계
[GitHub 샘플 프로젝트](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)를 분기하고 주문 데이터를 시작합니다. 

## <a name="references"></a>참조
* [DocumentDB 쿼리 참조](documentdb-sql-query.md)
* [DocumentDB 인덱싱 정책 참조](documentdb-indexing-policies.md)
* [DocumentDB SQL 참조(영문)](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [DocumentDB Order By 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)


