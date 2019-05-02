---
title: Azure SQL Database 하이퍼스케일 개요 | Microsoft Docs
description: 이 문서에서는 Azure SQL Database의 vCore 기반 구매 모델에 포함된 하이퍼스케일 서비스 계층에 대해 설명하고 범용 및 중요 비즈니스용 서비스 계층과의 차이점을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 04/04/2019
ms.openlocfilehash: 5e323b28913e0ba259654d39f97e0436e6bff2db
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786025"
---
# <a name="hyperscale-service-tier-preview-for-up-to-100-tb"></a>최대 100TB의 하이퍼스케일 서비스 계층(미리 보기)

Azure SQL Database는 인프라 오류의 경우에도 99.99%의 가용성을 보장하기 위해 클라우드 환경에 대해 조정되는 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. Azure SQL Database에 사용되는 세 가지 아키텍처 모델이 있습니다.

- 범용/표준 
- 중요 비즈니스용/프리미엄
- 하이퍼스케일

Azure SQL Database의 하이퍼스케일 서비스 계층은 vCore 기반 구매 모델의 최신 서비스 계층입니다. 이 서비스 계층은 Azure 아키텍처를 활용하여 범용 및 중요 비즈니스용 서비스 계층에 사용할 수 있는 제한을 초과하여 Azure SQL Database용 스토리지 및 계산 리소스를 스케일 아웃하는 확장성이 뛰어난 스토리지 및 계산 성능 계층입니다.

> [!IMPORTANT]
> 하이퍼스케일 서비스 계층은 현재 미리 보기 상태이며 제한된 Azure 지역에 제공됩니다. 전체 지역 목록은 [하이퍼스케일 서비스 계층 사용 가능 지역](#available-regions)을 참조하세요. 하이퍼스케일 데이터베이스에서는 프로덕션 워크로드를 아직 실행하지 않는 것이 좋습니다. 하이퍼스케일 데이터베이스는 다른 서비스 계층으로 업데이트할 수 없습니다. 테스트 목적으로 현재 데이터베이스의 복사본을 만들고 하이퍼스케일 서비스 계층에 복사본을 업데이트하는 것이 좋습니다.
> [!NOTE]
> vCore 기반 구매 모델의 범용 및 중요 비즈니스용 서비스 계층에 대한 자세한 내용은 [범용](sql-database-service-tier-general-purpose.md) 및 [중요 비즈니스용](sql-database-service-tier-business-critical.md) 서비스 계층을 참조하세요. vCore 기반 구매 모델과 DTU 기반 구매 모델의 비교는 [Azure SQL Database 구매 모델 및 리소스](sql-database-purchase-models.md)를 참조하세요.

## <a name="what-are-the-hyperscale-capabilities"></a>하이퍼스케일 기능에는 무엇이 있나요?

Azure SQL Database의 하이퍼스케일 서비스 계층은 다음과 같은 추가 기능을 제공합니다.

- 최대 100TB의 데이터베이스 크기 지원
- 계산 IO에 영향을 주지 않으면서 크기에 관계없이 거의 즉각적인 데이터베이스 백업(Azure Blob 스토리지에 저장된 파일 스냅숏 기반)   
- 몇 시간 또는 며칠이 아닌 몇 분 내에(데이터베이스 작업의 규모가 아닌) 빠른 데이터베이스 복원(파일 스냅숏 기반)
- 데이터 볼륨에 관계없이 더 높은 로그 처리량 및 더 빠른 트랜잭션 커밋 시간이 보장되므로 전반적인 성능 개선
- 빠른 스케일 아웃 - 읽기 워크로드를 오프로드하고 핫 대기로 사용하기 위해 하나 이상의 읽기 전용 노드를 프로비전할 수 있습니다.
- 빠른 강화 - 필요할 때 일정한 시간에 계산 리소스 규모를 확대하여 과도한 워크로드를 수용하고, 필요 없을 때 계산 리소스 규모를 다시 축소할 수 있습니다.

하이퍼스케일 서비스 계층은 클라우드 데이터베이스에서 기존에 확인되던 많은 실제 제한을 없애줍니다. 대부분의 다른 데이터베이스가 단일 노드에서 사용할 수 있는 리소스로 제한되지만 하이퍼스케일 서비스 계층의 데이터베이스에는 이러한 제한이 없습니다. 저장소 아키텍처가 유연하기 때문에 필요에 따라 저장소가 증가합니다. 실제로 하이퍼스케일 데이터베이스는 최대 크기가 정의된 상태로 생성되지 않습니다. 하이퍼스케일 데이터베이스는 필요에 따라 증가하므로 사용하는 용량에 대해서만 요금이 청구됩니다. 읽기 집약적 워크로드의 경우 하이퍼스케일 서비스 계층에서 읽기 워크로드를 오프로드하는 데 필요한 추가 읽기 복제본을 프로비전하여 신속한 스케일 아웃을 제공합니다.

또한 데이터베이스 백업을 만들거나 규모 확대 또는 축소에 필요한 시간이 더 이상 데이터베이스의 데이터 볼륨과 관련되지 않습니다. 하이퍼스케일 데이터베이스는 거의 동시에 백업할 수 있습니다. 몇 분 안에 수십 테라바이트의 데이터베이스 규모를 확대 또는 축소할 수도 있습니다. 이 기능은 초기 구성 선택에 따른 여러 가지 우려를 해소해줍니다.

하이퍼스케일 서비스 계층의 컴퓨팅 크기에 대한 자세한 내용은 [서비스 계층 특성](sql-database-service-tiers-vcore.md#service-tier-characteristics)을 참조하세요.

## <a name="who-should-consider-the-hyperscale-service-tier"></a>하이퍼스케일 서비스 계층을 고려하면 좋은 대상

하이퍼스케일 서비스 계층은 주로 온-프레미스에 대규모 데이터베이스를 보유하며 클라우드로 전환하여 애플리케이션을 최신 상태로 유지하려는 고객 또는 클라우드에 이미 있으며 최대 데이터베이스 크기 제한(1-4TB)에 따라 제한되는 고객을 대상으로 합니다. 또한 스토리지 및 계산을 위해 고성능 및 높은 확장성을 원하는 고객을 대상으로 합니다.

하이퍼스케일 서비스 계층은 모든 SQL Server 워크로드를 지원하지만 주로 OLTP에 맞게 최적화되어 있습니다. 또한 하이퍼스케일 서비스 계층은 하이브리드 및 분석(데이터 마트) 워크로드도 지원합니다.

> [!IMPORTANT]
> 탄력적 풀은 하이퍼스케일 서비스 계층을 지원하지 않습니다.

## <a name="hyperscale-pricing-model"></a>하이퍼스케일 가격 책정 모델

하이퍼스케일 서비스 계층은 [vCore 모델](sql-database-service-tiers-vcore.md)에만 사용할 수 있습니다. 새 아키텍처에 맞게 가격 책정 모델이 범용 또는 중요 비즈니스용 서비스 계층과 약간 다릅니다.

- **컴퓨팅**:

  하이퍼스케일 컴퓨팅 단위 가격은 복제본별로 정해집니다. [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 가격은 읽기 확장 복제본에 자동으로 적용됩니다. 공개 미리 보기에서는 하이퍼스케일 데이터베이스에 대해 기본적으로 2개의 복제본이 생성됩니다.

- **저장소**:

  하이퍼스케일 데이터베이스를 구성하는 경우 최대 데이터 크기를 지정할 필요가 없습니다. 하이퍼스케일 계층에서는 실제 사용량에 따라 데이터베이스의 저장소에 대한 요금이 청구됩니다. 저장소는 5GB~100TB 사이에 1GB씩 증분하여 동적으로 할당됩니다.  

하이퍼스케일 가격 책정에 대한 자세한 내용은 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/)을 참조하세요.

## <a name="distributed-functions-architecture"></a>분산 함수 아키텍처

모든 데이터 관리 기능을 하나의 위치/프로세스에 중앙 집중식으로 배치하는 기존 데이터베이스 엔진과 달리(소위 말하는 프로덕션의 분산 데이터베이스에도 모놀리식 데이터 엔진의 여러 복사본이 있음), 하이퍼스케일 데이터베이스는 여러 데이터 엔진의 의미 체계가 구분되어 있는 쿼리 처리 엔진을 데이터에 대한 장기 저장 및 지속성을 제공하는 구성 요소와 구분합니다. 이러한 방식으로 저장소 용량을 필요한 만큼 원활하게 스케일 아웃할 수 있습니다(초기 목표는 100TB). 읽기 전용 복제본은 동일한 계산 구성 요소를 공유하므로 읽기 가능한 새 복제본을 스핀업하는 데 데이터 복사본이 필요하지 않습니다. 미리 보기 중에는 읽기 전용 복제본이 하나만 지원됩니다.

다음 다이어그램은 하이퍼스케일 데이터베이스에 있는 여러 유형의 노드를 보여 줍니다.

![아키텍처](./media/sql-database-hyperscale/hyperscale-architecture.png)

하이퍼스케일 데이터베이스에는 다음과 같은 다른 유형의 노드가 포함됩니다.

### <a name="compute-node"></a>Compute 노드

계산 노드는 관계형 엔진이 존재하는 위치이므로 모든 언어 요소, 쿼리 처리 등이 발생합니다. 하이퍼스케일 데이터베이스와의 모든 사용자 상호 작용은 이러한 계산 노드를 통해 발생합니다. 계산 노드에는 데이터 페이지를 가져오는 데 필요한 네트워크 왕복 횟수를 최소화하기 위해 SSD 기반 캐시[이전 다이어그램에 나오는 RBPEX(Resilient Buffer Pool Extension)]가 있습니다. 모든 읽기/쓰기 워크로드 및 트랜잭션이 처리되는 기본 계산 노드가 1개 있습니다. 장애 조치(Failover)를 위해 핫 대기 노드의 역할을 할 뿐만 아니라 읽기 워크로드를 오프로드하기 위한 읽기 전용 계산 노드(이 기능이 필요한 경우)의 역할을 하는 하나 이상의 보조 계산 노드도 있습니다.

### <a name="page-server-node"></a>페이지 서버 노드

페이지 서버는 스케일 아웃된 저장소 엔진을 나타내는 시스템입니다.  각 페이지 서버는 데이터베이스의 페이지 하위 집합을 담당합니다.  일반적으로 각 페이지 서버는 1테라바이트의 데이터를 제어합니다. 데이터는 둘 이상의 (중복 및 가용성을 위해 유지되는 복제본 외부) 페이지 서버에서 공유되지 않습니다. 페이지 서버의 업무는 요청 시 계산 노드에 데이터베이스 페이지를 제공하고, 트랜잭션으로 인해 데이터가 업데이트될 때 페이지를 최신 상태로 유지하는 것입니다. 페이지 서버는 로그 서비스의 로그 레코드를 재생하여 최신 상태로 유지됩니다. 또한 페이지 서버는 성능 향상을 위해 SSD 기반 캐시를 유지 관리합니다. 데이터 페이지의 장기 스토리지는 추가 안정성을 위해 Azure Storage에 보관됩니다.

### <a name="log-service-node"></a>로그 서비스 노드

로그 서비스 노드는 기본 계산 노드의 로그 레코드를 수락하고, 지속형 캐시에 보관하고, 로그 레코드를 나머지 계산 노드(계산 노드에서 캐시를 업데이트할 수 있음) 뿐만 아니라 관련 페이지 서버에 전달하므로 이 서버에서 데이터가 업데이트될 수 있습니다. 이러한 방식으로 기본 계산 노드의 모든 데이터 변경 내용은 로그 서비스를 통해 모든 보조 계산 노드 및 페이지 서버로 전파됩니다. 마지막으로 로그 레코드는 무한 스토리지 리포지토리에 해당하는 Azure Storage의 장기 스토리지로 푸시됩니다. 이 메커니즘은 로그를 자주 잘라낼 필요를 없애줍니다. 로그 서비스에도 액세스 속도를 높이기 위한 로컬 캐시가 있습니다.

### <a name="azure-storage-node"></a>Azure Storage 노드

Azure Storage 노드는 페이지 서버에 있는 데이터의 최종 대상입니다. 이 저장소는 Azure 지역 간의 복제뿐만 아니라 백업용으로 사용됩니다. 백업은 데이터 파일의 스냅숏으로 구성됩니다. 복원 작업은 이러한 스냅숏에서 빠르게 수행되며 데이터는 특정 시점으로 복원될 수 있습니다.

## <a name="backup-and-restore"></a>Backup 및 복원

백업은 파일 스냅숏을 기준으로 하므로 거의 즉각적입니다. 스토리지 및 계산 분리를 사용하면 백업/복원 작업을 스토리지 계층으로 밀어넣어 기본 계산 노드의 처리 부담을 줄일 수 있습니다. 따라서 대형 데이터베이스의 백업은 기본 계산 노드의 성능에 영향을 미치지 않습니다. 마찬가지로, 복원도 파일 스냅숏을 복사하여 수행되므로 데이터 작업의 규모는 아닙니다. 동일한 저장소 계정 내에서 수행되는 복원의 경우 복원 작업은 빠르게 수행됩니다.

## <a name="scale-and-performance-advantages"></a>확장 및 성능상의 이점

추가 읽기 전용 계산 노드를 신속하게 스핀업/스핀다운하는 기능을 사용하여 하이퍼스케일 아키텍처는 상당한 읽기 기능을 허용하며 더 많은 쓰기 요청을 제공하기 위해 기본 계산 노드를 해제할 수도 있습니다. 또한 하이퍼스케일 아키텍처의 공유 스토리지 아키텍처로 인해 계산 노드 규모를 빠르게 확대/축소할 수 있습니다.

## <a name="create-a-hyperscale-database"></a>하이퍼스케일 데이터베이스 만들기

사용 하 여 대규모 데이터베이스를 만들 수 있습니다 합니다 [Azure portal](https://portal.azure.com)를 [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)를 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase) 또는 [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)합니다. 하이퍼스케일 데이터베이스는 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 사용해야만 사용이 가능합니다.

다음 T-SQL 명령은 하이퍼스케일 데이터베이스를 생성합니다. `CREATE DATABASE` 문에 버전 및 서비스 목표를 둘 다 지정해야 합니다.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>기존 Azure SQL Database를 하이퍼스케일 서비스 계층으로 마이그레이션

대규모 사용 하 여 기존 Azure SQL database를 이동할 수는 [Azure portal](https://portal.azure.com)를 [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)를 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) 또는 [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)합니다. 공개 미리 보기에서는 단방향 마이그레이션입니다. 하이퍼스케일에서 또 다른 서비스 계층으로 데이터베이스를 이동할 수는 없습니다. 프로덕션 데이터베이스의 복사본을 만들어서 POC(개념 증명)용으로 하이퍼스케일에 마이그레이션하는 것이 좋습니다.

다음 T-SQL 명령은 하이퍼스케일 서비스 계층으로 데이터베이스를 이동합니다. `ALTER DATABASE` 문에 버전 및 서비스 목표를 둘 다 지정해야 합니다.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>하이퍼스케일 데이터베이스의 읽기 확장 복제본에 연결

하이퍼스케일 데이터베이스에서 클라이언트가 제공한 연결 문자열의 `ApplicationIntent` 인수는 연결이 쓰기 복제본으로 라우팅되는지 또는 읽기 전용 보조 복제본으로 라우팅되는지를 나타냅니다. `ApplicationIntent`가 `READONLY`로 설정되고 데이터베이스에 보조 복제본이 없는 경우에는 연결이 주 복제본으로 라우팅되고 기본값은 `ReadWrite` 동작으로 설정됩니다.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="available-regions"></a>사용 가능한 지역

하이퍼스케일 서비스 계층은 현재 미리 보기 상태이며 다음 Azure 지역에서 사용할 수 있습니다. 1 미국 동부, 미국 동부 2, 미국 서 부 2, 미국 중부, 북유럽 CentralU S, 유럽 서 부, 유럽 북부, 오스트레일리아 동부, 오스트레일리아 남동부, 동남 아시아, 일본 동부, 및 대한민국 중부

## <a name="known-limitations"></a>알려진 제한 사항

| 문제 | 설명 |
| :---- | :--------- |
| SQL Database 서버의 [백업 관리] 창에 SQL Server에서 필터링된 하이퍼스케일 데이터베이스가 표시되지 않음  | 하이퍼스케일에는 백업을 관리하는 별도의 방법이 있기 때문에 장기 보존 및 특정 시점 백업 보존 설정이 적용되지 않고 무효화됩니다. 따라서 하이퍼스케일 데이터베이스가 백업 관리 창에 나타나지 않습니다. |
| 지정 시간 복원 | 데이터베이스가 하이퍼스케일 서비스 계층에 마이그레이션되고 나면 마이그레이션 이전에 지정 시간으로 복원이 지원되지 않습니다.|
| 활성 워크로드로 인해 마이그레이션 중에 데이터베이스 파일이 커져서 파일 경계당 1TB를 넘으면 마이그레이션이 실패합니다. | 해결 방법: <br> - 가능하면 실행 중인 업데이트 워크로드가 없을 때 데이터베이스를 마이그레이션합니다.<br> - 마이그레이션을 다시 시도합니다. 마이그레이션 중에 1TB 경계를 초과하지만 않으면 성공합니다.|
| Managed Instance는 현재 지원되지 않습니다. | 현재 지원되지 않음 |
| 하이퍼스케일로 마이그레이션은 현재 단방향 작업입니다. | 데이터베이스가 하이퍼스케일로 마이그레이션되고 나면 하이퍼스케일이 아닌 서비스 계층에 직접 마이그레이션할 수 없습니다. 현재 하이퍼스케일에서 하이퍼스케일이 아닌 곳으로 데이터베이스를 마이그레이션하는 유일한 방법은 BACPAC 파일을 사용하여 내보내기/가져오기를 수행하는 것 입니다.|
| 메모리 내 개체를 사용하는 데이터베이스 마이그레이션은 현재 지원되지 않습니다. | 데이터베이스를 하이퍼스케일 서비스 계층으로 마이그레이션하기 전에 메모리 내 개체를 삭제하고 메모리 내 개체가 아닌 개체로 다시 만들어야 합니다.|
| 데이터 변경 내용 추적은 현재 지원되지 않습니다. | 하이퍼스케일 데이터베이스에서 데이터 변경 내용 추적을 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- 하이퍼스케일에 대한 FAQ는 [하이퍼스케일에 대한 질문과 대답](sql-database-service-tier-hyperscale-faq.md)을 참조하세요.
- 서비스 계층에 대한 자세한 내용은 [서비스 계층](sql-database-purchase-models.md)을 참조하세요.
- 서버 및 구독 수준의 한도에 관한 정보는 [SQL Database 서버의 리소스 한도 개요](sql-database-resource-limits-database-server.md)를 참조하세요.
- 단일 데이터베이스에 대한 구매 모델 제한은 [단일 데이터베이스에 대한 Azure SQL Database vCore 기반 구매 모델 제한](sql-database-vcore-resource-limits-single-databases.md)을 참조하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](sql-database-features.md)을 참조하세요.
