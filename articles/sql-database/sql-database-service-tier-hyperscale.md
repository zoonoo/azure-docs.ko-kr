---
title: Azure SQL Database 하이퍼스케일 개요 | Microsoft Docs
description: 이 문서에서는 Azure SQL Database의 vCore 기반 구매 모델에 포함된 하이퍼스케일 서비스 계층에 대해 설명하고 범용 및 중요 비즈니스용 서비스 계층과의 차이점을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 0fe098bd644762fb291eb623a7b41cd987c7fa26
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779182"
---
# <a name="hyperscale-service-tier-for-up-to-100-tb"></a>최대 100TB의 대규모 서비스 계층

Azure SQL Database는 인프라 오류의 경우에도 99.99%의 가용성을 보장하기 위해 클라우드 환경에 대해 조정되는 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. Azure SQL Database에 사용되는 세 가지 아키텍처 모델이 있습니다.
- 범용/표준 
-  하이퍼스케일
-  중요 비즈니스용/프리미엄

Azure SQL Database의 하이퍼스케일 서비스 계층은 vCore 기반 구매 모델의 최신 서비스 계층입니다. 이 서비스 계층은 Azure 아키텍처를 활용하여 범용 및 중요 비즈니스용 서비스 계층에 사용할 수 있는 제한을 초과하여 Azure SQL Database용 스토리지 및 계산 리소스를 스케일 아웃하는 확장성이 뛰어난 스토리지 및 계산 성능 계층입니다.

> 
> [!NOTE]
> vCore 기반 구매 모델의 범용 및 중요 비즈니스용 서비스 계층에 대한 자세한 내용은 [범용](sql-database-service-tier-general-purpose.md) 및 [중요 비즈니스용](sql-database-service-tier-business-critical.md) 서비스 계층을 참조하세요. vCore 기반 구매 모델과 DTU 기반 구매 모델의 비교는 [Azure SQL Database 구매 모델 및 리소스](sql-database-service-tiers.md)를 참조하세요.


## <a name="what-are-the-hyperscale-capabilities"></a>하이퍼스케일 기능에는 무엇이 있나요?

Azure SQL Database의 하이퍼스케일 서비스 계층은 다음과 같은 추가 기능을 제공합니다.

- 최대 100TB의 데이터베이스 크기 지원
- 계산 IO에 영향을 주지 않으면서 크기에 관계없이 거의 즉각적인 데이터베이스 백업(Azure Blob 스토리지에 저장된 파일 스냅샷 기반)   
- 몇 시간 또는 며칠이 아닌 몇 분 내에(데이터베이스 작업의 규모가 아닌) 빠른 데이터베이스 복원(파일 스냅샷 기반)
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

  하이퍼스케일 컴퓨팅 단위 가격은 복제본별로 정해집니다. [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 가격은 읽기 확장 복제본에 자동으로 적용됩니다. 기본적으로 주 복제본 및 대규모 데이터베이스 별로 읽기 전용 복제본이 하나 만듭니다.  사용자 1-5에서 주를 포함 하 여 복제본의 총 수를 조정할 수 있습니다.

- **저장소**:

  하이퍼스케일 데이터베이스를 구성하는 경우 최대 데이터 크기를 지정할 필요가 없습니다. 하이퍼스케일 계층에서는 실제 사용량에 따라 데이터베이스의 저장소에 대한 요금이 청구됩니다. 저장소는 10GB와 40GB 동적으로 조정 되는 단위로 10GB와 100TB를 자동으로 할당 됩니다.  

하이퍼스케일 가격 책정에 대한 자세한 내용은 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/)을 참조하세요.

## <a name="distributed-functions-architecture"></a>분산 함수 아키텍처

모든 데이터 관리 기능을 하나의 위치/프로세스에 중앙 집중식으로 배치하는 기존 데이터베이스 엔진과 달리(소위 말하는 프로덕션의 분산 데이터베이스에도 모놀리식 데이터 엔진의 여러 복사본이 있음), 하이퍼스케일 데이터베이스는 여러 데이터 엔진의 의미 체계가 구분되어 있는 쿼리 처리 엔진을 데이터에 대한 장기 저장 및 지속성을 제공하는 구성 요소와 구분합니다. 이러한 방식으로 저장소 용량을 필요한 만큼 원활하게 스케일 아웃할 수 있습니다(초기 목표는 100TB). 읽기 전용 복제본 데이터 복사본이 새 읽기 가능한 복제본을 스핀업 해야 하므로 동일한 저장소 구성 요소를 공유 합니다. 

다음 다이어그램은 하이퍼스케일 데이터베이스에 있는 여러 유형의 노드를 보여 줍니다.

![아키텍처](./media/sql-database-hyperscale/hyperscale-architecture.png)

하이퍼스케일 데이터베이스에는 다음과 같은 다른 유형의 노드가 포함됩니다.

### <a name="compute-node"></a>Compute 노드

계산 노드는 관계형 엔진이 존재하는 위치이므로 모든 언어 요소, 쿼리 처리 등이 발생합니다. 하이퍼스케일 데이터베이스와의 모든 사용자 상호 작용은 이러한 계산 노드를 통해 발생합니다. 계산 노드에는 데이터 페이지를 가져오는 데 필요한 네트워크 왕복 횟수를 최소화하기 위해 SSD 기반 캐시[이전 다이어그램에 나오는 RBPEX(Resilient Buffer Pool Extension)]가 있습니다. 모든 읽기/쓰기 워크로드 및 트랜잭션이 처리되는 기본 계산 노드가 1개 있습니다. 장애 조치(Failover)를 위해 핫 대기 노드의 역할을 할 뿐만 아니라 읽기 워크로드를 오프로드하기 위한 읽기 전용 계산 노드(이 기능이 필요한 경우)의 역할을 하는 하나 이상의 보조 계산 노드도 있습니다.

### <a name="page-server-node"></a>페이지 서버 노드

페이지 서버는 스케일 아웃된 저장소 엔진을 나타내는 시스템입니다.  각 페이지 서버는 데이터베이스의 페이지 하위 집합을 담당합니다.  명목상 128GB 사이의 1TB 데이터의 각 페이지 서버 컨트롤입니다. 데이터는 둘 이상의 (중복 및 가용성을 위해 유지되는 복제본 외부) 페이지 서버에서 공유되지 않습니다. 페이지 서버의 업무는 요청 시 계산 노드에 데이터베이스 페이지를 제공하고, 트랜잭션으로 인해 데이터가 업데이트될 때 페이지를 최신 상태로 유지하는 것입니다. 페이지 서버는 로그 서비스의 로그 레코드를 재생하여 최신 상태로 유지됩니다. 또한 페이지 서버는 성능 향상을 위해 SSD 기반 캐시를 유지 관리합니다. 데이터 페이지의 장기 스토리지는 추가 안정성을 위해 Azure Storage에 보관됩니다.

### <a name="log-service-node"></a>로그 서비스 노드

로그 서비스 노드는 기본 계산 노드의 로그 레코드를 수락하고, 지속형 캐시에 보관하고, 로그 레코드를 나머지 계산 노드(계산 노드에서 캐시를 업데이트할 수 있음) 뿐만 아니라 관련 페이지 서버에 전달하므로 이 서버에서 데이터가 업데이트될 수 있습니다. 이러한 방식으로 기본 계산 노드의 모든 데이터 변경 내용은 로그 서비스를 통해 모든 보조 계산 노드 및 페이지 서버로 전파됩니다. 마지막으로 로그 레코드는 무한 스토리지 리포지토리에 해당하는 Azure Storage의 장기 스토리지로 푸시됩니다. 이 메커니즘은 로그를 자주 잘라낼 필요를 없애줍니다. 로그 서비스에도 액세스 속도를 높이기 위한 로컬 캐시가 있습니다.

### <a name="azure-storage-node"></a>Azure Storage 노드

Azure Storage 노드는 페이지 서버에 있는 데이터의 최종 대상입니다. 이 저장소는 Azure 지역 간의 복제뿐만 아니라 백업용으로 사용됩니다. 백업은 데이터 파일의 스냅샷으로 구성됩니다. 복원 작업은 이러한 스냅샷에서 빠르게 수행되며 데이터는 특정 시점으로 복원될 수 있습니다.

## <a name="backup-and-restore"></a>Backup 및 복원

백업은 파일 스냅숏을 기준으로 하므로 거의 즉각적입니다. 스토리지 및 계산 분리를 사용하면 백업/복원 작업을 스토리지 계층으로 밀어넣어 기본 계산 노드의 처리 부담을 줄일 수 있습니다. 따라서 대형 데이터베이스의 백업은 기본 계산 노드의 성능에 영향을 미치지 않습니다. 마찬가지로, 복원도 파일 스냅샷을 복사하여 수행되므로 데이터 작업의 규모는 아닙니다. 동일한 저장소 계정 내에서 수행되는 복원의 경우 복원 작업은 빠르게 수행됩니다.

## <a name="scale-and-performance-advantages"></a>확장 및 성능상의 이점

추가 읽기 전용 계산 노드를 신속하게 스핀업/스핀다운하는 기능을 사용하여 하이퍼스케일 아키텍처는 상당한 읽기 기능을 허용하며 더 많은 쓰기 요청을 제공하기 위해 기본 계산 노드를 해제할 수도 있습니다. 또한 하이퍼스케일 아키텍처의 공유 스토리지 아키텍처로 인해 계산 노드 규모를 빠르게 확대/축소할 수 있습니다.

## <a name="create-a-hyperscale-database"></a>하이퍼스케일 데이터베이스 만들기

하이퍼스케일 데이터베이스는 [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) 또는 [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)를 사용하여 만들 수 있습니다. 하이퍼스케일 데이터베이스는 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 사용해야만 사용이 가능합니다.

다음 T-SQL 명령은 하이퍼스케일 데이터베이스를 생성합니다. `CREATE DATABASE` 문에 버전 및 서비스 목표를 둘 다 지정해야 합니다.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
4 개 코어를 사용 하 여 5 세대 하드웨어에서 대규모 데이터베이스가 만들어집니다.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>기존 Azure SQL Database를 하이퍼스케일 서비스 계층으로 마이그레이션

[Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) 또는 [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)를 사용하여 기존 Azure SQL 데이터베이스를 하이퍼스케일로 이동할 수 있습니다. 이때 단방향 마이그레이션입니다. 하이퍼스케일에서 또 다른 서비스 계층으로 데이터베이스를 이동할 수는 없습니다. 프로덕션 데이터베이스의 복사본을 만들어서 POC(개념 증명)용으로 하이퍼스케일에 마이그레이션하는 것이 좋습니다.

다음 T-SQL 명령은 하이퍼스케일 서비스 계층으로 데이터베이스를 이동합니다. `ALTER DATABASE` 문에 버전 및 서비스 목표를 둘 다 지정해야 합니다.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>하이퍼스케일 데이터베이스의 읽기 확장 복제본에 연결

하이퍼스케일 데이터베이스에서 클라이언트가 제공한 연결 문자열의 `ApplicationIntent` 인수는 연결이 쓰기 복제본으로 라우팅되는지 또는 읽기 전용 보조 복제본으로 라우팅되는지를 나타냅니다. `ApplicationIntent`가 `READONLY`로 설정되고 데이터베이스에 보조 복제본이 없는 경우에는 연결이 주 복제본으로 라우팅되고 기본값은 `ReadWrite` 동작으로 설정됩니다.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```
## <a name="disaster-recovery-for-hyperscale-databases"></a>대규모 데이터베이스에 대 한 재해 복구
### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>다른 지역에 대규모 데이터베이스를 복원합니다.
Azure SQL Database 대규모 DB와 재해 복구 작업 또는 드릴, 재배치 또는 기타 다른 이유로의 일부로 현재 호스트에서 다른 지역으로 복원 해야 할 경우 기본 메서드는 데이터베이스의 지역 복원을 수행 됩니다.  이 단계를 정확 하 게 동일한으로 다른 지역으로 AZURE의 SQL DB를 복원 하려면 사용 합니다.
1. 이미 없는 적절 한 서버 있는 경우에 대상 지역에 SQL Database 서버를 만듭니다.  이 서버 원본 (원본) 서버와 동일한 구독에서 소유 해야 합니다.
2. 지침에 따라 합니다 [지역에서 복원은](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) Azure SQL Database 자동 백업에서 복원 페이지의 항목입니다.

#### <a name="notes-on-geo-restores-of-a-hyperscale-database"></a>대규모 데이터베이스의 지역에서 복원은 대 한 참고 사항
별도 하위 지역에서 원본 및 대상 이기 때문에 데이터베이스는 매우 빠르게 완료 되지 않은 지역 복원 하면 원본 데이터베이스를 사용 하 여 스냅숏 저장소를 공유할 수 없습니다.  대규모 데이터베이스의 지역 복원의 경우 대상 지역에서 복제 된 저장소의 쌍을 이루는 지역의 경우에 크기의 데이터 작업을 해당 됩니다.  즉,이 지역 복원을 수행 하는 시간이 복원할 데이터베이스의 크기에 비례 합니다.  대상 쌍을 이루는 지역에 있으면 복사 될 장거리 복사본 보다 훨씬 빠릅니다 인터넷을 통해, 데이터 센터 내에서 수 있지만 모든 비트를 계속 복사 합니다.

## <a name=regions></a>사용 가능한 지역

Azure SQL Database 대규모 계층은 다음 지역에서 현재 사용할 수 있습니다.

- 오스트레일리아 동부
- 오스트레일리아 남동부
- 브라질 남부
- 캐나다 중부
- 미국 중부
- 중국 동부 2
- 중국 북부 2
- 동아시아
- 미국 동부
- East Us 2
- 프랑스 중부
- 일본 동부
- 일본 서부
- 한국 중부
- 한국 남부
- 미국 중북부
- 유럽 북부
- 남아프리카 북부
- 미국 중남부
- 동남아시아
- 영국 남부
- 영국 서부
- 서유럽
- 미국 서부
- 미국 서부 2

지원 되는 나열 되지 않은 지역에서 대규모 데이터베이스를 만들려면 Azure portal 통해 온 보 딩 요청을 보낼 수 있습니다. 목록을 확장 하는 중 지원 되는 지역에 있으므로 최신 지역 목록 확인 하시기 바랍니다.

나열 되지 않은 지역에서 대규모 데이터베이스를 만들 수 있는 기능을 요청:

1. 이동할 [Azure 도움말 및 지원 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

2. 클릭할 [ **새 지원 요청**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Azure 도움말 및 지원 블레이드](media/sql-database-service-tier-hyperscale/whitelist-request-screen-1.png)

3. 에 대 한 **문제 유형**, 선택 **서비스 및 구독 제한 (할당량)**

4. 데이터베이스를 만드는 데 사용할 수는 구독 선택

5. 에 대 한 **할당량 유형**, 선택 **SQL database**

6. **다음: 솔루션**

1. 클릭 **세부 정보를 제공 합니다.**

    ![문제 세부 정보](media/sql-database-service-tier-hyperscale/whitelist-request-screen-2.png)

8. 선택할 **SQL Database 할당량 유형**: **다른 할당량 요청**

9. 다음 템플릿을 입력 합니다.

    ![할당량 정보](media/sql-database-service-tier-hyperscale/whitelist-request-screen-3.png)

    템플릿에서 다음 정보를 제공 합니다.

    > 새 지역에서 대규모 SQL Database를 만드는 요청<br/> 지역: [요청한 지역을 입력]  <br/>
    > 읽기 가능한 복제본을 포함 하 여 SKU/총 코어를 계산 합니다. <br/>
    > TB 예상 개수 
    >

10. **심각도 C**를 선택합니다.

11. 적절 한 연락처 메서드를 선택 하 고 세부 정보를 입력 합니다.

12. 클릭 **저장할** 고 **계속**

## <a name="known-limitations"></a>알려진 제한 사항
조지아부터 대규모 서비스 계층으로 현재 제한 사항은  이러한 제한 사항을 최대한 많은 제거할 위해 적극적으로 노력 합니다.

| 문제 | 설명 |
| :---- | :--------- |
| 논리 서버의 백업 관리 창에 SQL 서버에서 필터링된 하이퍼스케일 데이터베이스가 표시되지 않음  | 하이퍼스케일에는 백업을 관리하는 별도의 방법이 있기 때문에 장기 보존 및 특정 시점 백업 보존 설정이 적용되지 않고 무효화됩니다. 따라서 하이퍼스케일 데이터베이스가 백업 관리 창에 나타나지 않습니다. |
| 지정 시간 복원 | 데이터베이스가 하이퍼스케일 서비스 계층에 마이그레이션되고 나면 마이그레이션 이전에 지정 시간으로 복원이 지원되지 않습니다.|
| 비-대규모 DB의 Hypserscale를 반대로 복원 | 비 대규모 데이터베이스로 대규모 데이터베이스를 복원할 수 없습니다도 아닌 대규모 데이터베이스 대규모 데이터베이스를 복원할 수 있습니다.|
| 활성 워크로드로 인해 마이그레이션 중에 데이터베이스 파일이 커져서 파일 경계당 1TB를 넘으면 마이그레이션이 실패합니다. | 해결 방법: <br> - 가능하면 실행 중인 업데이트 워크로드가 없을 때 데이터베이스를 마이그레이션합니다.<br> - 마이그레이션을 다시 시도합니다. 마이그레이션 중에 1TB 경계를 초과하지만 않으면 성공합니다.|
| 관리되는 인스턴스 | Azure SQL Database Managed Instance 대규모 데이터베이스를 사용 하 여 현재 지원 되지 않습니다. |
| 탄력적 풀 |  탄력적 풀 데이터베이스 대규모 SQL을 사용 하 여 현재 지원 되지 않습니다.|
| 하이퍼스케일로 마이그레이션은 현재 단방향 작업입니다. | 데이터베이스가 하이퍼스케일로 마이그레이션되고 나면 하이퍼스케일이 아닌 서비스 계층에 직접 마이그레이션할 수 없습니다. 현재 하이퍼스케일에서 하이퍼스케일이 아닌 곳으로 데이터베이스를 마이그레이션하는 유일한 방법은 BACPAC 파일을 사용하여 내보내기/가져오기를 수행하는 것 입니다.|
| 메모리 내 개체를 사용 하 여 데이터베이스 마이그레이션 | 데이터베이스를 하이퍼스케일 서비스 계층으로 마이그레이션하기 전에 메모리 내 개체를 삭제하고 메모리 내 개체가 아닌 개체로 다시 만들어야 합니다.|
| 변경 데이터 추적 | 대규모 데이터베이스를 사용 하 여 변경 데이터 추적을 사용할 수 없습니다. |
| 지역에서 복제  | 아직 Azure SQL Database 대규모에 대 한 지역에서 복제를 구성할 수 없습니다.  지역에서 복원은 (DR 또는 기타 목적을 위해 다른 지역에서 데이터베이스 복원)를 수행할 수 있습니다. |
| TDE/AKV 통합 | Transparent Database Encryption (Bring Your-Own Key 또는 BYOK 라고 함)는 Azure Key Vault를 사용 하 여 아직 지원 되지 않습니다 Azure SQL Database에 하이퍼 스케일에 대 한 서비스 관리 키를 사용 하 여 TDE를 완벽 하 게 사용할 수 있지만. |
|지능형 데이터베이스 기능 | 1. Drop Index 고문 대규모 Db에 대 한 모델 익숙하지 않은 인덱스를 만듭니다. <br/>2. 스키마 문제, DbParameterization-최근에 추가 된 대규모 데이터베이스에 대 한 관리자를 사용할 수 없습니다.|



## <a name="next-steps"></a>다음 단계

- 하이퍼스케일에 대한 FAQ는 [하이퍼스케일에 대한 질문과 대답](sql-database-service-tier-hyperscale-faq.md)을 참조하세요.
- 서비스 계층에 대한 자세한 내용은 [서비스 계층](sql-database-service-tiers.md)을 참조하세요.
- 서버 및 구독 수준의 한도에 관한 정보는 [논리 서버의 리소스 한도 개요](sql-database-resource-limits-logical-server.md)를 참조하세요.
- 단일 데이터베이스에 대한 구매 모델 제한은 [단일 데이터베이스에 대한 Azure SQL Database vCore 기반 구매 모델 제한](sql-database-vcore-resource-limits-single-databases.md)을 참조하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](sql-database-features.md)을 참조하세요.
