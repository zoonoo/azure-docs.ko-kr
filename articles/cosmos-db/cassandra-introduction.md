---
title: Azure Cosmos DB Cassandra API 소개 | Microsoft Docs
description: Azure Cosmos DB를 사용하여 기존 응용 프로그램을 “리프트 앤 시프트”하고, 이미 익숙한 Cassandra 드라이버와 CQL을 사용하여 Cassandra API를 사용하는 새 응용 프로그램을 빌드하는 방법을 알아봅니다.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: na
ms.topic: overview
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: fe0ed06c5ecd0a84e9999c09cd312ddea2557f80
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221658"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API 소개

Azure Cosmos DB Cassandra API는 [Apache Cassandra](https://cassandra.apache.org/)용으로 작성된 앱의 데이터 저장소로 사용할 수 있습니다. 이는 CQLv4와 호환되는 기존의 [Apache 드라이버](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)를 사용하면 기존의 Cassandra 응용 프로그램이 이제는 Azure Cosmos DB Cassandra API와 통신할 수 있음을 의미합니다. 많은 경우 연결 문자열을 변경하는 것만으로 Apache Cassandra를 사용하는 것에서 Azure Cosmos DB의 Cassandra API를 사용하는 것으로 전환할 수 있습니다. 

Cassandra API를 사용하면 이미 익숙한 CQL(Cassandra 쿼리 언어), Cassandra 기반 도구(예: cqlsh) 및 Cassandra 클라이언트 드라이버를 사용하여 Azure Cosmos DB에 저장된 데이터와 상호 작용할 수 있습니다.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Azure Cosmos DB용 Apache Cassandra API를 사용하는 이점은 무엇인가요?

**운영 관리 없음**: 완전히 관리되는 클라우드 서비스인 Azure Cosmos DB Cassandra API는 OS, JVM, yaml 파일 및 해당 상호 연동에서 무수한 설정을 관리하고 모니터링하는 것에 대한 오버헤드를 없애줍니다. Azure Cosmos DB는 처리량, 대기 시간, 저장소, 가용성 및 구성 가능한 경고에 대한 모니터링을 제공합니다.

**성능 관리**: Azure Cosmos DB는 백분위수 99에서 짧은 대기 시간의 읽기 및 쓰기를 제공하며 SLA로 이를 지원합니다. 사용자는 고성능 및 짧은 대기 시간의 읽기 및 쓰기를 보장하기 위해 운영 오버 헤드를 걱정할 필요가 없습니다. 즉, 사용자는 압축 예약, 삭제 표식 관리, bloom 필터 및 복제본 설정을 수동으로 처리할 필요가 없습니다. Azure Cosmos DB는 이러한 문제를 관리하는 오버헤드를 없애주므로 사용자는 응용 프로그램 논리에 집중할 수 있습니다.

**기존 코드 및 도구 사용 가능**: Azure Cosmos DB는 기존 Cassandra SDK 및 도구와 유선 프로토콜 수준의 호환성을 제공합니다. 이 호환성을 통해 간단한 변경 사항이 있는 Azure Cosmos DB Cassandra API와 함께 기존 코드베이스를 사용할 수 있습니다.

**처리량 및 저장소 탄력성**: Azure Cosmos DB는 Azure Portal, PowerShell 또는 CLI 작업을 통해 지역 전체에서 보장된 처리량을 제공하여 프로비전된 처리량을 크기 조정할 수 있습니다. 예측 가능한 성능으로 필요에 따라 테이블에 대한 처리량 및 저장소를 탄력적으로 확장할 수 있습니다.

**글로벌 배포 및 가용성**: Azure Cosmos DB는 전 세계 모든 Azure 지역에서 데이터를 배포하고 대기 시간이 짧은 데이터 액세스 및 높은 가용성을 보장하면서 데이터를 로컬로 제공하는 기능을 제공합니다. Azure Cosmos DB는 한 지역 내에서는 99.99%의 가용성을 제공하고 여러 지역에 걸쳐서는 작업 오버헤드 없이 99.999%의 읽기 및 쓰기 가용성을 제공합니다. [데이터를 글로벌 배포](distribute-data-globally.md) 문서에서 자세히 알아보세요. 

**일관성 선택**: Azure Cosmos DB는 일관성과 성능 간에 최적의 절충을 실현할 수 있도록 잘 정의된 5가지 일관성 수준 옵션을 제공합니다. 이러한 일관성 수준은 강력, 제한된 부실, 세션, 일관된 접두사 및 최종입니다. 이러한 잘 정의된 실용적이고 직관적인 일관성 수준을 통해 개발자는 일관성, 가용성 및 대기 시간 간에 타당한 절충을 수행할 수 있습니다. [일관성 수준](consistency-levels.md) 문서에서 자세히 알아보세요. 

**엔터프라이즈급**: Azure Cosmos DB는 사용자가 플랫폼을 안전하게 사용할 수 있도록 [준수 인증](https://www.microsoft.com/trustcenter)을 제공합니다. Azure Cosmos DB는 또한 휴식 및 작동 시 암호화, IP 방화벽, 제어 영역 활동에 대한 감사 로그를 제공합니다.

## <a name="next-steps"></a>다음 단계

* Cassandra API 데이터를 만들고 관리하기 위한 다음 언어별 앱 빌드를 빠르게 시작할 수 있습니다.
  - [Node.js 앱](create-cassandra-nodejs.md)
  - [.NET 앱](create-cassandra-dotnet.md)
  - [Python 앱](create-cassandra-python.md)

* Java 응용 프로그램을 사용하여 [Cassandra API 계정, 데이터베이스 및 테이블 만들기](create-cassandra-api-account-java.md)를 시작합니다.

* Java 응용 프로그램을 사용하여 [샘플 데이터를 Cassandra API 테이블에 로드](cassandra-api-load-data.md)합니다.

* Java 응용 프로그램을 사용하여 [Cassandra API 계정에서 데이터를 쿼리](cassandra-api-query-data.md)합니다.

* Azure Cosmos DB Cassandra API에서 지원하는 Apache Cassandra 기능에 대해 알아보려면 [Cassandra 지원](cassandra-support.md) 문서를 참조하세요.

* [질문과 대답](faq.md#cassandra)을 읽어보세요.
