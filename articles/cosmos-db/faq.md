---
title: "Azure Cosmos DB 질문과 대답 | Microsoft Docs"
description: "전역적으로 분산된 다중 모델 데이터베이스 서비스인 Azure Cosmos DB에 대해 자주 묻는 질문에 대한 대답을 살펴봅니다. 용량, 성능 수준 및 크기 조정에 대해 알아봅니다."
keywords: "데이터베이스 질문, 질문과 대답, Documentdb, Azure, Microsoft Azure"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: f2c0559057e677c824a2ecd971488b35bc2cc8f8
ms.contentlocale: ko-kr
ms.lasthandoff: 06/20/2017


---
# <a name="azure-cosmos-db-faq"></a>Azure Cosmos DB FAQ
## <a name="azure-cosmos-db-fundamentals"></a>Azure DB Cosmos 기본 사항
### <a name="what-is-azure-cosmos-db"></a>Azure Cosmos DB란 무엇인가요?
Azure Cosmos DB는 전역적으로 복제된 다중 모델 데이터베이스 서비스로, Microsoft Azure의 성능과 범위로 지원되는 관리 플랫폼을 통해 스키마 제약이 없는 데이터에 대해 다양한 쿼리를 제공하고, 구성 가능하며 안정적인 성능을 제공하고, 신속한 개발을 지원합니다. 

예측 가능한 처리량, 높은 가용성, 짧은 대기 시간 및 스키마 제약이 없는 데이터 모델이 핵심 요구 사항인 경우 Azure Cosmos DB는 웹, 모바일, 게임 및 IoT 응용 프로그램에 적합한 솔루션입니다. Azure Cosmos DB는 스키마 유연성과 다양한 인덱싱을 제공하며, 통합된 JavaScript로 다중 문서 트랜잭션도 지원합니다. 

이 서비스 배포 및 사용에 대한 데이터베이스 질문, 대답 및 지침은 [Azure Cosmos DB 설명서 페이지](https://azure.microsoft.com/documentation/services/cosmos-db/)를 참조하세요.

### <a name="what-happened-to-documentdb"></a>DocumentDB의 변경된 내용
DocumentDB API는 지원되는 API 중 하나로, Azure Cosmos DB용 데이터 모델입니다. 또한 Azure Cosmos DB는 Graph API(미리 보기), Table API(미리 보기) 및 MongoDB API를 통해 사용자를 지원합니다. 자세한 내용은 [DocumentDB 고객의 질문](#moving-to-cosmos-db)을 참조하세요.

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>Azure Portal에서 DocumentDB 계정을 얻는 방법
Azure Portal의 왼쪽 창에서 Azure Cosmos DB 아이콘을 클릭합니다. 이전에 DocumentDB 계정을 갖고 있었으면 청구 금액에 변동 없이 Azure Cosmos DB 계정이 생성됩니다.

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB의 대표적인 사용 사례는 무엇인가요?
Azure Cosmos DB는 자동 크기 조정, 예측 가능한 성능, 밀리초 응답 시간의 빠른 주문, 스키마 제약 없는 데이터에 대한 쿼리 기능 등이 중요한 경우 새로운 웹, 모바일, 게임 및 IoT 응용 프로그램에 적합한 솔루션입니다. Azure Cosmos DB는 응용 프로그램 데이터 모델의 신속한 개발 및 연속적인 반복 지원에 적합합니다. 사용자 생성 콘텐츠 및 데이터를 관리하는 응용 프로그램은 [Azure Cosmos DB의 일반적인 사용 사례](use-cases.md)입니다. 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB는 어떻게 예측 가능한 성능을 제공하나요?
RU([요청 단위](request-units.md))는 Azure Cosmos DB의 처리량 측정 단위입니다. 1RU 처리량은 1KB 문서의 GET 처리량에 해당합니다. 읽기, 쓰기, SQL 쿼리, 저장 프로시저 실행 등 Azure Cosmos DB에서의 모든 작업에는 작업을 완료하는 데 필요한 처리량을 기반으로 하는 결정적 RU 값이 있습니다. CPU, IO 및 메모리와 이 각각이 응용 프로그램 처리량에 미치는 영향을 고려하는 대신 단일 RU 측정값 측면에서 고려할 수 있습니다.

초당 RU 처리량 면에서 프로비전된 처리량으로 각 Azure Cosmos DB 컨테이너를 예약할 수 있습니다. 모든 규모의 응용 프로그램에 대해 개별 요청을 벤치마킹하여 해당 RU 값을 측정하고 모든 요청에서 총 요청 단위 합계를 처리하도록 컨테이너를 프로비전할 수 있습니다. 또한 응용 프로그램 요구 사항이 진화함에 따라 컨테이너의 처리량을 확장하거나 축소할 수 있습니다. 요청 단위에 대한 자세한 내용은 [필요한 처리량 예측](request-units.md#estimating-throughput-needs)을 참조하고, 컨테이너 요구 사항을 계산하려면 [처리량 계산기](https://www.documentdb.com/capacityplanner)를 사용하세요. 여기서 *컨테이너*라는 용어는 DocumentDB API 컬렉션, Graph API 그래프, MongoDB API 컬렉션 및 Table API 테이블을 의미합니다. 

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB는 HIPAA 규격인가요?
예, Azure Cosmos DB는 HIPAA 규격입니다. HIPAA는 개별적으로 식별할 수 있는 상태 정보의 사용, 공개 및 보호에 대한 요구 사항을 설정합니다. 자세한 내용은 [Microsoft 보안 센터](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)를 참조하세요.

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Azure Cosmos DB의 저장소 한도는 어느 정도인가요?
하나의 컨테이너가 Azure Cosmos DB에 저장할 수 있는 총 데이터 양에 대한 한도는 없습니다.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Azure Cosmos DB의 처리량 한도는 어느 정도인가요?
하나의 컨테이너가 Azure Cosmos DB에서 지원할 수 있는 총 처리량에 대한 한도는 없습니다. 이는 대다수 파티션 키 간에 워크로드를 거의 균등하게 분산시키기 위한 것입니다.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Azure Cosmos DB 비용은 얼마인가요?
자세한 내용은 [Azure Cosmos DB 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cosmos-db/) 페이지를 참조하세요. Azure Cosmos DB 사용 요금은 프로비전된 컨테이너 수, 컨테이너가 온라인이었던 시간 및 각 컨테이너의 프로비전된 처리량에 따라 결정됩니다. 여기서 *컨테이너*라는 용어는 DocumentDB API 컬렉션, Graph API 그래프, MongoDB API 컬렉션 및 Table API 테이블을 의미합니다. 

### <a name="is-a-free-account-available"></a>무료 계정을 사용할 수 있나요?
Azure를 처음 사용하는 경우 [Azure 무료 계정](https://azure.microsoft.com/free/)에 등록하면 30일간 제공된 $200로 모든 Azure 서비스를 사용해 볼 수 있습니다. 또는 Visual Studio 구독이 있다면 모든 Azure 서비스에 사용할 수 있는 [월별 $150의 무료 Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)에 대한 자격이 주어집니다. 

[Azure Cosmos DB 에뮬레이터](local-emulator.md)를 사용하여 Azure 구독을 구입하지 않고도 무료로 로컬에서 응용 프로그램을 개발하고 테스트할 수 있습니다. Azure Cosmos DB 에뮬레이터에서 응용 프로그램이 작동하는 방식에 만족하는 경우 Azure Cosmos DB 계정을 클라우드에서 사용하도록 전환할 수 있습니다.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Azure Cosmos DB 추가 도움말은 어떻게 구할 수 있나요?
도움이 필요한 경우 [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb) 또는 [MSDN 포럼](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)을 통해 문의하거나 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)으로 메일을 보내 Azure Cosmos DB 엔지니어링 팀과의 1:1 채팅을 예약하세요. 

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB 설정
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Azure Cosmos DB에는 어떻게 등록하나요?
Azure Cosmos DB는 Azure Portal에서 사용할 수 있습니다. 먼저 Azure 구독에 등록합니다. 등록한 후에는 Azure 구독에 DocumentDB API, Graph API(미리 보기), Table API(미리 보기) 또는 MongoDB API 계정을 추가할 수 있습니다.

### <a name="what-is-a-master-key"></a>마스터 키란 무엇인가요?
마스터 키는 계정의 모든 리소스에 액세스할 수 있는 보안 토큰입니다. 이 키가 있는 개인은 데이터베이스 계정의 모든 리소스에 대해 읽기 및 쓰기 액세스 권한을 가집니다. 마스터 키를 배포할 때 주의하세요. 주 마스터 키 및 보조 마스터 키는 [Azure Portal][azure-portal]의 **키** 블레이드에서 사용할 수 있습니다. 키에 대한 자세한 내용은 [액세스 키 보기, 복사 및 다시 생성](manage-account.md#keys)을 참조하세요.

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>PreferredLocations를 설정할 수 있는 지역은 어디인가요? 
Cosmos DB를 사용할 수 있는 모든 Azure 지역에 PreferredLocations 값을 설정할 수 있습니다. 사용 가능한 영역 목록은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Azure의 데이터 센터를 통해 전 세계에서 데이터를 배포하는 경우 알아야 할 점이 있나요? 
Azure Cosmos DB는 [Azure 지역](https://azure.microsoft.com/regions/) 페이지에 지정된 대로 모든 Azure 지역에 존재합니다. 핵심 서비스이기 때문에 새로운 데이터 센터에는 모두 Azure Cosmos DB가 갖춰집니다. 

지역을 설정할 때 Azure Cosmos DB는 국가 및 정부 클라우드를 따른다는 점을 염두에 두어야 합니다. 즉, 국가 지역에 계정을 만든 경우 해당 국가 지역 외부로 복제할 수 없습니다. 마찬가지로 외부 계정에서 다른 국가 위치로 복제할 수 없습니다. 

## <a name="develop-against-the-documentdb-api"></a>DocumentDB API에 대해 개발

### <a name="how-do-i-start-developing-against-the-documentdb-api"></a>DocumentDB API에 대해 어떻게 개발을 시작하나요?
Microsoft DocumentDB API는 [Azure Portal][azure-portal]에서 사용할 수 있습니다. 먼저 Azure 구독에 등록해야 합니다. Azure 구독에 등록하면 사용자의 Azure 구독에 DocumentDB API 컨테이너를 추가할 수 있습니다. Azure Cosmos DB 계정 추가에 대한 지침은 [Azure Cosmos DB 데이터베이스 계정 만들기](create-documentdb-dotnet.md#create-account)를 참조하세요. 이미 DocumentDB 계정이 있는 경우에는 Azure Cosmos DB 계정이 자동으로 생성됩니다. 

[SDK](documentdb-sdk-dotnet.md) 는 .NET, Python, Node.js, JavaScript 및 Java용으로 제공됩니다. 또한 개발자는 [RESTful HTTP API](/rest/api/documentdb/)를 사용하여 다양한 플랫폼 및 언어의 Azure Cosmos DB 리소스를 조작할 수 있습니다.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>유리한 출발을 위해 사전에 준비된 몇 가지 샘플에 액세스할 수 있나요?
GitHub에서 DocumentDB API [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) 및 [Python](documentdb-python-samples.md) SDK의 샘플을 사용할 수 있습니다.


### <a name="does-the-documentdb-api-database-support-schema-free-data"></a>DocumentDB API 데이터베이스에서 스키마 제약이 없는 데이터를 지원하나요?
예, DocumentDB API를 통해 응용 프로그램은 스키마 정의 또는 힌트 없이 임의의 JSON 문서를 저장할 수 있습니다. Azure Cosmos DB SQL 쿼리 인터페이스를 통해 쿼리에 즉시 데이터를 사용할 수 있습니다.  

### <a name="does-the-documentdb-api-support-acid-transactions"></a>DocumentDB API에서 ACID 트랜잭션을 지원하나요?
예, DocumentDB API는 JavaScript 저장 프로시저 및 트리거로 표현되는 문서 간 트랜잭션을 지원합니다. 동시에 실행되는 다른 코드 및 사용자 요청과 "모두 분리되거나 전혀 분리되지 않으므로" 트랜잭션은 범위가 각 컬렉션 내의 단일 파티션으로 지정되고 ACID 시맨틱으로 실행됩니다. JavaScript 응용 프로그램 코드의 서버 쪽 실행을 통해 예외가 발생하는 경우 전체 트랜잭션이 롤백됩니다. 트랜잭션에 대한 자세한 내용은 [데이터베이스 프로그램 트랜잭션](programming.md#database-program-transactions)을 참조하세요.

### <a name="what-is-a-collection"></a>컬렉션이란 무엇인가요?
컬렉션은 문서 및 관련 JavaScript 응용 프로그램 논리의 그룹입니다. 컬렉션은 처리량 및 사용한 저장소에 따라 [비용](performance-levels.md)이 결정되는 청구 가능 엔터티입니다. 컬렉션은 하나 이상의 파티션 또는 서버에 걸쳐 있을 수 있으며 크기가 거의 무제한인 저장소 또는 처리량을 처리하도록 확장할 수 있습니다.

컬렉션은 Azure Cosmos DB의 청구 엔터티이기도 합니다. 각 컬렉션은 프로비전된 처리량 및 사용된 저장소 공간에 따라 시간 단위로 요금이 청구됩니다. 자세한 내용은 [DocumentDB API 가격 책정](https://azure.microsoft.com/pricing/details/cosmos-db/)을 참조하세요. 

### <a name="how-do-i-create-a-database"></a>데이터베이스를 어떻게 만드나요?
[컬렉션 추가](create-documentdb-dotnet.md#create-collection)의 설명대로 [Azure Portal](https://portal.azure.com)을 사용하거나 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 중 하나를 사용하거나 [REST API](/rest/api/documentdb/)를 사용하여 데이터베이스를 만들 수 있습니다. 

### <a name="how-do-i-set-up-users-and-permissions"></a>사용자 및 사용 권한을 어떻게 설정하나요?
[DocumentDB API SDK](documentdb-sdk-dotnet.md) 중 하나를 사용하거나 [REST API](/rest/api/documentdb/)를 사용하여 사용자 및 사용 권한을 만들 수 있습니다.  

### <a name="does-the-documentdb-api-support-sql"></a>DocumentDB API에서 SQL을 지원하나요?
SQL 쿼리 언어는 SQL에서 지원하는 쿼리 기능의 향상된 하위 집합입니다. Azure Cosmos DB SQL 쿼리 언어는 JavaScript 기반 UDF(사용자 정의 함수)를 통해 다양한 계층 구조 및 관계형 연산자를 제공합니다. JSON 문법은 레이블이 지정된 노드가 있는 트리로 JSON 문서 모델링을 허용합니다. 이 내용은 Azure Cosmos DB 자동 인덱싱 기술 및 Azure Cosmos DB의 SQL 쿼리 언어 모두에 사용됩니다. SQL 문법을 사용하는 방법에 대한 자세한 내용은 [DocumentDB 쿼리][query] 문서를 참조하세요.

### <a name="does-the-documentdb-api-support-sql-aggregation-functions"></a>DocumentDB API는 SQL 집계 함수를 지원하나요?
DocumentDB API는 `COUNT`, `MIN`, `MAX`, `AVG` 집계 함수를 통해 모든 규모의 짧은 대기 시간 집계를 지원하고 SQL 문법을 통해 `SUM`을 지원합니다. 자세한 내용은 [집계 함수](documentdb-sql-query.md#Aggregates)를 참조하세요.

### <a name="how-does-the-documentdb-api-provide-concurrency"></a>DocumentDB API는 어떻게 동시성을 제공하나요?
DocumentDB API는 HTTP 엔터티 태그 또는 ETag를 통해 OCC(낙관적 동시성 제어)를 지원합니다. 모든 DocumentDB API 리소스에는 ETag가 있고 ETag는 문서가 업데이트될 때마다 서버에서 설정됩니다. ETag 헤더 및 현재 값은 모든 응답 메시지에 포함됩니다. ETag는 If-Match 헤더와 함께 사용하여 서버가 리소스를 업데이트해야 하는지를 결정하도록 할 수 있습니다. If-Match 값은 검사해야 할 ETag 값입니다. ETag 값이 서버 ETag 값과 일치하는 경우 리소스가 업데이트됩니다. ETag가 최신 상태가 아닌 경우 서버에서는 "HTTP 412 사전 조건 실패" 응답 코드와 함께 작업을 거부합니다. 그러면 클라이언트는 리소스를 다시 반입하여 리소스에 대한 현재 ETag 값을 가져옵니다. 또한 If-None-Match 헤더와 함께 ETag를 사용하여 리소스를 다시 인출해야 하는지를 결정합니다.

.NET에서 낙관적 동시성을 사용하려면 [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) 클래스를 사용합니다. .NET 샘플은 GitHub에 있는 DocumentManagement 샘플에서 [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) 를 참조하세요.

### <a name="how-do-i-perform-transactions-in-the-documentdb-api"></a>DocumentDB API는 어떻게 트랜잭션을 수행하나요?
DocumentDB API는 JavaScript 저장 프로시저 및 트리거를 통해 언어 통합 트랜잭션을 지원합니다. 스크립트 내의 모든 데이터베이스 작업은 스냅숏이 격리된 상태에서 실행됩니다. 단일 파티션 컬렉션인 경우 실행 범위는 해당 컬렉션으로 지정됩니다. 컬렉션이 분할된 경우 실행 범위는 컬렉션 내에서 파티션 키 값이 동일한 문서로 지정됩니다. 문서 버전의 스냅숏(ETags)은 트랜잭션의 시작 부분에서 찍고 스크립트가 성공한 경우에만 커밋됩니다. JavaScript에서 오류가 발생한 경우 트랜잭션이 롤백됩니다. 자세한 내용은 [DocumentDB API 서버 쪽 프로그래밍](programming.md)을 참조하세요.

### <a name="how-can-i-bulk-insert-documents-into-the-documentdb-api"></a>DocumentDB API에 문서를 어떻게 일괄 삽입할 수 있나요?
다음 두 가지 방법 중 하나로 Azure Cosmos DB에 문서를 대량 삽입할 수 있습니다.

* [DocumentDB API로 데이터 가져오기](import-data.md)에 설명된 데이터 마이그레이션 도구
* [DocumentDB API 서버 쪽 프로그래밍](programming.md)에 설명된 저장 프로시저

### <a name="does-the-documentdb-api-support-resource-link-caching"></a>DocumentDB API에서 리소스 링크 캐싱을 지원하나요?
예, Azure Cosmos DB는 RESTful 서비스이므로 리소스 링크가 제한되며 캐시될 수 있습니다. DocumentDB API 클라이언트는 문서 또는 컬렉션과 같은 모든 리소스 읽기에 대해 "If-None-Match" 헤더를 지정하고, 서버 버전이 변경된 후 로컬 복사본을 업데이트할 수 있습니다.

### <a name="is-a-local-instance-of-documentdb-api-available"></a>DocumentDB API의 로컬 인스턴스를 사용할 수 있나요?
예. [Azure Cosmos DB 에뮬레이터](local-emulator.md)는 신뢰도 있는 DocumentDB API 서비스의 에뮬레이션을 제공합니다. JSON 문서 만들기 및 쿼리, 컬렉션 프로비전 및 확장, 저장 프로시저 및 트리거 실행을 비롯하여 Azure Cosmos DB와 동일한 기능을 지원합니다. Azure Cosmos DB 에뮬레이터를 사용하여 응용 프로그램을 개발 및 테스트하고 Azure Cosmos DB에 대한 연결 끝점에 대한 단일 구성을 변경하여 글로벌 규모로 Azure에 배포할 수 있습니다.

## <a name="develop-against-the-api-for-mongodb"></a>API for MongoDB에 대해 개발
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB API for MongoDB란 무엇인가요?
Azure Cosmos DB API for MongoDB는 응용 프로그램에서 기존의 커뮤니티 지원 Apache MongoDB API 및 드라이버를 사용하여 네이티브 Azure Cosmos DB 데이터베이스 엔진과 간편하고 투명하게 통신할 수 있도록 도와주는 호환성 계층입니다. 이제 개발자는 기존 MongoDB 도구 체인 및 기술을 사용하여 Azure Cosmos DB를 활용하는 응용 프로그램을 빌드할 수 있습니다. 개발자는 자동 인덱싱, 백업 유지 관리, 재정적으로 뒷받침되는 SLA(서비스 수준 계약) 등 Azure Cosmos DB의 고유한 기능을 활용할 수 있습니다.

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>MongoDB 데이터베이스용 API에 연결하려면 어떻게 해야 하나요?
Azure Cosmos DB API for MongoDB에 연결하는 가장 빠른 방법은 [Azure Portal](https://portal.azure.com)로 가는 것입니다. 계정으로 이동하여 왼쪽 탐색 메뉴에서 **빠른 시작**을 클릭합니다. 빠른 시작은 데이터베이스에 연결하는 코드 조각을 얻는 가장 좋은 방법입니다. 

Azure Cosmos DB에는 엄격한 보안 요구 사항과 표준이 적용됩니다. Azure Cosmos DB 계정에는 SSL을 통한 인증 및 보안 통신이 필요하므로 TLSv1.2를 사용해야 합니다.

자세한 내용은 [MongoDB 데이터베이스용 API에 연결](connect-mongodb-account.md)을 참조하세요.

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>MongoDB 데이터베이스용 API에 대한 추가 오류 코드가 있나요?
MongoDB API는 일반적인 MongoDB 오류 코드 외에도 자체적인 특정 오류 코드를 갖고 있습니다.


| 오류               | 코드  | 설명  | 해결 방법  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 사용된 총 요청 단위 수가 컬렉션에 프로비전된 요청 단위 비율을 초과하여 제한되었습니다. | Azure Portal에서 컬렉션의 처리량을 늘리거나 다시 시도해 보세요. |
| ExceededMemoryLimit | 16501 | 다중 테넌트 서비스로써 작업이 클라이언트의 메모리 할당량을 초과했습니다. | [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에서 보다 제한적인 쿼리 조건을 통해 작업 범위를 줄이거나 고객 지원에 문의하세요. <br><br>예: *&nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api-preview"></a>Table API(미리 보기)를 사용하여 개발

### <a name="terms"></a>용어 
Azure Cosmos DB: Table API(미리 보기)는 빌드 2017에서 발표한 테이블 지원을 위해 Azure Cosmos DB에서 제공하는 프리미엄 제품을 가리킵니다. 

표준 테이블 SDK는 기존 Azure Storage 테이블 SDK입니다. 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>새 Table API(미리 보기) 제품을 사용하려면 어떻게 해야 할까요? 
Azure Cosmos DB Table API는 [Azure Portal][azure-portal]에서 사용할 수 있습니다. 먼저 Azure 구독에 등록해야 합니다. 등록한 후에는 Azure 구독에 Azure Cosmos DB Table API 계정을 추가한 다음 계정에 테이블을 추가할 수 있습니다. 

미리 보기 기간 동안 .NET에 [SDK](../cosmos-db/table-sdk-dotnet.md)를 사용할 수 있을 때 [Table API](../cosmos-db/create-table-dotnet.md) 빠른 시작 문서를 완료하여 시작할 수 있습니다.

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>Table API(미리 보기)를 사용하려면 새 SDK가 필요하나요? 
예, [Windows Azure Storage 프리미엄 테이블(미리 보기) SDK](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable)는 NuGet에서 사용할 수 있습니다. 자세한 내용은 [Azure Cosmos DB Table .NET API: 다운로드 및 릴리스 정보](https://github.com/Microsoft/azure-docs-pr/cosmos-db/table-sdk-dotnet.md) 페이지에서 확인할 수 있습니다. 

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>SDK 또는 버그에 대한 피드백은 어떻게 제공할 수 있나요?
다음 방법 중 하나로 사용자 의견을 공유할 수 있습니다.

* [Uservoice](https://feedback.azure.com/forums/263030-documentdb)
* [MSDN 포럼](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>Table API(미리 보기)에 연결하는 데 사용해야 하는 연결 문자열은 무엇인가요?
연결 문자열은 `DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com`입니다. Azure Portal의 [키] 페이지에서 연결 문자열을 가져올 수 있습니다. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-api-preview"></a>새 Table API(미리 보기)에서 요청 옵션에 대한 구성 설정은 어떻게 재정의하나요?
구성 설정에 대한 자세한 내용은 [Azure Cosmos DB 기능](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)을 참조하세요. 설정은 클라이언트 응용 프로그램의 appsettings 섹션에서 app.config에 추가하여 변경할 수 있습니다.

    <appSettings>
        <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
        <add key="TableThroughput" value="<PositiveIntegerValue"/>
        <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
        <add key="TableUseGatewayMode" value="True|False"/>
        <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
    </appSettings>


### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-standard-table-sdk"></a>기존 표준 테이블 SDK를 사용하는 고객에 대한 변동 사항이 있나요?
없음. 기존의 표준 테이블 SDK를 사용하는 기존 고객 또는 신규 고객에 대한 변동 사항은 없습니다. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>Table API(미리 보기)와 함께 사용하기 위해 Azure Cosmos DB에 저장된 테이블 데이터는 어떻게 볼 수 있나요? 
Azure Portal을 사용하여 데이터를 찾을 수 있습니다. 또한 Table API(미리 보기) 코드 또는 다음 답변에 언급된 도구를 사용할 수 있습니다. 

### <a name="which-tools-work-with-the-table-api-preview"></a>Table API(미리 보기)에는 어떤 도구를 사용할 수 있나요? 
이전 버전의 Azure Explorer(0.8.9)를 사용할 수 있습니다.

이전에 지정된 형식으로 연결 문자열을 가져오는 유연성이 있는 도구에서 새 Table API(미리 보기)를 지원할 수 있습니다. 테이블 도구 목록은 [Azure Storage Client 도구](../storage/storage-explorers.md) 페이지에 나와 있습니다. 

### <a name="do-powershell-or-azure-cli-work-with-the-new-table-api-preview"></a>PowerShell 또는 Azure CLI를 새 Table API(미리 보기)에서 사용할 수 있나요?
Table API(미리 보기)에 대한 PowerShell 및 Azure CLI 지원을 추가할 예정입니다. 

### <a name="is-the-concurrency-on-operations-controlled"></a>제어되는 작업에 대한 동시성이 있나요?
예, 낙관적 동시성은 ETag 메커니즘 사용을 통해 제공됩니다. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>엔터티에 대해 OData 쿼리 모델이 지원되나요? 
예, Table API(미리 보기)는 OData 쿼리 및 LINQ 쿼리를 지원합니다. 

### <a name="can-i-connect-to-the-standard-azure-table-and-the-new-premium-table-api-preview-side-by-side-in-the-same-application"></a>동일한 응용 프로그램에서 표준 Azure 테이블과 프리미엄 Table API(미리 보기)에 함께 연결할 수 있나요? 
예, 각각 연결 문자열을 통해 자체 URI를 가리키는 CloudTableClient의 두 가지 개별 인스턴스를 만들어 연결할 수 있습니다.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-new-offering"></a>기존 Azure Table Storage 응용 프로그램을 새 기능으로 마이그레이션하려면 어떻게 해야 하나요?
기존 Table Storage 데이터에 대해 새 Table API 기능을 활용하려면 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)에 문의하세요. 

### <a name="what-is-the-roadmap-for-this-service-and-when-will-you-offer-other-standard-table-api-functionality"></a>이 서비스에 대한 로드맵은 무엇이며, 표준 Table API의 다른 기능은 언제 제공되나요?
GA로 나아감에 따라 SAS 토큰, ServiceContext, 통계, 클라이언트 쪽 암호화, 분석 및 기타 기능에 대한 지원을 추가할 계획입니다. [Uservoice](https://feedback.azure.com/forums/263030-documentdb)에서 의견을 제공할 수 있습니다. 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>예를 들어 *n*GB에서 시작한 데이터가 시간이 흘러 1TB까지 증가하게 되면 이 서비스의 경우 저장소 크기를 어떻게 확장할 수 있나요? 
Azure Cosmos DB는 수평적 확장을 통해 무제한 저장소를 제공하도록 설계되어 있습니다. 이 서비스는 저장소를 모니터링하여 효율적으로 증가시킬 수 있습니다. 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>Table API(미리 보기) 서비스는 어떻게 모니터링하나요?
Table API(미리 보기) **메트릭** 창에서 요청 및 저장소 사용량을 모니터링할 수 있습니다. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>필요한 처리량은 어떻게 계산하나요?
용량 추정기를 사용하여 작업에 필요한 TableThroughput을 계산할 수 있습니다. 자세한 내용은 [요청 단위 및 데이터 저장소 예측](https://www.documentdb.com/capacityplanner)을 참조하세요. 일반적으로 엔터티를 JSON으로 나타내고 작업 수를 제공할 수 있습니다. 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>로컬에서 새 Table API(미리 보기) SDK를 에뮬레이터와 함께 사용할 수 있나요?
예, 새 SDK를 사용하는 경우 로컬 에뮬레이터에서 Table API(미리 보기)를 사용할 수 있습니다. 새 에뮬레이터를 다운로드하려면 [로컬 개발 및 테스트에 Azure Cosmos DB 에뮬레이터 사용](local-emulator.md)으로 이동하세요. app.config에서 StorageConnectionString 값은 `DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081`이어야 합니다. 

### <a name="can-my-existing-application-work-with-the-table-api-preview"></a>기존 응용 프로그램을 Table API(미리 보기)와 함께 사용할 수 있나요? 
.NET API를 사용한 만들기, 삭제, 업데이트 및 쿼리 작업에서 새 Table API(미리 보기)의 노출 영역은 기존 Azure 표준 테이블 SDK와 호환됩니다. Table API(미리 보기)에는 파티션 키와 행 키가 모두 필요하기 때문에 행 키가 있는지 확인합니다. 또한 이 서비스의 GA를 계속 진행하면서 더 많은 SDK 지원을 추가할 계획입니다.

### <a name="do-i-need-to-migrate-my-existing-azure-table-based-applications-to-the-new-sdk-if-i-do-not-want-to-use-the-table-api-preview-features"></a>Table API (미리 보기) 기능을 사용할 생각이 없는데 기존의 Azure 테이블 기반 응용 프로그램을 새 SDK로 마이그레이션해야 하나요?
아니요, 어떤 종류의 중단도 없이 기존 표준 테이블 자산을 만들고 사용할 수 있습니다. 그러나 새 Table API(미리 보기)를 사용하지 않으면 자동 인덱스, 추가 일관성 옵션 또는 전역 배포의 이점을 누릴 수 없습니다. 

### <a name="how-do-i-add-replication-of-the-data-in-the-premium-table-api-preview-across-multiple-regions-of-azure"></a>어떻게 하면 여러 Azure 지역에 걸쳐 프리미엄 Table API(미리 보기)의 데이터 복제를 추가할 수 있나요?
Azure Cosmos DB 포털의 [전역 복제 설정](tutorial-global-distribution-documentdb.md#portal)을 사용하여 응용 프로그램에 적합한 지역을 추가할 수 있습니다. 전역적으로 분산된 응용 프로그램을 개발하려면 읽기 대기 시간을 단축하기 위해 로컬 지역에 PreferredLocation 정보가 설정된 응용 프로그램을 추가해야 합니다. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-premium-table-api-preview"></a>프리미엄 Table API(미리 보기)의 계정에 대한 기본 쓰기 지역을 어떻게 변경할 수 있나요?
Azure Cosmos DB의 전역 복제 포털 창에서 지역을 추가한 다음 필요한 지역에 장애 조치(Failover)를 추가할 수 있습니다. 지침은 [다중 지역 Azure Cosmos DB 계정을 사용하여 개발](regional-failover.md)을 참조하세요. 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>데이터 분산 시 대기 시간을 단축하려면 기본 읽기 하위 지역을 어떻게 구성해야 하나요? 
로컬 위치에서 쉽게 읽으려면 app.config 파일에서 PreferredLocation 키를 사용합니다. 기존 응용 프로그램에 대해 LocationMode가 설정된 경우 Table API(미리 보기)는 오류를 throw합니다. 프리미엄 Table API(미리 보기)는 app.config 파일에서 이 정보를 선택하므로 해당 코드를 제거합니다. 자세한 내용은 [Azure Cosmos DB 기능](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)을 참조하세요.

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api-preview"></a>Table API(미리 보기)의 일관성 수준에 대해 어떻게 생각해야 하나요? 
Azure Cosmos DB는 일관성, 가용성 및 대기 시간 간을 매우 논리적으로 절충합니다. Azure Cosmos DB는 Table API(미리 보기) 개발자에게 5가지 일관성 수준을 제공하므로 테이블 수준에서 올바른 일관성 모델을 선택하고 데이터를 쿼리하는 동안 개별적으로 요청할 수 있습니다. 클라이언트가 연결되면 일관성 수준을 지정할 수 있습니다. TableConsistencyLevel 키 값에 대한 app.config 설정을 통해 수준을 변경할 수 있습니다. 

Table API(미리 보기)는 읽기 및 쓰기에 짧은 대기 시간 읽기와 세션 일관성을 기본적으로 제공합니다. 자세한 내용은 [ 일관성 수준](consistency-levels.md)을 참조하세요. 

기본적으로 Azure Table Storage는 지역 내에서 강력한 일관성을 제공하고 보조 위치에서 최종 일관성을 제공합니다. 

### <a name="does-azure-cosmos-db-offer-more-consistency-levels-than-standard-tables"></a>Azure Cosmos DB는 표준 테이블보다 더 많은 일관성 수준을 제공하나요?
예, Azure Cosmos DB의 분산된 특성에서 활용하는 방법에 대한 자세한 내용은 [일관성 수준](consistency-levels.md)을 참조하세요. 일관성 수준에 대한 보장을 제공하기 때문에 확신을 가지고 사용할 수 있습니다. 자세한 내용은 [Azure Cosmos DB 기능](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)을 참조하세요.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>전역 분산이 가능한 경우 데이터 복제에 얼마나 걸리나요?
로컬 지역에서 지속적으로 데이터를 적용하고 몇 밀리초 이내에 즉시 다른 지역에 데이터를 밀어넣습니다. 이 복제는 데이터 센터의 RTT(왕복 시간)에만 종속됩니다. Azure Cosmos DB의 전역 분산 기능에 대한 자세한 내용은 [Azure Cosmos DB: Azure에서 전역적으로 분산 데이터베이스 서비스](distribute-data-globally.md)를 참조하세요.

### <a name="can-the-read-request-consistency-level-be-changed"></a>읽기 요청 일관성 수준을 변경할 수 있나요?
Azure Cosmos DB를 사용하면 컨테이너 수준에서 일관성 수준을 설정할 수 있습니다. SDK를 사용하면 또한 app.config 파일에서 TableConsistencyLevel 키에 대한 값을 제공하여 컨테이너 수준을 변경할 수 있습니다. 가능한 값은 강력, 제한된 부실, 세션, 일관적인 접두사 및 최종입니다. 자세한 내용은 [Azure Cosmos DB의 조정 가능한 데이터 일관성 수준](consistency-levels.md)을 참조하세요. 주요 개념은 테이블 설정보다 높게 요청 일관성 수준을 설정할 수 없다는 점입니다. 예를 들어 테이블에 대한 일관성 수준을 최종으로 설정하고 요청 일관성 수준을 강력을 설정할 수 없습니다. 

### <a name="how-does-the-premium-table-api-preview-account-handle-failover-if-a-region-goes-down"></a>지역에서 작동이 중지된 경우 프리미엄 Table API(미리 보기) 계정은 장애 조치(Failover)를 어떻게 처리하나요? 
프리미엄 Table API(미리 보기)는 Azure Cosmos DB의 전역적으로 분산된 플랫폼을 활용합니다. 응용 프로그램이 데이터 센터 가동 중지 시간을 견뎌낼 수 있도록 하려면 Azure Cosmos DB 포털 [다중 하위 지역 Azure Cosmos DB 계정을 사용하여 개발](regional-failover.md)에서 계정에 대해 하위 지역을 하나 이상 사용하도록 설정합니다. 포털 [다중 하위 지역 Azure Cosmos DB 계정을 사용하여 개발](regional-failover.md)을 사용하여 하위 지역의 우선 순위를 설정할 수 있습니다. 

계정에 대해 원하는 개수의 지역을 추가하고 장애 조치(Failover) 우선 순위를 지정하여 장애 조치할 수 있는 위치를 제어할 수 있습니다. 물론, 데이터베이스를 사용하려면 응용 프로그램도 제공해야 합니다. 이렇게 하면 고객에게 가동 중지 시간이 발생하지 않습니다. 클라이언트 SDK는 자동으로 호밍합니다. 즉, 중단된 지역을 검색하여 새 지역으로 자동으로 장애 조치할 수 있습니다.

### <a name="is-the-premium-table-api-preview-enabled-for-backups"></a>백업에 프리미엄 Table API(미리 보기)를 사용할 수 있나요?
예, 프리미엄 Table API(미리 보기)는 Azure Cosmos DB의 플랫폼을 백업에 활용합니다. 백업은 자동으로 수행됩니다. 자세한 내용은 [Azure Cosmos DB로 자동 온라인 백업 및 복원](online-backup-and-restore.md)을 참조하세요.

 
### <a name="does-the-table-api-preview-index-all-attributes-of-an-entity-by-default"></a>Table API(미리 보기)는 기본적으로 엔터티의 모든 특성을 인덱싱하나요?
예, 기본적으로 엔터티의 모든 특성이 인덱싱됩니다. 자세한 내용은 [Azure Cosmos DB: 인덱싱 정책](indexing-policies.md)을 참조하세요. 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>그렇다면 쿼리를 충족하기 위해 여러 인덱스를 만들 필요가 없다는 의미인가요? 
예, Azure Cosmos DB에서는 스키마 정의 없이 모든 특성에 대한 자동 인덱싱을 제공합니다. 이 자동화 덕분에 개발자는 인덱스 만들기 및 관리보다 응용 프로그램에 역량을 집중할 수 있습니다. 자세한 내용은 [Azure Cosmos DB: 인덱싱 정책](indexing-policies.md)을 참조하세요.

### <a name="can-i-change-the-indexing-policy"></a>인덱싱 정책을 변경할 수 있나요?
예, 인덱스 정의를 제공하여 인덱싱 정책을 변경할 수 있습니다. 자세한 내용은 [Azure Cosmos DB 기능](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)을 참조하세요. 설정을 적절하게 인코딩 및 이스케이프해야 합니다. 

app.config 파일의 json 형식 문자열에서:
```
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        } 
      ]
    }
  ],
  "excludedPaths": 
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>플랫폼인 Azure Cosmos DB에는 정렬, 집계, 계층 구조 등 많은 기능이 있는데, 이러한 기능이 Table API에 추가될 계획이 있나요 ? 
미리 보기에서 Table API는 Azure Table Storage와 동일한 쿼리 기능을 제공합니다. 또한 Azure Cosmos DB는 정렬, 집계, 지리 공간적 쿼리, 계층 구조 및 다양한 기본 제공 함수도 지원합니다. 추가 기능은 향후 서비스 업데이트의 Table API에서 제공될 예정입니다. 자세한 내용은 [Azure Cosmos DB 쿼리](../documentdb/documentdb-sql-query.md)를 참조하세요.
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>Table API(미리 보기)에 대한 TableThroughput은 언제 변경해야 하나요?
다음 조건 중 하나가 적용된 경우 TableThroughput을 변경해야 합니다.
* 데이터의 ETL(추출, 변환 및 로드)을 수행하거나, 짧은 시간 내에 많은 양의 데이터를 업로드할 때 
* 백 엔드에서 컨테이너에 추가 처리량이 필요할 때. 예를 들어 사용되는 처리량이 프로비전된 처리량보다 많은 경우 제한에 이르게 됩니다. 자세한 내용은 [처리량 설정](set-throughput.md)을 참조하세요.

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-preview-table"></a>내 Table API(미리 보기) 테이블의 처리량을 늘리거나 줄일 수 있나요? 
예, Azure Cosmos DB Azure 포털의 배율 창을 사용하여 처리량의 크기를 조정할 수 있습니다. 자세한 내용은 [처리량 설정](set-throughput.md)을 참조하세요.

### <a name="can-the-premium-table-api-preview-take-advantage-of-the-ru-per-minute-offering"></a>프리미엄 Table API(미리 보기)는 분당 RU 서비스를 활용할 수 있나요? 
예, 프리미엄 Table API(미리 보기)는 성능, 대기 시간, 가용성 및 일관성에 대한 SLA를 제공하기 위해 Azure Cosmos DB의 기능을 활용합니다. 이 기능은 테이블에서 분당 RU 서비스를 사용할 수 있도록 합니다. 자세한 내용은 [Azure Cosmos DB의 요청 단위](request-units.md)를 참조하세요. 이 기능을 통해 고객은 최대 사용을 대비해 프로비전하지 않고 워크로드 급증을 원활하게 처리할 수 있습니다.

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>새로 프로비전된 테이블에 대해 기본 TableThroughput이 설정되나요?
예, app.config를 통해 TableThroughput을 재정의하지 않고 Azure Cosmos DB에서 미리 만든 컨테이너를 사용하지 않으면 서비스에서 처리량이 400인 테이블을 만듭니다.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-standard-table-api"></a>표준 Table API의 기존 고객에 대한 가격 책정에 변동 사항이 있나요?
없음. 기존 표준 Table API 고객에 대한 가격에는 변동 사항이 없습니다. 

### <a name="how-is-the-price-calculated-for-the-table-api-preview"></a>Table API(미리 보기)의 가격은 어떻게 계산하나요? 
가격은 할당된 TableThroughput에 따라 달라집니다. 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-preview-offering"></a>Table API(미리 보기) 제공에서는 테이블에 대한 제한을 어떻게 처리하나요? 
요청 속도가 기본 컨테이너에 대해 프로비전된 처리량의 용량을 초과하면 오류가 발생하고 SDK에서 다시 시도 정책을 적용하여 호출을 다시 시도합니다.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-premium-table-api-preview-offering-of-azure-cosmos-db"></a>Azure Cosmos DB의 프리미엄 Table(미리 보기) 제공을 활용하려면 PartitionKey 및 RowKey와 별도로 처리량을 선택해야 하는 이유는 무엇인가요?
사용자가 app.config 파일에서 기본 처리량을 제공하지 않으면 Azure Cosmos DB가 사용자의 컨테이너에 대해 기본 처리량을 설정합니다. 

Azure Cosmos DB는 작업에 대한 상한을 사용하여 성능 및 대기 시간을 보장합니다. 이 보장은 엔진이 테넌트의 작업에 대한 거버넌스를 적용할 수 있는 경우 가능합니다. TableThroughput을 설정하면 플랫폼에서 이 용량을 예약하고 작업 성공을 보장하므로 보장된 처리량 및 대기 시간을 가져올 수 있습니다. 

처리량 지정을 사용하면 처리량을 탄력적으로 변경하여 응용 프로그램의 계절성을 활용하고, 처리량 요구 사항을 충족하고, 비용을 절감할 수 있습니다.

### <a name="azure-storage-sdk-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-new-azure-cosmos-db-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>저는 데이터 저장 비용만 부담하면 되고 거의 쿼리하지 않기 때문에 Azure Storage SDK가 매우 저렴했습니다. 그런데 새로운 Azure Cosmos DB 제품은 단일 트랜잭션을 수행하지 않았거나 어떠한 것도 저장하지 않았는데도 비용이 청구되는 것 같습니다. 설명해 주실 수 있나요?

Azure Cosmos DB는 전역적으로 분산된 SLA 기반 시스템으로, 가용성, 대기 시간 및 처리량을 보장하도록 설계되었습니다. Azure Cosmos DB에서 처리량을 예약하는 경우 다른 시스템과 달리 처리량이 보장됩니다. Azure Cosmos DB는 보조 인덱스 및 글로벌 배포와 같이 고객이 요청할 수 있는 추가 기능을 제공합니다. 미리 보기 기간 중에는 처리량에 최적화된 모델을 제공하고 최종적으로는 저장소에 최적화된 모델을 제공해 고객의 요구 사항을 충족할 계획입니다. 

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-table-storage-with-the-table-api-preview-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Table Storage에 데이터를 수집할 때 파티션이 가득 찼음을 나타내는 "할당량 가득 참" 알림을 받은 적이 없는데, Table API(미리 보기)를 사용하면 이 메시지가 나타냅니다. 이 제품이 현재 사용자를 제한하거나 기존 응용 프로그램을 변경하도록 강제하나요?

Azure Cosmos DB는 SLA 기반 시스템으로, 대기 시간, 처리량, 가용성 및 일관성을 보장할 뿐만 아니라 무제한 확장을 제공합니다. 보장된 최고의 성능을 얻기 위해서는 데이터 크기 및 인덱스가 관리 및 확장 가능해야 합니다. 파티션 키당 엔터티 또는 항목 수에 대한 10GB 한도는 뛰어난 조회 및 쿼리 성능을 제공하도록 보장합니다. Azure Storage에 대해서도 응용 프로그램이 원활하게 확장할 수 있도록 하려면 단일 파티션에 대한 모든 정보를 저장 및 쿼리하여 핫 파티션을 만들지 *않는* 것이 좋습니다. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>그렇다면 새 Table API(미리 보기)에도 여전히 PartitionKey 및 RowKey가 필요한가요? 
예. Table API(미리 보기)의 노출 영역은 Table Storage SDK와 유사하기 때문에 파티션 키를 사용하면 데이터를 효율적으로 배포할 수 있습니다. 행 키는 파티션 내에서 고유합니다. 행 키는 반드시 존재해야 하며 표준 SDK에서와 같이 null일 수 없습니다. RowKey의 길이는 255바이트이고 PartitionKey의 길이는 100바이트입니다(곧 1KB로 늘어날 예정임). 

### <a name="what-are-the-error-messages-for-the-table-api-preview"></a>Table API(미리 보기)의 오류 메시지로는 어떤 것이 있나요?
이 미리 보기는 표준 테이블과 호환되므로 대부분의 오류는 표준 테이블의 오류에 매핑됩니다. 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api-preview"></a>Tables API(미리 보기)에서 많은 수의 테이블을 차례로 만들려고 하면 제한되는 이유는 무엇인가요?
Azure Cosmos DB는 SLA 기반 시스템으로, 대기 시간, 처리량, 가용성 및 일관성을 보장하며, 프로비전된 시스템이기 때문에 이러한 요구 사항을 보장하기 위해 리소스를 예약해 둡니다. 따라서 빠른 테이블 만들기 속도가 감지되고 제한됩니다. 테이블 만들기 속도를 살펴보고 분당 5 미만으로 낮추는 것이 좋습니다. 테이블 API(미리 보기)는 프로비전된 시스템입니다. 프로비전하는 순간 비용이 발생하기 시작합니다. 

## <a name="develop-against-the-graph-api-preview"></a>Graph API(미리 보기)에 대해 개발
### <a name="how-can-i-apply-the-functionality-of-graph-api-preview-to-azure-cosmos-db"></a>Azure Cosmos DB에 Graph API(미리 보기)의 기능을 적용하려면 어떻게 해야 하나요?
확장 라이브러리를 사용하여 Graph API(미리 보기) 기능을 적용할 수 있습니다. 이 라이브러리를 Microsoft Azure Graph라고 하며 NuGet에서 사용할 수 있습니다. 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>이는 Gremlin 그래프 통과 언어를 지원하는 것과 같습니다. 더 많은 형식의 쿼리를 추가할 계획이 있나요?
예, 향후에 쿼리를 위한 다른 메커니즘을 추가할 계획입니다. 

### <a name="how-can-i-use-the-new-graph-api-preview-offering"></a>새 Graph API(미리 보기) 기능은 어떻게 사용할 수 있나요? 
시작하려면 [Graph API](../cosmos-db/create-graph-dotnet.md) 빠른 시작 문서를 완료하세요.

<a id="moving-to-cosmos-db"></a>
## <a name="questions-from-documentdb-customers"></a>DocumentDB 고객의 질문
### <a name="why-are-you-moving-to-azure-cosmos-db"></a>Azure Cosmos DB로 전환하는 이유는 무엇인가요? 

Azure Cosmos DB는 대규모로 글로벌하게 배포된 클라우드 데이터베이스에서 다음으로 큰 도약입니다. 이제 DocumentDB 고객은 Azure Cosmos DB에서 제공하는 혁신적인 시스템 및 기능에 액세스할 수 있습니다.

Azure Cosmos DB는 2010년, Microsoft 내부에서 대규모 응용 프로그램을 구축 중인 개발자가 직면한 과제에 부응하기 위해 “Project Florence”로 시작되었습니다. 글로벌하게 배포되는 앱을 구축하는 문제는 Microsoft만의 문제가 아니므로 2015년, Azure 개발자에게 Azure DocumentDB라는 형태로 이 기술의 1세대를 제공했습니다. 

그 이후로, 새로운 기능이 추가되었으며 중요한 새 기능이 도입되었습니다. Azure Cosmos DB가 그 결과물입니다. 이 릴리스에서는 DocumentDB 고객(데이터 포함)이 자동으로 그리고 원활하게 Azure Cosmos DB 고객이 됩니다. 이러한 기능은 핵심 데이터베이스 엔진 및 글로벌 배포, 탄력적 확장성, 업계 최고의 포괄적인 SLA 분야에 관한 것입니다. 특히, 널리 사용되는 모든 데이터 모델, 형식 시스템 및 API를 Azure Cosmos DB의 기본 데이터 모델에 효과적으로 매핑할 수 있도록 Azure Cosmos DB 데이터베이스 엔진을 발전시켜 왔습니다. 

이러한 노력으로 현재 개발자에게 제공되는 것이 [Gremlin](../cosmos-db/graph-introduction.md) 및 [Table Storage API](../cosmos-db/table-introduction.md)에 대한 새로운 지원입니다. 그리고 이것은 시작일 뿐입니다. 시간이 지날수록 다른 인기있는 API 및 최신 데이터 모델을 추가하여 글로벌 규모로 성능 및 저장소를 더욱 발전시킬 예정입니다. 

DocumentDB의 [SQL 언어](../documentdb/documentdb-sql-query.md)는 기본 Azure Cosmos DB가 지원할 수 있는 많은 API 중 하나일 뿐이라는 점에 주목해야 합니다. Azure Cosmos DB와 같은 완전히 관리되는 서비스를 사용하는 개발자로서 서비스에 대한 유일한 인터페이스는 서비스에 노출된 API입니다. 기존 DocumentDB 고객에 대해 실제로 변경되는 사항은 아무 것도 없습니다. Azure Cosmos DB는 DocumentDB와 정확히 동일한 SQL API를 제공합니다. 또한 지금부터 이전에 액세스할 수 없었던 다른 기능에 액세스할 수 있습니다 

지속적인 노력의 또 다른 결과는 처리량 및 저장소의 전역적이고 탄력적인 확장성을 위한 기반 확장입니다. 확장성의 첫 번째 징후 중 하나는 [RU/m](../cosmos-db/request-units-per-minute.md)이지만 여러 워크로드에 대한 고객 비용을 절감하는 데 도움이 될 수 있는 추가 기능을 발표할 예정입니다. 글로벌 배포 하위 시스템에 몇 가지 기본적인 향상된 기능을 제공하였습니다. 많은 개발자에게 제공되는 기능 하나는 일관된 접두사 일관성 모델입니다(총 5가지의 잘 정의된 일관성 모델 제공). 기술이 발전하면서 더 많은 흥미로운 기능을 출시할 예정입니다. 

### <a name="what-do-i-need-to-do-to-ensure-that-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>내 DocumentDB 리소스가 Azure Cosmos DB에서 계속 실행되도록 하려면 어떻게 해야 하나요?

아무 것도 변경할 필요가 없습니다. DocumentDB 리소스는 이제 Azure Cosmos DB 리소스이며 전환되더라도 서비스 중단이 없습니다.

### <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>내 앱을 Azure Cosmos DB와 함께 작동하도록 하려면 무엇을 변경해야 하나요?

변경할 사항이 없습니다. 클래스, 네임스페이스 및 NuGet 패키지 이름은 변경되지 않습니다. 항상 그렇듯이, 최신 기능과 개선 사항을 활용하려면 SDK를 최신 상태로 유지하는 것이 좋습니다. 

### <a name="whats-changed-in-the-azure-portal"></a>Azure Portal에서 변경된 내용은 무엇인가요?

DocumentDB는 포털에 Azure 서비스로 더 이상 나타나지 않습니다. 대신, 다음 이미지에 나와 있는 것처럼 새 Azure Cosmos DB 아이콘이 표시됩니다. 모든 컬렉션은 이전과 마찬가지로 사용할 수 있으며 처리량을 조정하고 일관성 수준을 변경하며 SLA를 모니터링할 수 있습니다. 데이터 탐색기(미리 보기) 기능이 향상되었습니다. 이제 다음 이미지와 같이 한 페이지에서 문서를 보고 편집하고, 쿼리를 작성하고 실행하며 저장 프로시저, 트리거 및 UDF로 작업할 수 있습니다. 

![Azure Cosmos DB 컬렉션 블레이드](./media/faq/cosmos-db-data-explorer.png)

### <a name="are-there-changes-to-pricing"></a>가격 책정에 변경 사항이 있나요?

아니요, Azure Cosmos DB에서 앱 실행 비용은 이전과 동일합니다. 하지만 새로운 "분당 요청 단위" 기능을 활용할 수 있습니다. 자세한 내용은 [분당 처리량 조정](../cosmos-db/request-units-per-minute.md) 문서를 참조하세요.

### <a name="are-there-changes-to-the-slas"></a>SLA에 대한 변경 사항이 있나요?

아니요, 가용성, 일관성, 대기 시간 및 처리량에 대한 SLA는 변경되지 않으며 포털에 계속 표시됩니다. 자세한 내용은 [Azure Cosmos DB에 대한 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)를 참조하세요.
   
![샘플 데이터를 사용한 Todo 앱](./media/faq/azure-cosmosdb-portal-metrics-slas.png)


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md

