---
title: 인스턴스 풀(미리 보기)
description: 이 문서에서는 Azure SQL Database 인스턴스 풀(미리 보기)에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 0938fbe94cb0d1e6dae3dcb84950a11f90dd9db8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878156"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>SQL Database 인스턴스 풀(미리 보기)이란 무엇입니까?

인스턴스 풀은 Azure SQL Database의 새 리소스로, 소규모 SQL 인스턴스를 대규모로 클라우드로 마이그레이션하는 편리하고 비용 효율적인 방법을 제공합니다.

인스턴스 풀을 사용하면 총 마이그레이션 요구 사항에 따라 계산 리소스를 미리 프로비전할 수 있습니다. 그런 다음 미리 프로비전된 계산 수준까지 여러 개의 개별 관리되는 인스턴스를 배포할 수 있습니다. 예를 들어 8개의 vCore를 미리 프로비전하는 경우 2vCore 및 4vCore 인스턴스 1개를 배포한 다음 데이터베이스를 이러한 인스턴스로 마이그레이션할 수 있습니다. 인스턴스 풀을 사용할 수 있게 되기 전에는 클라우드로 마이그레이션할 때 더 작고 계산 집약적인 워크로드를 더 큰 관리형 인스턴스로 통합해야 하는 경우가 많습니다. 데이터베이스 그룹을 대규모 인스턴스로 마이그레이션해야 하는 경우 일반적으로 신중한 용량 계획 및 리소스 거버넌스, 추가 보안 고려 사항 및 인스턴스 수준에서 몇 가지 추가 데이터 통합 작업이 필요합니다.

또한 인스턴스 풀은 네이티브 VNet 통합을 지원하므로 동일한 서브넷에 여러 인스턴스 풀과 여러 단일 인스턴스를 배포할 수 있습니다.


## <a name="key-capabilities-of-instance-pools"></a>인스턴스 풀의 주요 기능

인스턴스 풀은 다음과 같은 이점을 제공합니다.

1. 2개의 vCore 인스턴스를 호스팅할 수 있는 기능입니다. *인스턴스 풀의 인스턴스에만 적합합니다. \**
2. 예측 가능하고 빠른 인스턴스 배포 시간(최대 5분).
3. 최소한의 IP 주소 할당.

다음 다이어그램은 가상 네트워크 서브넷 내에 여러 인스턴스가 배포된 인스턴스 풀을 보여 줍니다.

![여러 인스턴스가 있는 인스턴스 풀](./media/sql-database-instance-pools/instance-pools1.png)

인스턴스 풀을 사용하면 가상 시스템의 계산 크기가 풀에 할당된 총 vCore수에 따라 동일한 가상 시스템에 여러 인스턴스를 배포할 수 있습니다. 이 아키텍처를 사용하면 가상 컴퓨터를 여러 인스턴스로 *분할할* 수 있으며, 이 중 2개의 vCore를 포함하여 지원되는 크기일 수 있습니다(2vCore 인스턴스는 풀의 인스턴스에서만 사용할 수 있음).

풀이 처음 배포되면 풀의 인스턴스에 대한 관리 작업이 훨씬 빠릅니다. 이러한 작업은 [관리되는](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) 인스턴스프로비저닝의 일부가 아닌 가상 클러스터(가상 시스템의 전용 집합)의 배포 또는 확장이 더 빠릅니다.

풀의 모든 인스턴스가 동일한 가상 컴퓨터를 공유하기 때문에 총 IP 할당은 배포된 인스턴스 수에 의존하지 않으므로 IP 범위가 좁은 서브넷에 배포하는 데 편리합니다.

각 풀에는 9개의 IP 주소만 고정된 IP 할당이 있습니다(자체 필요에 따라 예약된 서브넷에 5개의 IP 주소 는 포함되지 않음). 자세한 내용은 [단일 인스턴스에 대한 서브넷 크기 요구 사항을](sql-database-managed-instance-determine-size-vnet-subnet.md)참조하십시오.

## <a name="application-scenarios-for-instance-pools"></a>인스턴스 풀을 위한 응용 프로그램 시나리오

다음 목록은 인스턴스 풀을 고려해야 하는 주요 사용 사례를 제공합니다.

- SQL *인스턴스 그룹을* 동시에 마이그레이션하는 데 는 대다수가 크기(예: 2 또는 4 vCores)가 더 작습니다.
- 예측 *가능하고 짧은 인스턴스 생성 또는 크기 조정이* 중요한 시나리오입니다. 예를 들어 인스턴스 수준 기능이 필요한 다중 테넌트 SaaS 응용 프로그램 환경에서 새 테넌트를 배포합니다.
- 고정 된 *비용* 또는 *지출 한도가* 중요한 시나리오입니다. 예를 들어, 필요할 때 관리되는 인스턴스를 주기적으로 배포하는 고정(또는 자주 변경되지 않는) 크기의 공유 개발 테스트 또는 데모 환경을 실행합니다.
- VNet 서브넷에서 *최소한의 IP 주소 할당이* 중요한 시나리오입니다. 풀의 모든 인스턴스는 가상 컴퓨터를 공유하므로 할당된 IP 주소 수가 단일 인스턴스의 경우보다 낮습니다.


## <a name="architecture-of-instance-pools"></a>인스턴스 풀의 아키텍처

인스턴스 풀은 일반 관리 인스턴스(단일 인스턴스)와 유사한*아키텍처를*갖습니다.  Azure [가상 네트워크(VNet) 내의 배포를](../virtual-network/virtual-network-for-azure-services.md)지원하고 고객에게 격리 및 보안을 제공하기 위해 인스턴스 풀도 [가상 클러스터를](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)사용합니다. 가상 클러스터는 고객의 가상 네트워크 서브넷 내에 배포된 격리된 가상 시스템의 전용 집합을 나타냅니다.

두 배포 모델의 주요 차이점은 인스턴스 풀을 통해 동일한 가상 시스템 노드에서 여러 SQL Server 프로세스 배포를 허용하고 Windows [Job Objects를](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects)사용하여 리소스가 관리되는 반면 단일 인스턴스는 항상 가상 시스템 노드에서 혼자 진행됩니다.

다음 다이어그램에서는 인스턴스 풀과 동일한 서브넷에 배포된 두 개의 개별 인스턴스를 보여 주며 두 배포 모델의 주요 아키텍처 세부 정보를 보여 줍니다.

![인스턴스 풀과 두 개의 개별 인스턴스](./media/sql-database-instance-pools/instance-pools2.png)

모든 인스턴스 풀은 아래에 별도의 가상 클러스터를 만듭니다. 풀 내의 인스턴스와 동일한 서브넷에 배포된 단일 인스턴스는 SQL Server 프로세스 및 게이트웨이 구성 요소에 할당된 컴퓨팅 리소스를 공유하지 않으며, 이를 통해 성능 예측 가능성을 보장합니다.

## <a name="instance-pools-resource-limitations"></a>인스턴스 풀 리소스 제한

인스턴스 풀 및 풀 내의 인스턴스에 대한 몇 가지 리소스 제한이 있습니다.

- 인스턴스 풀은 Gen5 하드웨어에서만 사용할 수 있습니다.
- 풀 내의 인스턴스에는 전용 CPU와 RAM이 있으므로 모든 인스턴스에서 집계된 vCore 수보다 적거나 풀에 할당된 vCore수와 같아야 합니다.
- 모든 [인스턴스 수준 제한은](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) 풀 내에서 생성된 인스턴스에 적용됩니다.
- 인스턴스 수준 제한 외에도 *인스턴스 풀 수준에서*두 가지 제한이 부과됩니다.
  - 풀당 총 스토리지 크기(8TB).
  - 풀당 총 데이터베이스 수(100개)입니다.

모든 인스턴스의 총 저장소 할당 및 데이터베이스 수는 인스턴스 풀에서 노출되는 제한보다 낮거나 같아야 합니다.

- 인스턴스 풀은 8, 16, 24, 32, 40, 64 및 80 vCores를 지원합니다.
- 풀 내에서 관리되는 인스턴스는 2, 4, 8, 16, 24, 32, 40, 64 및 80 vCores를 지원합니다.
- 풀 내에서 관리되는 인스턴스는 다음을 제외하고 32GB에서 8TB 사이의 저장소 크기를 지원합니다.
  - 2 vCore 인스턴스는 32GB에서 640GB 사이의 크기를 지원합니다.
  - 4 vCore 인스턴스는 32GB에서 2TB 사이의 크기를 지원합니다.

[서비스 계층 속성은](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) 인스턴스 풀 리소스와 연결되므로 풀의 모든 인스턴스는 풀의 서비스 계층과 동일한 서비스 계층이어야 합니다. 현재범용 서비스 계층만 사용할 수 있습니다(현재 미리 보기의 제한 사항에 대한 다음 섹션 참조).

### <a name="public-preview-limitations"></a>공용 미리 보기 제한 사항

공개 미리 보기에는 다음과 같은 제한 사항이 있습니다.

- 현재범용 서비스 계층만 사용할 수 있습니다.
- 인스턴스 풀은 공개 미리 보기 중에 확장할 수 없으므로 배포 전에 신중한 용량 계획이 중요합니다.
- 인스턴스 풀 생성 및 구성에 대한 Azure 포털 지원은 아직 사용할 수 없습니다. 인스턴스 풀의 모든 작업은 PowerShell을 통해서만 지원됩니다. 미리 만들어진 풀의 초기 인스턴스 배포는 PowerShell을 통해서만 지원됩니다. 풀에 배포되면 Azure 포털을 사용하여 관리되는 인스턴스를 업데이트할 수 있습니다.
- 풀 외부에서 생성된 관리되는 인스턴스는 기존 풀로 이동할 수 없으며 풀 내에서 생성된 인스턴스는 외부로 단일 인스턴스 또는 다른 풀로 이동할 수 없습니다.
- 예약 인스턴스 가격(라이선스 포함 또는 Azure 하이브리드 혜택 포함)은 사용할 수 없습니다.

## <a name="sql-features-supported"></a>지원되는 SQL 기능

풀에서 생성된 인스턴스는 [단일 관리 인스턴스에서 지원되는](sql-database-managed-instance.md#sql-features-supported)동일한 호환성 수준 및 기능을 지원합니다.

풀에 배포된 모든 관리형 인스턴스에는 별도의 SQL 에이전트 인스턴스가 있습니다.

특정 값(예: 인스턴스 수준 데이터 정렬, 표준 시간대, 데이터 트래픽에 대한 공용 끝점, 장애 조치 그룹)을 선택해야 하는 선택적 기능 또는 기능은 인스턴스 수준에서 구성되며 풀의 각 인스턴스마다 다를 수 있습니다.

## <a name="performance-considerations"></a>성능 고려 사항

풀 내에서 관리되는 인스턴스에는 전용 vCore 및 RAM이 있지만 로컬 디스크(tempdb 사용)와 네트워크 리소스를 공유합니다. 가능성은 없지만 풀의 여러 인스턴스가 동시에 리소스 소비량이 많은 경우 *시끄러운 이웃* 효과를 경험할 수 있습니다. 이 동작을 관찰하는 경우 이러한 인스턴스를 더 큰 풀또는 단일 인스턴스로 배포하는 것이 좋습니다.

## <a name="security-considerations"></a>보안 고려 사항

풀에 배포된 인스턴스는 동일한 가상 컴퓨터를 공유하므로 보안 위험이 높아지는 기능을 사용하지 않도록 설정하거나 이러한 기능에 대한 액세스 권한을 확실하게 제어하는 것이 좋습니다. 예를 들어 CLR 통합, 기본 백업 및 복원, 데이터베이스 전자 메일 등

## <a name="instance-pool-support-requests"></a>인스턴스 풀 지원 요청

[Azure 포털에서](https://portal.azure.com)인스턴스 풀에 대한 지원 요청을 만들고 관리합니다.

인스턴스 풀 배포(생성 또는 삭제)와 관련된 문제가 발생하는 경우 **문제 하위 유형** 필드에 인스턴스 **풀을** 지정해야 합니다.

![인스턴스 풀 지원 요청](./media/sql-database-instance-pools/support-request.png)

풀 내에서 단일 인스턴스 또는 데이터베이스와 관련된 문제가 발생하는 경우 Azure SQL Database 관리 형 인스턴스에 대한 일반 지원 티켓을 만들어야 합니다.

인스턴스 풀유무이또는없는 더 큰 관리형 인스턴스 배포를 만들려면 더 큰 지역 할당량을 얻어야 할 수 있습니다. 자세한 내용은 [Azure SQL 데이터베이스에 대한 할당량 증가를](quota-increase-request.md)요청합니다. 인스턴스 풀을 사용하는 경우 배포 논리는 *풀 수준의* 총 vCore 사용량을 할당량과 비교하여 할당량을 추가로 늘리지 않고 새 리소스를 만들 수 있는지 여부를 결정합니다.

## <a name="instance-pool-billing"></a>인스턴스 풀 청구

인스턴스 풀을 사용하면 계산 및 스토리지를 독립적으로 확장할 수 있습니다. 고객은 vCores로 측정된 풀 리소스와 연결된 계산및 기가바이트단위로 측정된 모든 인스턴스와 연결된 스토리지에 대해 비용을 지불합니다(처음 32GB는 모든 인스턴스에 대해 무료임).

vCore 풀에 대한 가격은 해당 풀에 배포되는 인스턴스 수에 관계없이 청구됩니다.

계산 가격(vCore로 측정됨)의 경우 두 가지 가격 옵션을 사용할 수 있습니다.

  1. *라이센스 포함*: SQL 라이센스의 가격이 포함되어 있습니다. 이는 소프트웨어 보증을 사용하여 기존 SQL Server 라이선스를 적용하지 않기로 선택한 고객을 위한 것입니다.
  2. *Azure 하이브리드 혜택*: SQL Server에 대한 Azure 하이브리드 이점을 포함하는 할인된 가격입니다. 고객은 소프트웨어 보증을 사용하여 기존 SQL Server 라이선스를 사용하여 이 가격을 선택할 수 있습니다. 자격 및 기타 세부 정보는 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 참조하십시오.

풀의 개별 인스턴스에는 다른 가격 옵션을 설정할 수 없습니다. 상위 풀의 모든 인스턴스는 라이선스 포함 가격 또는 Azure 하이브리드 혜택 가격이어야 합니다. 풀을 만든 후 풀에 대한 라이센스 모델을 변경할 수 있습니다.

> [!IMPORTANT]
> 풀과 다른 인스턴스에 대한 라이센스 모델을 지정하면 풀 가격이 사용되고 인스턴스 수준 값이 무시됩니다.

[개발 테스트 혜택을 받을 수 있는 구독에](https://azure.microsoft.com/pricing/dev-test/)인스턴스 풀을 만드는 경우 Azure SQL 관리 인스턴스에서 최대 55%의 할인 요금이 자동으로 적용됩니다.

인스턴스 풀 가격에 대한 자세한 내용은 [관리되는 인스턴스 가격 책정 페이지의](https://azure.microsoft.com/pricing/details/sql-database/managed/)인스턴스 *풀* 섹션을 참조하십시오.

## <a name="next-steps"></a>다음 단계

- 인스턴스 풀을 시작하려면 [SQL Database 인스턴스 풀 방법 가이드를](sql-database-instance-pools-how-to.md)참조하십시오.
- 첫 번째 관리인스턴스를 만드는 방법에 대해 알아보려면 [빠른 시작 가이드를](sql-database-managed-instance-get-started.md)참조하십시오.
- 기능 및 비교 목록은 [SQL 일반 기능](sql-database-features.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [관리형 인스턴스 VNet 구성을](sql-database-managed-instance-connectivity-architecture.md)참조하십시오.
- 관리되는 인스턴스를 만들고 백업 파일에서 데이터베이스를 복원하는 빠른 시작의 경우 [관리되는 인스턴스 만들기를](sql-database-managed-instance-get-started.md)참조하십시오.
- 마이그레이션을 위해 Azure 데이터베이스 마이그레이션 서비스(DMS)를 사용하는 자습서의 경우 [DMS를 사용하여 관리되는 인스턴스 마이그레이션을](../dms/tutorial-sql-server-to-managed-instance.md)참조하십시오.
- 기본 제공 문제 해결 인텔리전스를 사용하여 관리되는 인스턴스 데이터베이스 성능에 대한 고급 모니터링은 [Azure SQL Analytics를 사용하여 Azure SQL Database 모니터를](../azure-monitor/insights/azure-sql.md)참조하십시오.
- 가격 정보는 [SQL Database 관리 인스턴스 가격 책정을](https://azure.microsoft.com/pricing/details/sql-database/managed/)참조하십시오.
