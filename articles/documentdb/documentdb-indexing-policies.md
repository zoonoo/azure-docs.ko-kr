<properties 
    pageTitle="DocumentDB 인덱싱 정책 | Microsoft Azure" 
    description="인덱싱이 DocumentDB에서 작동하는 방식을 이해하고 인덱싱 정책 구성 및 변경하는 방법을 알아봅니다. 자동 인덱싱 및 성능 향상을 위해 DocumentDB 내에서 인덱싱 정책을 구성합니다." 
    keywords="인덱싱 작동 방법, 자동 인덱싱, 데이터베이스, Documentdb, Azure, Microsoft Azure"
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="08/08/2016" 
    ms.author="arramac"/>



# <a name="documentdb-indexing-policies"></a>DocumentDB 인덱싱 정책

많은 고객이 Azure DocumentDB를 사용하여 [인덱싱의 모든 측면](documentdb-indexing.md)을 자동으로 처리할 수 있어 만족하지만, DocumentDB는 작성 중 컬렉션에 대한 사용자 지정 **인덱싱 정책** 을 지정하도록 지원합니다. DocumentDB의 인덱싱 정책은 더 유연하며 다른 데이터베이스 플랫폼에서 제공하는 보조 인덱스보다 강력하므로, 스키마 유연성을 유지하면서 인덱스의 모양을 디자인하고 사용자 지정할 수 있습니다. DocumentDB 내에서 인덱싱이 작동하는 방식을 알아보려면, 인덱싱 정책을 관리하여 인덱스 저장소 오버헤드, 쓰기 및 쿼리 처리량, 쿼리 일관성 사이에서 세부적으로 균형을 맞출 수 있음을 이해해야 합니다.  

이 문서에서는 DocumentDB 인덱싱 정책, 인덱싱 정책을 사용자 지정할 수 있는 방법 및 연관된 장단점을 살펴보겠습니다. 

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

- 인덱싱에서 포함하거나 제외할 속성을 어떻게 재정의하나요?
- 최종 업데이트에 대한 인덱스를 어떻게 구성하나요?
- Order By 또는 범위 쿼리를 수행하는 인덱싱을 어떻게 구성하나요?
- 컬렉션의 인덱싱 정책을 어떻게 변경하나요?
- 저장소 및 다른 인덱싱 정책의 성능을 어떻게 비교하나요?

##<a name="<a-id="customizingindexingpolicy"></a>-customizing-the-indexing-policy-of-a-collection"></a><a id="CustomizingIndexingPolicy"></a> 컬렉션의 인덱싱 정책 사용자 지정

개발자는 DocumentDB 컬렉션의 기본 인덱싱 정책을 재정의하고 다음 측면을 구성하여 저장소, 쓰기/쿼리 성능 및 쿼리 일관성 간의 장단점을 사용자 지정할 수 있습니다.

- **문서 및 인덱스까지/로부터의 경로 포함/제외**. 개발자는 특정 문서를 선택하여 컬렉션에 삽입하거나 대체할 때 인덱스에 포함하거나 제외할 수 있습니다. 개발자는 인덱스에 포함된 문서 간에 인덱싱할 특정 JSON 속성 a.k.a  경로(와일드 카드 패턴 포함)를 포함하거나 제외하도록 선택할 수도 있습니다.
- **다양한 인덱스 유형 구성**. 포함된 각 경로에 대해 개발자는 자신의 데이터 및 예상 쿼리 워크로드 및 각 경로에 대한 숫자/문자열 "전체 자릿수"를 기반으로 컬렉션에 대해 필요한 인덱스의 유형을 지정할 수도 있습니다.
- **인덱스 업데이트 모드 구성**. DocumentDB는 DocumentDB 컬렉션의 인덱싱 정책을 통해 구성될 수 있는 세 가지 인덱싱 모드, 일관성, 지연 및 없음을 지원합니다. 

다음 .NET 코드 조각에서는 컬렉션을 만드는 동안 사용자 지정 인덱싱 정책을 설정하는 방법을 보여 줍니다. 여기에서 최대 전체 자릿수의 문자열 및 숫자에 대한 범위 인덱스를 사용하여 정책을 설정합니다. 이 정책을 통해 문자열에 대한 Order By 쿼리를 실행할 수 있습니다.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };
    
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


>[AZURE.NOTE] 인덱싱 정책에 대한 JSON 스키마는 REST API 버전 2015-06-03의 릴리스로 변경되어 문자열에 대한 범위 인덱스를 지원합니다. .NET SDK 1.2.0과 Java, Python 및 Node.js SDK 1.1.0은 새 정책 스키마를 지원합니다. 이전 SDK는 REST API 버전 2015-04-08을 사용하고 인덱싱 정책의 이전 스키마를 지원합니다.
>
>기본적으로 DocumentDB는 해시 인덱스를 사용하여 문서 내의 모든 문자열 속성을 일관되게 인덱싱하고, 범위 인덱스를 사용하여 숫자 속성을 인덱싱합니다.  

### <a name="database-indexing-modes"></a>데이터베이스 인덱싱 모드

DocumentDB는 DocumentDB 컬렉션의 인덱싱 정책을 통해 구성될 수 있는 세 가지 인덱싱 모드, 일관성, 지연 및 없음을 지원합니다.

**일관성**: DocumentDB 컬렉션의 정책이 "일관되게" 지정되면, 지정된 DocumentDB 컬렉션의 쿼리는 지점 읽기(즉, 강력, 제한된 부실, 세션 또는 최종)에 대해 지정된 것과 동일한 일관성 수준을 따릅니다. 인덱스는 문서 업데이트(즉, DocumentDB 컬렉션의 문서 삽입, 대체, 업데이트 및 삭제)의 일부로 동기적으로 업데이트됩니다.  일관된 인덱싱은 쓰기 처리량에 가능한 비용 감소로 일관된 쿼리를 지원합니다. 이 감소는 "일관성 수준"으로 인덱싱할 필요가 있는 고유 경로 함수입니다. 일관성 인덱싱 모드는 "신속하게 쿼리를 즉시 쓰기" 워크로드를 위해 설계되었습니다.

**지연**: 최대 문서 수집 처리량을 허용하려면 DocumentDB 컬렉션은 지연 일관성으로 구성할 수 있으며 쿼리가 결국 일치함을 의미합니다. DocumentDB 컬렉션이 정지 상태 즉, 사용자 요청을 처리할 만큼 컬렉션의 처리량 용량이 충분히 활용되지 않은 경우 인덱스가 비동기적으로 업데이트됩니다. 아무런 제약 없이 문서 수집을 필요로 하는 "지금 수집, 나중에 쿼리" 워크로드의 경우, "지연" 인덱싱 모드가 적합할 수 있습니다.

**없음**: 인덱스 모드가 "없음"으로 표시되는 컬렉션에는 연관된 인덱스가 없습니다. 이 모드는 DocumentDB를 키-값 저장소로 이용하고 해당 ID 속성에 의해서만 문서에 액세스하는 경우에 흔히 사용됩니다. 

>[AZURE.NOTE] “없음”으로 인덱싱 정책을 구성하면 모든 기존 인덱스를 삭제하는 부작용이 있습니다. 액세스 패턴이 "Id" 및/또는 "자체 링크"만을 필요로 하면 이를 사용합니다.

다음 샘플은 모든 문서 삽입에 일관된 자동 인덱싱을 사용하여 .NET SDK로 DocumentDB 컬렉션을 만드는 방법을 보여 줍니다.

다음 표에서 컬렉션에 대해 구성된 인덱싱 모드(일관성 및 지연) 및 쿼리 요청에 대해 지정된 일관성 수준에 따라 쿼리에 대한 일관성을 보여줍니다. 모든 인터페이스 - REST API, SDK또는 저장 프로시저 및 트리거 내에서 사용하여 만든 쿼리에 적용합니다. 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>일관성</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>지연</strong>
                </p>
            </td>            
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>강력</strong>
                </p>
            </td>
            <td valign="top">
                <p>
강력 </p>
            </td>
            <td valign="top">
                <p>
최종 </p>
            </td>            
        </tr>       
        <tr>
            <td valign="top">
                <p>
                    <strong>제한된 부실</strong>
                </p>
            </td>
            <td valign="top">
                <p>
제한된 부실 </p>
            </td>
            <td valign="top">
                <p>
최종 </p>
            </td>            
        </tr>          
        <tr>
            <td valign="top">
                <p>
                    <strong>세션</strong>
                </p>
            </td>
            <td valign="top">
                <p>
세션 </p>
            </td>
            <td valign="top">
                <p>
최종 </p>
            </td>            
        </tr>      
        <tr>
            <td valign="top">
                <p>
                    <strong>최종</strong>
                </p>
            </td>
            <td valign="top">
                <p>
최종 </p>
            </td>
            <td valign="top">
                <p>
최종 </p>
            </td>            
        </tr>         
    </tbody>
</table>

DocumentDB는 없음 인덱싱 모드를 사용할 경우 컬렉션에 대해 실행된 쿼리에 대해 오류를 반환합니다. 여전히 .NET SDK를 사용하여 REST API의 명시적인 `x-ms-documentdb-enable-scan` 헤더 또는 `EnableScanInQuery` 요청 옵션을 통해 쿼리를 검색으로 실행할 수 있습니다. ORDER BY와 같은 일부 쿼리 기능은 `EnableScanInQuery`을(를) 사용한 검색으로 지원되지 않습니다.

다음 표에서 EnableScanInQuery가 지정되면 인덱싱 모드(일관성, 지연 및 없음)에 기반하는 쿼리에 대한 일관성을 보여 줍니다.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>일관성</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>지연</strong>
                </p>
            </td>       
            <td valign="top">
                <p>
                    <strong>없음</strong>
                </p>
            </td>             
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>강력</strong>
                </p>
            </td>
            <td valign="top">
                <p>
강력 </p>
            </td>
            <td valign="top">
                <p>
최종 </p>
            </td>    
            <td valign="top">
                <p>
강력 </p>
            </td>                
        </tr>       
        <tr>
            <td valign="top">
                <p>
                    <strong>제한된 부실</strong>
                </p>
            </td>
            <td valign="top">
                <p>
제한된 부실 </p>
            </td>
            <td valign="top">
                <p>
최종 </p>
            </td>      
            <td valign="top">
                <p>
제한된 부실 </p>
            </td> 
        </tr>          
        <tr>
            <td valign="top">
                <p>
                    <strong>세션</strong>
                </p>
            </td>
            <td valign="top">
                <p>
세션 </p>
            </td>
            <td valign="top">
                <p>
최종 </p>
            </td>   
            <td valign="top">
                <p>
세션 </p>
            </td>             
        </tr>      
        <tr>
            <td valign="top">
                <p>
                    <strong>최종</strong>
                </p>
            </td>
            <td valign="top">
                <p>
최종 </p>
            </td>
            <td valign="top">
                <p>
최종 </p>
            </td>      
            <td valign="top">
                <p>
최종 </p>
            </td>              
        </tr>         
    </tbody>
</table>

다음 코드 샘플은 모든 문서 삽입에 일관된 인덱싱을 사용하여 .NET SDK로 DocumentDB 컬렉션을 만드는 방법을 보여 줍니다.

     // Default collection creates a hash index for all string and numeric    
     // fields. Hash indexes are compact and offer efficient
     // performance for equality queries.
     
     var collection = new DocumentCollection { Id ="defaultCollection" };
     
     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
     
     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>인덱스 경로

DocumentDB는 JSON 문서 및 인덱스를 트리로 모델링하고 트리 내 경로에 대한 정책을 튜닝할 수 있습니다. 이 [DocumentDB 인덱싱 소개](documentdb-indexing.md)에서 자세한 세부 정보를 찾을 수 있습니다. 문서 내에서 인덱싱에 포함하거나 인덱싱에서 제외할 경로를 선택할 수 있습니다. 따라서 쿼리 패턴을 사전에 알고 있는 경우 시나리오의 쓰기 성능이 향상되고 인덱스 저장소를 줄일 수 있습니다.

인덱스 경로는 루트(/)로 시작하며, 일반적으로 ?  와일드카드 연산자로 끝나 접두사에 대해 가능한 값이 여러 개 있음을 나타냅니다. 예를 들어 SELECT * FROM Families F WHERE F.familyName = "Andersen"을 처리하려면  컬렉션의 인덱스 정책에 /familyName/?의 인덱스 경로를 포함해야 합니다.

또한 인덱스 경로에 * 와일드카드 연산자를 사용하여 접두사 아래에 재귀적으로 경로에 대한 동작을 지정할 수 있습니다. 예를 들어 /payload/*를 사용하여 페이로드 속성의 모든 항목을 인덱싱에서 제외할 수 있습니다.

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
인덱스 경로는 다음과 같은 쿼리를 처리하는 데 필요합니다(각각 해시, 범위 유형이 포함되어 있음). </p>
                <p>
SELECT * FROM collection c WHERE c.prop = "value" </p>
                <p>
SELECT * FROM collection c WHERE c.prop &gt; 5 </p>
                <p>
SELECT * FROM collection c ORDER BY c.prop </p>                
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
/prop/* </p>
            </td>
            <td valign="top">
                <p>
지정된 레이블 아래의 모든 경로의 인덱스 경로입니다. 다음 쿼리로 작동 </p>
                <p>
SELECT * FROM collection c WHERE c.prop = "value" </p>
                <p>
SELECT * FROM collection c WHERE c.prop.subprop &gt; 5 </p>
                <p>
SELECT * FROM collection c WHERE c.prop.subprop.nextprop = "value" </p>
                <p>
SELECT * FROM collection c ORDER BY c.prop </p>
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
["a", "b", "c"]와 같은 스칼라의 배열에 대한 반복 및 JOIN 쿼리를 제공하는 데 필요한 인덱스 경로입니다. </p>
                <p>
SELECT tag FROM tag IN collection.props WHERE tag = "value" </p>
                <p>
SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5 </p>
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
[{subprop: "a"}, {subprop: "b"}]와 같은 개체의 배열에 대한 반복 및 JOIN 쿼리를 제공하는 데 필요한 인덱스 경로입니다. </p>
                <p>
SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value" </p>
                <p>
SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value" </p>
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
인덱스 경로는 다음과 같은 쿼리를 처리하는 데 필요합니다(각각 해시 또는 범위 유형이 포함되어 있음). </p>
                <p>
SELECT * FROM collection c WHERE c.prop.subprop = "value" </p>
                <p>
SELECT * FROM collection c WHERE c.prop.subprop &gt; 5 </p>
                <p>
SELECT * FROM collection c ORDER BY c.prop.subprop </p>                
            </td>
        </tr>
    </tbody>
</table>

>[AZURE.NOTE] 사용자 지정 인덱스 경로를 설정하는 동안 특수 경로 "/*”로 지정된 전체 문서 트리에 대한 기본 인덱싱 규칙을 지정해야 합니다. 

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
        
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);


### <a name="index-data-types,-kinds-and-precisions"></a>인덱스 데이터 형식, 종류 및 전체 자릿수

이제 경로를 지정하는 방법을 살펴보았으므로 경로에 대한 인덱싱 정책을 구성하는 데 사용할 수 있는 옵션에 대해 살펴보겠습니다. 모든 경로에 대해 하나 이상의 인덱싱 정의를 지정할 수 있습니다.

- 데이터 형식: **문자열**, **숫자** 또는 **점**(경로별로 데이터 형식당 하나만 포함할 수 있음). **다각형** 및 **LineString**은 비공개 미리 보기로 지원됨
- 인덱스 종류: **해시**(같음 쿼리), **범위**(같음, 범위 또는 Order By 쿼리) 또는 **공간**(공간 쿼리) 
- 전체 자릿수: 숫자의 경우 1-8 또는 -1(최대 전체 자릿수), 문자열의 경우 1-100(최대 전체 자릿수)

#### <a name="index-kind"></a>인덱스 종류

DocumentDB는 모든 경로에 대해 해시 및 범위 인덱스 종류를 지원합니다(문자열, 숫자 또는 둘 다로 구성할 수 있음).

- **해시** 는 효율적인 같음 및 JOIN 쿼리를 지원합니다. 대부분의 사용 사례에서는 기본값 3바이트보다 큰 자릿수의 해시 인덱스가 불필요합니다.
- **범위**는 효율적인 같음 쿼리, 범위 쿼리(>, <, >=, <=, != 사용) 및 Order By 쿼리를 지원합니다. 또한 Order By 쿼리에는 기본적으로 최대 인덱스 전체 자릿수(-1)가 필요합니다.

DocumentDB는 점 데이터 형식에 지정할 수 있는 공간 인덱스 종류도 모든 경로에 대해 지원합니다. 지정된 경로에 있는 값은 `{"type": "Point", "coordinates": [0.0, 10.0]}`과 같은 유효한 GeoJSON 점이어야 합니다.

- **공간** 은 효율적인 공간(이내 및 거리) 쿼리를 지원합니다.

>[AZURE.NOTE] DocumentDB는 점, 다각형(비공개 미리 보기) 및 LineStrings(비공개 미리 보기)의 자동 인덱싱을 지원합니다. 미리 보기에 대한 액세스를 위해서는 전자 메일 askdocdb@microsoft.com,을 보내거나 Azure 지원을 통해 문의하세요.

다음은 지원되는 인덱스 종류와 제공될 수 있는 쿼리 예입니다.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>인덱스 종류</strong>
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
해시 </p>
            </td>
            <td valign="top">
                <p>
/prop/?  (또는 /*)를 통한 해시를 사용하여 다음 쿼리를 효율적으로 처리할 수 있습니다. SELECT * FROM collection c WHERE c.prop = "value" /props/[]/?  (또는 /* 또는 /props*)를 통한 해시를 사용하여 다음 쿼리를 효율적으로 처리할 수 있습니다. SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5 </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
범위 </p>
            </td>
            <td valign="top">
                <p>
/prop/?  (또는 /*)를 통한 범위를 사용하여 다음 쿼리를 효율적으로 처리할 수 있습니다. SELECT * FROM collection c WHERE c.prop = "value" SELECT * FROM collection c WHERE c.prop > 5 SELECT * FROM collection c ORDER BY c.prop </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
공간 </p>
            </td>
            <td valign="top">
                <p>
/prop/?  (또는 /*)를 통한 범위를 사용하여 다음 쿼리를 효율적으로 처리할 수 있습니다. SELECT * FROM collection c WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40 SELECT * FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) </p>
            </td>
        </tr>        
    </tbody>
</table>

기본적으로, 쿼리를 처리하는 데 검색이 필요할 수 있음을 알리기 위한 범위 인덱스가 없는 경우(자릿수) >=와 같은 범위 연산자로 쿼리에 대한 오류가 반환됩니다. REST API의 x-ms-documentdb-enable-scan 헤더를 사용하거나 .NET SDK의 EnableScanInQuery 요청 옵션을 사용하여 범위 인덱스 없이 범위 쿼리를 수행할 수 있습니다. DocumentDB가 필터링하는 데 인덱스를 사용할 수 있는 쿼리에 다른 필터가 있는 경우, 오류가 반환되지 않습니다.

공간 쿼리에 대해서도 동일한 규칙이 적용됩니다. 기본적으로 공간 인덱스가 없고 인덱스에서 제공될 수 있는 다른 필터가 없는 경우 공간 쿼리에 오류가 반환됩니다. x-ms-documentdb-enable-scan/EnableScanInQuery를 사용하여 스캔으로 수행할 수 있습니다.

#### <a name="index-precision"></a>인덱스 전체 자릿수

인덱스 전체 자릿수를 사용하면 인덱스 저장소 오버헤드와 쿼리 성능 간에 적절한 균형을 유지할 수 있습니다. 숫자의 경우 기본 전체 자릿수 구성인 -1("최대값")을 사용하는 것이 좋습니다. 숫자는 JSON에서 8바이트이므로 8바이트 구성과 같습니다. 1-7과 같이 낮은 전체 자릿수 값을 선택하면 일부 범위 내의 값이 같은 인덱스 항목에 매핑됩니다. 따라서 인덱스 저장소 공간은 줄어들지만 쿼리 실행은 더 많은 문서를 처리하고 결과적으로 더 많은 처리량(즉, 요청 단위)을 소모해야 할 수 있습니다.

인덱스 전체 자릿수 구성은 문자열 범위와 함께 사용할 때 실질적으로 더 유용합니다. 문자열은 임의의 길이일 수 있으므로 인덱스 전체 자릿수 선택은 문자열 범위 쿼리의 성능에 영향을 줄 수 있으며 필요한 인덱스 저장소 공간의 크기에 영향을 줄 수 있습니다. 문자열 범위 인덱스는 1-100 또는 -1("최대값")로 구성할 수 있습니다. 문자열 속성에 대한 Order By 쿼리를 수행하려는 경우, 해당 경로에 대해 -1의 전체 자릿수를 지정해야 합니다.

공간 인덱스는 항상 점에 대한 기본 인덱스 전체 자릿수를 사용하며 재정의할 수 없습니다. 

다음 예제에서는 .NET SDK를 사용하여 컬렉션의 범위 인덱스 자릿수를 늘리는 방법을 보여 줍니다. 

**사용자 지정 인덱스 전체 자릿수로 컬렉션 만들기**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };
    
    // Override the default policy for Strings to range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [AZURE.NOTE] DocumentDB는 쿼리가 Order By를 사용하지만 최대 전체 자릿수로 쿼리된 경로에 대한 범위 인덱스가 없는 경우 오류를 반환합니다. 

마찬가지로 경로는 인덱싱에서 완전히 제외될 수 있습니다. 다음 예제는 "*" 와일드카드를 사용하여 인덱싱에서  문서의 전체 섹션(하위 트리라고도 함)을 제외하는 방법을 보여 줍니다.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opting-in-and-opting-out-of-indexing"></a>인덱싱 옵트인 및 옵트아웃

컬렉션에서 모든 문서를 자동으로 인덱싱하도록 할지 여부를 선택할 수 있습니다. 기본적으로 모든 문서는 자동으로 인덱싱되지만 이 기능을 해제하도록 선택할 수 있습니다. 인덱싱을 해제하면 자체 링크를 통해서나 ID를 사용한 쿼리로만 문서에 액세스할 수 있습니다.

자동 인덱싱이 해제된 상태에서도 특정 문서만 선택적으로 인덱스에 추가할 수 있습니다. 반대로, 자동 인덱싱을 설정된 상태로 두고 특정 문서만 선택적으로 제외할 수도 있습니다. 인덱싱 설정/해제 구성은 문서의 하위 집합만 쿼리해야 할 경우에 유용합니다.

예를 들어 다음 샘플은 [DocumentDB .NET SDK](https://github.com/Azure/azure-documentdb-java) 및 [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) 속성을 사용하여 문서를 명시적으로 포함하는 방법을 보여 줍니다.

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modifying-the-indexing-policy-of-a-collection"></a>컬렉션의 인덱싱 정책 수정

DocumentDB를 사용하면 즉석에서 컬렉션의 인덱싱 정책을 변경할 수 있습니다. DocumentDB 컬렉션의 인덱싱 정책 변경으로 인덱싱할 수 있는 경로, 자릿수 및 인덱스 자체의 일관성 모델을 포함한 인덱스 모양이 변경될 수 있습니다. 따라서 색인 정책을 변경하려면 이전 인덱스에서 새 인덱스로의 효과적인 변환이 필요합니다. 

**온라인 인덱스 변환**

![인덱싱 작동 방식 – DocumentDB 온라인 인덱스 변환](media/documentdb-indexing-policies/index-transformations.png)

인덱스 변환은 온라인으로 수행되므로, 이전 정책을 기준으로 인덱싱된 문서가 컬렉션의 **쓰기 가용성 또는 프로비전된 처리량에 영향을 주지 않고** 새 정책을 기준으로 효율적으로 변환됩니다. 일관성 읽기 및 쓰기 작업은 REST API, SDK 또는 내부 저장된 프로시저를 사용하여 수행되며 인덱스 변환 중 트리거는 영향을 받지 않습니다. 인덱싱 정책을 변경해도 앱에 대한 성능 저하나 가동 중지가 없음을 의미합니다.

그러나 인덱스 변환이 진행되는 동안 쿼리는 결국 인덱싱 모드 구성(일관성 또는 지연)에 관계 없이 일관됩니다. 모든 인터페이스, REST API, SDK 및 저장 프로시저 및 트리거 내에서 사용하여 만든 쿼리에도 적용합니다. 지연 인덱싱과 마찬가지로, 인덱스 변환은 지정된 복제본에 대에 사용 가능한 예비 리소스를 사용하여 백그라운드에서 비동기적으로 수행됩니다. 

또한 인덱스 변환은 **내부** 에서 수행되므로 DocumentDB가 인덱스의 두 복사본을 유지하지 않고 이전 인덱스를 새 인덱스로 교환합니다. 즉, 추가 디스크 공간이 없는 필요하지 않거나 인덱스 변환을 수행하는 동안 컬렉션에서 사용할 수 있음을 의미합니다.

인덱싱 정책을 변경하는 경우 이전 인덱스에서 새 인덱스로 이동하기 위해 변경 내용을 적용하는 방식은 주로 포함/제회 경로, 인덱스 유형 및 자릿수보다 인덱싱 모드 구성을 기반으로 합니다. 이전 정책과 새 정책 모두가 일관성 인덱싱을 사용하는 경우, DocumentDB는 온라인 인덱스 변환을 수행합니다. 변환이 진행 중인 동안 일관성 인덱싱 모드로 다른 인덱싱 정책 변경을 적용할 수 없습니다.

그러나 변환이 진행 중인 동안 지연 또는 없음 인덱싱 모드로 이동할 수 있습니다. 

- 지연으로 이동하면 인덱스 정책이 효과적으로 즉시 변경되고 DocumentDB는 비동기적으로 인덱스 다시 만들기를 시작합니다. 
- 없음으로 이동하면 인덱스가 즉시 삭제됩니다. 진행 중인 변환을 취소하고 다른 인덱싱 정책으로 새로 시작하려는 경우 없음으로 이동하는 것이 유용합니다. 

.NET SDK를 사용하는 경우, 새 **ReplaceDocumentCollectionAsync** 메서드를 사용하여 인덱싱 정책 변경을 시작하고 **ReadDocumentCollectionAsync** 호출에서 **IndexTransformationProgress** 응답 속성을 사용하여 인덱스 변환의 진행률을 추적할 수 있습니다. 다른 SDK 및 REST API는 인덱싱 정책 변경에 해당하는 속성 및 메서드를 지원합니다.

일관성 인덱싱 모드에서 지연으로 인덱싱 정책을 수정하는 방법을 보여주는 코드 조각은 다음과 같습니다.

**일관성에서 지연으로 인덱싱 정책 수정**

    // Switch to lazy indexing.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);


예를 들어, 아래와 같이 ReadDocumentCollectionAsync를 호출하여 인덱스 변환의 진행률을 확인할 수 있습니다.

**인덱스 변환의 진행률 추적**

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

없음 인덱싱 모드로 이동하여 컬렉션에 대한 인덱스를 삭제할 수 있습니다. 진행 중인 변환을 취소하고 새 변환을 즉시 시작하려는 경우 유용한 작업 도구일 수 있습니다.

**컬렉션에 대한 인덱스 삭제**

    // Switch to lazy indexing.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

언제 DocumentDB 컬렉션에 대한 인덱싱 정책을 변경하나요? 다음은 가장 일반적인 사용 사례입니다.

- 정상 작동 중에는 일관된 결과를 제공하지만 대량 데이터를 가져오는 동안 지연 인덱싱으로 대체됩니다.
- 공간 인덱스 종류가 필요한 지리 공간 쿼리 또는 문자열 범위 인덱스 종류가 필요한 Order By/문자열 범위 쿼리와 같은 새 인덱싱 기능을 현재 DocumentDB 컬렉션에서 사용합니다.
- 인덱싱할 속성을 선택하고 시간이 지남에 따라 변경합니다.
- 쿼리 성능을 향상시키거나 저장소 소비를 줄일 인덱싱 정밀도를 조정합니다.

>[AZURE.NOTE] ReplaceDocumentCollectionAsync를 사용하여 인덱싱 정책을 수정하려면 NET SDK의 1.3.0 이상의 버전이 필요합니다.
>
> 인덱스 변환을 성공적으로 완료하려면 컬렉션에 사용할 수 있는 충분한 저장 공간이 있는지 확인해야 합니다. 컬렉션이 해당 저장소 할당량에 도달하면 인덱스 변환은 중단됩니다. 저장소 공간을 사용할 수 있게 되면 예를 들어 일부 문서를 삭제하는 경우 인덱스 변환은 자동으로 다시 시작됩니다

## <a name="performance-tuning"></a>성능 튜닝

DocumentDB API는 사용된 인덱스 저장소와 같은 성능 메트릭에 대한 정보와 모든 작업에 대한 처리량 비용(요청 단위)을 제공합니다. 이 정보는 다양한 인덱싱 정책 및 성능 튜닝을 비교하는 데 사용할 수 있습니다.

컬렉션의 저장소 할당량 및 사용량을 확인하려면 컬렉션 리소스에 대해 HEAD 또는 GET 요청을 실행하고 x-ms-request-quota 및 x-ms-request-usage 헤더를 검사합니다. .NET SDK에서는 [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx)의 [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) 및 [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) 속성은 이에 해당하는 값을 포함합니다.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


각 쓰기 작업(만들기, 업데이트 또는 삭제)에 대한 인덱싱 오버헤드를 측정하려면 x-ms-request-charge 헤더(또는 .NET SDK의 [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx)에 있는 동등한 [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) 속성)를 검사하여 이 작업에 사용된 요청 단위 수를 측정합니다.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
     
     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"), queryString).AsDocumentQuery();

     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }
     
     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## <a name="changes-to-the-indexing-policy-specification"></a>인덱싱 정책 사양의 변경 내용
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
          "/\"nonIndexedContent\"/*"
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

## <a name="next-steps"></a>다음 단계

인덱스 정책 관리 샘플 및 DocumentDB의 쿼리 언어에 대한 자세한 내용을 보려면 아래 링크를 참조하세요.

1.  [DocumentDB .NET 인덱스 관리 코드 샘플](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2.  [DocumentDB REST API 컬렉션 작업](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3.  [DocumentDB SQL을 사용한 쿼리](documentdb-sql-query.md)

 




<!--HONumber=Oct16_HO2-->


