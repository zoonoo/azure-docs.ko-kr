---
title: Visual Studio App Center 및 Azure 서비스를 사용 하 여 클라우드에서 응용 프로그램 데이터 저장, 관리 및 유지
description: 클라우드에서 모바일 응용 프로그램 데이터를 저장, 관리 및 유지할 수 있는 Visual Studio App Center와 같은 서비스에 대해 알아봅니다.
author: elamalani
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 04de01e1fd3dba88145c7f3748ca30b31ab1740a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454457"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>클라우드에서 모바일 응용 프로그램 데이터 저장, 동기화 및 쿼리
작성 하는 응용 프로그램의 종류에 관계 없이 데이터를 생성 하 고 처리할 가능성이 높습니다. 응용 프로그램의 사용자는 기대 수준이 높습니다. 모든 상황에서 응용 프로그램이 신속 하 고 원활 하 게 작동 하도록 하려고 합니다. 대부분의 응용 프로그램은 여러 장치에서 작동 합니다. 데스크톱 또는 모바일 장치에서 응용 프로그램에 액세스할 수 있습니다. 여러 사용자가 동시에 응용 프로그램을 사용 하 고 데이터를 공유 하 여 데이터에 대 한 즉각적인 실시간 액세스를 얻을 수 있습니다.

응용 프로그램 사용자는 항상 인터넷에 연결 되어 있지 않습니다. 응용 프로그램은 인터넷 연결을 사용 하거나 사용 하지 않고 작동 하도록 설계 되 고 예상 됩니다. 개발자는 응용 프로그램에 뛰어난 고객 환경을 제공 하기 위해 데이터를 저장 하 고 클라우드에 동기화 하는 데 적합 한 솔루션을 선택 해야 합니다.

Microsoft에서 제공 하는 다양 한 서비스를 제공 하 여 서버를 실행 하거나, 데이터베이스를 선택 하거나, 규모 또는 보안을 걱정 하 여 가능한 한 풍부한 환경을 제공할 필요가 없습니다. 이러한 서비스는 SQL 또는 NoSQL Api를 사용 하 여 클라우드에 응용 프로그램 데이터를 저장할 수 있는 뛰어난 개발자 환경을 제공 합니다. 모든 장치에서 데이터를 동기화 하 고 응용 프로그램이 네트워크 연결을 사용 하거나 사용 하지 않고 작동 하 여 확장 가능 하 고 강력한 응용 프로그램을 빌드하는 데 도움이 될 수도 있습니다.

다음 서비스를 사용 하 여 클라우드에서 모바일 응용 프로그램 데이터를 관리 하 고 저장 합니다.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
데이터 [App Center](/appcenter/data/) 데이터 관리 서비스입니다. 이를 통해 응용 프로그램은 온라인 및 오프 라인 시나리오의 다양 한 장치 및 플랫폼에서 클라우드의 응용 프로그램 데이터를 관리, 유지 및 동기화 할 수 있습니다. Azure Cosmos DB을 기반으로 구축 된 App Center 데이터는 사용자 기반 및 응용 프로그램 수가 증가 함에 따라 확장 됩니다. 모든 데이터에 대해 짧은 대기 시간, 고가용성 및 높은 확장성을 보장 합니다.

**주요 기능**
- 새 Azure Cosmos DB 데이터베이스 또는 Visual Studio App Center 포털에서 기존 Azure Cosmos DB 데이터베이스로 쉽게 프로 비전 할 수 있습니다.
- 응용 프로그램 데이터를 쉽게 저장, 동기화 및 쿼리할 수 있는 NoSQL 데이터베이스 지원
- Azure Cosmos DB을 기반으로 구축 된이 서비스는 Azure Cosmos DB에서 제공 하는 주요 기능을 대부분 상속 하며 비즈니스 요구 사항에 맞게 전역적으로 확장할 수 있습니다.
- 장치 간에 데이터를 동기화 하는 온라인 및 오프 라인 동기화 기능.
- 개인 응용 프로그램 데이터를 쉽게 관리 하는 데 사용할 수 있는 모바일 클라이언트 Sdk입니다.
- IOS, Android, Xamarin 및 기본에 대 한 플랫폼 지원.

**참조**
- [Visual Studio App Center 등록](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center 데이터 시작](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)는 전 세계에 배포된 다중 모델 데이터베이스 서비스입니다. 이를 사용 하 여 전 세계 규모의 응용 프로그램을 빌드할 수 있습니다. Azure Cosmos DB를 사용 하면 전 세계 수많은 Azure 지역에서 처리량 및 저장소를 독립적으로 확장 하 고 탄력적으로 수 있습니다. 선호 하는 API 화면을 사용 하 여 빠르고 단일 자리 밀리초 데이터 액세스를 활용할 수 있습니다. 이러한 표면에는 SQL, MongoDB, Cassandra, Tables 또는 Gremlin가 포함 됩니다. Azure Cosmos DB는 처리량, 대기 시간, 가용성 및 일관성에 대 한 포괄적인 Sla (서비스 수준 계약)를 고유 하 게 제공 합니다.

**주요 기능**
- 는 SQL (핵심) API, Cassandra API, MongoDB API, Gremlin API 및 Table API를 포함 하는 광범위 한 Api를 지원 합니다.
- 턴키 전역 배포는 사용자가 어디에 있든 데이터를 복제 합니다. 사용자는 가장 가까운 데이터의 복제본과 상호 작용할 수 있습니다.
- 데이터베이스 엔진이 완전히 스키마를 알 수 없기 때문에 스키마 또는 인덱스를 관리 하지 않습니다.
- 전 세계의 모든 Azure 지역에서 Azure Cosmos DB를 사용할 수 있기 때문에 지역에서 사용할 수 있습니다. 여기에는 공용 클라우드의 54 + 지역이 포함 됩니다.
- 정확 하 게 정의 된 여러 일관성 선택은 잘 정의 된 5 가지 일관성 선택 항목을 제공 하도록 Azure Cosmos DB의 다중 마스터 복제 프로토콜을 신중 하 게 설계 했기 때문입니다. 강력, 제한 된 부실, 세션, 일관적인 접두사 및 최종 선택 항목을 선택할 수 있습니다.
- 읽기 및 쓰기 둘 다에 대해 99.999%의 가용성을 제공 합니다.
- 프로그래밍 방식으로 (또는 Azure Portal를 통해) Azure Cosmos DB 계정의 지역 장애 조치 (failover)를 호출 하 여 지역 재해를 견딜 수 있도록 응용 프로그램을 설계 합니다.
- 전 세계 99 번째 백분위 수의 짧은 대기 시간을 보장 합니다.

**참조**
- [Azure Portal](https://portal.azure.com) 
- [Azure Cosmos DB 설명서](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 은 범용 관계형 데이터베이스 관리 서비스입니다. 이를 사용 하 여 Azure 클라우드의 응용 프로그램 및 솔루션에 대 한 고가용성 및 고성능 데이터 저장소 계층을 만들 수 있습니다.

**주요 기능**
- **탄력적 데이터베이스 모델 및 도구:** 탄력적 데이터베이스를 사용 하면 개발자는 크기를 조정 하기 위해 데이터베이스 그룹 간에 리소스를 풀링할 수 있습니다. 이러한 리소스를 관리적으로 관리 하려면 스크립트를 작업으로 제출 합니다. 그러면 SQL 데이터베이스에서 데이터베이스에 대해 스크립트를 수행 합니다.
- **고성능:** 높은 처리량의 응용 프로그램은 최신 버전을 활용할 수 있습니다. 25% 더 프리미엄 데이터베이스 기능을 제공 합니다.
- **백업, 복제 및 고가용성:** 데이터베이스 수준에서 기본 제공 되는 복제 및 Microsoft 지원 SLA는 응용 프로그램 연속성을 제공 하 고 치명적 이벤트를 방지 합니다. 활성 지역 복제를 사용 하면 장애 조치 (failover) 및 셀프 서비스 복원을 구성 하 여 "복구"를 완벽 하 게 제어할 수 있습니다. 데이터 복원은 최대 35 일 동안의 데이터 백업에서 사용할 수 있습니다.
- **0에 가까운 유지 관리:** 자동 소프트웨어는 서비스의 일부입니다. 기본 제공 시스템 복제본은 기본적인 데이터 보호, 데이터베이스 작동 시간 및 시스템 안정성을 제공 하는 데 도움이 됩니다. 시스템 복제본은 새 컴퓨터로 자동으로 이동 됩니다. 이전에 실패 하는 즉시 프로 비전 됩니다.
- **보안:** Azure SQL Database는 조직 또는 업계 규정 준수 정책을 충족 하는 보안 기능 포트폴리오를 제공 합니다.
    - 감사는 개발자에 게 규정 준수 관련 작업을 수행 하 고 작업에 대 한 정보를 얻을 수 있는 기능을 제공 합니다.
    - 개발자와 IT는 데이터베이스 수준에서 정책을 구현 하 여 행 수준 보안, 동적 데이터 마스킹 및 Azure SQL Database에 대 한 투명 한 데이터 암호화를 통해 중요 한 데이터에 대 한 액세스를 제한할 수 있습니다.
    - Azure SQL Database은 HIPAA BAA, ISO/IEC 27001:2005, FedRAMP 및 EU Model 절과 같은 주요 Azure 준수 인증 및 승인 범위의 일부로 주요 클라우드 감사자에 의해 확인 됩니다.

**참조**
- [Azure Portal](https://portal.azure.com) 
- [Azure SQL Database 설명서](/azure/sql-database/) 
