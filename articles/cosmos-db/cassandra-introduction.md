---
title: Azure Cosmos DB Cassandra API 소개
description: Azure Cosmos DB를 사용하여 기존 애플리케이션을 “리프트 앤 시프트”하고, Cassandra 드라이버와 CQL을 사용하여 새 애플리케이션을 빌드하는 방법을 알아봅니다.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 05/21/2019
ms.openlocfilehash: 70dbceb51ed030124d1b793d77c6bc287da91065
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "81687631"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API 소개

Azure Cosmos DB Cassandra API는 [Apache Cassandra](https://cassandra.apache.org)용으로 작성된 앱의 데이터 저장소로 사용할 수 있습니다. 이는 CQLv4와 호환되는 기존의 [Apache 드라이버](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)를 사용하면 기존의 Cassandra 애플리케이션이 이제는 Azure Cosmos DB Cassandra API와 통신할 수 있음을 의미합니다. 대부분의 경우 연결 문자열을 변경하기만 하면 Apache Cassandra 사용에서 Azure Cosmos DB의 Cassandra API 사용으로 전환할 수 있습니다. 

Cassandra API를 사용하면 이미 익숙한 CQL(Cassandra 쿼리 언어), Cassandra 기반 도구(예: cqlsh) 및 Cassandra 클라이언트 드라이버를 사용하여 Azure Cosmos DB에 저장된 데이터와 상호 작용할 수 있습니다.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Azure Cosmos DB용 Apache Cassandra API를 사용하는 이점은 무엇인가요?

**운영 관리 없음**: 완전히 관리되는 클라우드 서비스인 Azure Cosmos DB Cassandra API는 OS, JVM, yaml 파일 및 해당 상호 연동에서 무수한 설정을 관리하고 모니터링하는 것에 대한 오버헤드를 없애줍니다. Azure Cosmos DB는 처리량, 대기 시간, 스토리지, 가용성 및 구성 가능한 경고에 대한 모니터링을 제공합니다.

**오픈 소스 표준**: 완전 관리형 서비스임에도 불구하고 Cassandra API는 여전히 기본 [Apache Cassandra 유선 프로토콜](cassandra-support.md)의 큰 노출 영역을 지원하므로 널리 사용되는 클라우드에 독립적인 오픈 소스 표준에 맞게 애플리케이션을 빌드할 수 있습니다.

**성능 관리**: Azure Cosmos DB는 백분위수 99에서 짧은 대기 시간의 읽기 및 쓰기를 제공하며 SLA로 이를 지원합니다. 사용자는 고성능 및 짧은 대기 시간의 읽기 및 쓰기를 보장하기 위해 운영 오버 헤드를 걱정할 필요가 없습니다. 즉, 사용자는 압축 예약, 삭제 표식 관리, bloom 필터 및 복제본 설정을 수동으로 처리할 필요가 없습니다. Azure Cosmos DB는 이러한 문제를 관리하는 오버헤드를 없애주므로 사용자는 애플리케이션 논리에 집중할 수 있습니다.

**기존 코드 및 도구 사용 가능**: Azure Cosmos DB는 기존 Cassandra SDK 및 도구와 유선 프로토콜 수준의 호환성을 제공합니다. 이 호환성을 통해 간단한 변경 사항이 있는 Azure Cosmos DB Cassandra API와 함께 기존 코드베이스를 사용할 수 있습니다.

**처리량 및 스토리지 탄력성**: Azure Cosmos DB는 Azure Portal, PowerShell 또는 CLI 작업을 통해 지역 전체에서 보장된 처리량을 제공하여 프로비전된 처리량을 크기 조정할 수 있습니다. 예측 가능한 성능으로 필요에 따라 테이블에 대한 처리량 및 스토리지를 [탄력적으로 확장](manage-scale-cassandra.md)할 수 있습니다.

**글로벌 배포 및 가용성**: Azure Cosmos DB는 전 세계 모든 Azure 지역에서 데이터를 전역적으로 배포하고 대기 시간이 짧은 데이터 액세스 및 높은 가용성을 보장하면서 데이터를 로컬로 제공하는 기능을 제공합니다. Azure Cosmos DB는 한 지역 내에서는 99.99%의 가용성을 제공하고 여러 지역에 걸쳐서는 작업 오버헤드 없이 99.999%의 읽기 및 쓰기 가용성을 제공합니다. [데이터를 글로벌 배포](distribute-data-globally.md) 문서에서 자세히 알아보세요. 

**일관성 옵션**: Azure Cosmos DB는 일관성과 성능 간에 최적의 균형을 유지할 수 있도록 잘 정의된 5가지 일관성 수준 옵션을 제공합니다. 이러한 일관성 수준은 강력, 제한된 부실, 세션, 일관된 접두사 및 최종입니다. 이러한 잘 정의된 실용적이고 직관적인 일관성 수준을 통해 개발자는 일관성, 가용성 및 대기 시간 간에 타당한 절충을 수행할 수 있습니다. [일관성 수준](consistency-levels.md) 문서에서 자세히 알아보세요. 

**엔터프라이즈급**: Azure Cosmos DB는 사용자가 플랫폼을 안전하게 사용할 수 있도록 [규정 준수 인증](https://www.microsoft.com/trustcenter)을 제공합니다. Azure Cosmos DB는 저장 데이터 암호화 및 작동 시 암호화, IP 방화벽, 제어 영역 활동에 대한 감사 로그를 제공합니다.

**이벤트 소싱**: Cassandra API는 영구적 변경 로그인 [변경 피드](cassandra-change-feed.md)에 대한 액세스 권한을 제공합니다. 이는 데이터베이스에서 직접 이벤트 소싱을 용이하게 할 수 있습니다. Apache Cassandra에서 유일하게 동등한 것은 CDC(변경 데이터 캡처)입니다. 이는 CDC 로그에 대해 구성 가능한 디스크 크기에 도달하면 해당 테이블에 대한 쓰기를 거부할 뿐만 아니라 보관용으로 특정 테이블에 플래그를 지정하는 메커니즘일 뿐입니다(관련 측면이 자동으로 관리되기 때문에 Cosmos DB에서 이러한 기능이 중복됨).

## <a name="next-steps"></a>다음 단계

* Cassandra API 데이터를 만들고 관리하기 위한 다음 언어별 앱 빌드를 빠르게 시작할 수 있습니다.
  - [Node.js 앱](create-cassandra-nodejs.md)
  - [.NET 앱](create-cassandra-dotnet.md)
  - [Python 앱](create-cassandra-python.md)

* Java 애플리케이션을 사용하여 [Cassandra API 계정, 데이터베이스 및 테이블 만들기](create-cassandra-api-account-java.md)를 시작합니다.

* Java 애플리케이션을 사용하여 [샘플 데이터를 Cassandra API 테이블에 로드](cassandra-api-load-data.md)합니다.

* Java 애플리케이션을 사용하여 [Cassandra API 계정에서 데이터를 쿼리](cassandra-api-query-data.md)합니다.

* Azure Cosmos DB Cassandra API에서 지원하는 Apache Cassandra 기능에 대해 알아보려면 [Cassandra 지원](cassandra-support.md) 문서를 참조하세요.

* [질문과 대답](cassandra-faq.md)을 읽어보세요.
