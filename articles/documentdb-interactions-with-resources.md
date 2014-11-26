<properties title="Interact with DocumentDB resources" pageTitle="Interact with DocumentDB resources | Azure" description="DocumentDB manages resources--uniquely identified by logical URIs--that developers can interact with using HTTP verbs, request/response headers, and status codes." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# DocumentDB 리소스 조작

DocumentDB는 HTTP를 통해 단순한 개방형 RESTful 프로그래밍 모델을 제공합니다. 미리 보기 릴리스에서 DocumentDB는 .NET, Python, Node.js 및 JavaScript용 클라이언트 SDK를 제공하며, 모두 기본 REST API 위의 단순한 래퍼입니다. 이후 릴리스에서는 C++ 및 Java SDK도 제공합니다. Microsoft에서 SDK를 개방한 것처럼, 특정 프로그래밍 환경을 위한 고유한 SDK를 작성하고 커뮤니티와 공유하는 것이 좋습니다.

> [AZURE.NOTE] 통신 모델이 RESTful이며 .NET 클라이언트 SDK를 통해 사용할 수 있는 효율성 높은 TCP 프로토콜도 제공합니다.

## 리소스

DocumentDB에서 관리하는 엔터티를 **리소스**라고 하며, 논리적 URI로 고유하게 식별됩니다. 개발자는 표준 HTTP 동사, 요청/응답 헤더 및 상태 코드를 사용하여 리소스를 조작할 수 있습니다. 다음 다이어그램에 표시된 대로, DocumentDB의 **리소스 모델**은 단일 데이터베이스 계정 아래에 있고 각각 논리적이고 안정적인 URI를 통해 주소 지정이 가능한 리소스 집합으로 구성됩니다. 이 문서에서는 리소스 집합을 **피드**라고 합니다.

![][0]

## 단일 데이터베이스 계정 아래의 계층적 리소스 모델

DocumentDB의 고객은 먼저 Azure 구독을 사용하여 DocumentDB **데이터베이스 계정**을 프로비전합니다. 데이터베이스 계정은 각각 여러 **컬렉션**을 포함하는 **데이터베이스** 집합으로 구성되고, 각 컬렉션에는 다시 **저장 프로시저, 트리거, UDF, 문서** 및 관련 **첨부 파일**이 포함됩니다. 데이터베이스에는 각각 다양한 다른 컬렉션, 저장 프로시저, 트리거, UDF, 문서 또는 첨부 파일에 액세스할 **권한** 집합을 가진 관련 **사용자**도 있습니다. 데이터베이스, 사용자, 사용 권한 및 컬렉션은 잘 알려진 스키마가 있는 시스템 정의 리소스인 반면 문서 및 첨부 파일에는 임의의 사용자 정의 JSON 콘텐츠가 포함됩니다.

| 리소스            | 설명                                                                                                                                                                                                                     |
|-------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 데이터베이스 계정 | 데이터베이스 계정은 프로비전된 문서 저장소 및 처리량, 데이터베이스 집합 및 blob 저장소를 나타내는 하나 이상의 용량 단위와 연결됩니다. Azure 구독을 사용하여 데이터베이스 계정을 하나 이상 만들 수 있습니다.              |
| 데이터베이스      | 데이터베이스는 여러 컬렉션으로 분할된 문서 저장소의 논리적 컨테이너입니다. 또한 사용자 컨테이너입니다.                                                                                                                   |
| 사용자            | 사용 권한 범위 지정/분할을 위한 논리적 네임스페이스입니다.                                                                                                                                                               |
| 사용 권한         | 특정 리소스에 대한 권한 있는 액세스를 위해 사용자와 연결된 권한 부여 토큰입니다.                                                                                                                                         |
| 컬렉션            | 컬렉션은 JSON 문서 및 관련 JavaScript 응용 프로그램 논리의 컨테이너입니다.                                                                                                                                               |
| 저장 프로시저     | JavaScript로 작성된 응용 프로그램 논리로, 컬렉션에 등록되고 데이터베이스 엔진 내에서 트랜잭션으로 실행됩니다.                                                                                                            |
| 트리거            | JavaScript로 작성된 응용 프로그램 논리로, 삽입, 바꾸기 또는 삭제 작업과 연결된 파생 작업을 모델링합니다.                                                                                                                 |
| UDF               | JavaScript로 작성된 응용 프로그램 논리로, 부작용이 없습니다. UDF를 사용하면 사용자 지정 쿼리 연산자를 모델링하여 핵심 DocumentDB 쿼리 언어를 확장할 수 있습니다.                                                         |
| 문서              | 사용자 정의(임의) JSON 콘텐츠입니다. 기본적으로 스키마를 정의할 필요가 없거나 컬렉션에 추가된 모든 문서에 대해 보조 인덱스를 제공해야 합니다.                                                                            |
| 첨부 파일         | 첨부 파일은 외부 blob/미디어에 대한 참조 및 관련 메타데이터가 포함된 특수 문서입니다. 개발자는 DocumentDB에서 blob을 관리하도록 선택하거나 OneDrive, Dropbox 등의 외부 blob 서비스 공급자를 사용하여 저장할 수 있습니다. |

### 시스템 및 사용자 정의 리소스

데이터베이스 계정, 데이터베이스, 컬렉션, 사용자, 사용 권한, 저장 프로시저, 트리거 및 UDF와 같은 리소스에는 모두 고정 스키마가 있으며 *시스템 리소스*라고 합니다. 반면, 문서 및 첨부 파일과 같은 리소스는 스키마에 제한이 없으며 *사용자 정의 리소스*의 예입니다. DocumentDB에서는 시스템 및 사용자 정의 리소스 둘 다 표준 규격 JSON으로 표시되고 관리됩니다. 모든 리소스(시스템 또는 사용자 정의)에는 다음과 같은 공통 속성이 있습니다.

> [AZURE.NOTE] JSON 표현에서는 리소스의 모든 시스템 생성 속성 앞에 밑줄(\_)이 추가됩니다.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p><strong>속성</strong></p></td>
<td align="left"><p><strong>사용자 설정 가능 또는 시스템 생성?</strong></p></td>
<td align="left"><p><strong>목적</strong></p></td>
</tr>
<tr class="even">
<td align="left"><p>_rid</p></td>
<td align="left"><p>시스템 생성</p></td>
<td align="left"><p>리소스의 고유한 시스템 생성 계층적 식별자</p></td>
</tr>
<tr class="odd">
<td align="left"><p>_etag</p></td>
<td align="left"><p>시스템 생성</p></td>
<td align="left"><p>낙관적 동시성 제어에 필요한 리소스의 etag</p></td>
</tr>
<tr class="even">
<td align="left"><p>_ts</p></td>
<td align="left"><p>시스템 생성</p></td>
<td align="left"><p>리소스의 마지막 업데이트 타임스탬프</p></td>
</tr>
<tr class="odd">
<td align="left"><p>_self</p></td>
<td align="left"><p>시스템 생성</p></td>
<td align="left"><p>리소스의 고유한 주소 지정 가능 URI</p></td>
</tr>
<tr class="even">
<td align="left"><p>id</p></td>
<td align="left"><p>사용자 설정 가능</p></td>
<td align="left"><p>리소스의 고유한 사용자 정의 이름</p></td>
</tr>
</tbody>
</table>

### 리소스의 네트워크 표현

DocumentDB는 JSON 표준 또는 특수 인코딩에 대한 소유 확장을 위임하지 않습니다. 표준 규격 JSON 문서로 작동합니다.

### 리소스 주소 지정

모든 리소스는 URI로 주소 지정이 가능합니다. 리소스의 \*\*\_self\*\* 속성 값은 리소스의 상대 URI를 나타냅니다. URI의 형식은 /<feed>/{\_rid} 경로 세그먼트로 구성됩니다.

|\_self 값 |설명
|-------------------|-----------
|/dbs |단일 데이터베이스 계정 아래의 데이터베이스 피드
|/dbs/{\_rid-db} |고유한 id 속성 값이 {\_rid-db}인 데이터베이스
|/dbs/{\_rid-db}/colls/ |단일 데이터베이스 아래의 컬렉션 피드
|/dbs/{\_rid-db}/colls/{\_rid-coll} |고유한 id 속성 값이 {\_rid-coll}인 컬렉션
|/dbs/{\_rid-db}/users/ |단일 데이터베이스 아래의 사용자 피드
|/dbs/{\_rid-db}/users/{\_rid-user} |고유한 id 속성 값이 {\_rid-user}인 사용자
|/dbs/{\_rid-db}/users/{\_rid-user}/permissions |단일 데이터베이스 아래의 사용 권한 피드
|/dbs/{\_rid-db}/users/{\_rid-user}/permissions/{\_rid-permission} |고유한 id 속성 값이 {\_rid-permission}인 사용 권한

리소스에는 리소스의 id 속성을 통해 노출되는 고유한 사용자 정의 이름도 있습니다. id는 특정 부모 리소스의 컨텍스트 내에서 고유한 최대 256자 길이의 사용자 정의 문자열입니다. 예를 들어 지정된 컬렉션 내에서 모든 문서의 id 속성 값은 고유하지만 컬렉션 간에는 고유하지 않을 수 있습니다. 마찬가지로 지정된 사용자에 대한 모든 사용 권한의 id 속성 값은 고유하지만 사용자 간에는 고유하지 않을 수 있습니다. \_rid 속성은 리소스의 주소 지정 가능 \_self 링크를 생성하는 데 사용됩니다.

각 리소스에는 \_rid 속성을 통해 사용할 수 있는 시스템 생성 계층적 리소스 식별자(RID라고도 함)도 있습니다. RID는 지정된 리소스의 전체 계층 구조를 인코드하며 분산 방식으로 참조 무결성을 적용하는 데 사용되는 편리한 내부 표현입니다. RID는 데이터베이스 계정 내에서 고유하며 파티션 간 조회가 필요 없는 효율적인 라우팅을 위해 DocumentDB에서 내부적으로 사용됩니다.

\_self 및 \_rid 속성 값은 둘 다 리소스의 대체 정식 표현입니다.

## 기본 조작 모델

리소스는 다음 HTTP 동사 및 해당 표준 해석을 지원합니다.

> [AZURE.NOTE] 앞으로 PATCH를 통해 선택적 업데이트를 추가로 지원할 예정입니다.

1.  POST는 새 항목 리소스 만들기를 의미합니다.
2.  GET은 기존 항목 또는 피드 리소스 읽기를 의미합니다.
3.  PUT은 기존 항목 리소스 바꾸기를 의미합니다.
4.  DELETE는 기존 항목 리소스 삭제를 의미합니다.
5.  HEAD는 응답 페이로드가 없는 GET(즉, 헤더만)을 의미합니다.

이후 릴리스에서는 항목 리소스가 PATCH 동사도 지원할 것입니다.

아래 그림에 표시된 대로, POST는 피드 리소스에 대해서만 실행할 수 있고 PUT, DELETE는 항목 리소스에 대해서만 실행할 수 있고 GET 및 HEAD는 피드 또는 항목 리소스에 대해서만 실행할 수 있습니다.

![][1]

**표준 HTTP 동사를 사용한 조작 모델**

조작 모델의 이해를 돕기 위해 새 리소스를 만드는 경우(INSERT라고도 함)를 고려해 보겠습니다. 새 리소스를 만들려면 리소스가 속하는 컨테이너 피드의 URI에 대해 리소스 표현을 요청 본문에 포함하여 HTTP POST 요청을 실행해야 합니다. 요청의 필수 속성은 리소스 id뿐입니다.

예를 들어 새 데이터베이스를 만들기 위해 id 속성을 고유 이름으로 설정하여 데이터베이스 리소스를 /dbs에 대해 POST하고, 새 컬렉션을 만들기 위해 컬렉션 리소스를 /dbs/\_rid/colls/에 대해 POST합니다. 응답에는 다른 리소스로 이동하는 데 사용할 수 있는 리소스의 \_self 링크를 비롯한 시스템 생성 속성이 있는 완전히 커밋된 리소스가 포함됩니다. 단순한 HTTP 기반 조작 모델의 예로, 클라이언트가 HTTP 요청을 실행하여 계정 내에 새 데이터베이스를 만들 수 있습니다.

    POST http://fabrikam.documents.azure.net/dbs
    {
          "id":"MyDb"
    }

DocumentDB가 성공 응답 및 데이터베이스가 성공적으로 만들어졌음을 나타내는 상태 코드로 응답합니다.

    [201 Created]
    {
          "id": "MyDb",
          "_rid": "UoEi5w==",
          "_self": "dbs/MyDb/",
          "_ts": 1407370063,
          "_etag": "\"00002100-0000-0000-0000-50e71fda0000\"",
          "_colls": "colls/",
          "_users": "users/"
    }

DocumentDB 서비스가 성공 응답 및 데이터베이스가 성공적으로 만들어졌음을 나타내는 상태 코드로 응답합니다.

리소스를 만들고 실행하는 또 다른 예로, JavaScript로만 작성된 다음 저장 프로시저를 고려해 보세요.

    function sproc(docToCreate, addedPropertyName, addedPropertyValue) {
        var collectionManager = getContext().getCollection();
        collectionManager.createDocument(collectionManager.getSelfLink(), docToCreate,   
        function(err, docCreated) {
            if(err) throw new Error('Error while creating document: ' + err.message);
            
            docCreated.addedPropertyName = addedPropertyValue;
            getContext().getResponse().setBody(docCreated);
        });
    }

/dbs/\_rid-db/colls/\_rid-coll/sprocs에 대해 POST를 실행하여 MyDb 아래의 컬렉션에 저장 프로시저를 등록할 수 있습니다.

    POST /dbs/MyDb/colls/MyColl/sprocs HTTP/1.1

    {
      "id": "sproc1",
      "body": "function (docToCreate, addedPropertyName, addedPropertyValue) {
                    var collectionManager = getContext().getCollection();
                    collectionManager.createDocument(collectionManager.getSelfLink(), 
                                docToCreate, function(err, docCreated) {
                        if(err) throw new Error('Error while creating document: ' + 
                                                         err.message);
                        
                        docCreated.addedPropertyName = addedPropertyValue;
                        getContext().getResponse().setBody(docCreated);
                    });
                }"
    }

DocumentDB 서비스가 성공 응답 및 저장 프로시저가 성공적으로 등록되었음을 나타내는 상태 코드로 응답합니다.

    [201 Created]
    {
          "id": "sproc1",
          "_rid": "EoEi5w==",
          "_self": "dbs/MyDb/colls/MyColl/sprocs/sproc1",
          "_etag": "\"00002100-0000-0000-0000-50f71fda0000\"",
           ...
    }

마지막으로, 위의 예에서 저장 프로시저를 실행하려면 저장 프로시저 리소스의 URI(/dbs/\_rid-db/colls/\_rid-coll/sprocs/sproc1)에 대해 POST를 실행해야 합니다. 이 내용은 아래에 나와 있습니다.

    POST /dbs/MyDb/colls/MyColl/sprocs/sproc1 HTTP/1.1
     [ { "id": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]

DocumentDB 서비스가 다음 응답으로 응답합니다.

    HTTP/1.1 200 OK
     { 
      "id": "TestDocument",  
      "_rid": "ZTlcANiwqwIBAAAAAAAAAA==",
      "_ts": 1407370063,
      "_self": "dbs/ZTlcAA==/colls/ZTlcANiwqwI=/docs/ZTlcANiwqwIBAAAAAAAAAA==/",
      "_etag": "00000900-0000-0000-0000-53e2c34f0000",
      "_attachments": "attachments/",
      "book": "Autumn of the Patriarch", 
      "price": 200
    }

POST 동사는 새 리소스를 만들거나 저장 프로시저를 실행하거나 SQL 쿼리를 실행하는 데 사용됩니다. SQL 쿼리 실행을 보여 주기 위해 다음을 고려해 보세요.

    POST /dbs/MyDb/colls/MyColl/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/sql

    SELECT * FROM root.children

서비스가 SQL 쿼리 결과로 응답합니다.

    HTTP/1.1 200 Ok
    x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
    x-ms-item-count: 2
    x-ms-session-token: 81
    x-ms-request-charge: 1.43
    Content-Length: 287

    {"_rid":"sehcAIE2Qy4=","Documents":[[{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}],[{"familyName":"Merriam","givenName":"Jesse","gender":"female","grade":1},{"familyName":"Miller","givenName":"Lisa","gender":"female","grade":8}]],"count":2}

리소스 바꾸기 또는 읽기는 각각 리소스의 \_self 링크에 대한 PUT(유효한 요청 본문 포함) 및 GET 동사 실행에 해당합니다. 마찬가지로 리소스 삭제는 리소스의 \_self 링크에 대한 DELETE 동사 실행에 해당합니다. DocumentDB의 리소스 모델에서 리소스를 계층적으로 구성하려면 소유자 리소스를 삭제할 경우 종속 리소스도 삭제되는 계단식 삭제를 지원해야 합니다. 종속 리소스가 소유자 리소스와 다른 노드에 분산될 수도 있으므로 삭제가 지연 발생할 수 있습니다. 가비지 수집 방식에 관계없이 리소스를 삭제하면 할당량이 즉시 해제되고 사용할 수 있게 됩니다. 참조 무결성은 시스템에서 유지합니다. 예를 들어 삭제된 데이터베이스에 컬렉션을 삽입하거나 더 이상 존재하지 않는 컬렉션의 문서를 바꾸거나 쿼리할 수 없습니다.

리소스 피드에 대해 GET을 실행하거나 컬렉션을 쿼리하면 수백만 개의 항목이 반환될 수 있으므로 단일 왕복/요청 및 응답 교환의 일부로 서버가 구체화하는 것은 물론 클라이언트가 사용하는 것도 사실상 불가능합니다. 이 문제를 처리하기 위해 DocumentDB에서는 클라이언트가 큰 피드를 한 번에 한 페이지씩 넘길 수 있습니다. 클라이언트는 [x-ms-continuationToken] 응답 헤더를 커서로 사용하여 다음 페이지로 이동할 수 있습니다.

## 낙관적 동시성 제어

대부분의 웹 응용 프로그램은 "업데이트 손실" 및 "삭제 손실" 문제를 방지하기 위해 엔터티 태그 기반 낙관적 동시성 제어를 사용합니다. 엔터티 태그는 HTTP에서 잘 작동하는 논리적 타임스탬프로, 리소스와 연결됩니다. DocumentDB는 기본적으로 특정 리소스와 연결된 버전(지속적)이 모든 HTTP 응답에 포함되도록 하여 낙관적 동시성 제어를 지원합니다. 다음과 같은 경우 동시성 제어 충돌이 올바르게 검색됩니다.

1.  두 클라이언트가 [if-match] 요청 헤더를 통해 지정된 최신 리소스 버전을 사용하여 리소스에서 PUT/ DELETE 동사를 통해 변형되는 요청을 동시에 실행할 경우 DocumentDB 데이터베이스 엔진이 트랜잭션 동시성 제어를 적용합니다.
2.  클라이언트가 [if-match] 요청 헤더를 통해 지정된 이전 리소스 버전을 제공할 경우 요청이 거부됩니다.

## 연결 옵션

DocumentDB는 각 리소스에 해당 \_self 링크로 식별되는 논리적이고 안정적인 URI가 있는 논리적 주소 지정 모델을 노출합니다. 여러 지역에 퍼져 있는 분산 저장소 시스템으로, DocumentDB의 다양한 데이터베이스 계정 아래에 있는 리소스는 많은 컴퓨터에 분할되며 고가용성을 위해 각 파티션이 복제됩니다. 지정된 파티션의 리소스를 관리하는 복제본은 물리적 주소를 등록합니다. 물리적 주소는 실패로 인해 점차 변경되지만 논리적 주소는 안정적이고 일정하게 유지됩니다. 논리적-물리적 주소 변환이 유지되는 라우팅 테이블도 내부적으로 리소스로 사용할 수 있습니다. DocumentDB는 다음 두 가지 연결 모드를 노출합니다.

1.  **게이트웨이 모드:** 클라이언트가 논리적 주소를 물리적 주소로 변환하거나 라우팅 세부 정보에 액세스할 수 없습니다. 단순히 논리적 URI를 다루며 RESTful 방식으로 리소스 모델을 탐색합니다. 클라이언트가 논리적 URI로 요청을 실행하면 에지 컴퓨터가 논리적 URI를 복제본의 물리적 주소로 변환합니다. 복제본은 리소스를 관리하고 요청을 전달합니다. 에지 컴퓨터가 라우팅 테이블을 캐시하고 주기적으로 새로 고치므로 라우팅이 매우 효율적입니다.
2.  **직접 연결 모드:** 클라이언트가 해당 프로세스 공간에서 직접 라우팅 테이블을 관리하고 주기적으로 새로 고칩니다. 클라이언트가 복제본에 직접 연결하고 에지 컴퓨터를 무시할 수 있습니다.

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p><strong>연결 모드</strong></p></td>
<td align="left"><p><strong>프로토콜</strong></p></td>
<td align="left"><p><strong>세부 정보</strong></p></td>
<td align="left"><p><strong>DocumentDB SDK</strong></p></td>
</tr>
<tr class="even">
<td align="left"><p>게이트웨이</p></td>
<td align="left"><p>HTTPS</p></td>
<td align="left"><p>응용 프로그램이 논리적 URI를 사용하여 에지 노드에 직접 연결합니다. 이 경우 추가 네트워크 홉이 발생합니다.</p></td>
<td align="left"><p>REST API</p>
<p>.NET, JavaScript, Node.js, Python</p></td>
</tr>
<tr class="odd">
<td align="left"><p>직접 연결</p></td>
<td align="left"><p>HTTPS 및</p>
<p>TCP</p></td>
<td align="left"><p>응용 프로그램이 라우팅 테이블에 직접 액세스하고 클라이언트 쪽 라우팅을 수행하여 복제본에 직접 연결할 수 있습니다.</p></td>
<td align="left"><p>.NET</p></td>
</tr>
</tbody>
</table>

## 참조

-   REST [][]<http://en.wikipedia.org/wiki/Representational_state_transfer></a>
-   JSON 사양 [][2]<http://-www.ietf.org/rfc/rfc4627.txt></a>
-   HTTP 사양 [][3]<http://www.w3.org/Protocols/rfc2616/rfc2616.html></a>
-   엔터티 태그 [][4]<http://en.wikipedia.org/wiki/HTTP_ETag></a>
-   [DocumentDB 쿼리][DocumentDB 쿼리]
-   [DocumentDB SQL 참조][DocumentDB SQL 참조]
-   [DocumentDB 프로그래밍: 저장 프로시저, 트리거 및 UDF][DocumentDB 프로그래밍: 저장 프로시저, 트리거 및 UDF]
-   [DocumentDB 참조 설명서][DocumentDB 참조 설명서]

  [0]: ./media/documentdb-interactions-with-resources/interactions-with-resources1.png
  [1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png
  [0]: http://en.wikipedia.org/wiki/Representational_state_transfer
  [2]: http://-www.ietf.org/rfc/rfc4627.txt
  [3]: http://www.w3.org/Protocols/rfc2616/rfc2616.html
  [4]: http://en.wikipedia.org/wiki/HTTP_ETag
  [DocumentDB 쿼리]: /documentation/articles/documentdb-sql-query/
  [DocumentDB SQL 참조]: http://go.microsoft.com/fwlink/p/?LinkID=510612
  [DocumentDB 프로그래밍: 저장 프로시저, 트리거 및 UDF]: /documentation/articles/documentdb-programming/
  [DocumentDB 참조 설명서]: http://go.microsoft.com/fwlink/p/?LinkID=402384
