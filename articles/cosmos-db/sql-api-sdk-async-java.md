---
title: Azure Cosmos DB는 SQL Async Java API, SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL 비동기 Java SDK의 각 버전 간 변경 내용을 포함하여 SQL 비동기 Java API 및 SDK에 대한 모든 것을 알아봅니다.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 3/5/2019
ms.author: moderakh
ms.openlocfilehash: 356838f16f7f13506657326bae5dbe994d54bdd5
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570099"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>SQL API용 Azure Cosmos DB Async Java SDK: 릴리스 정보 및 리소스
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 변경 피드](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [비동기 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST (영문)](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

SQL API 비동기 Java SDK는 [Netty 라이브러리](https://netty.io/)를 지원하고 비동기 작업을 제공하기 때문에 SQL API Java SDK와는 다릅니다. 기존 [SQL API Java SDK](sql-api-sdk-java.md)는 비동기 작업을 지원하지 않습니다. 

| |  |
|---|---|
| **SDK 다운로드** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API 설명서** |[Java API 참조 설명서](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**SDK에 참여** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**시작** | [비동기 Java SDK 시작](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**코드 샘플** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **성능 팁**| [Github 추가 정보](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **지원되는 최소 런타임**|[JDK 8](https://aka.ms/azure-jdks) | 

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name243243"></a><a name="2.4.3"/>2.4.3
* Client#close()에서 리소스 누수에 대 한 픽스 ([github #88](https://github.com/Azure/azure-cosmosdb-java/issues/88)).

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* 추가 된 연속 토큰이 지원에 대 한 파티션 간 쿼리에 합니다.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* 직접 모드에서 몇 가지 버그도를 수정 되었습니다.
* 직접 모드에서 향상 된 로깅입니다.
* 향상 된 연결 관리 합니다.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* 이제 직접 모드 연결이 GA(일반 공급)로 출시되었습니다. 직접 모드 연결을 사용하는 샘플은 [azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java) GitHub 리포지토리를 참조하세요.
* QueryMetrics 지원이 추가되었습니다.
* 순서가 중요한 java.util.Collection을 수락하는 API가 java.util.List를 대신 수락하도록 변경되었습니다. 이제 ConnectionPolicy#getPreferredLocations(), JsonSerialization 및 PartitionKey(.)는 List를 수락합니다.

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-beta-1
* 직접 모드 연결 지원이 추가되었습니다.
* 순서가 중요한 java.util.Collection을 수락하는 API가 java.util.List를 대신 수락하도록 변경되었습니다.
  이제 ConnectionPolicy#getPreferredLocations(), JsonSerialization 및 PartitionKey(.)는 List를 수락합니다.
* 게이트웨이 모드의 문서 쿼리에 대한 세션 버그를 수정했습니다.
* 종속성을 업그레이드했습니다(netty 0.4.20 [github #79](https://github.com/Azure/azure-cosmosdb-java/issues/79), RxJava 1.3.8).

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* 매우 큰 쿼리 응답의 처리를 수정합니다.
* 클라이언트를 인스턴스화할 때 리소스 토큰 처리를 수정합니다([github #78](https://github.com/Azure/azure-cosmosdb-java/issues/78)).
* 취약한 종속성 jackson-databind를 업그레이드했습니다([github #77](https://github.com/Azure/azure-cosmosdb-java/pull/77)).

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* 리소스 누수 버그를 수정했습니다.
* MultiPolygon 지원이 추가되었습니다.
* RequestOptions에서 사용자 지정 헤더 지원이 추가되었습니다.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* 패키징 버그를 수정했습니다.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* 쓰기 재시도 경로의 NPE 버그를 수정했습니다.
* 엔드포인트 관리의 NPE 버그를 수정했습니다.
* 취약한 종속성을 업그레이드했습니다([GitHub #68](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* 문제 해결을 위한 Netty 네트워크 로깅 지원이 추가되었습니다.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* 다중 지역 쓰기 지원이 추가되었습니다.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* 프록시에 대한 지원이 추가되었습니다.
* 리소스 토큰 권한 부여에 대한 지원이 추가되었습니다.
* 대용량 파티션 키([GitHub #63](https://github.com/Azure/azure-cosmosdb-java/issues/63))를 처리할 때 발생하는 버그가 수정되었습니다.
* 설명서가 개선되었습니다.
* SDK가 더욱 세분화된 모듈로 재구성되었습니다.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* 영어가 아닌 로캘에 대한 버그가 수정되었습니다([GitHub #51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* 충돌 리소스에 도우미 메서드가 추가되었습니다.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* 성능상의 이유 및 라이선싱 때문에 jackson에 의한 org.json 종속성이 대체되었습니다([GitHub #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
* 사용되지 않는 OfferV2 클래스가 제거되었습니다.
* 처리량 콘텐츠에 대한 Off 클래스에 접근자 메서드가 추가되었습니다.
* org.json 형식을 반환하는 Document/Resource의 모든 메서드가 jackson 개체 형식을 반환하도록 변경되었습니다.
* JsonSerializable을 확장하는 클래스의 getObject(.) 메서드가 jackson ObjectNode 형식을 반환하도록 변경되었습니다.
* getCollection(.) 메서드가 ObjectNode의 컬렉션을 반환하도록 변경되었습니다.
* org.json.JSONObject 인수가 있는 JsonSerializable 하위 클래스의 생성자가 제거되었습니다.
* 이제 JsonSerializable.toJson(SerializationFormattingPolicy.Indented)은 들어쓰기에 공백 두 개를 사용합니다.
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* 고유 인덱스 정책에 대한 지원이 추가되었습니다.
* 피드 옵션에서 응답 연속 토큰 크기를 제한할 수 있는 지원이 추가되었습니다.
* 교차 파티션 쿼리에서 파티션 분할에 대한 지원이 추가되었습니다.
* Json 타임스탬프 serialization의 버그를 수정했습니다([GitHub #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Json 열거형 serialization의 버그를 수정했습니다.
* 크기가 2MB인 문서 관리의 버그를 수정했습니다([GitHub #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* 버그 때문에 com.fasterxml.jackson.core:jackson-databind 종속성이 2.9.5로 업그레이드되었습니다([jackson-databind: GitHub #1599](https://github.com/FasterXML/jackson-databind/issues/1599)).
* 버그 때문에 rxjava-extras 종속성이 0.8.0.17로 업그레이드되었습니다([rxjava-extras: GitHub #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* pom 파일의 메타데이터 설명이 설명서의 나머지 부분과 일치하도록 업데이트되었습니다.
* 구문이 개선되었습니다([GitHub #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([GitHub #40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* 쿼리에서 백 압력 지원이 추가되었습니다.
* 쿼리에서 파티션 키 범위 ID에 대한 지원이 추가되었습니다.
* 요청 헤더에서 더 큰 연속 토큰을 허용하도록 수정합니다(bugfix GitHub #24).
* 주 스레드가 완료된 후 JVM이 종료하도록 4.1.22.Final로 Netty 종속성이 업그레이드되었습니다.
* 마스터 리소스를 읽을 때 세션 토큰을 전달하지 못하도록 수정합니다.
* 더 많은 예제가 추가되었습니다.
* 더 많은 벤치마킹 시나리오가 추가되었습니다.
* 적절한 java 문서 생성을 위한 Java 헤더 파일을 수정했습니다.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* 게이트웨이 모드에서 [Netty 라이브러리](https://netty.io/)를 사용하여 비차단 IO에 대한 엔드투엔드 지원을 제공하는 GA SDK입니다. 

## <a name="release-and-retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가됩니다. 따라서 가능한 빨리 항상 최신 SDK 버전으로 업그레이드하는 것이 좋습니다.

사용 중지된 SDK를 사용하는 Cosmos DB에 대한 요청은 서비스에서 거부됩니다.

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [2.4.3](#2.4.3) |2019 년 3 월 5 일|--- |
| [2.4.2](#2.4.2) |2019 년 3 월 1 일|--- |
| [2.4.1](#2.4.1) |2019 년 2 월 20 일|--- |
| [2.4.0](#2.4.0) |2019년 2월 8일|--- |
| [2.4.0-beta-1](#2.4.0-beta-1) |2019년 2월 4일|--- |
| [2.3.1](#2.3.1) |2019년 1월 15일|--- |
| [2.3.0](#2.3.0) |2018년 11월 29일|--- |
| [2.2.2](#2.2.2) |2018년 11월 8일|--- |
| [2.2.1](#2.2.1) |2018년 11월 2일|--- |
| [2.2.0](#2.2.0) |2018년 9월 22일|--- |
| [2.1.0](#2.1.0) |2018년 9월 5일|--- |
| [2.0.1](#2.0.1) |2018년 8월 16일|--- |
| [2.0.0](#2.0.0) |2018년 6월 20일|--- |
| [1.0.2](#1.0.2) |2018년 5월 18일|--- |
| [1.0.1](#1.0.1) |2018년 4월 20일|--- |
| [1.0.0](#1.0.0) |2018년 2월 27일|--- |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.

