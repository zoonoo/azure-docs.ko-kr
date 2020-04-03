---
title: SQL 관리 인스턴스 개요
description: 이 문서에서는 Azure SQL Database 관리 인스턴스에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 04/02/2020
ms.openlocfilehash: 06242af6cb00e3adebbc80da722898fb8e348e36
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585367"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Azure SQL Database 관리 인스턴스란 무엇입니까?

Managed Instance는 Azure SQL Database의 새로운 배포 옵션입니다. 최신 SQL Server 온-프레미스(Enterprise Edition) 데이터베이스 엔진과 거의 100% 호환되며, 일반적인 보안 문제를 해결하는 원시 [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 구현과 온-프레미스 SQL Server 고객이 편리하게 사용할 수 있는 [비즈니스 모델](https://azure.microsoft.com/pricing/details/sql-database/)을 제공합니다. Managed Instance 배포 모델을 사용하면 기존 SQL Server 고객은 애플리케이션 및 데이터베이스 변경을 최소화하고 온-프레미스 애플리케이션을 클라우드로 이동할 수 있습니다. 이와 동시에 Managed Instance 배포 옵션은 모든 PaaS 기능(자동 패치 및 버전 업데이트, [자동화된 백업](sql-database-automated-backups.md), [고가용성](sql-database-high-availability.md))을 유지하므로 관리 오버헤드와 TCO를 크게 줄일 수 있습니다.

> [!IMPORTANT]
> Managed Instance 배포 옵션을 현재 사용할 수 있는 지역의 목록은 [지원되는 지역](sql-database-managed-instance-resource-limits.md#supported-regions)을 참조하세요.

다음 다이어그램은 Managed Instance의 주요 기능을 설명합니다.

![주요 기능](./media/sql-database-managed-instance/key-features.png)

Managed Instance 배포 모델은 가능한 한 적은 마이그레이션 작업을 통해 많은 수의 앱을 온-프레미스 또는 IaaS, 자체 구축 또는 ISV 제공 환경에서 완전히 관리되는 PaaS 클라우드 환경으로 마이그레이션하려는 고객을 위해 설계되었습니다. 고객은 Azure에서 완전히 자동화된 [DMS(데이터 마이그레이션 서비스)를](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) 사용하여 온-프레미스 SQL Server와의 호환성을 제공하고 네이티브 VNet 지원을 통해 고객 인스턴스를 완전히 격리하는 관리형 인스턴스로 온-프레미스 SQL Server를 들어 올립니다.  소프트웨어 보증을 사용하면 [SQL Server에 대한 Azure 하이브리드 혜택을](https://azure.microsoft.com/pricing/hybrid-benefit/)사용하여 관리되는 인스턴스에서 기존 라이선스를 할인된 요금으로 교환할 수 있습니다.  Managed Instance는 클라우드에서 높은 보안과 풍부한 프로그래밍 기능 영역이 필요한 SQL Server 인스턴스를 위한 최상의 마이그레이션 대상입니다.

Managed Instance 배포 옵션의 목표는 단계별 릴리스 계획을 통해 최신 온-프레미스 SQL Server 버전과 거의 100% 호환되는 노출 영역 호환성을 제공하는 것입니다.

Azure SQL Database 배포 옵션인 단일 데이터베이스, 풀링된 데이터베이스, 관리되는 인스턴스 및 가상 머신에서 호스트되는 SQL Server 중에서 결정하려면 [Azure에서 올바른 SQL Server 버전을 선택하는 방법](sql-database-paas-vs-sql-server-iaas.md)을 참조하세요.

## <a name="key-features-and-capabilities"></a>주요 특징 및 기능

Managed Instance는 Azure SQL Database와 SQL Server 데이터베이스 엔진 모두에서 사용할 수 있는 최상의 기능을 결합합니다.

> [!IMPORTANT]
> Managed Instance에는 온라인 작업, 자동 계획 수정 및 기타 엔터프라이즈 성능 향상을 비롯한 SQL Server 최신 버전의 모든 기능이 실행됩니다. 제공되는 기능에 대한 비교는 [기능 비교: Azure SQL Database 및 SQL Server](sql-database-features.md)를 참조하세요.

| **PaaS의 이점** | **비즈니스 연속성** |
| --- | --- |
|하드웨어를 구입하고 관리할 필요가 없음 <br>기본 인프라 관리를 위한 오버헤드가 없음 <br>신속한 프로비전 및 서비스 크기 조정 <br>자동 패치 적용 및 버전 업그레이드 <br>다른 PaaS 데이터 서비스와 통합 |99.99% 작동 시간 SLA  <br>기본 제공 [고가용성](sql-database-high-availability.md) <br>[자동화된 백업](sql-database-automated-backups.md)으로 보호되는 데이터 <br>고객이 구성 가능한 백업 보존 기간 <br>사용자가 시작하는 [백업](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[특정 시점 데이터베이스 복원](sql-database-recovery-using-backups.md#point-in-time-restore) 기능 |
|**보안 및 규정 준수** | **관리**|
|격리된[환경(VNet 통합,](sql-database-managed-instance-connectivity-architecture.md)단일 테넌트 서비스, 전용 컴퓨팅 및 스토리지) <br>[투명 데이터 암호화(TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD 인증](sql-database-aad-authentication.md), Single Sign-On 지원 <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD 서버 주체(로그인)</a>  <br>Azure SQL 데이터베이스와 동일한 표준 준수 <br>[SQL 감사](sql-database-managed-instance-auditing.md) <br>[고급 위협 보호](sql-database-managed-instance-threat-detection.md) |서비스 프로비전 및 크기 조정을 자동화하는 Azure Resource Manager API <br>수동 서비스 프로비전 및 크기 조정을 위한 Azure Portal 기능 <br>데이터 마이그레이션 서비스

> [!IMPORTANT]
> Azure SQL Database(모든 배포 옵션)는 여러 규정 준수 표준에 대해 인증되었습니다. 자세한 내용은 최신 SQL 데이터베이스 준수 인증 목록을 찾을 수 있는 [Microsoft Azure 트러스트 센터를](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 참조하십시오.

Managed Instance의 주요 기능을 다음 표에서 볼 수 있습니다.

|기능 | Description|
|---|---|
| SQL Server 버전/빌드 | SQL Server 데이터베이스 엔진(안정적인 최신 버전) |
| 관리되는 자동 백업 | 예 |
| 기본 제공 인스턴스/데이터베이스 모니터링 및 메트릭 | 예 |
| 자동 소프트웨어 패치 | 예 |
| 최신 데이터베이스 엔진 기능 | 예 |
| 데이터베이스당 데이터 파일(행) 수 | 여러 접두사 |
| 데이터베이스당 로그 파일(로그) 수 | 1 |
| VNet - Azure Resource Manager 배포 | 예 |
| VNet - 클래식 배포 모델 | 예 |
| 포털 지원 | 예|
| 기본 제공 통합 서비스(SSIS) | 아니요 - SSIS는 [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)의 일부입니다. |
| 기본 제공 분석 서비스(SSAS) | 아니요 - SSAS는 별도의 [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview)입니다. |
| 기본 제공 보고 서비스(SSRS) | 아니오 - [대신 Power BI 가식 보고서를](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) 사용하거나 Azure VM에서 SSRS를 호스트합니다. 관리되는 인스턴스는 SSRS를 서비스로 실행할 수 없지만 SQL Server 인증을 사용하여 외부 보고 서버에 대한 SSRS 2019 카탈로그 데이터베이스를 호스트할 수 있습니다. |
|||

## <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

Managed Instance의 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)은 유연성, 제어 기능, 투명성 및 온-프레미스 워크로드 요구 사항을 클라우드로 전환하는 직관적인 방법을 제공합니다. 이 모델을 사용하면 워크로드 요구 사항에 따라 컴퓨팅, 메모리 및 스토리지를 선택할 수 있습니다. 또한 vCore 모델은 [SQL Server에 대한 Azure 하이브리드 혜택으로](https://azure.microsoft.com/pricing/hybrid-benefit/)최대 55% 절감혜택을 받을 수 있습니다.

vCore 모델에서는 하드웨어 세대를 선택할 수 있습니다.

- **4세대** 논리적 CPU는 인텔 E5-2673 v3(하스웰) 2.4GHz 프로세서, 연결된 SSD, 물리적 코어, 코어당 7GB RAM, 8~24vCore 사이의 크기를 계산합니다.
- **5세대** 논리적 CPU는 인텔 E5-2673 v4(브로드웰) 2.3GHz 및 인텔 SP-8160(스카이레이크) 프로세서, 빠른 NVMe SSD, 하이퍼 스레드 논리 코어 및 4~80코어 사이의 컴퓨팅 크기를 기반으로 합니다.

[관리되는 인스턴스 리소스 제한에서](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)하드웨어 세대 간의 차이점에 대해 자세히 알아보십시오.

## <a name="managed-instance-service-tiers"></a>Managed Instance 서비스 계층

관리형 인스턴스는 두 개의 서비스 계층에서 사용할 수 있습니다.

- **범용**: 일반적인 성능 및 IO 대기 시간 요구 사항이 있는 응용 프로그램을 위해 설계되었습니다.
- **비즈니스 크리티컬:** IO 대기 시간이 적고 기본 유지 관리 작업이 워크로드에 미치는 영향을 최소화한 애플리케이션을 위해 설계되었습니다.

두 서비스 계층은 모두 99.99% 가용성을 보장하며 스토리지 크기와 컴퓨팅 용량을 독립적으로 선택할 수 있습니다. Azure SQL Database의 고가용성 아키텍처에 대한 자세한 내용은 [고가용성 및 Azure SQL Database](sql-database-high-availability.md)를 참조하세요.

### <a name="general-purpose-service-tier"></a>범용 서비스 계층

다음 목록에서는 범용 서비스 계층의 주요 특징을 설명합니다.

- 일반적인 성능 요구 사항이 있는 대부분의 비즈니스 애플리케이션에 적합한 디자인
- 고성능 Azure Blob Storage(8TB)
- 신뢰할 수 있는 Azure Blob Storage 및 [Azure Service Fabric](../service-fabric/service-fabric-overview.md)에 기반한 [고가용성](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) 기본 제공

자세한 내용은 [범용 계층의 스토리지 레이어](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) 및 [Managed Instance(범용)에 대한 스토리지 성능 모범 사례 및 고려 사항](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/)을 참조하세요.

[관리되는 인스턴스 리소스 제한에서](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)서비스 계층 간의 차이점에 대해 자세히 알아보십시오.

### <a name="business-critical-service-tier"></a>중요 비즈니스 서비스 계층

중요 비즈니스용 서비스 계층은 IO 요구 사항이 높은 애플리케이션용으로 빌드됩니다. 여러 개의 격리된 복제본을 사용하여 장애에 대한 최고의 복원력을 제공합니다.

다음 목록에서는 중요 비즈니스용 서비스 계층의 주요 특징을 설명합니다.

- 최고의 성능과 HA 요구 사항을 가진 대부분의 비즈니스 애플리케이션용으로 설계됨
- 초고속 로컬 SSD 스토리지(4세대의 최대 1TB 및 5세대의 최대 4TB)와 함께 제공됨
- [항상 사용 가능 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) 및 Azure 서비스 [패브릭을](../service-fabric/service-fabric-overview.md)기반으로 하는 기본 제공 [고가용성.](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)
- 보고 및 기타 읽기 전용 워크로드에 사용할 수 있는 기본 제공 추가 [읽기 전용 데이터베이스 복제본](sql-database-read-scale-out.md)
- 고성능 요구 사항이 있는 워크로드에 사용할 수 있는 [메모리 내 OLTP](sql-database-in-memory.md)  

[관리되는 인스턴스 리소스 제한에서](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)서비스 계층 간의 차이점에 대해 자세히 알아보십시오.


## <a name="managed-instance-management-operations"></a>관리되는 인스턴스 관리 작업

Azure SQL Database에서는 관리되는 새로운 인스턴스를 자동으로 배포하고, 인스턴스 속성을 업데이트하며, 더 이상 필요하지 않은 경우 인스턴스를 삭제하는 데 사용할 수 있는 관리 작업을 제공합니다. 이 섹션에서는 관리 작업 및 일반적인 기간에 대한 정보를 제공합니다.

Azure [가상 네트워크(VNet) 내의 배포를](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) 지원하고 고객에게 격리 및 보안을 제공하기 위해 관리되는 인스턴스는 고객의 가상 네트워크 서브넷 내에 배포된 격리된 가상 시스템의 전용 집합을 나타내는 가상 [클러스터를](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)사용합니다. 기본적으로 빈 서브넷의 모든 관리형 인스턴스 배포는 새로운 가상 클러스터 빌드아웃을 초래합니다.

배포된 관리형 인스턴스에 대한 후속 작업은 기본 가상 클러스터에도 영향을 미칠 수 있습니다. 이는 추가 가상 컴퓨터를 배포하면 기존 관리형 인스턴스에 대한 새 배포 또는 업데이트를 계획할 때 고려해야 할 오버헤드가 함께 제공되기 때문에 관리 작업의 기간에 영향을 줍니다.

모든 관리 작업은 다음과 같이 분류될 수 있습니다.

- 인스턴스 배포(새 인스턴스 만들기). 
- 인스턴스 업데이트(vCores 또는 예약된 저장소와 같은 인스턴스 속성 변경)
- 인스턴스 삭제.

일반적으로 가상 클러스터의 작업은 가장 오래 소요됩니다. 가상 클러스터에서의 작업 기간은 다양합니다.

- 가상 클러스터 생성. 이 단계는 인스턴스 관리 작업의 동기 단계입니다. **작업의 90 %가 4 시간 안에 완료됩니다.**
- 가상 클러스터 크기 조정(확장 또는 축소). 확장은 동기 단계이지만 축소는 인스턴스 관리 작업의 기간에 영향을 주지 않고 비동기적으로 수행됩니다. **클러스터 확장의 90%는 2.5시간 이내에 완료됩니다.**
- 가상 클러스터 삭제. 삭제는 비동기 단계이지만 빈 가상 클러스터에서 [수동으로 시작할](sql-database-managed-instance-delete-virtual-cluster.md) 수도 있으며, 이 경우 동기적으로 실행됩니다. **가상 클러스터 삭제의 90%가 1.5시간 만에 완료됩니다.**

또한 인스턴스 관리에는 호스팅된 데이터베이스에 대한 작업 중 하나가 포함될 수 있으며, 이로 인해 기간이 더 길어질 수 있습니다.

- Azure 저장소에서 데이터베이스 파일 연결. 이 단계는 범용 서비스 계층에서 계산(vCore) 또는 저장소 확장 또는 축소와 같은 동기 단계입니다. **이러한 작업의 90 %는 5 분 안에 완료됩니다.**
- 항상 가용성 그룹 시드. 이 단계는 컴퓨팅(vCore) 또는 비즈니스 크리티컬 서비스 계층의 스토리지 확장과 같은 동기 단계이며 서비스 계층을 범용 에서 비즈니스 중요(또는 그 반대)로 변경하는 경우입니다. 이 작업의 기간은 현재 데이터베이스 활동(활성 트랜잭션 수)뿐만 아니라 총 데이터베이스 크기에 비례합니다. 인스턴스를 업데이트할 때 데이터베이스 활동은 총 기간에 상당한 차이를 유발할 수 있습니다. **이러한 작업의 90 %는 220 GB / 시간 이상에서 실행됩니다.**

다음 표에는 작업 및 일반적인 전체 기간이 요약되어 있습니다.

|Category  |작업(Operation)  |장기 실행 세그먼트  |예상 소요 시간  |
|---------|---------|---------|---------|
|**배포** |빈 서브넷의 첫 번째 인스턴스|가상 클러스터 생성|90%의 운영이 4시간 만에 완료됩니다.|
|배포 |비어 없는 서브넷에서 다른 하드웨어 생성의 첫 번째 인스턴스(예: Gen 4 인스턴스가 있는 서브넷의 첫 번째 Gen 5 인스턴스)|가상 클러스터 생성*|90%의 운영이 4시간 만에 완료됩니다.|
|배포 |빈 서브넷 또는 비어 없는 서브넷에서 4개의 vCores를 처음 생성합니다.|가상 클러스터 생성**|90%의 운영이 4시간 만에 완료됩니다.|
|배포 |비어 없는 서브넷 내의 후속 인스턴스 생성(2, 3번째 인스턴스 등)|가상 클러스터 크기 조정|2.5시간 만에 90%의 작업 완료|
|**업데이트** |인스턴스 속성 변경(관리자 암호, AAD 로그인, Azure 하이브리드 혜택 플래그)|N/A|최대 1분|
|업데이트 |인스턴스 스토리지 확장/축소(범용 서비스 계층)|데이터베이스 파일 첨부|90%의 운영이 5분 만에 완료됩니다.|
|업데이트 |인스턴스 스토리지 확장/축소(비즈니스 크리티컬 서비스 계층)|- 가상 클러스터 크기 조정<br>- 항상 가용성 그룹 시드|90%의 작업이 2.5시간 + 모든 데이터베이스를 시드하는 시간(220GB/시간)에 완료됩니다.|
|업데이트 |인스턴스 계산(vCores) 확장 및 축소(범용)|- 가상 클러스터 크기 조정<br>- 데이터베이스 파일 첨부|2.5시간 만에 90%의 작업 완료|
|업데이트 |인스턴스 계산(vCores) 확장 및 축소(비즈니스 중요)|- 가상 클러스터 크기 조정<br>- 항상 가용성 그룹 시드|90%의 작업이 2.5시간 + 모든 데이터베이스를 시드하는 시간(220GB/시간)에 완료됩니다.|
|업데이트 |인스턴스를 4vCore로 축소(범용)|- 가상 클러스터 크기 조정 (처음으로 수행되는 경우, 가상 클러스터 생성이 필요할 수 있습니다**)<br>- 데이터베이스 파일 첨부|90%의 운영 완료 4시간 5분**|
|업데이트 |인스턴스를 4vCore로 축소(비즈니스 중요)|- 가상 클러스터 크기 조정 (처음으로 수행되는 경우, 가상 클러스터 생성이 필요할 수 있습니다**)<br>- 항상 가용성 그룹 시드|작업의 90 %는 모든 데이터베이스를 시드하는 4 시간 + 시간 (220 GB / 시간)에 완료|
|업데이트 |인스턴스 서비스 계층 변경(비즈니스 크리티컬에 대한 범용 목적 및 그 반대의 경우도 마찬가지)|- 가상 클러스터 크기 조정<br>- 항상 가용성 그룹 시드|90%의 작업이 2.5시간 + 모든 데이터베이스를 시드하는 시간(220GB/시간)에 완료됩니다.|
|**삭제**|인스턴스 삭제|모든 데이터베이스에 대한 로그 테일 백업|90%의 작업이 최대 1분 만에 완료됩니다.<br>참고: 서브넷의 마지막 인스턴스가 삭제되면 이 작업은 12시간 후에 가상 클러스터 삭제를 예약합니다***|
|삭제|가상 클러스터 삭제(사용자가 시작한 작업)|가상 클러스터 삭제|90%의 작업 완료 시 최대 1.5시간|

\*가상 클러스터는 하드웨어 생성에 따라 빌드됩니다.

\*\*4 vCores 배포 옵션은 2019년 6월에 릴리스되었으며 새 가상 클러스터 버전이 필요합니다. 6월 12일 이전에 모두 생성된 대상 서브넷에 인스턴스가 있는 경우 4개의 vCore 인스턴스를 호스트하기 위해 새 가상 클러스터가 자동으로 배포됩니다.

\*\*\*12 시간은 현재 구성이지만 나중에 변경 될 수 있으므로 하드 의존성을 하지 마십시오. 예를 들어 서브넷을 해제하려면 가상 클러스터를 더 일찍 삭제해야 하는 경우 [Azure SQL Database 관리 인스턴스를 삭제한 후 서브넷 삭제를](sql-database-managed-instance-delete-virtual-cluster.md)참조하십시오.

### <a name="instance-availability-during-management"></a>관리 중 인스턴스 가용성

배포 및 삭제 작업 중에 클라이언트 응용 프로그램에서 관리되는 인스턴스를 사용할 수 없습니다.

관리되는 인스턴스는 업데이트 작업 중에 사용할 수 있지만 일반적으로 최대 10초동안 지속되는 업데이트 끝에서 발생하는 장애 조치로 인해 짧은 가동 중지 시간이 발생합니다. 예외는 장애 조치(failover)를 발생시키지 않거나 인스턴스 가용성에 영향을 주지 않는 범용 서비스 계층의 예약된 저장소 공간의 업데이트입니다.

> [!IMPORTANT]
> 장애 조치 기간은 [복구 시간이 길어](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process)데이터베이스에서 발생하는 장기 실행 트랜잭션의 경우 크게 다를 수 있습니다. 따라서 Azure SQL Database 관리 인스턴스의 계산 또는 저장소를 확장하거나 장기 실행 트랜잭션(데이터 가져오기, 데이터 처리 작업, 인덱스 다시 작성 등)을 사용하여 동시에 서비스 계층을 변경하는 것은 권장되지 않습니다. 작업이 끝날 때 수행되는 데이터베이스 장애 조치(failover)는 진행 중인 트랜잭션을 취소하고 복구 시간이 길어집니다.

> [!TIP]
> 범용 서비스 계층에서 예약된 저장소 공간을 업데이트해도 장애 조치(failover)가 발생하거나 인스턴스 가용성에 영향을 주지 않습니다.

Azure SQL Database 관리 인스턴스에서 [가속 데이터베이스 복구를](sql-database-accelerated-database-recovery.md) 현재 사용할 수 없습니다. 이 기능을 사용하면 장기 실행 트랜잭션의 경우에도 장애 조치 시간의 가변성이 크게 줄어듭니다.

### <a name="canceling-management-operations"></a>관리 작업 취소

다음 표에는 특정 관리 작업 및 일반적인 전체 기간을 취소하는 기능이 요약되어 있습니다.

Category  |작업(Operation)  |취소 가능  |예상 취소 기간  |
|---------|---------|---------|---------|
|배포 |인스턴스 생성 |예 |  |
|업데이트 |인스턴스 스토리지 확장/축소(범용) |예 |  |
|업데이트 |인스턴스 스토리지 확장/축소(비즈니스 중요) |예 |90%의 운영이 5분 만에 완료됩니다. |
|업데이트 |인스턴스 계산(vCores) 확장 및 축소(범용) |예 |90%의 운영이 5분 만에 완료됩니다. |
|업데이트 |인스턴스 계산(vCores) 확장 및 축소(비즈니스 중요) |예 |90%의 운영이 5분 만에 완료됩니다. |
|업데이트 |인스턴스 서비스 계층 변경(비즈니스 크리티컬에 대한 범용 목적 및 그 반대의 경우도 마찬가지) |예 |90%의 운영이 5분 만에 완료됩니다. |
|DELETE |인스턴스 삭제 |예 |  |
|DELETE |가상 클러스터 삭제(사용자가 시작한 작업) |예 |  |

관리 작업을 취소하려면 개요 블레이드로 이동하여 진행 중인 작업의 알림 상자를 클릭합니다. 오른쪽에서 진행 중인 작업이 있는 화면이 나타나고 작업을 취소하는 버튼이 있습니다. 처음 클릭한 후 다시 클릭하고 작업을 취소할 지 확인하라는 메시지가 표시됩니다.

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

취소 요청이 제출되고 처리된 후 취소 제출이 성공했는지 여부에 대한 알림을 받게 됩니다. 

취소가 성공하면 몇 분 안에 관리 작업이 취소되어 오류가 발생합니다.

![작업 결과 취소](./media/sql-database-managed-instance/canceling-operation-result.png)

취소 요청에 실패하거나 취소 버튼이 활성화되어 있지 않으면 관리 작업이 취소 할 수없는 상태로 입력되었으며 몇 분 안에 완료됩니다. 관리 작업은 완료될 때까지 실행을 계속합니다.

> [!IMPORTANT]
> 취소 작업은 현재 포털에서만 지원됩니다.

## <a name="advanced-security-and-compliance"></a>고급 보안 및 규정 준수

Managed Instance 배포 옵션은 Azure 클라우드와 SQL Server 데이터베이스 엔진에서 제공하는 고급 보안 기능을 결합합니다.

### <a name="managed-instance-security-isolation"></a>Managed Instance 보안 격리

Managed Instance는 Azure 클라우드의 다른 테넌트와 추가로 격리되는 보안을 제공합니다. 보안 격리에는 다음이 포함됩니다.

- Azure Express 경로 또는 VPN 게이트웨이를 사용하여 온-프레미스 환경에 대한 [네이티브 가상 네트워크 구현](sql-database-managed-instance-connectivity-architecture.md) 및 연결
- 기본 배포에서 SQL 끝점은 개인 IP 주소를 통해서만 노출되므로 개인 Azure 또는 하이브리드 네트워크에서 안전하게 연결될 수 있습니다.
- 전용 기본 인프라(컴퓨팅, 스토리지)를 제공하는 단일 테넌트

다음 다이어그램은 애플리케이션에 대한 다양한 연결 옵션을 설명합니다.

![고가용성](./media/sql-database-managed-instance/application-deployment-topologies.png)  

서브넷 수준의 VNet 통합 및 네트워킹 정책 적용에 대한 자세한 내용은 [Managed Instance에 대한 VNet 아키텍처](sql-database-managed-instance-connectivity-architecture.md) 및 [애플리케이션을 Managed Instance에 연결](sql-database-managed-instance-connect-app.md)을 참조하세요.

> [!IMPORTANT]
> 동일한 서브넷에서 보안 요구 사항에 따라 허용되는 곳마다 여러 개의 관리되는 인스턴스를 배치하면 추가적인 이점을 얻을 수 있습니다. 인스턴스를 동일한 서브넷에 배치하면 네트워킹 인프라 유지 관리가 크게 단순화되고 인스턴스 프로비저닝 시간이 단축됩니다. 긴 프로비저닝 기간은 첫 번째로 관리되는 인스턴스를 서브넷에 배포하는 비용과 관련되기 때문입니다.

### <a name="azure-sql-database-security-features"></a>Azure SQL Database 보안 기능

Azure SQL Database는 데이터를 보호하는 데 사용할 수 있는 일단의 고급 보안 기능을 제공합니다.

- [관리되는 인스턴스 감사는](sql-database-managed-instance-auditing.md) 데이터베이스 이벤트를 추적하고 Azure 저장소 계정에 배치된 감사 로그 파일에 기록합니다. 감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.
- 이동 중인 데이터 암호화 - Managed Instance는 전송 계층 보안을 사용하여 이동 중인 데이터를 암호화함으로써 데이터를 보호합니다. 전송 계층 보안 외에도, Managed Instance 배포 옵션은 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)를 사용하여 전송 중인 데이터, 미사용 데이터, 쿼리를 처리 중인 데이터를 보호하는 기능을 제공합니다. Always Encrypted는 중요한 데이터의 도용을 비롯한 위반에 대해 최상의 데이터 보안을 제공하는 업계 최고의 기능입니다. 예를 들어 Always Encrypted를 사용하여 쿼리를 처리하는 동안에도 신용 카드 번호가 데이터베이스에 암호화되어 저장됩니다. 또한 해당 데이터를 처리해야 하는 권한이 부여된 직원 또는 애플리케이션에 의해 사용 시점에 암호를 해독할 수 있습니다.
- [고급 위협 보호는](sql-database-managed-instance-threat-detection.md) 데이터베이스에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하는 서비스에 내장된 추가 보안 인텔리전스 계층을 제공하여 [감사를](sql-database-managed-instance-auditing.md) 보완합니다. 의심스러운 활동, 잠재적 취약성 및 SQL 삽입 공격은 물론 비정상적인 데이터베이스 액세스 패턴에 대해 경고합니다. 고급 위협 보호 경고는 [Azure Security Center에서](https://azure.microsoft.com/services/security-center/) 볼 수 있으며 의심스러운 활동에 대한 세부 정보를 제공하고 위협을 조사하고 완화하는 방법에 대한 조치를 권장합니다.  
- [동적 데이터 마스킹은](/sql/relational-databases/security/dynamic-data-masking) 권한이 없는 사용자에게 마스킹하여 중요한 데이터 노출을 제한합니다. 동적 데이터 마스킹을 수행하면 애플리케이션 계층에 대한 영향을 최소화하면서 표시할 중요한 데이터의 양을 지정할 수 있도록 하여 중요한 데이터에 대한 무단 액세스를 방지할 수 있습니다. 데이터베이스의 데이터는 변경되지 않으면서 지정된 데이터베이스 필드에 대한 쿼리의 결과 집합에서 중요한 데이터를 숨기는 정책 기반 보안 기능입니다.
- [행 수준 보안](/sql/relational-databases/security/row-level-security)을 통해 쿼리를 실행하는 사용자의 특성(예: 그룹 멤버 자격 또는 실행 컨텍스트)을 기반으로 하여 데이터베이스 테이블의 행에 대한 액세스를 제어할 수 있습니다. RLS(행 수준 보안)는 애플리케이션의 보안 설계 및 코딩을 간소화합니다. RLS를 사용하면 데이터 행 액세스에 대한 제한을 구현할 수 있습니다. 예를 들어 작업자가 소속 부서와 관련된 데이터 행에만 액세스하게 하거나 데이터 액세스를 관련 데이터로 제한할 수 있습니다.
- [TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)는 Managed Instance 데이터 파일을 암호화하고, 이를 미사용 데이터 암호화라고 합니다. TDE는 데이터 및 로그 파일에 대한 실시간 I/O 암호화 및 암호 해독을 수행합니다. 암호화에는 복구 중에 사용 가능하도록 데이터베이스 부트 레코드에 저장된 DEK(데이터베이스 암호화 키)가 사용됩니다. 투명한 데이터 암호화를 사용하여 Managed Instance의 모든 데이터베이스를 보호할 수 있습니다. TDE는 스토리지 미디어의 도용으로부터 보호하기 위해 많은 규정 준수 표준에 필요한 정지된 암호화 기술로써 SQL Server에서 입증되었습니다.

암호화된 데이터베이스를 Managed Instance로 마이그레이션하는 작업은 Azure DMS(Database Migration Service) 또는 네이티브 복원을 통해 지원됩니다. 네이티브 복원을 사용하여 암호화된 데이터베이스를 마이그레이션하려는 경우 가상 시스템의 SQL Server 온-프레미스 또는 SQL Server에서 관리되는 인스턴스로 기존 TDE 인증서를 마이그레이션하는 것이 필수 단계입니다. 마이그레이션 옵션에 대한 자세한 내용은 [SQL Server 인스턴스를 Managed Instance로 마이그레이션](sql-database-managed-instance-migrate.md)을 참조하세요.

## <a name="azure-active-directory-integration"></a>Azure Active Directory 통합

Managed Instance 배포 옵션은 기존 SQL 서버 데이터베이스 엔진 로그인 및 AAD(Azure Active Directory)와 통합된 로그인을 지원합니다. Azure AD 서버 보안 주체(로그인)(**공개 미리 보기**)는 온-프레미스 환경에서 사용 중인 온-프레미스 데이터베이스 로그인의 Azure 클라우드 버전입니다. Azure AD 서버 주체(로그인)를 사용하면 Azure Active Directory 테넌트의 사용자 및 그룹을 동일한 관리되는 인스턴스 내의 데이터베이스 간 쿼리를 포함하여 모든 인스턴스 수준 작업을 수행할 수 있는 실제 인스턴스 범위의 보안 주체로 지정할 수 있습니다.

**외부 공급자에서**Azure AD 서버 주체(로그인)를 만들기 위해 새 구문이 도입됩니다. 구문에 대한 자세한 내용은 <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">LOGIN 만들기를</a>참조하고 관리되는 인스턴스 아티클에 [대한 Azure Active Directory 관리자 프로비저닝을 검토합니다.](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 통합 및 Multi-Factor Authentication

Managed Instance 배포 옵션을 사용하면 [Azure Active Directory 통합](sql-database-aad-authentication.md)에서 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 중앙 집중식으로 관리할 수 있습니다. 이 기능은 사용 권한 관리를 간소화하고 보안을 향상시킵니다. Azure Active Directory는 MFA([Multi-Factor Authentication](sql-database-ssms-mfa-authentication-configure.md))을 제공하여 SSO(Single Sign-On) 프로세스를 지원하는 동시에 데이터 및 애플리케이션 보안을 향상시킵니다.

### <a name="authentication"></a>인증

Managed Instance 인증은 사용자가 데이터베이스에 연결할 때 자신의 ID를 증명하는 방법을 나타냅니다. SQL Database는 두 가지 인증 유형을 지원합니다.  

- **SQL 인증**:

  이 인증 방법은 사용자 이름과 암호를 사용합니다.
- **Azure Active Directory 인증**:

  이 인증 방법은 Azure Active Directory에서 관리하는 ID를 사용하며, 관리되는 도메인과 통합된 도메인에 대해 지원됩니다. [가능한 경우](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) Active Directory 인증(통합 보안)을 사용합니다.

### <a name="authorization"></a>권한 부여

권한 부여는 사용자가 Azure SQL Database에서 수행할 수 있는 작업을 나타내며, 사용자 계정의 데이터베이스 역할 멤버 자격과 개체 수준 권한으로 제어합니다. Managed Instance는 SQL Server 2017과 동일한 권한 부여 기능을 갖고 있습니다.

## <a name="database-migration"></a>데이터베이스 마이그레이션

Managed Instance는 온-프레미스 또는 IaaS 데이터베이스 구현에서 대량의 데이터베이스를 마이그레이션하는 사용자 시나리오를 대상으로 합니다. Managed Instance는 여러 데이터베이스 마이그레이션 옵션을 지원합니다.

### <a name="back-up-and-restore"></a>백업 및 복원  

마이그레이션 방식에서는 Azure Blob Storage에 SQL을 백업합니다. Azure Storage Blob에 저장된 백업은 [T-SQL RESTORE 명령](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)을 사용하여 Managed Instance에 직접 복원할 수 있습니다.

- 와이드 월드 가져오기 - 표준 데이터베이스 백업 파일을 복원하는 방법을 보여주는 빠른 시작은 [관리되는 인스턴스에 백업 파일 복원을](sql-database-managed-instance-get-started-restore.md)참조하십시오. 이 빠른 시작은 Azure Blob 저장소에 백업 파일을 업로드하고 SAS(공유 액세스 서명) 키를 사용하여 보호해야 한다는 것을 보여줍니다.
- URL에서 복원하는 방법에 대한 자세한 내용은 [URL에서 네이티브 복원](sql-database-managed-instance-migrate.md#native-restore-from-url)을 참조하세요.

> [!IMPORTANT]
> Managed Instance에서 백업은 다른 Managed Instance로 복원될 수만 있습니다. 온-프레미스 SQL Server 또는 단일 데이터베이스/탄력적 풀로 복원할 수 없습니다.

### <a name="data-migration-service"></a>데이터 마이그레이션 서비스

Azure Database Migration Service는 가동 중지 시간을 최소화하면서 여러 데이터베이스 소스에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션할 수 있도록 설계된 완벽하게 관리되는 서비스입니다. 이 서비스는 기존 타사 및 SQL Server 데이터베이스를 Azure VM의 Azure SQL Database(단일 데이터베이스, 탄력적 풀의 풀링된 데이터베이스 및 관리형 인스턴스의 인스턴스 데이터베이스) 및 SQL Server로 이동하는 데 필요한 작업을 간소화합니다. [DMS를 사용하여 온-프레미스 데이터베이스를 Managed Instance로 마이그레이션하는 방법](https://aka.ms/migratetoMIusingDMS)을 참조하세요.

## <a name="sql-features-supported"></a>지원되는 SQL 기능

Managed Instance 배포 옵션의 목표는 서비스의 일반 공급이 시작될 때까지 단계별로 제공되는 온-프레미스 SQL Server와 거의 100% 호환되는 노출 영역 호환성을 제공하는 것입니다. 기능 및 비교 목록은 [SQL Database 기능 비교](sql-database-features.md)를 참조하고, Managed Instance와 SQL Server 간의 T-SQL 차이점 목록은 [Managed Instance 및 SQL Server 간의 T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md)을 참조하세요.

Managed Instance 배포 옵션은 SQL 2008 데이터베이스와 호환됩니다. SQL 2005 데이터베이스 서버에서 직접 마이그레이션할 수 있으며, 마이그레이션된 SQL 2005 데이터베이스의 호환성 수준은 SQL 2008로 업데이트됩니다.
  
다음 다이어그램은 Managed Instance의 노출 영역 호환성을 설명합니다.  

![마이그레이션](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>SQL Server 온-프레미스와 Managed Instance의 주요 차이점

Managed Instance 배포 옵션은 클라우드에서 항상 최신 상태로 유지되는 이점이 있습니다. 즉, 온-프레미스 SQL Server의 일부 기능이 사용되지 않거나 사용 중지되거나 대체될 수 있습니다. 도구가 특정 기능이 약간 다른 방식으로 작동하거나 완전히 제어하지 않는 환경에서 서비스가 실행되고 있음을 인식해야 하는 특정 경우가 있습니다.

- [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)과 유사한 기술을 사용하여 고가용성이 기본 제공되고 미리 구성됩니다.
- 자동 백업 및 특정 시점 복원. 고객은 자동 백업 체인을 방해하지 않는 `copy-only` 백업을 시작할 수 있습니다.
- 관리되는 인스턴스는 전체 물리적 경로를 지정할 수 없으므로 모든 해당 시나리오를 다르게 지원해야 합니다: RESTORE DB는 MOVE를 지원하지 않으며, CREATE DB는 물리적 경로를 허용하지 않으며, BULK INSERT는 Azure Blob에서만 작동합니다.
- 관리형 인스턴스는 Windows [인증대신 Azure AD](sql-database-aad-authentication.md) 인증을 지원합니다.
- Managed Instance는 메모리 내 OLTP 개체가 포함된 데이터베이스의 XTP 파일 그룹 및 파일을 자동으로 관리합니다.
- 관리형 인스턴스는 SQL Server 통합 서비스(SSIS)를 지원하며 SSIS 패키지를 저장하는 SSIS 카탈로그(SSISDB)를 호스팅할 수 있지만 ADF(Azure 데이터 팩터리)에서 관리되는 AZURE-SSIS 통합 런타임(IR)에서 실행되며 [ADF에서 Azure-SSIS IR 만들기를](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)참조하십시오. SQL 데이터베이스의 SSIS 기능을 비교하려면 [Azure SQL Database 단일 데이터베이스, 탄력적 풀 및 관리되는 인스턴스 비교를](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)참조하십시오.

### <a name="managed-instance-administration-features"></a>Managed Instance 관리 기능

SQL Database 서비스는 사용자를 위해 작업을 수행하거나 이러한 작업을 크게 간소화하므로 Managed Instance 배포 옵션을 사용하여 시스템 관리자는 관리 작업 시간을 크게 단축할 수 있습니다. 예를 들어 [OS/RDBMS 설치 및 패치,](sql-database-high-availability.md) [동적 인스턴스 크기 조정 및 구성,](sql-database-single-database-scale.md) [백업,](sql-database-automated-backups.md) [데이터베이스 복제(시스템](replication-with-sql-database-managed-instance.md) 데이터베이스 포함), [고가용성 구성](sql-database-high-availability.md)및 상태 및 성능 [모니터링](../azure-monitor/insights/azure-sql.md) 데이터 스트림구성.

> [!IMPORTANT]
> 지원되는 기능, 부분적으로 지원되는 기능 및 지원되지 않는 기능 목록은 [SQL Database 기능](sql-database-features.md)을 참조하세요. 관리되는 인스턴스와 SQL Server의 T-SQL 차이점 목록은 [SQL Server에서 관리되는 인스턴스 T-SQL 차이점을](sql-database-managed-instance-transact-sql-information.md) 참조하세요.

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Managed Instance를 프로그래밍 방식으로 식별하는 방법

다음 표는 Transact SQL을 통해 액세스할 수 있으며 애플리케이션이 Managed Instance와 함께 작동 중인지 감지하고 중요한 속성을 검색하는 데 사용할 수 있는 여러 속성을 보여줍니다.

|속성|값|주석|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure(RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|이 값은 SQL Database와 같습니다. SQL 엔진 버전 12(SQL Server 2014)를 **나타내지는 않습니다.** 관리되는 인스턴스는 항상 SQL Server의 최신 RTM 버전과 같거나 더 높은 최신 안정 SQL 엔진 버전을 실행합니다.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|이 값은 SQL Database와 같습니다.|
|`SERVERPROPERTY('EngineEdition')`|8|이 값은 Managed Instance를 고유하게 식별합니다.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|`<instanceName>`.`<dnsPrefix>`.database.windows.net 형식의 전체 인스턴스 DNS 이름. 여기서 `<instanceName>`은 고객이 제공한 이름이고, `<dnsPrefix>`는 글로벌 DNS 이름의 고유성을 보장하기 위해 이름에서 자동으로 생성되는 부분(예: “wcus17662feb9ce98”)입니다.|예: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>다음 단계

- 첫 번째 관리인스턴스를 만드는 방법에 대해 알아보려면 [빠른 시작 가이드를](sql-database-managed-instance-get-started.md)참조하십시오.
- 기능 및 비교 목록은 [SQL 일반 기능](sql-database-features.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [관리형 인스턴스 VNet 구성을](sql-database-managed-instance-connectivity-architecture.md)참조하십시오.
- 관리되는 인스턴스를 만들고 백업 파일에서 데이터베이스를 복원하는 빠른 시작의 경우 [관리되는 인스턴스 만들기를](sql-database-managed-instance-get-started.md)참조하십시오.
- 마이그레이션을 위해 Azure 데이터베이스 마이그레이션 서비스(DMS)를 사용하는 자습서의 경우 [DMS를 사용하여 관리되는 인스턴스 마이그레이션을](../dms/tutorial-sql-server-to-managed-instance.md)참조하십시오.
- 기본 제공 문제 해결 인텔리전스를 사용하여 관리되는 인스턴스 데이터베이스 성능에 대한 고급 모니터링은 [Azure SQL Analytics를 사용하여 Azure SQL Database 모니터를](../azure-monitor/insights/azure-sql.md)참조하십시오.
- 가격 정보는 [SQL Database 관리 인스턴스 가격 책정을](https://azure.microsoft.com/pricing/details/sql-database/managed/)참조하십시오.
