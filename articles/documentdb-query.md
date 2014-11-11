<properties title="Query with Azure DocumentDB" pageTitle="Query with DocumentDB | Azure" description="DocumentDB's SQL query language supports a subset of ANSI SQL grammar and adds document-oriented support. Queries are served through up-to-date indexes that don't require index management."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# Azure DocumentDB 쿼리

## 사용해야 하는 이유

DocumentDB의 SQL 쿼리 언어는 ANSI SQL 문법의 하위 집합을 지원하며 중첩 데이터 구조, 배열 및 개체 생성 형식으로 문서 지향 지원을 추가합니다. 인덱스 관리의 오버헤드 없이 문서의 모든 속성에 대해 최신 상태를 자동으로 유지하는 DocumentDB의 인덱스를 통해 쿼리가 효율적으로 제공됩니다. 또한 개발자는 UDF(사용자 정의 함수)를 통해 쿼리 내부에서 JavaScript의 전체 기능을 활용할 수 있습니다.

## 왜 SQL 쿼리인가?

DocumentDB는 SQL을 지원하여 데이터를 쿼리하는 데 매우 단순하고 접근 가능한 인터페이스를 제공합니다. 언어로서 SQL은 구조적으로 간단하지만 동시에 강력하므로 응용 프로그램 개발자가 널리 사용하고 있습니다. DocumentDB는 HTTP, JSON 및 JavaScript 그리고 이제는 SQL과 같은 개방형 플랫폼 및 표준에서 심층적으로 사용됩니다.
SQL 쿼리는 .NET, Node.js 및 JavaScript 클라이언트 SDK뿐만 아니라 REST API를 통해서도 지원됩니다. 또한 SQL 쿼리는 저장 프로시저에서도 지원되며 서버 쪽 JavaScript API에서 트리거됩니다. DocumentDB는 SQL 쿼리 외에도 .NET SDK의 LINQ를 통한 쿼리를 지원합니다.

# 데이터 형식

DocumentDB에서 문서 및 메타데이터용 데이터 형식은 JSON(JavaScript Object Notation)으로 표현됩니다. JSON은 JavaScript 응용 프로그램의 성장과 플랫폼 독립 데이터 교환 형식으로의 발전 때문에 널리 사용되고 있습니다. JSON은 자체 기술성으로 인해 스키마 없는 데이터베이스에도 매우 적합합니다.

DocumentDB 쿼리에서 지원되는 원시 데이터 형식은 JSON의 형식과 같습니다. JSON에는 다음으로 구성된 단순 형식 시스템이 있습니다.

-   문자열
-   숫자(IEEE754 배정밀도)
-   부울 - True 및 False
-   Null

더욱 복잡한 데이터 형식은 JSON과 DocumentDB 둘 다에서 { } 연산자를 사용하는 중첩 개체 및 [ ] 연산자를 사용하는 배열을 생성하여 나타낼 수 있습니다.

# 쿼리 문법

사용자는 DocumentDB SQL 언어를 사용할 때 **SELECT** 문을 사용하여 JSON 문서에서 데이터를 검색할 수 있습니다. 다음은 소셜 네트워크의 게시물을 포함한 문서 컬렉션에 대한 샘플 SQL 문입니다.

     SELECT p.message, p.user.id AS user_id, p.tags[0] AS first_tag 
     FROM posts p 
     WHERE p.type = "Comment"

쿼리 출력은 JSON 문서 조각의 배열로, 지정된 제약 조건과 일치하는 각 문서에 대해 하나씩 출력됩니다.

    {
      "Documents": [
        {
            "message": "JSON rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        },
        {
            "message": "SQL rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        }
      ]
    }

SQL 쿼리 언어의 완벽한 형식 문법(BNF)은 본 문서의 끝 부분에 포함되어 있습니다.

# FROM 절

**FROM** 절은 일반적으로 쿼리가 실행되는 현재 컬렉션의 이름입니다. 예를 들어 이 절은 “posts”라는 컬렉션의 모든 문서를 선택합니다. 이름이 잘못된 경우 오류가 사용자에게 반환됩니다.

    SELECT * 
    FROM posts

이름으로 컬렉션을 참조하지 않고 **ROOT** 키워드를 사용할 수 있습니다. SDK 또는 REST API를 통하므로 특정 컬렉션 범위의 쿼리를 대상으로 할 수 있습니다.

    SELECT * 
    FROM ROOT

FROM의 출력 속성 별칭을 지정할 수 있습니다. **JOINs**를 사용하여 여러 FROM 소스를 사용하는 경우 별칭이 유용합니다. 이 내용은 뒷부분에서 다룹니다.

    SELECT P.* 
    FROM posts P

또한 중첩 속성에서 쿼리할 수도 있습니다. 예를 들어 게시물 문서에 게시물을 작성한 사용자의 하위 문서가 포함된 경우 다음 구문을 사용하여 쿼리할 수 있습니다.

    SELECT U.* 
    FROM posts.user U

FROM 문의 소스는 스칼라 배열(순서가 지정된 집합)일 수도 있습니다.

    SELECT E
    FROM ["documentdb", "json", "sql"] E

스칼라 식을 평가하는 동안 FROM 문을 한 번에 생략할 수도 있습니다.

`SELECT "documentdb"`

# WHERE 절(필터링)

DocumentDB SQL 언어는 선택적 **WHERE** 절을 지원합니다. 이 절은 문서가 충족해야 하는 지정된 조건을 기반으로 하여 결과 세트를 필터링합니다. 필터 식은 문서의 속성 또는 중첩 경로에 대한 논리적 비교를 포함할 수 있습니다.

예를 들어 메시지와 같은 속성을 쿼리에 사용할 수 있습니다. DocumentDB의 자동 인덱스를 사용하여 필터링을 효율적으로 수행할 수 있습니다.

    SELECT * 
    FROM posts p 
    WHERE p.message = "DocumentDB now supports SQL!"

점 표기법을 사용하여 필터링에 중첩 속성을 사용할 수 있습니다. 경로 길이는 문서 스키마에 따라 달라질 수 있습니다.

    SELECT * 
    FROM posts p 
    WHERE p.user.name = "Azure DocumentDB"

배열 내의 개별 값은 배열 역참조 연산자를 사용하여 참조할 수 있습니다. 배열 해제는 **IN** 키워드(뒷부분에서 설명)를 사용하여 수행할 수 있습니다.

    SELECT * 
    FROM posts p 
    WHERE p.views[0] = 100

범위 비교(\>, \<. \>=, \<=, !=)는 숫자 값에 사용할 수 있습니다. 또한 아래와 같은 산술 식의 결과와 숫자를 비교할 수도 있습니다.

    SELECT * 
    FROM posts p 
    WHERE p.views[0] > 100 * 2

일반 SQL에서와 같이 논리적 연산자를 AND, OR 및 NOT 연산자와 함께 사용할 수 있습니다. 이러한 연산자는 지정된 속성에 대한 인덱스의 공통 영역을 통해 효율적으로 처리됩니다.

    SELECT * 
    FROM posts p 
    WHERE ((p.user.name = "Azure DocumentDB")
    OR (p.views[0] = 100 AND p.message = "DocumentDB now supports SQL!"))

## 누락된 속성 및 Null 속성 처리

Null은 키워드로 지원되며, 속성은 다른 스칼라와 마찬가지로 Null과 비교할 수 있습니다.

    SELECT * 
    FROM posts p 
    WHERE p.message IS NULL

문서의 스키마가 빠르게 변하거나 이전에 알려지지 않았기 때문에 이 언어는 JavaScript의 **undefined**와 유사한 ISUNDEFINED 연산자를 통해 누락된 속성을 확인하는 특수 연산자도 지원합니다.

    SELECT * 
    FROM posts p 
    WHERE p.message IS UNDEFINED

> 참고: JavaScript와 유사하게, = undefined는 항상 false를 반환합니다.

# SELECT 절(프로젝션)

**SELECT** 절을 사용하여 쿼리에서 특정 속성을 추출할 수 있습니다. 별(\*) 연산자는 쿼리와 일치하는 전체 문서를 반환합니다.

    SELECT * 
    FROM posts

SELECT 절에서 지정된 필드를 추출할 수 있습니다. 필드는 원자성(문자열, 숫자, 부울)이거나 복합(배열, JSON 개체)일 수 있습니다. 아래 예제의 “user”와 같이 SELECT 절의 복합 유형은 속성의 전체 JSON 개체를 반환합니다. SELECT 절의 속성은 고유한 이름 또는 별칭을 포함해야 합니다.

    SELECT posts.id, posts.user
    FROM posts

> 참고: JSON은 순서가 지정되지 않으므로, 문서의 개별 속성이 특정 순서로 반환되지 않습니다.

> 참고: 필드 이름은 명시적으로 규정해야 합니다. 문서에 고정된 스키마가 없으므로, 쿼리 런타임에서 올바른 바인딩을 수행해야 합니다.

속성에 액세스하는 다른 방법은 아래와 같이 사전 조회 구문을 사용하는 것입니다.

    SELECT posts.id, posts["user"], posts["from"]
    FROM posts

> 참고: 사전 조회 구문은 위의 예제에서 FROM과 같은 예약 키워드를 사용하는 속성 이름을 이스케이프하기 위해 사용해야 합니다.

## 식 평가

스칼라 식을 식과 함께 사용할 수 있습니다. 지정된 이름이 없는 경우 자동 생성된 이름이 자리 표시자($1, $2, $3 등)로 사용됩니다.

    SELECT ((2 + 11 % 7) – 2)/3
    FROM posts

SQL 쿼리 식에서 다음 식을 사용할 수 있습니다. SQL에서와 마찬가지로 연산자는 강력한 형식입니다. 예를 들어 등호(=)는 강력한 같음으로 5 != “5”입니다.

<table>
<tr>
<td>산술 연산자</td>	
<td>+, -, \*, /, % (Modulo)</td>
</tr>
<tr>
<td>논리 연산자</td>
<td>AND, OR, NOT</td>
</tr>
<tr>
<td>비트 연산자</td>	
<td>& (비트 AND), | (비트 OR), ^ (비트 XOR)</td>
</tr>
<tr>
<td>문자열 연산자</td>	
<td>||(연결)</td>
</tr>
<tr>
<td>비교 연산자</td>	
<td>=, !=, >, <, >=, <=</td>
</tr>
</table>

## JSON 변환

SQL 쿼리는 완전한 문서가 아닌 JSON 조각을 반환할 수 있습니다. **VALUE** 키워드를 사용하여 그렇게 할 수 있습니다. 예를 들어 다음 문서는 {“id”: “2”}가 아닌 "2"를 반환합니다.

    SELECT VALUE posts.id
    FROM posts

{} 및 [] 연산자를 사용하면 JavaScript에서와 같이 더 복잡한 JSON 식을 구성할 수 있습니다. 따라서 결과 집합의 모양을 변환하는 쿼리 유연성을 얻을 수 있습니다.

    SELECT {"user_name": p.user.name, "recent_views": [p.views[0], p.views[1]]}
    FROM posts p

> 참고: 값은 임의의 유효한 식이 될 수 있지만 레이블은 리터럴(문자열)이어야 합니다.

# 조인 및 반복

문서 데이터베이스에서 참조 데이터가 하위 문서로 또는 배열 내에 포함됩니다. 따라서 SQL 언어는 문서 간 즉, **JOIN** 키워드를 사용한 셀프 조인을 지원합니다.

    SELECT p.id, tag 
    FROM posts p 
    JOIN tag IN p.tags

> 참고: 조인은 공식적으로 크로스 조인이지만 데이터가 포함되므로 내부 조인 역할을 합니다.

예제에서 **IN** 연산자는 소스의 모든 요소에 걸쳐 반복하는 데 사용됩니다. IN 절의 소스는 배열이나 개체가 될 수 있습니다. 개체와 함께 사용할 경우 반복기는 각 속성을 통과합니다. IN을 아래와 같이 직접 사용할 수도 있고, 배열 리터럴에도 사용할 수 있습니다.

    SELECT tag 
    FROM tag IN posts.tags

아래와 같이 SQL 문 내에서 여러 조인을 사용할 수 있습니다.

    SELECT p.id, tag, liked
    FROM posts p 
    JOIN tag IN p.tags
    JOIN liked IN p.likes  

# UDF(사용자 정의 함수)

DocumentDB 쿼리는 JavaScript 사용자 정의 함수 형식의 프로그래밍 가능한 확장을 지원합니다. 각 컬렉션에는 JSON 조각을 변환하여 스크립트에서 직접 이름으로 참조하는 **UDF**가 하나 이상 저장되어 있을 수 있습니다.

예를 들어 만들기 사용자 정의 함수를 사용하여 “regex\_match” 함수를 다음과 같이 정의할 수 있습니다.

    function regex_match(input, pattern) {
        getContext().getResponse().setBody(pattern.test(input));
    }

그런 다음 이 예제와 같이 쿼리에서 참조할 수 있습니다.

    SELECT *
    FROM posts p
    WHERE regex_match(p.message, "doc.*db") = true

> 참고: UDF는 입력 매개 변수에만 액세스할 수 있습니다. DocumentDB 저장 작업(읽기, 쓰기, 쿼리 등)은 UDF 내에서 지원되지 않습니다.

> 참고: 지금은 쿼리 내에서 UDF를 하나만 사용할 수 있습니다.

UDF는 리터럴뿐 아니라 JSON 조각도 처리하고 반환할 수 있습니다. 예를 들어 “array\_count” 함수는 다음을 정의할 수 있습니다.

    function array_count(input) {
        getContext().getResponse().setBody(input.length);
    }

그런 다음 쿼리에서 사용하여 문서 내 배열의 크기를 알아볼 수 있습니다.

    SELECT *
    FROM posts p
    WHERE array_count(p.likes) > 5

또한 쿼리의 SELECT 절 또는 FROM 절에서 UDF를 지정할 수 있습니다. 예를 들면 다음과 같습니다.

    SELECT array_count(p.likes) AS count
    FROM posts p

UDF 만들기 및 관리에 대한 자세한 내용은 JavaScript 프로그래밍 기능 문서를 참조하세요.

# 페이징

각 쿼리 실행 시 클라이언트에서 구성한 페이지 크기에 따라 결과의 배치가 반환됩니다. 쿼리의 모든 결과를 읽기 위해 응용 프로그램은 더 이상 읽을 데이터가 없을 때까지 각 결과 집합에 대해 페이지를 매겨야 합니다. 예를 들어 10개 이하의 문서를 읽기 위해 클라이언트에서 페이지 크기를 10으로 수정하여 반환되는 최대 문서 수를 제한할 수 있습니다. 쿼리에서 페이지 크기보다 적은 결과를 반환하거나 일부 쿼리에 대한 결과를 전혀 반환하지 않을 수 있습니다. 특정 쿼리의 모든 결과를 읽기 위해 클라이언트는 응답 연속 토큰을 사용하여 다음 배치를 가져와야 합니다(더 이상 배치가 없을 때까지).

    // Fetch pages of results up to 10 at a time. FeedOptions is optional
    DocumentServiceQuery<Database> query = (
        from db in client.CreateDatabaseQuery(new FeedOptions { MaxItemCount = 10 })
        where db.Name == dbName
        select db).AsDocumentServiceQuery();

    while (query.HasMoreResults)
    {
        databases.AddRange(await query.ExecuteNextAsync<Database>());
    }

이 기능은 기본 클라이언트 SDK/REST API의 연속 토큰을 통해 이미 사용 가능하므로, SQL 문법에서 TOP 또는 LIMIT 키워드로 구현되지 않습니다. 자세한 내용 및 예제에 대해서는 SDK의 GET 문서 또는 ReadFeed 메서드에 대한 REST API 설명서를 참조하세요.

# 쿼리 일관성 동작

DocumentDB는 4가지 튜닝 가능한 개발자 일관성 수준인 강력, 제한된 부실, 세션 및 최종 수준을 지원합니다. 쿼리는 일관성 수준과 동일한 보장을 제공합니다. 기본적으로 쿼리의 결과는 클라이언트에서 요청한 일관성 수준과 일치합니다. DocumentDB의 인덱스는 로그 구조이며, 볼륨과 상관없이 항상 최신 상태로 데이터와의 일관성을 유지하도록 설계되었습니다. 문서를 삽입 또는 업데이트할 때 쿼리 결과에서 즉시 사용할 수 있습니다.

Eventual 일관성이 필요한 응용 프로그램의 경우 선택적으로 지연 인덱싱을 사용하도록 인덱싱 정책을 구성할 수 있습니다. 이 경우 컬렉션이 유휴 상태일 때마다 인덱스는 최종적으로 일관된 방식으로 업데이트됩니다. 요약하면 다음은 여러 데이터베이스 계정 구성을 사용하는 쿼리 일관성 동작에 대한 표입니다.

| 데이터 일관성 | 인덱싱 정책 | 쿼리 동작   |
|---------------|-------------|-------------|
| 강력          | 일관        | 강력        |
| 제한된 부실   | 일관        | 제한된 부실 |
| 세션          | 일관        | 세션        |
| 최종          | 일관        | 최종        |
| 강력          | 지연        | 최종        |
| 제한된 부실   | 지연        | 최종        |
| 세션          | 지연        | 최종        |
| 최종          | 지연        | 최종        |

자세한 내용은 인덱싱 정책 및 구성에 대한 설명서를 참조하세요.

# API 및 SDK

쿼리는 REST API, 클라이언트 SDK 및 SQL 문법을 사용하는 서버 쪽 JavaScript API로 실행할 수 있습니다.

## REST API를 사용하여 쿼리

응용 프로그램은 REST API를 사용하여 컬렉션에 대해 쿼리 POST를 수행할 수 있습니다. 다음을 쿼리 요청에 포함해야 합니다.

-   헤더 x-ms-documentdb-isquery: True - 이것이 쿼리임을 나타냅니다.
-   헤더 Content-Type: application/sql - SQL 언어가 사용된다는 것을 나타냅니다.
-   SELECT 문을 포함하는 본문
    <!-- -->

    POST .../docs/executeQuery HTTP/1.1
    authorization: ...
    x-ms-continuation:
    x-ms-activity-id: 82342881-769e-4113-a662-a85c7617ed5b
    x-ms-date: Fri, 30 May 2014 22:46:13 GMT
    Match:
    x-docdb-resource-id: 9MEKcum9C2g=
    x-docdb-entity-id:
    x-ms-documentdb-isquery: True
    Cache-Control: no-cache
    x-ms-version: 2014-02-25
    User-Agent: Microsoft.Azure.Documents.Client/1.0.0.0
    Content-Type: application/sql
    Host: ...
    Content-Length: 59
    Expect: 100-continue

    SELECT b.title FROM books b WHERE b.title = 'War and Peace'

> 참고: GET 또는 쿼리 문자열을 사용하는 쿼리는 지원되지 않습니다.

## .NET SDK를 사용하는 쿼리

.NET SDK는 CreateDocumentQuery 메서드를 사용하는 쿼리를 지원하며, 이 메서드는 SQL 쿼리를 문자열로 지원합니다. 쿼리의 출력은 클라이언트 쪽 LINQ를 사용하여 처리할 수 있는 IQueryable 인터페이스입니다.

    IQueryable<dynamic> results = client.CreateDocumentQuery(collectionId).AsSQL<dynamic>(
       "SELECT b.title FROM books b WHERE b.title = 'War and Peace'");

자세한 내용은 .NET SDK 설명서를 참조하세요.

## 저장 프로시저 및 트리거 내의 쿼리

저장 프로시저 및 트리거용 서버 쪽 JavaScript API는 SQL을 사용한 쿼리도 지원합니다.

    collection.queryDocuments(collection.GetSelfLink(),
    "SELECT b.title FROM books b WHERE b.title = 'War and Peace'",
    callback);

자세한 내용은 JavaScript 서버 쪽 SDK 설명서를 참조하세요.

# 부록 A – SQL 구문

다음 철로 다이어그램은 DocumentDB 쿼리 언어용 공식 SQL 문법을 보여 줍니다.

![][0]

![][1]

![][2]

![][3]

![][4]

![][5]

![][6]

![][7]

![][8]

![][9]

![][10]

![][11]

![][12]

![][13]

![][14]

![][15]

![][16]

  [0]: ./media/documentdb-query/query1.png
  [1]: ./media/documentdb-query/query2.png
  [2]: ./media/documentdb-query/query3.png
  [3]: ./media/documentdb-query/query4.png
  [4]: ./media/documentdb-query/query5.png
  [5]: ./media/documentdb-query/query6.png
  [6]: ./media/documentdb-query/query7.png
  [7]: ./media/documentdb-query/query8.png
  [8]: ./media/documentdb-query/query9.png
  [9]: ./media/documentdb-query/query10.png
  [10]: ./media/documentdb-query/query11.png
  [11]: ./media/documentdb-query/query12.png
  [12]: ./media/documentdb-query/query13.png
  [13]: ./media/documentdb-query/query14.png
  [14]: ./media/documentdb-query/query15.png
  [15]: ./media/documentdb-query/query16.png
  [16]: ./media/documentdb-query/query17.png
