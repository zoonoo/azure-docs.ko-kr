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
ms.openlocfilehash: 074a28af8c80c109dbe97306900e8f00618e435a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411693"
---
# <a name="hyperscale-service-tier"></a>하이퍼스케일 서비스 계층

Azure SQL Database는 인프라 오류의 경우에도 99.99%의 가용성을 보장하기 위해 클라우드 환경에 대해 조정되는 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. Azure SQL Database에 사용되는 세 가지 아키텍처 모델이 있습니다.
- 범용/표준 
-  하이퍼스케일
-  중요 비즈니스용/프리미엄

Azure SQL Database의 하이퍼스케일 서비스 계층은 vCore 기반 구매 모델의 최신 서비스 계층입니다. 이 서비스 계층은 Azure 아키텍처를 활용하여 범용 및 중요 비즈니스용 서비스 계층에 사용할 수 있는 제한을 초과하여 Azure SQL Database용 스토리지 및 컴퓨팅 리소스를 확장하는 확장성이 뛰어난 스토리지 및 컴퓨팅 성능 계층입니다.

> 
> [!NOTE]
> vCore 기반 구매 모델의 범용 목적 및 비즈니스 중요 서비스 계층에 대한 자세한 내용은 [범용 및](sql-database-service-tier-general-purpose.md) [비즈니스 중요](sql-database-service-tier-business-critical.md) 서비스 계층을 참조하십시오. vCore 기반 구매 모델과 DTU 기반 구매 모델의 비교는 [Azure SQL Database 구매 모델 및 리소스](sql-database-service-tiers.md)를 참조하세요.


## <a name="what-are-the-hyperscale-capabilities"></a>하이퍼스케일 기능에는 무엇이 있나요?

Azure SQL Database의 하이퍼스케일 서비스 계층은 다음과 같은 추가 기능을 제공합니다.

- 최대 100TB의 데이터베이스 크기 지원
- 컴퓨팅 리소스에 IO 에 영향을 미치지 않는 크기에 관계없이 거의 즉각적인 데이터베이스 백업(Azure Blob 저장소에 저장된 파일 스냅숏 기반).  
- 몇 시간 또는 며칠이 아닌 몇 분 내에(데이터베이스 작업의 규모가 아닌) 빠른 데이터베이스 복원(파일 스냅샷 기반)
- 데이터 볼륨에 관계없이 더 높은 로그 처리량 및 더 빠른 트랜잭션 커밋 시간이 보장되므로 전반적인 성능 개선
- 빠른 스케일 아웃 - 읽기 워크로드를 오프로드하고 핫 대기로 사용하기 위해 하나 이상의 읽기 전용 노드를 프로비전할 수 있습니다.
- 신속한 확장 - 필요할 때 무거운 워크로드를 수용하도록 일정한 시간에 컴퓨팅 리소스를 확장한 다음 필요하지 않을 때 계산 리소스를 다시 축소할 수 있습니다.

하이퍼스케일 서비스 계층은 클라우드 데이터베이스에서 기존에 확인되던 많은 실제 제한을 없애줍니다. 대부분의 다른 데이터베이스가 단일 노드에서 사용할 수 있는 리소스로 제한되지만 하이퍼스케일 서비스 계층의 데이터베이스에는 이러한 제한이 없습니다. 스토리지 아키텍처가 유연하기 때문에 필요에 따라 스토리지가 증가합니다. 실제로 하이퍼스케일 데이터베이스는 최대 크기가 정의된 상태로 생성되지 않습니다. 하이퍼스케일 데이터베이스는 필요에 따라 증가하므로 사용하는 용량에 대해서만 요금이 청구됩니다. 읽기 집약적 워크로드의 경우 하이퍼스케일 서비스 계층에서 읽기 워크로드를 오프로드하는 데 필요한 추가 읽기 복제본을 프로비전하여 신속한 스케일 아웃을 제공합니다.

또한 데이터베이스 백업을 만들거나 규모 확대 또는 축소에 필요한 시간이 더 이상 데이터베이스의 데이터 볼륨과 관련되지 않습니다. 하이퍼스케일 데이터베이스는 거의 동시에 백업할 수 있습니다. 몇 분 안에 수십 테라바이트의 데이터베이스 규모를 확대 또는 축소할 수도 있습니다. 이 기능은 초기 구성 선택에 따른 여러 가지 우려를 해소해줍니다.

하이퍼스케일 서비스 계층의 컴퓨팅 크기에 대한 자세한 내용은 [서비스 계층 특성](sql-database-service-tiers-vcore.md#service-tiers)을 참조하세요.

## <a name="who-should-consider-the-hyperscale-service-tier"></a>하이퍼스케일 서비스 계층을 고려하면 좋은 대상

하이퍼스케일 서비스 계층은 독립적으로 확장 가능한 컴퓨팅 및 스토리지 리소스를 통해 뛰어난 유연성과 높은 성능을 제공하기 때문에 대부분의 비즈니스 워크로드를 위한 것입니다. 최대 100TB의 스토리지를 자동으로 확장할 수 있는 기능을 통해 다음과 같은 고객을 위한 훌륭한 선택입니다.

- 온-프레미스에 대규모 데이터베이스를 가지고 클라우드로 이동하여 응용 프로그램을 현대화하려고 합니다.
- 이미 클라우드에 있으며 다른 서비스 계층의 최대 데이터베이스 크기 제한(1-4TB)으로 제한됩니다.
- 더 작은 데이터베이스를 가지고 있지만 빠른 수직 및 수평 계산 확장, 고성능, 즉각적인 백업 및 빠른 데이터베이스 복원이 필요합니다.

하이퍼스케일 서비스 계층은 순수 OLTP에서 순수 분석에 이르는 광범위한 SQL Server 워크로드를 지원하지만 주로 OLTP 및 하이브리드 트랜잭션 및 분석 처리(HTAP) 워크로드에 최적화되어 있습니다.

> [!IMPORTANT]
> 탄력적 풀은 하이퍼스케일 서비스 계층을 지원하지 않습니다.

## <a name="hyperscale-pricing-model"></a>하이퍼스케일 가격 책정 모델

하이퍼스케일 서비스 계층은 [vCore 모델](sql-database-service-tiers-vcore.md)에만 사용할 수 있습니다. 새 아키텍처에 맞게 가격 책정 모델이 범용 또는 중요 비즈니스용 서비스 계층과 약간 다릅니다.

- **계산**:

  하이퍼스케일 컴퓨팅 단위 가격은 복제본별로 정해집니다. [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 가격은 읽기 확장 복제본에 자동으로 적용됩니다. 기본적으로 하이퍼스케일 데이터베이스당 기본 복제본과 하나의 읽기 전용 복제본을 만듭니다.  사용자는 1-5에서 기본을 포함하여 복제본의 총 수를 조정할 수 있습니다.

- **스토리지**:

  하이퍼스케일 데이터베이스를 구성하는 경우 최대 데이터 크기를 지정할 필요가 없습니다. 하이퍼스케일 계층에서는 실제 할당을 기반으로 데이터베이스에 대한 저장소에 대한 요금이 부과됩니다. 저장소는 40GB에서 100TB 사이에 10GB 단위로 자동으로 할당됩니다. 필요한 경우 여러 데이터 파일이 동시에 증가할 수 있습니다. 하이퍼스케일 데이터베이스는 시작 크기가 10GB로 만들어지며 40GB 크기에 도달할 때까지 10분마다 10GB씩 증가하기 시작합니다.

하이퍼스케일 가격 책정에 대한 자세한 내용은 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/)을 참조하세요.

## <a name="distributed-functions-architecture"></a>분산 함수 아키텍처

모든 데이터 관리 기능을 하나의 위치/프로세스에 중앙 집중식으로 배치하는 기존 데이터베이스 엔진과 달리(소위 말하는 프로덕션의 분산 데이터베이스에도 모놀리식 데이터 엔진의 여러 복사본이 있음), 하이퍼스케일 데이터베이스는 여러 데이터 엔진의 의미 체계가 구분되어 있는 쿼리 처리 엔진을 데이터에 대한 장기 스토리지 및 지속성을 제공하는 구성 요소와 구분합니다. 이러한 방식으로 스토리지 용량을 필요한 만큼 원활하게 스케일 아웃할 수 있습니다(초기 목표는 100TB). 읽기 전용 복제본은 동일한 저장소 구성 요소를 공유하므로 새 읽기 가능한 복제본을 스핀업하는 데 데이터 복사본이 필요하지 않습니다. 

다음 다이어그램은 하이퍼스케일 데이터베이스에 있는 여러 유형의 노드를 보여 줍니다.

![아키텍처](./media/sql-database-hyperscale/hyperscale-architecture.png)

하이퍼스케일 데이터베이스에는 다음과 같은 다양한 유형의 구성 요소가 포함되어 있습니다.

### <a name="compute"></a>컴퓨팅

컴퓨팅 노드는 관계형 엔진이 존재하는 위치이므로 모든 언어 요소, 쿼리 처리 등이 발생합니다. 하이퍼스케일 데이터베이스와의 모든 사용자 상호 작용은 이러한 컴퓨팅 노드를 통해 발생합니다. 컴퓨팅 노드에는 데이터 페이지를 가져오는 데 필요한 네트워크 왕복 횟수를 최소화하기 위해 SSD 기반 캐시[이전 다이어그램에 나오는 RBPEX(Resilient Buffer Pool Extension)]가 있습니다. 모든 읽기/쓰기 워크로드 및 트랜잭션이 처리되는 기본 컴퓨팅 노드가 1개 있습니다. 장애 조치(Failover)를 위해 핫 대기 노드의 역할을 할 뿐만 아니라 읽기 워크로드를 오프로드하기 위한 읽기 전용 컴퓨팅 노드(이 기능이 필요한 경우)의 역할을 하는 하나 이상의 보조 컴퓨팅 노드도 있습니다.

### <a name="page-server"></a>페이지 서버

페이지 서버는 스케일 아웃된 스토리지 엔진을 나타내는 시스템입니다.  각 페이지 서버는 데이터베이스의 페이지 하위 집합을 담당합니다.  명목상으로, 각 페이지 서버는 128GB와 1TB의 데이터를 제어합니다. 데이터는 둘 이상의 (중복 및 가용성을 위해 유지되는 복제본 외부) 페이지 서버에서 공유되지 않습니다. 페이지 서버의 업무는 요청 시 컴퓨팅 노드에 데이터베이스 페이지를 제공하고, 트랜잭션으로 인해 데이터가 업데이트될 때 페이지를 최신 상태로 유지하는 것입니다. 페이지 서버는 로그 서비스의 로그 레코드를 재생하여 최신 상태로 유지됩니다. 또한 페이지 서버는 성능 향상을 위해 SSD 기반 캐시를 유지 관리합니다. 데이터 페이지의 장기 스토리지는 추가 안정성을 위해 Azure Storage에 보관됩니다.

### <a name="log-service"></a>로그 서비스

로그 서비스는 주 계산 복제본의 로그 레코드를 허용하고, 지속 가능한 캐시에 보관하고, 로그 레코드를 나머지 계산 복제본(캐시를 업데이트할 수 있도록)과 관련 페이지 서버로 전달하여 데이터를 업데이트할 수 있도록 합니다. 이러한 방식으로 기본 계산 복제본의 모든 데이터 변경 내용이 로그 서비스를 통해 모든 보조 계산 복제본 및 페이지 서버로 전파됩니다. 마지막으로 로그 레코드는 사실상 무한한 저장소 저장소인 Azure Storage의 장기 저장소로 푸시됩니다. 이 메커니즘은 빈번한 로그 잘림의 필요성을 제거합니다. 또한 로그 서비스에는 로그 레코드에 대한 액세스 속도를 높이기 위한 로컬 캐시가 있습니다.

### <a name="azure-storage"></a>Azure Storage

Azure Storage에는 데이터베이스에 있는 모든 데이터 파일이 포함됩니다. 페이지 서버는 Azure 저장소에 데이터 파일을 최신 상태로 유지합니다. 이 저장소는 Azure 지역 간의 복제뿐만 아니라 백업 용도로 사용됩니다. 백업은 데이터 파일의 저장소 스냅숏을 사용하여 구현됩니다. 스냅샷을 사용한 복원 작업은 데이터 크기에 관계없이 빠릅니다. 데이터는 데이터베이스의 백업 보존 기간 내에 있는 모든 시점으로 복원할 수 있습니다.

## <a name="backup-and-restore"></a>백업 및 복원

백업은 파일 스냅숏 기반이므로 거의 즉각적입니다. 스토리지 및 계산 분리를 통해 백업/복원 작업을 저장소 계층으로 푸시하여 기본 계산 복제본의 처리 부담을 줄일 수 있습니다. 따라서 데이터베이스 백업은 기본 계산 노드의 성능에 영향을 미치지 않습니다. 마찬가지로 복원은 파일 스냅숏으로 되돌리면 수행되며 데이터 작업 크기가 아닙니다. 복원은 일정한 시간 작업이며 몇 시간 또는 며칠이 아닌 몇 분 만에 여러 테라바이트 의 데이터베이스를 복원할 수 있습니다. 기존 백업을 복원하여 새 데이터베이스를 만들면 테라바이트 크기의 데이터베이스도 몇 분 만에 개발 또는 테스트 목적으로 데이터베이스 복사본을 만들 수 있습니다.

## <a name="scale-and-performance-advantages"></a>확장 및 성능상의 이점

추가 읽기 전용 컴퓨팅 노드를 신속하게 스핀업/스핀다운하는 기능을 사용하여 하이퍼스케일 아키텍처는 상당한 읽기 기능을 허용하며 더 많은 쓰기 요청을 제공하기 위해 기본 컴퓨팅 노드를 해제할 수도 있습니다. 또한 하이퍼스케일 아키텍처의 공유 스토리지 아키텍처로 인해 컴퓨팅 노드 규모를 빠르게 확대/축소할 수 있습니다.

## <a name="create-a-hyperscale-database"></a>하이퍼스케일 데이터베이스 만들기

하이퍼스케일 데이터베이스는 [Azure 포털,](https://portal.azure.com) [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) 또는 [CLI를](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)사용하여 만들 수 있습니다. 하이퍼스케일 데이터베이스는 [vCore 기반 구매 모델을](sql-database-service-tiers-vcore.md)통해서만 사용할 수 있습니다.

다음 T-SQL 명령은 하이퍼스케일 데이터베이스를 생성합니다. `CREATE DATABASE` 문에 버전 및 서비스 목표를 둘 다 지정해야 합니다. 유효한 서비스 목표 목록에 대한 [리소스 제한을](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4) 참조하십시오.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
이렇게 하면 4개의 코어가 있는 Gen5 하드웨어에 하이퍼스케일 데이터베이스가 생성됩니다.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>기존 Azure SQL Database를 하이퍼스케일 서비스 계층으로 마이그레이션

Azure [포털,](https://portal.azure.com) [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) 또는 [CLI를](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)사용하여 기존 Azure SQL 데이터베이스를 하이퍼스케일로 이동할 수 있습니다. 현재 이 마이그레이션은 단방향 마이그레이션입니다. 데이터를 내보내고 가져오는 것 외에는 하이퍼스케일에서 다른 서비스 계층으로 데이터베이스를 이동할 수 없습니다. 개념 증명(POC)의 경우 프로덕션 데이터베이스의 복사본을 만들고 복사본을 Hyperscale으로 마이그레이션하는 것이 좋습니다. 기존 Azure SQL 데이터베이스를 하이퍼스케일 계층으로 마이그레이션하는 것은 데이터 작업의 크기입니다.

다음 T-SQL 명령은 하이퍼스케일 서비스 계층으로 데이터베이스를 이동합니다. `ALTER DATABASE` 문에 버전 및 서비스 목표를 둘 다 지정해야 합니다.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>하이퍼스케일 데이터베이스의 읽기 확장 복제본에 연결

하이퍼스케일 데이터베이스에서 클라이언트가 제공하는 연결 문자열의 `ApplicationIntent` 인수는 연결이 쓰기 복제본또는 읽기 전용 보조 복제본으로 라우팅되는지 여부를 결정합니다. `ApplicationIntent`가 `READONLY`로 설정되고 데이터베이스에 보조 복제본이 없는 경우에는 연결이 주 복제본으로 라우팅되고 기본값은 `ReadWrite` 동작으로 설정됩니다.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

하이퍼스케일 보조 복제본은 모두 동일하며 기본 복제본과 동일한 서비스 수준 목표를 사용합니다. 두 개 이상의 보조 복제본이 있는 경우 워크로드는 사용 가능한 모든 보조 데이터베이스에 분산됩니다. 각 보조 복제본은 독립적으로 업데이트되므로 다른 복제본은 기본 복제본에 비해 서로 다른 데이터 대기 시간을 가질 수 있습니다.

## <a name="database-high-availability-in-hyperscale"></a>하이퍼스케일의 데이터베이스 고가용성

다른 모든 서비스 계층과 마찬가지로 Hyperscale은 계산 복제 본가 가용성에 관계없이 커밋된 트랜잭션에 대한 데이터 내구성을 보장합니다. 주 복제본을 사용할 수 없게 된 가동 중지 시간은 장애 조치 유형(계획된 것과 계획되지 않은) 및 하나 이상의 보조 복제본이 있는 경우에 따라 달라집니다. 계획된 장애 조치(예: 유지 관리 이벤트)에서 시스템은 장애 조치(failover)를 시작하기 전에 새 주 복제본을 만들거나 기존 보조 복제본을 장애 조치 대상으로 사용합니다. 계획되지 않은 장애 조치(예: 주 복제본의 하드웨어 오류)에서 시스템은 보조 복제본을 장애 조치 대상으로 사용하거나 사용 가능한 컴퓨팅 용량 풀에서 새 주 복제본을 만듭니다. 후자의 경우 새 주 복제본을 만드는 데 필요한 추가 단계로 인해 가동 중지 시간이 더 깁니다.

하이퍼스케일 SLA의 경우 [Azure SQL 데이터베이스에 대한 SLA 를](https://azure.microsoft.com/support/legal/sla/sql-database/)참조하십시오.

## <a name="disaster-recovery-for-hyperscale-databases"></a>하이퍼스케일 데이터베이스에 대한 재해 복구

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>하이퍼스케일 데이터베이스를 다른 지역으로 복원
재해 복구 작업 또는 드릴, 재배치 또는 기타 이유의 일부로 현재 호스팅되는 지역이 아닌 다른 지역으로 Azure SQL Database 하이퍼스케일 DB를 복원해야 하는 경우 기본 방법은 데이터베이스의 지리적 복원을 수행하는 것입니다.  여기에는 다른 AZURE SQL DB를 다른 지역으로 복원하는 데 사용하는 것과 정확히 동일한 단계가 포함됩니다.
1. 적절한 서버가 아직 없는 경우 대상 지역에 Azure SQL Database 서버를 만듭니다.  이 서버는 원본(원본) 서버와 동일한 구독으로 소유해야 합니다.
2. 자동 백업에서 Azure SQL Database를 복원하는 페이지의 [지리적 복원](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) 항목의 지침을 따릅니다.

> [!NOTE]
> 원본과 대상은 별도의 지역에 있기 때문에 데이터베이스는 비지리적 복원에서와 같이 원본 데이터베이스와 스냅샷 저장소를 공유할 수 없으므로 매우 빠르게 완료됩니다. 하이퍼스케일 데이터베이스의 지리적 복원의 경우 대상이 지리적 복제 저장소의 쌍을 이루는 영역에 있더라도 데이터 크기 작업이 됩니다.  즉, 지역 복원을 수행하면 복원되는 데이터베이스의 크기에 비례하는 시간이 소요됩니다.  대상이 쌍을 이루는 영역에 있는 경우 복사본은 지역 내에 있을 수 있으며, 이는 지역 간 복사보다 훨씬 빠르지만 여전히 데이터 크기 작업입니다.

## <a name="available-regions"></a><a name=regions></a>사용 가능한 지역

Azure SQL 데이터베이스 하이퍼스케일 계층은 현재 다음 리전에서 사용할 수 있습니다.

- 오스트레일리아 동부
- 오스트레일리아 남동부
- 브라질 남부
- 캐나다 중부
- 미국 중부
- 중국 동부 2
- 중국 북부 2
- 동아시아
- 미국 동부
- 이스트 Us 2
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

지원되지 않는 지역에서 Hyperscale 데이터베이스를 만들려면 Azure 포털을 통해 온보딩 요청을 보낼 수 있습니다. 지침은 Azure [SQL 데이터베이스에 대한 요청 할당량 증가를](quota-increase-request.md) 참조하세요. 요청을 제출할 때 다음 지침을 따르십시오.

- 기타 [할당량 요청](quota-increase-request.md#other) SQL 데이터베이스 할당량 형식을 사용합니다.
- 텍스트 세부 정보에서 읽을 수 있는 복제본을 포함하여 계산 SKU/총 코어를 추가합니다.
- 또한 예상 결핵을 지정합니다.

## <a name="known-limitations"></a>알려진 제한 사항

이는 GA의 하이퍼스케일 서비스 계층에 대한 현재 제한 사항입니다.  우리는 적극적으로 가능한 한 이러한 제한의 많은 제거하기 위해 노력하고 있습니다.

| 문제 | Description |
| :---- | :--------- |
| 논리 서버에 대한 백업 관리 창에는 하이퍼스케일 데이터베이스가 표시되지 않으며 뷰에서 필터링됩니다.  | 하이퍼스케일은 백업을 관리하기 위한 별도의 방법을 가지고 있으며, 따라서 장기 보존 및 시간 기간 백업 보존 설정이 적용되지 않거나 무효화됩니다. 따라서 하이퍼스케일 데이터베이스가 백업 관리 창에 나타나지 않습니다. |
| 지정 시간 복원 | 하이퍼스케일 데이터베이스가 아닌 데이터베이스 보존 기간 내에 하이퍼스케일 데이터베이스가 아닌 데이터베이스로 복원할 수 있습니다. 하이퍼스케일이 아닌 데이터베이스는 하이퍼스케일 데이터베이스로 복원할 수 없습니다.|
| 데이터베이스에 1TB보다 큰 데이터 파일이 하나 이상 있는 경우 마이그레이션이 실패합니다. | 경우에 따라 대용량 파일을 1TB 미만으로 축소하여 이 문제를 해결할 수 있습니다. 마이그레이션 프로세스 중에 사용 중인 데이터베이스를 마이그레이션하는 경우 파일이 1TB보다 큰 파일이 없는지 확인합니다. 다음 쿼리를 사용하여 데이터베이스 파일의 크기를 확인합니다. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| 관리되는 인스턴스 | Azure SQL Database 관리 인스턴스는 현재 하이퍼스케일 데이터베이스에서 지원되지 않습니다. |
| 탄력적 풀 |  탄력적 풀은 현재 SQL 데이터베이스 하이퍼스케일에서 지원되지 않습니다.|
| 하이퍼스케일로 마이그레이션은 현재 단방향 작업입니다. | 데이터베이스가 하이퍼스케일로 마이그레이션되고 나면 하이퍼스케일이 아닌 서비스 계층에 직접 마이그레이션할 수 없습니다. 현재, 하이퍼스케일에서 비하이퍼스케일로 데이터베이스를 마이그레이션하는 유일한 방법은 BACPAC 파일 또는 기타 데이터 이동 기술(대량 복사, Azure 데이터 팩터리, Azure Databricks, SSIS 등)을 사용하여 내/가져오는 것입니다.|
| 영구 인메모리 개체가 있는 데이터베이스 마이그레이션 | 하이퍼스케일은 비영구 인메모리 개체(테이블 유형, 네이티브 SP 및 함수)만 지원합니다.  데이터베이스를 하이퍼스케일 서비스 계층으로 마이그레이션하기 전에 영구 인메모리 테이블 및 기타 개체를 삭제하고 메모리가 아닌 개체로 다시 만들어야 합니다.|
| 지오 복제  | 아직 Azure SQL 데이터베이스 하이퍼스케일에 대한 지역 복제를 구성할 수 없습니다. |
| 데이터베이스 복사 | 아직 데이터베이스 복사본을 사용하여 Azure SQL 하이퍼스케일에서 새 데이터베이스를 만들 수 없습니다. |
| TDE/AKV 통합 | Azure 키 볼트(일반적으로 사용자 소유 키 가져오기 또는 BYOK라고 함)를 사용하는 투명 데이터베이스 암호화는 Azure SQL Database 하이퍼스케일에서 아직 지원되지 는 않았지만 서비스 관리 키가 있는 TDE는 완전히 지원됩니다. |
|지능형 데이터베이스 기능 | "강제 계획" 옵션을 제외 하면 다른 모든 자동 조정 옵션은 아직 Hyperscale에서 지원 되지 않습니다. |
|Query Performance Insight | 쿼리 성능 통찰력은 현재 하이퍼스케일 데이터베이스에서 지원되지 않습니다. |
| 데이터베이스 축소 | DBCC 수축 데이터베이스 또는 DBCC SHRINKFILE은 현재 하이퍼스케일 데이터베이스에 대해 지원되지 않습니다. |
| 데이터베이스 무결성 검사 | DBCC CHECKDB는 현재 하이퍼스케일 데이터베이스에 대해 지원되지 않습니다. [Azure SQL 데이터베이스의 데이터 무결성](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) 관리에 대한 자세한 내용은 Azure SQL 데이터베이스의 데이터 무결성을 참조하십시오. |

## <a name="next-steps"></a>다음 단계

- 하이퍼스케일에 대한 FAQ는 [하이퍼스케일에 대한 질문과 대답](sql-database-service-tier-hyperscale-faq.md)을 참조하세요.
- 서비스 계층에 대한 자세한 내용은 [서비스 계층을 참조하십시오.](sql-database-service-tiers.md)
- 서버 및 구독 수준의 한도에 관한 정보는 [논리 서버의 리소스 한도 개요](sql-database-resource-limits-logical-server.md)를 참조하세요.
- 단일 데이터베이스에 대한 구매 모델 제한은 [단일 데이터베이스에 대한 Azure SQL Database vCore 기반 구매 모델 제한](sql-database-vcore-resource-limits-single-databases.md)을 참조하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](sql-database-features.md)을 참조하세요.
