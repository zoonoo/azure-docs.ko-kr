<properties 
	pageTitle="DocumentDB 리소스와 RESTful 상호 작용 | Microsoft Azure" 
	description="HTTP 동사를 사용하여 Microsoft Azure DocumentDB 리소스와 함께 RESTful 상호 작용을 수행하는 방법을 알아봅니다." 
	services="documentdb" 
	authors="h0n" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/03/2015" 
	ms.author="h0n"/>

# DocumentDB 리소스와 RESTful 상호 작용 

DocumentDB는 HTTP 메서드를 사용하여 DocumentDB 리소스를 만들고, 읽고, 바꾸고, 가져오고, 삭제하는 작업을 지원합니다.

이 문서를 읽어 보면 다음을 알게 됩니다.

- 표준 HTTP 메서드가 DocumentDB 리소스와 작동하는 방법
- POST를 사용하여 새 리소스를 만드는 방법
- POST를 사용하여 저장 프로시저를 등록하는 방법
- DocumentDB가 동시성 제어를 지원하는 방법
- HTTPS 및 TCP에 대한 연결 옵션

## HTTP 동사 개요
DocumentDB 리소스는 다음 HTTP 동사 및 해당 표준 해석을 지원합니다.

1.	POST는 새 항목 리소스 만들기를 의미합니다. 
2.	GET은 기존 항목 또는 피드 리소스 읽기를 의미합니다. 
3.	PUT은 기존 항목 리소스 바꾸기를 의미합니다. 
4.	DELETE는 기존 항목 리소스 삭제를 의미합니다.
5.	HEAD는 응답 페이로드가 없는 GET(즉, 헤더만)을 의미합니다. 

>[AZURE.NOTE]앞으로 PATCH를 통해 선택적 업데이트를 추가로 지원할 예정입니다.

다음 다이어그램에 표시된 대로, POST는 피드 리소스에 대해서만 실행할 수 있고 PUT 및 DELETE는 항목 리소스에 대해서만 실행할 수 있고 GET 및 HEAD는 피드 또는 항목 리소스에 대해서만 실행할 수 있습니다.

![][1]

**표준 HTTP 메서드를 사용한 조작 모델**

## POST를 사용하여 새 리소스 만들기 
조작 모델의 이해를 돕기 위해 새 리소스를 만드는 경우(INSERT라고도 함)를 고려해 보겠습니다. 새 리소스를 만들려면 리소스가 속하는 컨테이너 피드의 URI에 대해 리소스 표현을 요청 본문에 포함하여 HTTP POST 요청을 실행해야 합니다. 요청의 필수 속성은 리소스 id뿐입니다.

예를 들어 새 데이터베이스를 만들려면 /dbs에 대해 (ID 속성을 고유한 이름으로 설정하여) 데이터베이스 리소스를 게시합니다. 마찬가지로, 새 컬렉션을 만들기 위해 */dbs/\_rid/colls/* 등에 대해 컬렉션 리소스를 게시할 수 있습니다. 응답에는 다른 리소스로 이동하는 데 사용할 수 있는 리소스의 *\_self* 링크를 비롯한 시스템 생성 속성이 있는 완전히 커밋된 리소스가 포함됩니다. 단순한 HTTP 기반 조작 모델의 예로, 클라이언트가 HTTP 요청을 실행하여 계정 내에 새 데이터베이스를 만들 수 있습니다.

```
	POST https://fabrikam.documents.azure.com/dbs
	{
	      "id":"MyDb"
	}

The DocumentDB service responds with a successful response and a status code indicating successful creation of the database.  

	HTTP/1.1 201 Created
	Content-Type: application/json
	x-ms-request-charge: 4.95
	...

	{
	      "id": "MyDb",
	      "_rid": "UoEi5w==",
	      "_self": "dbs/UoEi5w==/",
	      "_ts": 1407370063,
	      "_etag": "00000100-0000-0000-0000-54b636600000",
	      "_colls": "colls/",
	      "_users": "users/"
	}
```
  
## POST를 사용하여 저장 프로시저 등록
리소스를 만들고 실행하는 또 다른 예로, JavaScript로만 작성된 단순한 "HelloWorld" 저장 프로시저를 고려해 보세요.

```
 	function HelloWorld() {
 	var context = getContext();
 	var response = context.getResponse();
 	
        response.setBody("Hello, World");
     }
```

*/dbs/\_rid-db/colls/\_rid-coll/sprocs*에 대해 POST를 실행하여 MyDb 아래의 컬렉션에 저장 프로시저를 등록할 수 있습니다.

```
	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs HTTP/1.1
	
	{
	  "id": "HelloWorld",
	  "body": "function HelloWorld() {
	           var context = getContext();
 	           var response = context.getResponse();
 	           
 	           response.setBody("Hello, World");
        	   }"
	}
```

DocumentDB 서비스가 성공 응답 및 저장 프로시저가 성공적으로 등록되었음을 나타내는 상태 코드로 응답합니다.

```
	HTTP/1.1 201 Created
	Content-Type: application/json
	x-ms-request-charge: 4.95
	...

	{
	       "body": "function HelloWorld() {
	           var context = getContext();
 	           var response = context.getResponse();
 	           
 	           response.setBody("Hello, World");
        	   }",
	      "id": "HelloWorld"
	      "_rid": "UoEi5w+upwABAAAAAAAAgA==",
	      "_ts" :  1421227641
	      "_self": "dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA==/",
	      "_etag": "00002100-0000-0000-0000-50f71fda0000"
	}
```

## POST를 사용하여 저장 프로시저 실행
마지막으로, 위의 예에서 저장 프로시저를 실행하려면 아래와 같이 저장 프로시저 리소스의 URI(/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA==/)에 대해 POST를 실행해야 합니다.

	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA== HTTP/1.1
	
DocumentDB 서비스가 다음 응답으로 응답합니다.

	HTTP/1.1 200 OK
	
	"Hello World"

POST 동사는 새 리소스를 만들거나 저장 프로시저를 실행하거나 SQL 쿼리를 실행하는 데 사용됩니다. SQL 쿼리 실행을 보여 주기 위해 다음을 고려하세요.

	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	x-ms-documentdb-query-enable-scan: True
	Content-Type: application/query+json
	...
	
	{"query":"SELECT f.LastName AS Name, f.Address.City AS City FROM Families f WHERE f.id='AndersenFamily' OR f.Address.City='NY'"}

서비스가 SQL 쿼리 결과로 응답합니다.

```
	HTTP/1.1 200 Ok
	...
	x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
	x-ms-item-count: 2
	x-ms-session-token: 4
	x-ms-request-charge: 3.1
	Content-Type: application/json1
	...
	{"_rid":"UoEi5w+upwA=","Documents":[{"Name":"Andersen","City":"Seattle"},{"Name":"Wakefield","City":"NY"}],"_count":2}
```


## PUT, GET 및 DELETE 사용
리소스 바꾸기 또는 읽기는 각각 리소스의 *\_self* 링크에 대한 PUT(유효한 요청 본문 포함) 및 GET 동사 실행에 해당합니다. 마찬가지로, 리소스 삭제는 리소스의 *\_self* 링크에 대한 DELETE 동사 실행에 해당합니다. DocumentDB의 리소스 모델에서 리소스를 계층적으로 구성하려면 소유자 리소스를 삭제할 경우 종속 리소스도 삭제되는 계단식 삭제를 지원해야 합니다. 종속 리소스가 소유자 리소스와 다른 노드에 분산될 수도 있으므로 삭제가 지연 발생할 수 있습니다. 가비지 수집 방식에 관계없이 리소스를 삭제하면 할당량이 즉시 해제되고 사용할 수 있게 됩니다. 참조 무결성은 시스템에서 유지합니다. 예를 들어 삭제된 데이터베이스에 컬렉션을 삽입하거나 더 이상 존재하지 않는 컬렉션의 문서를 바꾸거나 쿼리할 수 없습니다.
 
리소스 피드에 대해 GET을 실행하거나 컬렉션을 쿼리하면 수백만 개의 항목이 반환될 수 있으므로 단일 왕복/요청 및 응답 교환의 일부로 서버가 구체화하는 것은 물론 클라이언트가 사용하는 것도 사실상 불가능합니다. 이 문제를 처리하기 위해 DocumentDB에서는 클라이언트가 큰 피드를 한 번에 한 페이지씩 넘길 수 있습니다. 클라이언트는 [x-ms-continuation] 응답 헤더를 커서로 사용하여 다음 페이지로 이동할 수 있습니다.

## 낙관적 동시성 제어
대부분의 웹 응용 프로그램은 "업데이트 손실" 및 "삭제 손실" 문제를 방지하기 위해 엔터티 태그 기반 낙관적 동시성 제어를 사용합니다. 엔터티 태그는 HTTP에서 잘 작동하는 논리적 타임스탬프로, 리소스와 연결됩니다. DocumentDB는 기본적으로 특정 리소스와 연결된 버전(지속적)이 모든 HTTP 응답에 포함되도록 하여 낙관적 동시성 제어를 지원합니다. 다음과 같은 경우 동시성 제어 충돌이 올바르게 검색됩니다.

1.	두 클라이언트가 [if-match] 요청 헤더를 통해 지정된 최신 리소스 버전을 사용하여 리소스에서 PUT/ DELETE 동사를 통해 변형되는 요청을 동시에 실행할 경우 DocumentDB 데이터베이스 엔진이 트랜잭션 동시성 제어를 적용합니다.
2.	클라이언트가 [if-match] 요청 헤더를 통해 지정된 이전 리소스 버전을 제공할 경우 요청이 거부됩니다.  

## 연결 옵션
DocumentDB는 각 리소스에 해당 *\_self* 링크로 식별되는 논리적이고 안정적인 URI가 있는 논리적 주소 지정 모델을 노출합니다. 여러 지역에 퍼져 있는 분산 저장소 시스템으로, DocumentDB의 다양한 데이터베이스 계정 아래에 있는 리소스는 많은 컴퓨터에 분할되며 고가용성을 위해 각 파티션이 복제됩니다. 지정된 파티션의 리소스를 관리하는 복제본은 물리적 주소를 등록합니다. 물리적 주소는 실패로 인해 점차 변경되지만 논리적 주소는 안정적이고 일정하게 유지됩니다. 논리적-물리적 주소 변환이 유지되는 라우팅 테이블도 내부적으로 리소스로 사용할 수 있습니다. DocumentDB는 다음 두 가지 연결 모드를 노출합니다.

1.	**게이트웨이 모드:** 클라이언트가 논리적 주소를 물리적 주소로 변환하거나 라우팅 세부 정보에 액세스할 수 없습니다. 단순히 논리적 URI를 다루며 RESTful 방식으로 리소스 모델을 탐색합니다. 클라이언트가 논리적 URI로 요청을 실행하면 에지 컴퓨터가 논리적 URI를 복제본의 물리적 주소로 변환합니다. 복제본은 리소스를 관리하고 요청을 전달합니다. 에지 컴퓨터가 라우팅 테이블을 캐시하고 주기적으로 새로 고치므로 라우팅이 매우 효율적입니다. 
2.	**직접 연결 모드:** 클라이언트가 해당 프로세스 공간에서 직접 라우팅 테이블을 관리하고 주기적으로 새로 고칩니다. 클라이언트가 복제본에 직접 연결하고 에지 컴퓨터를 무시할 수 있습니다.   


연결 모드|프로토콜|세부 정보|DocumentDB SDK
---|---|---|---
게이트웨이|HTTPS|응용 프로그램이 논리적 URI를 사용하여 에지 노드에 직접 연결합니다. 이 경우 추가 네트워크 홉이 발생합니다.|REST API, .NET, Node.js, Java, Python, JavaScript
직접 연결|HTTPS 및 TCP|응용 프로그램이 라우팅 테이블에 직접 액세스하고 클라이언트 쪽 라우팅을 수행하여 복제본에 직접 연결할 수 있습니다.|.NET


## 다음 단계
REST API를 사용하여 리소스 작업을 수행하는 방법에 대한 자세한 내용은 [Azure DocumentDB REST API 참조](https://msdn.microsoft.com/library/azure/dn781481.aspx)(영문)를 살펴보세요.

## 참조
- [Azure DocumentDB REST API 참조(영문)](https://msdn.microsoft.com/library/azure/dn781481.aspx) 
- [DocumentDB 쿼리](../documentdb-sql-query/)
- [DocumentDB SQL 참조(영문)](https://msdn.microsoft.com/library/azure/dn782250.aspx)
- [DocumentDB 프로그래밍: 저장된 프로시저, 트리거 및 UDF](../documentdb-programming/)
- [DocumentDB 참조 설명서](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- REST [http://en.wikipedia.org/wiki/Representational\_state\_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
- JSON 사양 [http://www.ietf.org/rfc/rfc4627.txt](http://www.ietf.org/rfc/rfc4627.txt)
- HTTP 사양 [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
- 엔터티 태그 [http://en.wikipedia.org/wiki/HTTP\_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png
 

<!---HONumber=August15_HO7-->