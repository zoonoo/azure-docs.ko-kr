---
title: 'Azure 코스모스 DB: SQL Node.js API, SDK & 리소스'
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB Node.js SDK의 각 버전 간 변경 내용을 포함하여 SQL Node.js API 및 SDK에 대한 모든 것을 알아봅니다.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 181f43e060e3b4b41babeee375365a97e1d224aa
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411279"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK for SQL API: 릴리스 정보 및 리소스
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 변경 피드](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [비동기 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST (영문)](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [대량 실행기 - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행기 - 자바](sql-api-sdk-bulk-executor-java.md)

|리소스  |링크  |
|---------|---------|
|SDK 다운로드  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API 설명서  |  [JavaScript SDK 참조 설명서](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|SDK 설치 지침  |  [설치 지침](https://github.com/Azure/azure-cosmos-js#installation)
|SDK에 참여 | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| 샘플 | [Node.js 코드 샘플](sql-api-nodejs-samples.md)
| 시작 자습서 | [JavaScript SDK 시작](sql-api-nodejs-get-started.md)
| 웹앱 자습서 | [Azure Cosmos DB를 사용하여 Node.js 웹 애플리케이션 빌드](sql-api-nodejs-application.md)
| 현재 지원되는 플랫폼 | [노드.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) - SDK 버전 3.x.x<br/>[노드.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - SDK 버전 3.x.x<br/>[노드.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - SDK 버전 3.x.x<br/>[노드.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - SDK 버전 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)- SDK 버전 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)- SDK 버전 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)- SDK 버전 1.x.x

## <a name="release-notes"></a>릴리스 정보

### <a name=""></a><a name="3.1.0"/>3.1.0</a>
* 기본 응답 연속토큰LimitInKB를 1kb로 설정합니다. 기본적으로 긴 헤더를 피하기 위해 이 것을 1kb로 제한하고 있습니다(Node.js에는 전역 헤더 크기 제한이 있음). 사용자는 이 필드를 설정하여 더 긴 헤더를 허용할 수 있으며, 이는 백 엔드가 쿼리 실행을 최적화하는 데 도움이 될 수 있습니다.
* 비활성화SSL검증을 제거합니다. 이 옵션에는 [#388](https://github.com/Azure/azure-cosmos-js/pull/388) 설명된 새로운 대안이 있습니다.

### <a name=""></a><a name="3.0.4"/>3.0.4</a>
* 초기 헤더가 파티션 키 헤더를 명시적으로 설정할 수 있도록 허용
* package.json#파일을 사용하여 불필요한 파일이 게시되지 않도록 합니다.
* 이전 버전의 노드+v8에서 라우팅 맵 정렬 오류 수정
* 사용자가 부분 재시도 옵션을 제공 하면 버그 수정

### <a name=""></a><a name="3.0.3"/>3.0.3</a>
* Webpack이 요구 사항으로 호출된 모듈을 해결하지 못하도록 방지

### <a name=""></a><a name="3.0.2"/>3.0.2</a>
* 총 쿼리에 대해 항상 R이 0으로 보고되는 긴 미해결 버그수정

### <a name=""></a><a name="3.0.0"/>3.0.0</a>

🎉 v3 출시! 🎉 많은 새로운 기능, 버그 수정 및 몇 가지 주요 변경 사항. 이 릴리스의 주요 목표:

* 주요 새로운 기능 구현
  * 고유한 쿼리
  * 제한/오프셋 쿼리
  * 사용자 취소 가능한 요청
* 모든 컨테이너에 무제한 스케일이 있는 최신 Cosmos REST API 버전으로 업데이트
* 브라우저에서 코스모스를 더 쉽게 사용할 수 있도록 합니다.
* 새로운 Azure JS SDK 지침에 더 잘 부합

#### <a name="migration-guide-for-breaking-changes"></a>변경 사항 주요 변경을 위한 마이그레이션 가이드
##### <a name="improved-client-constructor-options"></a>향상된 클라이언트 생성자 옵션

생성자 옵션이 단순화되었습니다.

* masterKey의 이름이 바뀌고 최상위 수준으로 이동했습니다.
* 이전에 options.auth 아래의 속성이 최상위 수준으로 이동했습니다.

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>간소화된 쿼리이터 API
v2에는 쿼리에서 결과를 반복하거나 검색하는 여러 가지 방법이 있었습니다. v3 API를 단순화하고 유사하거나 중복된 API를 제거하려고 했습니다.

* 반복기.next() 및 반복기.current()를 제거합니다. fetchNext()를 사용하여 결과 페이지를 가져옵니다.
* 이터레이터를 제거합니다.forEach(). 대신 비동기 이터레이터를 사용합니다.
* iterator.executeNext() 이터레이터로 이름이 바뀌었습니다.fetchNext()
* iterator.toArray() 이름이 이터레이터로 변경되었습니다.fetchAll()
* 이제 페이지가 일반 JS 개체 대신 적절한 응답 개체가 되었습니다.
* const 컨테이너 = client.database(dbId).컨테이너(컨테이너Id)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>고정 컨테이너가 이제 분할됩니다.
Cosmos 서비스는 이제 이전에 고정 된 컨테이너로 만들어진 것을 포함하여 모든 컨테이너에서 파티션 키를 지원합니다. v3 SDK는 이 변경 사항을 구현하는 최신 API 버전으로 업데이트되지만 중단되지는 않습니다. 작업에 파티션 키를 제공하지 않는 경우 모든 기존 컨테이너 및 문서와 함께 작동하는 시스템 키가 기본값으로 설정됩니다.

##### <a name="upsert-removed-for-stored-procedures"></a>저장 프로시저에 대해 Upsert 제거
이전에는 upsert가 분할되지 않은 컬렉션에 대해 허용되었지만 API 버전 업데이트에서는 모든 컬렉션이 분할되므로 완전히 제거했습니다.

##### <a name="item-reads-will-not-throw-on-404"></a>항목 읽기는 404에 던지지 않습니다.
const 컨테이너 = client.database(dbId).컨테이너(컨테이너Id)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>기본 다중 영역 쓰기
Cosmos 구성이 지원하는 경우 SDK는 기본적으로 여러 지역에 씁니다. 이전에는 옵트인 동작이었습니다.

##### <a name="proper-error-objects"></a>적절한 오류 개체
실패한 요청은 이제 적절한 오류 또는 오류 하위 클래스를 throw합니다. 이전에는 일반 JS 오브젝트를 던졌습니다.

#### <a name="new-features"></a>새로운 기능
##### <a name="user-cancelable-requests"></a>사용자 취소 가능한 요청
내부적으로 가져오기이동하면 브라우저 AbortController API를 사용하여 사용자 취소 가능한 작업을 지원할 수 있습니다. 여러 요청이 진행 중일 수 있는 작업(예: 파티션 간 쿼리)의 경우 작업에 대한 모든 요청이 취소됩니다. 최신 브라우저 사용자는 이미 AbortController를 해야합니다. Node.js 사용자는 폴리필 라이브러리를 사용해야 합니다.

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>db/컨테이너 생성 작업의 일부로 처리량 설정
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
헤더 토큰 생성이 새 라이브러리로 @azure/cosmos-sign분할되었습니다. Cosmos REST API를 직접 호출하는 사람은 누구나 이 것을 사용하여 @azure/cosmos내부에서 호출하는 것과 동일한 코드를 사용하여 헤더에 서명할 수 있습니다.

##### <a name="uuid-for-generated-ids"></a>생성된 ID에 대한 UUID
v2에는 항목 암호를 생성하는 사용자 지정 코드가 있었습니다. 우리는 잘 알려진 유지 커뮤니티 라이브러리 uuid로 전환했습니다.

##### <a name="connection-strings"></a>연결 문자열
이제 Azure 포털에서 복사된 연결 문자열을 전달할 수 있습니다.

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>향상된 브라우저 환경
브라우저에서 v2 SDK를 사용할 수 있었지만 이상적인 경험은 아니었습니다. 여러 node.js 기본 제공 라이브러리를 폴리필하고 웹팩 이나 구획과 같은 번들러를 사용해야 했습니다. v3 SDK는 브라우저 사용자에게 훨씬 더 나은 경험을 제공합니다.

* 요청 내부를 가져오기(#245)로 바꿉니다.
* 버퍼 사용 제거(#330)
* 범용 패키지/API(#328)를 위해 노드 내장 사용 제거
* 노드 중단 컨트롤러로 전환(#294)

#### <a name="bug-fixes"></a>버그 수정
* 오퍼 읽기 수정 및 제안 테스트 다시 가져오기(#224)
* 사용 설정 Endpoint 검색(#207)
* 페이지가 생성된 결과에서 누락된 루(#360) 수정
* SQL 쿼리 매개 변수 유형 확장(#346)
* 항목 정의에 ttl 추가(#341)
* CP 쿼리 메트릭 수정(#311)
* 피드응답에 활동 추가Id(#293)
* 문자열에서 숫자로 _ts 유형 전환(#252)(#295)
* 요청 요금 집계 수정(#289)
* 빈 문자열 파티션 키 허용(#277)
* 충돌 쿼리 유형에 문자열 추가(#237)
* 컨테이너에 고유키정책 추가(#234)

#### <a name="engineering-systems"></a>엔지니어링 시스템
항상 가장 눈에 띄는 변화는 아니지만 팀이 더 나은 코드를 더 빨리 출시하는 데 도움이됩니다.

* 프로덕션 빌드에 롤업 사용(#104)
* 타이프스크립트 3.5로 업데이트(#327)
* TS 프로젝트 참조로 변환합니다. 테스트 폴더 추출(#270)
* 사용 unused 지역 및 noUnused매개 변수 (#275)
* CI 빌드에 대한 Azure 파이프라인 YAML(#298)

### <a name=""></a><a name="2.1.5"/>2.1.5</a>
* 코드가 변경되지 않습니다. 일부 추가 파일이 2.1.4 패키지에 포함된 문제를 해결합니다.

### <a name=""></a><a name="2.1.4"/>2.1.4</a>
* 재시도 정책 내에서 지역 장애 조치 수정
* 변경 피드 수정추가결과 속성
* 개발자 종속성 업데이트
* 폴리헥배제스 추가

### <a name=""></a><a name="2.1.3"/>2.1.3</a>
* _ts 형식을 문자열에서 번호로 전환
* 기본 인덱싱 테스트 수정
* 백포트 고유키정책에서 v2로
* 데모 및 데모 디버깅 수정 사항

### <a name=""></a><a name="2.1.2"/>2.1.2</a>
* 백포트는 v3 분기에서 수정 사항을 제공합니다.
* executeNext() 형식 서명에서 버그 수정
* 오타 수정

### <a name=""></a><a name="2.1.1"/>2.1.1</a>
* 구조 조정을 빌드합니다. 빌드 시 SDK 버전을 당길 수 있습니다.

### <a name=""></a><a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>새로운 기능
* 추가 된 변경 피드 지원 (#196)
* 인덱싱을 위한 멀티폴리곤 데이터 타입 추가(#191)
* masterKey (#202)에 대 한 별칭으로 생성자에 "키" 속성을 추가 합니다.

#### <a name="fixes"></a>수정 프로그램
* 다음() 이터레이터에서 잘못된 값을 반환하는 버그 수정

#### <a name="engineering-improvements"></a>엔지니어링 개선 사항
* 타이프스크립트 소비를 위한 통합 테스트 추가(#199)
* GitHub에서 직접 설치 활성화(#194)

### <a name=""></a><a name="2.0.5"/>2.0.5</a>
* 노드 에이전트 형식의 인터페이스를 추가합니다. Typescript 사용자는 더 이상 @types/node를 종속성으로 설치할 필요가 없습니다.
* 이제 기본 위치가 제대로 적용됩니다.
* 개발자 설명서에 포함된 향상된 기능
* 다양한 오타 수정

### <a name=""></a><a name="2.0.4"/>2.0.4</a>
* 2.0.3에 도입된 형식 정의 문제 수정

### <a name=""></a><a name="2.0.3"/>2.0.3</a>
* `big-integer` 종속성 제거
* AsyncIterable 형식에 대한 참조 지시문으로 전환 Typescript 사용자는 더 이상 해당 "lib" 설정을 사용자 지정할 필요가 없습니다.
* 오타 수정

### <a name=""></a><a name="2.0.2"/>2.0.2</a>
* 추가 정보 링크 수정

### <a name=""></a><a name="2.0.1"/>2.0.1</a>
* 재시도 인터페이스 구현 수정

### <a name=""></a><a name="2.0.0"/>2.0.0</a>
* JavaScript SDK 버전 2.0.0 GA
* 다중 지역 쓰기 지원이 추가되었습니다.

### <a name=""></a><a name="2.0.0-3"/>2.0.0-3</a>
* 공개 미리 보기용 JavaScript SDK 버전 2.0.0의 RC1
* 관련 데이터베이스, 컨테이너 및 항목 클래스에 분할된 최상위 CosmosClient 및 메서드가 있는 새로운 개체 모델 
* [프라미스](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)에 대한 지원 
* TypeScript로 변환된 SDK

### <a name=""></a><a name="1.14.4"/>1.14.4</a>
* npm 설명서가 수정되었습니다.

### <a name=""></a><a name="1.14.3"/>1.14.3</a>
* 연결 문제에 대한 기본 재시도 지원이 추가되었습니다.
* 컬렉션 변경 피드 읽기에 대한 지원이 추가되었습니다.
* 일시적인 "읽기 세션을 사용할 수 없음" 세션 일관성 버그가 수정되었습니다.
* 쿼리 메트릭에 대한 지원이 추가되었습니다.
* http 에이전트의 최대 연결 수가 수정되었습니다.

### <a name=""></a><a name="1.14.2"/>1.14.2</a>
* Azure DocumentDB 대신 Azure Cosmos DB를 참조하도록 업데이트된 설명서입니다.
* ConnectionPolicy에서 proxyUrl 설정에 대한 지원이 추가되었습니다.

### <a name=""></a><a name="1.14.1"/>1.14.1</a>
* 대/소문자 구분 파일 시스템에 대해 사소한 수정이 있었습니다.

### <a name=""></a><a name="1.14.0"/>1.14.0</a>
* 세션 일관성에 대한 지원이 추가되었습니다.
* 이 SDK 버전은 https://aka.ms/cosmosdb-emulator에서 다운로드할 수 있는 최신 버전의 Azure Cosmos DB 에뮬레이터가 필요합니다.

### <a name=""></a><a name="1.13.0"/>1.13.0</a>
* 파티션 간 쿼리를 분할 검증했습니다
* 선행 및 후행 슬래시(및 해당 테스트)와의 리소스 링크 지원이 추가되었습니다.

### <a name=""></a><a name="1.12.2"/>1.12.2</a>
*   npm 설명서가 수정되었습니다.

### <a name=""></a><a name="1.12.1"/>1.12.1</a>
* 관련된 문서에 특수 유니코드 문자(LS, PS)가 있는 executeStoredProcedure의 버그를 수정했습니다.
* 파티션 키의 유니코드 문자를 사용하여 문서를 처리할 때 버그를 수정했습니다.
* 이름 미디어를 사용하여 컬렉션을 만들기 위한 지원을 수정했습니다. GitHub 문제 #114.
* 권한 부여 토큰에 대한 지원을 수정했습니다. GitHub 문제 #178.

### <a name=""></a><a name="1.12.0"/>1.12.0</a>
* ConsistentPrefix라고 하는 새로운 [일관성 수준](consistency-levels.md)에 대한 지원이 추가되었습니다.
* UriFactory에 대한 지원이 추가되었습니다.
* 유니코드 지원 버그를 수정했습니다. GitHub 문제 #171.

### <a name=""></a><a name="1.11.0"/>1.11.0</a>
* 집계 쿼리(COUNT, MIN, MAX, SUM 및 AVG)에 대한 지원이 추가되었습니다.
* 파티션 간 쿼리에 대한 병렬 처리 수준을 제어하기 위한 옵션을 추가했습니다.
* Azure Cosmos DB 에뮬레이터에 대해 실행할 때 TLS 확인을 사용하지 않도록 설정하는 옵션이 추가되었습니다.
* 분할된 컬렉션에 대한 최소 처리량이 10,100RU/s에서 2500RU/s로 감소됩니다.
* 단일 파티션 컬렉션에 대한 연속 토큰 버그를 수정했습니다. GitHub 문제 #107.
* 단일 매개 변수인 0을 처리하는 도중 executeStoredProcedure 버그를 수정했습니다. GitHub 문제 #155.

### <a name=""></a><a name="1.10.2"/>1.10.2</a>
* SDK 버전을 포함하도록 수정된 사용자 에이전트 헤더입니다.
* 사소한 코드 정리입니다.

### <a name=""></a><a name="1.10.1"/>1.10.1</a>
* SDK를 사용하여 에뮬레이터(hostname=localhost)를 대상으로 사용할 때 TLS 확인을 사용하지 않도록 설정합니다.
* 저장된 프로시저가 실행되는 동안 스크립트 로깅을 사용할 수 있도록 지원이 추가되었습니다.

### <a name=""></a><a name="1.10.0"/>1.10.0</a>
* 파티션 간 병렬 쿼리에 대한 지원이 추가되었습니다.
* 분할된 컬렉션의 TOP/ORDER BY 쿼리에 대한 지원이 추가되었습니다.

### <a name=""></a><a name="1.9.0"/>1.9.0</a>
* 제한된 요청에 대한 재시도 정책 지원이 추가되었습니다. (제한 요청은 너무 큰 예외, 오류 코드 429를 요청 비율을 받습니다.) 기본적으로 Azure Cosmos DB는 오류 코드(429)가 발생할 때 각 요청에 대해 9번 다시 시도하여 응답 헤더에서 다시 시도후 시간을 지정합니다. 이제 다시 시도 간의 서버에서 반환한 retryAfter 시간을 무시하려는 경우 고정된 다시 시도 간격 시간은 ConnectionPolicy 개체에서 RetryOptions 속성의 일부로 설정될 수 있습니다. Azure Cosmos DB는 제한된 요청 각각에 대해 30초 동안 대기하고(다시 시도 횟수와 관계 없이) 오류 코드 429를 포함하는 응답을 반환합니다. 이 시간도 ConnectionPolicy 개체에 있는 RetryOptions 속성에서 재정의할 수 있습니다.
* 이제 Cosmos DB는 x-ms-throttle-retry-count 및 x-ms-throttle-retry-wait-time-ms를 다시 시도 사이에 요청이 대기한 제한 다시 시도 수 및 누적 시간을 표시하는 모든 요청의 응답 헤더로 반환합니다.
* 기본 다시 시도 옵션의 일부를 재정의하는 데 사용할 수 있는 ConnectionPolicy 클래스에 RetryOptions 속성을 노출하여 RetryOptions 클래스를 추가했습니다.

### <a name=""></a><a name="1.8.0"/>1.8.0</a>
* 다중 지역 데이터베이스 계정에 대한 지원이 추가되었습니다.

### <a name=""></a><a name="1.7.0"/>1.7.0</a>
* 문서의 TTL(Time to Live) 기능에 대한 지원이 추가되었습니다.

### <a name=""></a><a name="1.6.0"/>1.6.0</a>
* [분할된 컬렉션](partition-data.md) 및 [사용자 정의 성능 수준](performance-levels.md)이 구현되었습니다.

### <a name=""></a><a name="1.5.6"/>1.5.6</a>
* 잘못된 concat 결과로 인해 링크를 반환하지 못했던 RangePartitionResolver.resolveForRead 버그가 수정되었습니다.

### <a name=""></a><a name="1.5.5"/>1.5.5</a>
* hashPartitionResolver resolveForRead() 해결: 예외를 throw하는 제공된 파티션 키가 없는 경우 모든 등록된 링크의 목록을 대신 반환합니다.

### <a name=""></a><a name="1.5.4"/>1.5.4</a>
* 문제 해결 [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - 전용 HTTPS 에이전트: Azure Cosmos DB 목적으로 전역 에이전트를 수정하지 않도록 합니다. 모든 lib의 요청에 대해 전용 에이전트를 사용 합니다.

### <a name=""></a><a name="1.5.3"/>1.5.3</a>
* 문제 해결 [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - 미디어 ID에서 대시를 올바르게 처리합니다.

### <a name=""></a><a name="1.5.2"/>1.5.2</a>
* 문제 해결 [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter 수신기 누수 경고

### <a name=""></a><a name="1.5.1"/>1.5.1</a>
* 문제 [#92](https://github.com/Azure/azure-documentdb-node/issues/90) 해결 - 대/소문자 구분 시스템으로 인해 Hash 폴더의 이름을 hash로 바꿉니다.

### <a name=""></a><a name="1.5.0"/>1.5.0</a>
* 해시 및 범위 파티션 해결 프로그램을 추가하여 분할 지원 구현

### <a name=""></a><a name="1.4.0"/>1.4.0</a>
* Upsert를 구현합니다. DocumentClient의 새로운 upsertXXX 메서드입니다.

### <a name=""></a><a name="1.3.0"/>1.3.0</a>
* 다른 SDK와 정렬된 버전 번호를 가져오기 위해 건너뛰었습니다.

### <a name=""></a><a name="1.2.2"/>1.2.2</a>
* 프라미스 래퍼를 새 리포지토리로 분할합니다.
* npm 레지스트리에 대한 패키지 파일로 업데이트합니다.

### <a name=""></a><a name="1.2.1"/>1.2.1</a>
* ID 기반 라우팅을 구현합니다.
* 문제 [#49](https://github.com/Azure/azure-documentdb-node/issues/49) 수정 - 현재 속성이 메서드 current()와 충돌합니다.

### <a name=""></a><a name="1.2.0"/>1.2.0</a>
* 지리 공간 인덱스에 대한 지원이 추가되었습니다.
* 모든 리소스에 대한 ID 속성의 유효성을 검사합니다. 리소스에 대한 아이디에는 ?, /, #,  &#47;&#47;, 문자가 포함되거나 공백으로 끝날 수 없습니다.
* ResourceResponse에 새 헤더 "인덱스 변환 진행"을 추가합니다.

### <a name=""></a><a name="1.1.0"/>1.1.0</a>
* V2 인덱싱 정책을 구현합니다.

### <a name=""></a><a name="1.0.3"/>1.0.3</a>
* 문제 [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - 핵심 및 프라미스 SDK에서 eslint 및 grunt 구성을 구현했습니다.

### <a name=""></a><a name="1.0.2"/>1.0.2</a>
* 문제 [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - 프라미스 래퍼에 오류가 있는 헤더가 포함되지 않습니다.

### <a name=""></a><a name="1.0.1"/>1.0.1</a>
* readConflicts, readConflictAsync 및 queryConflicts를 추가하여 충돌을 쿼리하는 기능을 구현했습니다.
* API 설명서가 업데이트되었습니다.
* 문제 [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync 오류

### <a name=""></a><a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 최신/지원 버전으로 원활히 전환할 수 있도록 SDK 사용 중지 최소 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다.

사용 중지된 SDK를 사용하는 Cosmos DB에 대한 요청은 서비스에서 거부됩니다.

> [!WARNING]
> SQL API용 노드 클라이언트 SDK의 모든 버전 **1.x는** **2020년 8월 30일에**사용 중지됩니다. 이는 클라이언트 측 노드 SDK에만 영향을 미치며 서버 측 스크립트(저장 프로시저, 트리거 및 DUD)에는 영향을 주지 않습니다.
> 
>
<br/>

| 버전 | 출시 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [3.1.0](#3.1.0) |2019년 7월 26일 |--- |
| [3.0.4](#3.0.4) |2019년 7월 22일 |--- |
| [3.0.3](#3.0.3) |2019년 7월 17일 |--- |
| [3.0.2](#3.0.2) |2019년 7월 9일 |--- |
| [3.0.0](#3.0.0) |2019년 6월 28일 |--- |
| [2.1.5](#2.1.5) |2019년 3월 20일 |--- |
| [2.1.4](#2.1.4) |2019년 3월 15일 |--- |
| [2.1.3](#2.1.3) |2019년 3월 8일 |--- |
| [2.1.2](#2.1.2) |2019년 1월 28일 |--- |
| [2.1.1](#2.1.1) |2018년 12월 5일 |--- |
| [2.1.0](#2.1.0) |2018년 12월 4일 |--- |
| [2.0.5](#2.0.5) |2018년 11월 7일 |--- |
| [2.0.4](#2.0.4) |2018년 10월 30일 |--- |
| [2.0.3](#2.0.3) |2018년 10월 30일 |--- |
| [2.0.2](#2.0.2) |2018년 10월 10일 |--- |
| [2.0.1](#2.0.1) |2018년 9월 25일 |--- |
| [2.0.0](#2.0.0) |2018년 9월 24일 |--- |
| [2.0.0-3(RC)](#2.0.0-3) |2018년 8월 2일 |--- |
| [1.14.4](#1.14.4) |2018년 5월 3일 |2020년 8월 30일 |
| [1.14.3](#1.14.3) |2018년 5월 3일 |2020년 8월 30일 |
| [1.14.2](#1.14.2) |2017년 12월 21일 |2020년 8월 30일 |
| [1.14.1](#1.14.1) |2017년 11월 10일 |2020년 8월 30일 |
| [1.14.0](#1.14.0) |2017년 11월 9일 |2020년 8월 30일 |
| [1.13.0](#1.13.0) |2017년 10월 11일 |2020년 8월 30일 |
| [1.12.2](#1.12.2) |2017년 8월 10일 |2020년 8월 30일 |
| [1.12.1](#1.12.1) |2017년 8월 10일 |2020년 8월 30일 |
| [1.12.0](#1.12.0) |2017년 5월 10일 |2020년 8월 30일 |
| [1.11.0](#1.11.0) |2017년 3월 16일 |2020년 8월 30일 |
| [1.10.2](#1.10.2) |2017년 1월 27일 |2020년 8월 30일 |
| [1.10.1](#1.10.1) |2016년 12월 22일 |2020년 8월 30일 |
| [1.10.0](#1.10.0) |2016년 10월 3일 |2020년 8월 30일 |
| [1.9.0](#1.9.0) |2016년 7월 7일 |2020년 8월 30일 |
| [1.8.0](#1.8.0) |2016년 6월 14일 |2020년 8월 30일 |
| [1.7.0](#1.7.0) |2016년 4월 26일 |2020년 8월 30일 |
| [1.6.0](#1.6.0) |2016년 3월 29일 |2020년 8월 30일 |
| [1.5.6](#1.5.6) |2016년 3월 8일 |2020년 8월 30일 |
| [1.5.5](#1.5.5) |2016년 2월 2일 |2020년 8월 30일 |
| [1.5.4](#1.5.4) |2016년 2월 1일 |2020년 8월 30일 |
| [1.5.2](#1.5.2) |2016년 1월 26일 |2020년 8월 30일 |
| [1.5.2](#1.5.2) |2016년 1월 22일 |2020년 8월 30일 |
| [1.5.1](#1.5.1) |2016년 1월 4일 |2020년 8월 30일 |
| [1.5.0](#1.5.0) |2015년 12월 31일 |2020년 8월 30일 |
| [1.4.0](#1.4.0) |2015년 10월 6일 |2020년 8월 30일 |
| [1.3.0](#1.3.0) |2015년 10월 6일 |2020년 8월 30일 |
| [1.2.2](#1.2.2) |2015년 9월 10일 |2020년 8월 30일 |
| [1.2.1](#1.2.1) |2015년 8월 15일 |2020년 8월 30일 |
| [1.2.0](#1.2.0) |2015년 8월 5일 |2020년 8월 30일 |
| [1.1.0](#1.1.0) |2015년 7월 9일 |2020년 8월 30일 |
| [1.0.3](#1.0.3) |2015년 6월 4일 |2020년 8월 30일 |
| [1.0.2](#1.0.2) |2015년 5월 23일 |2020년 8월 30일 |
| [1.0.1](#1.0.1) |2015년 5월 15일 |2020년 8월 30일 |
| [1.0.0](#1.0.0) |2015년 4월 8일 |2020년 8월 30일 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참조
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.

