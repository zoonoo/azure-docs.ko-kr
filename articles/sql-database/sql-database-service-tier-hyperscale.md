---
title: Azure SQL Database 하이퍼스케일 개요 | Microsoft Docs
description: 이 문서에서는 Azure SQL Database의 vCore 기반 구매 모델에 포함된 하이퍼스케일 서비스 계층에 대해 설명하고 범용 및 중요 비즈니스용 서비스 계층과의 차이점을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: efb6cd1a45ac14dcbd5b2b6d8e70f5ee096ddbd8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255835"
---
# <a name="hyperscale-service-tier"></a>하이퍼스케일 서비스 계층

Azure SQL Database는 인프라 오류의 경우에도 99.99%의 가용성을 보장하기 위해 클라우드 환경에 대해 조정되는 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. Azure SQL Database에 사용되는 세 가지 아키텍처 모델이 있습니다.
- 범용/표준 
-  하이퍼스케일
-  중요 비즈니스용/프리미엄

Azure SQL Database의 하이퍼스케일 서비스 계층은 vCore 기반 구매 모델의 최신 서비스 계층입니다. 이 서비스 계층은 Azure 아키텍처를 활용하여 범용 및 중요 비즈니스용 서비스 계층에 사용할 수 있는 제한을 초과하여 Azure SQL Database용 스토리지 및 컴퓨팅 리소스를 확장하는 확장성이 뛰어난 스토리지 및 컴퓨팅 성능 계층입니다.

> 
> [!NOTE]
> vCore 기반 구매 모델의 범용 및 중요 비즈니스용 서비스 계층에 대한 자세한 내용은 [범용](sql-database-service-tier-general-purpose.md) 및 [중요 비즈니스용](sql-database-service-tier-business-critical.md) 서비스 계층을 참조하세요. vCore 기반 구매 모델과 DTU 기반 구매 모델의 비교는 [Azure SQL Database 구매 모델 및 리소스](sql-database-service-tiers.md)를 참조하세요.


## <a name="what-are-the-hyperscale-capabilities"></a>하이퍼스케일 기능에는 무엇이 있나요?

Azure SQL Database의 하이퍼스케일 서비스 계층은 다음과 같은 추가 기능을 제공합니다.

- 최대 100TB의 데이터베이스 크기 지원
- 계산 리소스에 대 한 IO 영향이 없는 크기와 관계 없이 Azure Blob storage에 저장 된 파일 스냅숏을 기반으로 하는 거의 즉각적인 데이터베이스 백업  
- 몇 시간 또는 며칠이 아닌 몇 분 내에(데이터베이스 작업의 규모가 아닌) 빠른 데이터베이스 복원(파일 스냅샷 기반)
- 데이터 볼륨에 관계없이 더 높은 로그 처리량 및 더 빠른 트랜잭션 커밋 시간이 보장되므로 전반적인 성능 개선
- 빠른 스케일 아웃 - 읽기 워크로드를 오프로드하고 핫 대기로 사용하기 위해 하나 이상의 읽기 전용 노드를 프로비전할 수 있습니다.
- 빠른 강화 - 필요할 때 일정한 시간에 컴퓨팅 리소스 규모를 확대하여 과도한 워크로드를 수용하고, 필요 없을 때 컴퓨팅 리소스 규모를 다시 축소할 수 있습니다.

하이퍼스케일 서비스 계층은 클라우드 데이터베이스에서 기존에 확인되던 많은 실제 제한을 없애줍니다. 대부분의 다른 데이터베이스가 단일 노드에서 사용할 수 있는 리소스로 제한되지만 하이퍼스케일 서비스 계층의 데이터베이스에는 이러한 제한이 없습니다. 스토리지 아키텍처가 유연하기 때문에 필요에 따라 스토리지가 증가합니다. 실제로 하이퍼스케일 데이터베이스는 최대 크기가 정의된 상태로 생성되지 않습니다. 하이퍼스케일 데이터베이스는 필요에 따라 증가하므로 사용하는 용량에 대해서만 요금이 청구됩니다. 읽기 집약적 워크로드의 경우 하이퍼스케일 서비스 계층에서 읽기 워크로드를 오프로드하는 데 필요한 추가 읽기 복제본을 프로비전하여 신속한 스케일 아웃을 제공합니다.

또한 데이터베이스 백업을 만들거나 규모 확대 또는 축소에 필요한 시간이 더 이상 데이터베이스의 데이터 볼륨과 관련되지 않습니다. 하이퍼스케일 데이터베이스는 거의 동시에 백업할 수 있습니다. 몇 분 안에 수십 테라바이트의 데이터베이스 규모를 확대 또는 축소할 수도 있습니다. 이 기능은 초기 구성 선택에 따른 여러 가지 우려를 해소해줍니다.

하이퍼스케일 서비스 계층의 컴퓨팅 크기에 대한 자세한 내용은 [서비스 계층 특성](sql-database-service-tiers-vcore.md#service-tiers)을 참조하세요.

## <a name="who-should-consider-the-hyperscale-service-tier"></a>하이퍼스케일 서비스 계층을 고려하면 좋은 대상

하이퍼 규모 서비스 계층은 독립적으로 확장 가능한 계산 및 저장소 리소스를 통해 뛰어난 유연성과 고성능을 제공 하므로 대부분의 비즈니스 워크 로드에 적합 합니다. 최대 100 TB의 저장소를 자동 크기 조정 하는 기능을 사용 하 여 다음을 수행 하는 고객에 게 적합 합니다.

- 대량 데이터베이스를 온-프레미스로 설정 하 고 클라우드로 이동 하 여 응용 프로그램을 현대화 합니다.
- 이미 클라우드에 있고 다른 서비스 계층의 최대 데이터베이스 크기 제한 (1-4 TB)에 의해 제한 됨
- 데이터베이스 크기가 작으므로 빠른 수직 및 수평 계산 크기 조정, 고성능, 즉시 백업 및 빠른 데이터베이스 복원이 필요 합니다.

하이퍼 규모 서비스 계층은 순수 OLTP에서 순수 분석까지 광범위 한 SQL Server 워크 로드를 지원 하지만 주로 OLTP 및 HTAP (하이브리드 트랜잭션 및 분석 처리) 워크 로드에 최적화 되어 있습니다.

> [!IMPORTANT]
> 탄력적 풀은 하이퍼스케일 서비스 계층을 지원하지 않습니다.

## <a name="hyperscale-pricing-model"></a>하이퍼스케일 가격 책정 모델

하이퍼스케일 서비스 계층은 [vCore 모델](sql-database-service-tiers-vcore.md)에만 사용할 수 있습니다. 새 아키텍처에 맞게 가격 책정 모델이 범용 또는 중요 비즈니스용 서비스 계층과 약간 다릅니다.

- **컴퓨팅**:

  하이퍼스케일 컴퓨팅 단위 가격은 복제본별로 정해집니다. [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 가격은 읽기 확장 복제본에 자동으로 적용됩니다. 기본적으로 기본 복제본과 하이퍼 크기 조정 데이터베이스당 하나의 읽기 전용 복제본을 만듭니다.  사용자가 1-5의 주 복제본을 포함 하 여 총 복제본 수를 조정할 수 있습니다.

- **스토리지**:

  하이퍼스케일 데이터베이스를 구성하는 경우 최대 데이터 크기를 지정할 필요가 없습니다. 대규모 계층에서는 실제 할당을 기반으로 데이터베이스에 대 한 저장소 요금이 청구 됩니다. 저장소는 40 기가바이트와 100 TB 사이에서 자동으로 할당 되며 10gb로 증가 합니다. 필요한 경우 여러 데이터 파일을 동시에 확장할 수 있습니다. 하이퍼 규모의 데이터베이스는 10gb의 시작 크기를 사용 하 여 생성 되며, 40 GB의 크기에 도달할 때까지 10 분 마다 10gb의 증가를 시작 합니다.

하이퍼스케일 가격 책정에 대한 자세한 내용은 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/)을 참조하세요.

## <a name="distributed-functions-architecture"></a>분산 함수 아키텍처

모든 데이터 관리 기능을 하나의 위치/프로세스에 중앙 집중식으로 배치하는 기존 데이터베이스 엔진과 달리(소위 말하는 프로덕션의 분산 데이터베이스에도 모놀리식 데이터 엔진의 여러 복사본이 있음), 하이퍼스케일 데이터베이스는 여러 데이터 엔진의 의미 체계가 구분되어 있는 쿼리 처리 엔진을 데이터에 대한 장기 스토리지 및 지속성을 제공하는 구성 요소와 구분합니다. 이러한 방식으로 스토리지 용량을 필요한 만큼 원활하게 스케일 아웃할 수 있습니다(초기 목표는 100TB). 읽기 전용 복제본은 동일한 저장소 구성 요소를 공유 하므로 읽기 가능한 새 복제본을 실행 하기 위해 데이터를 복사할 필요가 없습니다. 

다음 다이어그램은 하이퍼스케일 데이터베이스에 있는 여러 유형의 노드를 보여 줍니다.

![아키텍처](./media/sql-database-hyperscale/hyperscale-architecture.png)

Hyperscale 데이터베이스에는 다음과 같은 다양 한 유형의 구성 요소가 포함 됩니다.

### <a name="compute"></a>컴퓨팅

컴퓨팅 노드는 관계형 엔진이 존재하는 위치이므로 모든 언어 요소, 쿼리 처리 등이 발생합니다. 하이퍼스케일 데이터베이스와의 모든 사용자 상호 작용은 이러한 컴퓨팅 노드를 통해 발생합니다. 컴퓨팅 노드에는 데이터 페이지를 가져오는 데 필요한 네트워크 왕복 횟수를 최소화하기 위해 SSD 기반 캐시[이전 다이어그램에 나오는 RBPEX(Resilient Buffer Pool Extension)]가 있습니다. 모든 읽기/쓰기 워크로드 및 트랜잭션이 처리되는 기본 컴퓨팅 노드가 1개 있습니다. 장애 조치(Failover)를 위해 핫 대기 노드의 역할을 할 뿐만 아니라 읽기 워크로드를 오프로드하기 위한 읽기 전용 컴퓨팅 노드(이 기능이 필요한 경우)의 역할을 하는 하나 이상의 보조 컴퓨팅 노드도 있습니다.

### <a name="page-server"></a>페이지 서버

페이지 서버는 스케일 아웃된 스토리지 엔진을 나타내는 시스템입니다.  각 페이지 서버는 데이터베이스의 페이지 하위 집합을 담당합니다.  명목상 각 페이지 서버는 128 GB와 1tb의 데이터를 제어 합니다. 데이터는 둘 이상의 (중복 및 가용성을 위해 유지되는 복제본 외부) 페이지 서버에서 공유되지 않습니다. 페이지 서버의 업무는 요청 시 컴퓨팅 노드에 데이터베이스 페이지를 제공하고, 트랜잭션으로 인해 데이터가 업데이트될 때 페이지를 최신 상태로 유지하는 것입니다. 페이지 서버는 로그 서비스의 로그 레코드를 재생하여 최신 상태로 유지됩니다. 또한 페이지 서버는 성능 향상을 위해 SSD 기반 캐시를 유지 관리합니다. 데이터 페이지의 장기 스토리지는 추가 안정성을 위해 Azure Storage에 보관됩니다.

### <a name="log-service"></a>로그 서비스

로그 서비스는 기본 계산 복제본의 로그 레코드를 수락 하 고, 영구 캐시에 보관 하 고, 로그 레코드를 나머지 계산 복제본으로 전달 하 여 (해당 캐시를 업데이트할 수 있도록) 관련 페이지 서버 뿐만 아니라 데이터를 업데이트할 수 있도록 합니다. 있으면. 이러한 방식으로 기본 계산 복제본의 모든 데이터 변경 내용은 모든 보조 계산 복제본 및 페이지 서버로 로그 서비스를 통해 전파 됩니다. 마지막으로 로그 레코드는 거의 무한 한 저장소 저장소 인 Azure Storage의 장기 저장소로 푸시됩니다. 이 메커니즘을 통해 로그 잘림을 자주 수행 하지 않아도 됩니다. 로그 서비스에는 로그 레코드에 대 한 액세스를 가속화 하는 로컬 캐시도 있습니다.

### <a name="azure-storage"></a>Azure Storage

Azure Storage는 데이터베이스의 모든 데이터 파일을 포함 합니다. 페이지 서버는 Azure Storage의 데이터 파일을 최신 상태로 유지 합니다. 이 저장소는 백업 용도로 사용 되며 Azure 지역 간의 복제에도 사용 됩니다. 백업은 데이터 파일의 저장소 스냅숏을 사용 하 여 구현 됩니다. 스냅숏을 사용한 복원 작업은 데이터 크기에 관계 없이 빠르게 수행 됩니다. 데이터베이스의 백업 보존 기간 내의 특정 시점으로 데이터를 복원할 수 있습니다.

## <a name="backup-and-restore"></a>백업 및 복원

백업은 파일-스냅숏 기반 이므로 거의 즉각적입니다. 저장소 및 계산 분리를 사용 하면 백업/복원 작업을 저장소 계층에 푸시하여 기본 계산 복제본의 처리 부담을 줄일 수 있습니다. 따라서 데이터베이스 백업은 기본 계산 노드의 성능에 영향을 주지 않습니다. 마찬가지로 복원 작업은 파일 스냅숏으로 되돌리고 데이터 작업의 크기와는 다른 방식으로 수행 됩니다. 복원은 일정 시간 작업이 며 몇 시간 또는 며칠이 아닌 몇 분만에 여러 테라바이트 데이터베이스를 복원할 수 있습니다. 기존 백업을 복원 하 여 새 데이터베이스를 만들면이 기능이 활용 됩니다. 또한 1tb 크기의 데이터베이스를 포함 하 여 개발 또는 테스트 목적으로 데이터베이스 복사본을 만드는 작업은 몇 분 안에 심지어 됩니다.

## <a name="scale-and-performance-advantages"></a>확장 및 성능상의 이점

추가 읽기 전용 컴퓨팅 노드를 신속하게 스핀업/스핀다운하는 기능을 사용하여 하이퍼스케일 아키텍처는 상당한 읽기 기능을 허용하며 더 많은 쓰기 요청을 제공하기 위해 기본 컴퓨팅 노드를 해제할 수도 있습니다. 또한 하이퍼스케일 아키텍처의 공유 스토리지 아키텍처로 인해 컴퓨팅 노드 규모를 빠르게 확대/축소할 수 있습니다.

## <a name="create-a-hyperscale-database"></a>Hyperscale 데이터베이스 만들기

[Azure Portal](https://portal.azure.com), [t-sql](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) 또는 [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)를 사용 하 여 hyperscale 데이터베이스를 만들 수 있습니다. Hyperscale 데이터베이스는 [Vcore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 사용 하는 경우에만 사용할 수 있습니다.

다음 T-SQL 명령은 하이퍼스케일 데이터베이스를 생성합니다. `CREATE DATABASE` 문에 버전 및 서비스 목표를 둘 다 지정해야 합니다. 유효한 서비스 목표 목록은 [리소스 제한을](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4) 참조 하세요.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
그러면 4 개 코어를 사용 하는 Gen5 하드웨어에서 하이퍼 확장 데이터베이스를 만듭니다.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>기존 Azure SQL Database를 하이퍼스케일 서비스 계층으로 마이그레이션

[Azure Portal](https://portal.azure.com), [t-sql](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) 또는 [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)를 사용 하 여 기존 Azure SQL database를 hyperscale으로 이동할 수 있습니다. 이번에는 단방향 마이그레이션입니다. 데이터를 내보내고 가져오는 것 외에는 Hyperscale에서 다른 서비스 계층으로 데이터베이스를 이동할 수 없습니다. POCs (개념 증명)의 경우 프로덕션 데이터베이스의 복사본을 만들고 복사본을 Hyperscale으로 마이그레이션하는 것이 좋습니다. 기존 Azure SQL database를 Hyperscale 계층으로 마이그레이션하는 작업은 데이터 작업의 크기입니다.

다음 T-SQL 명령은 하이퍼스케일 서비스 계층으로 데이터베이스를 이동합니다. `ALTER DATABASE` 문에 버전 및 서비스 목표를 둘 다 지정해야 합니다.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>하이퍼스케일 데이터베이스의 읽기 확장 복제본에 연결

Hyperscale 데이터베이스에서 클라이언트가 제공 하는 연결 문자열의 `ApplicationIntent` 인수는 연결이 쓰기 복제본 또는 읽기 전용 보조 복제본으로 라우팅되도록 할지를 결정 합니다. `ApplicationIntent`가 `READONLY`로 설정되고 데이터베이스에 보조 복제본이 없는 경우에는 연결이 주 복제본으로 라우팅되고 기본값은 `ReadWrite` 동작으로 설정됩니다.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

하이퍼 확장 보조 복제본은 주 복제본과 동일한 서비스 수준 목표를 사용 하는 모두 동일 합니다. 보조 복제본이 두 개 이상 있는 경우 해당 작업은 사용 가능한 모든 보조 복제본에 분산 됩니다. 각 보조 복제본은 독립적으로 업데이트 되므로 다른 복제본에는 주 복제본에 비해 서로 다른 데이터 대기 시간이 있을 수 있습니다.

## <a name="database-high-availability-in-hyperscale"></a>Hyperscale의 데이터베이스 고가용성

다른 모든 서비스 계층과 마찬가지로, Hyperscale은 계산 복제본 가용성에 관계 없이 커밋된 트랜잭션에 대 한 데이터 내구성을 보장 합니다. 주 복제본이 사용할 수 없게 될 때 발생 하는 가동 중지 시간은 장애 조치 (failover) 유형 (계획 된 계획 또는 계획 되지 않음) 및 하나 이상의 보조 복제본이 있는지 여부에 따라 달라 집니다. 계획 된 장애 조치 (failover) (예: 유지 관리 이벤트)에서 시스템은 장애 조치 (failover)를 시작 하기 전에 새 주 복제본을 만들거나 기존 보조 복제본을 장애 조치 (failover) 대상으로 사용 합니다. 계획 되지 않은 장애 조치 (failover) (즉, 주 복제본의 하드웨어 오류)에서 시스템은 보조 복제본을 장애 조치 (failover) 대상으로 사용 하 고 (있는 경우) 사용 가능한 계산 용량의 풀에서 새 주 복제본을 만듭니다. 후자의 경우 새 주 복제본을 만드는 데 필요한 추가 단계 때문에 가동 중지 시간이 길어집니다.

하이퍼 확장 SLA는 [Azure SQL Database에 대 한 sla](https://azure.microsoft.com/support/legal/sla/sql-database/)를 참조 하세요.

## <a name="disaster-recovery-for-hyperscale-databases"></a>Hyperscale 데이터베이스에 대 한 재해 복구

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>다른 지리로 Hyperscale 데이터베이스 복원
Azure SQL Database Hyperscale DB를 현재 호스트 되는 지역이 아닌 다른 지역으로 복원 해야 하는 경우 재해 복구 작업 또는 드릴, 재배치 또는 기타 이유로 인해 주 방법은 데이터베이스의 지역 복원을 수행 하는 것입니다.  이 작업에는 다른 AZURE SQL DB를 다른 지역으로 복원 하는 데 사용 하는 것과 정확히 같은 단계가 포함 됩니다.
1. 적절 한 서버가 아직 없는 경우 대상 지역에 SQL Database 서버를 만듭니다.  이 서버는 원본 서버와 동일한 구독에서 소유 해야 합니다.
2. 자동 백업에서 Azure SQL 데이터베이스를 복원 하는 방법에 대 한 페이지의 [지역 복원](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) 항목의 지침을 따르세요.

> [!NOTE]
> 원본 및 대상이 별도의 지역에 있기 때문에 데이터베이스는 원본 데이터베이스와 함께 스냅숏 저장소를 원본 데이터베이스와 공유할 수 없습니다 .이는 비-지역 복원에서 매우 신속 하 게 완료 됩니다. 하이퍼 규모의 데이터베이스에 대 한 지역 복원의 경우 대상이 지역에서 복제 된 저장소의 쌍을 이루는 지역에 있는 경우에도 데이터 크기 조정 작업이 수행 됩니다.  즉, 지역 복원 작업을 수행 하는 경우 복원 되는 데이터베이스의 크기에 비례하여 시간이 소요 됩니다.  대상이 쌍을 이루는 지역에 있는 경우 복사본은 지역 내에 있으며,이는 지역 간 복사 보다 훨씬 빠르지만 여전히 데이터의 크기를 조정 하는 작업입니다.

## <a name=regions></a>사용 가능한 지역

Azure SQL Database Hyperscale 계층은 현재 다음 지역에서 사용할 수 있습니다.

- 오스트레일리아 동부
- 오스트레일리아 남동부
- 브라질 남부
- 캐나다 중부
- 미국 중부
- 중국 동부 2
- 중국 북부 2
- 동아시아
- 미국 동부
- 미국 동부 2
- 프랑스 중부
- 일본 동부
- 일본 서부
- 한국 중부
- 한국 남부
- 미국 중북부
- 북유럽
- 남아프리카 북부
- 미국 중남부
- 동남아시아
- 영국 남부
- 영국 서부
- 서유럽
- 미국 서부
- 미국 서부 2

지원 되는 것으로 표시 되지 않은 지역에 하이퍼 확장 데이터베이스를 만들려는 경우 Azure Portal를 통해 온 보 딩 요청을 보낼 수 있습니다. 지침에 대 한 자세한 내용은 [Azure SQL Database에 대 한 요청 할당량 늘리기](quota-increase-request.md) 를 참조 하세요. 요청을 제출 하는 경우 다음 지침을 따르십시오.

- [다른 할당량 요청](quota-increase-request.md#other) SQL 데이터베이스 할당량 유형을 사용 합니다.
- 텍스트 세부 정보에서 읽기 가능한 복제본을 포함 하 여 계산 SKU/총 코어를 추가 합니다.
- 또한 예상 TB를 지정 합니다.

## <a name="known-limitations"></a>알려진 제한 사항

이는 GA를 기준으로 하는 Hyperscale 서비스 계층에 대 한 현재 제한 사항입니다.  가능한 한 많은 제한 사항을 제거 하기 위해 적극적으로 노력 하 고 있습니다.

| 문제 | Description |
| :---- | :--------- |
| 논리 서버에 대 한 백업 관리 창은 SQL server에서 필터링 되는 Hyperscale 데이터베이스를 표시 하지 않습니다.  | Hyperscale에는 백업을 관리 하는 별도의 방법이 있지만 장기 보존 및 지정 시간 백업 보존 설정은 적용/무효화 되지 않습니다. 따라서 하이퍼스케일 데이터베이스가 백업 관리 창에 나타나지 않습니다. |
| 지정 시간 복원 | 데이터베이스가 하이퍼 크기 조정 서비스 계층으로 마이그레이션되면 마이그레이션 전의 특정 시점으로의 복원은 지원 되지 않습니다.|
| Hyperscale DB를 Hyperscale으로 또는 그 반대로 복원 | Hyperscale 데이터베이스를 Hyperscale이 아닌 데이터베이스로 복원할 수 없으며 hyperscale 데이터베이스를 Hyperscale 데이터베이스로 복원할 수 없습니다.|
| 데이터베이스에 1TB 보다 큰 데이터 파일이 하나 이상 있으면 마이그레이션이 실패 합니다. | 경우에 따라이 문제를 해결 하려면 많은 파일을 1TB 미만으로 축소 해야 할 수 있습니다. 마이그레이션 프로세스 중에 사용 되는 데이터베이스를 마이그레이션하는 경우 1tb 보다 큰 파일이 없는지 확인 합니다. 다음 쿼리를 사용 하 여 데이터베이스 파일의 크기를 확인 합니다. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| 관리되는 인스턴스 | 현재는 Hyperscale 데이터베이스에서 Azure SQL Database Managed Instance 지원 되지 않습니다. |
| 탄력적 풀 |  탄력적 풀은 현재 SQL Database Hyperscale에서 지원 되지 않습니다.|
| 하이퍼스케일로 마이그레이션은 현재 단방향 작업입니다. | 데이터베이스가 하이퍼스케일로 마이그레이션되고 나면 하이퍼스케일이 아닌 서비스 계층에 직접 마이그레이션할 수 없습니다. 현재는 데이터베이스를 Hyperscale에서 비-Hyperscale 마이그레이션하는 유일한 방법은 BACPAC 파일이 나 기타 데이터 이동 기술 (대량 복사, Azure Data Factory, Azure Databricks, SSIS 등)을 사용 하 여 내보내거나 가져오는 것입니다.|
| 영구적 메모리 내 개체가 있는 데이터베이스 마이그레이션 | Hyperscale은 비영구 메모리 내 개체 (테이블 형식, 네이티브 SPs 및 함수)만 지원 합니다.  데이터베이스를 Hyperscale service 계층으로 마이그레이션하기 전에 메모리 내 영구 테이블 및 기타 개체를 삭제 하 고 메모리 내 개체로 다시 만들어야 합니다.|
| 변경 내용 추적 | 변경 내용 추적은 현재 공개 미리 보기로 제공 되며 새 또는 기존 Hyperscale 데이터베이스에서 사용할 수 있습니다. |
| 지역 복제  | Azure SQL Database Hyperscale에 대해 지역에서 복제를 구성할 수 없습니다. |
| 데이터베이스 복사 | 아직 데이터베이스 복사를 사용 하 여 Azure SQL Hyperscale에서 새 데이터베이스를 만들 수는 없습니다. |
| TDE/AKV 통합 | Azure Key Vault를 사용 하는 투명 한 데이터베이스 암호화 (일반적으로 사용자 지정 키 또는 BYOK 라고도 함)는 Azure SQL Database Hyperscale에 대해 아직 지원 되지 않지만 서비스 관리 키를 사용 하는 TDE는 완전히 지원 됩니다. |
|Intelligent Database 기능 | "강제 계획" 옵션을 제외 하 고 다른 모든 자동 조정 옵션은 Hyperscale에서 아직 지원 되지 않습니다. 옵션은 사용 하도록 설정 된 것 처럼 보일 수 있지만 권장 사항이 나 작업은 적용 되지 않습니다. |
|Query Performance Insight | 쿼리 성능 정보는 현재 Hyperscale 데이터베이스에 대해 지원 되지 않습니다. |
| 데이터베이스 축소 | DBCC SHRINKDATABASE 또는 DBCC SHRINKFILE는 현재 Hyperscale 데이터베이스에 대해 지원 되지 않습니다. |
| 데이터베이스 무결성 검사 | DBCC CHECKDB는 현재 Hyperscale 데이터베이스에 대해 지원 되지 않습니다. Azure SQL Database의 데이터 무결성 관리에 대 한 자세한 내용은 [Azure SQL Database의 데이터 무결성](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) 을 참조 하세요. |

## <a name="next-steps"></a>다음 단계

- 하이퍼스케일에 대한 FAQ는 [하이퍼스케일에 대한 질문과 대답](sql-database-service-tier-hyperscale-faq.md)을 참조하세요.
- 서비스 계층에 대한 자세한 내용은 [서비스 계층](sql-database-service-tiers.md)을 참조하세요.
- 서버 및 구독 수준의 한도에 관한 정보는 [논리 서버의 리소스 한도 개요](sql-database-resource-limits-logical-server.md)를 참조하세요.
- 단일 데이터베이스에 대한 구매 모델 제한은 [단일 데이터베이스에 대한 Azure SQL Database vCore 기반 구매 모델 제한](sql-database-vcore-resource-limits-single-databases.md)을 참조하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](sql-database-features.md)을 참조하세요.
