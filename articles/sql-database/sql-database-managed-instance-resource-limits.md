---
title: 리소스 제한 - 관리되는 인스턴스
description: 이 문서는 관리되는 인스턴스의 Azure SQL Database 리소스 제한 사항에 대한 개요를 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/25/2020
ms.openlocfilehash: b2871ec87e4d7f337c26b3ff3de83c1c3c88aea2
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365378"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>개요 Azure SQL Database 관리 인스턴스 리소스 제한

이 문서에서는 Azure SQL Database 관리 인스턴스의 기술적 특성 및 리소스 제한에 대한 개요를 제공하고 이러한 제한증가를 요청하는 방법에 대한 정보를 제공합니다.

> [!NOTE]
> 지원되는 기능 및 T-SQL 문의 차이점은 [기능 차이](sql-database-features.md) 및 [T-SQL 문 지원](sql-database-managed-instance-transact-sql-information.md)을 참조하세요. 단일 데이터베이스의 서비스 계층과 관리되는 인스턴스 간의 일반적인 차이점은 [서비스 계층 비교를](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison)참조하십시오.

## <a name="hardware-generation-characteristics"></a>하드웨어 세대 특성

관리되는 인스턴스에는 기본 인프라 및 아키텍처에 종속된 특성 및 리소스 제한이 있습니다. Azure SQL Database 관리 형 인스턴스는 Gen4 및 Gen5의 두 하드웨어 세대에 배포할 수 있습니다. 하드웨어 세대는 다음 표에 설명된 대로 서로 다른 특성을 갖습니다.

|   | **Gen4** | **5세대** |
| --- | --- | --- |
| 하드웨어 | Intel E5-2673 v3(Haswell) 2.4GHz 프로세서, 연결형 SSD, vCore = 1PP(물리적 코어) | 인텔 E5-2673 v4 (브로드 웰) 2.3 G Hz와 인텔 SP-8160 (스카이 레이크) 프로세서, 빠른 NVMe SSD, vCore = 1 LP (하이퍼 스레드) |
| vCore 수 | 8, 16, 24개 vCore | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| 최대 메모리(메모리/코어 비율) | vCore당 7GB<br/>더 많은 vCore를 추가하여 더 많은 메모리를 가져옵니다. | vCore당 5.1GB<br/>더 많은 vCore를 추가하여 더 많은 메모리를 가져옵니다. |
| 최대 메모리 내 OLTP 메모리 | 인스턴스 제한: vCore당 1-1.5GB| 인스턴스 제한: vCore당 0.8 - 1.65GB |
| 최대 인스턴스 예약 스토리지 |  범용: 8 TB<br/>비즈니스 크리티컬: 1 TB | 범용: 8 TB<br/> 코어 수에 따라 비즈니스 크리티컬 1TB, 2TB 또는 4TB |

> [!IMPORTANT]
> - Gen4 하드웨어는 단계적으로 폐지되고 있으며 새 배포에 더 이상 사용할 수 없습니다. 모든 새 관리되는 인스턴스는 Gen5 하드웨어에 배포해야 합니다.
> - [관리형 인스턴스를 Gen 5 하드웨어로 이동하여](sql-database-service-tiers-vcore.md) 광범위한 vCore 및 스토리지 확장성, 가속화된 네트워킹, 최상의 IO 성능 및 최소한의 대기 시간을 경험하는 것이 좋습니다.

### <a name="in-memory-oltp-available-space"></a>메모리 내 OLTP 사용 가능한 공간 

[비즈니스 크리티컬](sql-database-service-tier-business-critical.md) 서비스 계층의 인메모리 OLTP 공간의 양은 vCore및 하드웨어 생성 수에 따라 달라집니다. 다음 표에는 메모리 내 OLTP 개체에 사용할 수 있는 메모리 제한이 나열되어 있습니다.

| 인메모리 OLTP 공간  | **5세대** | **Gen4** |
| --- | --- | --- |
| vCore 4개  | 3.14 GB | |   
| vCore 8개  | 6.28 GB | 8GB |
| 16 vCores | 15.77 GB | 20GB |
| 24 vCores | 25.25GB | 36GB |
| 32 vCores | 37.94 GB | |
| 40 vCores | 52.23 GB | |
| 64 vCores | 99.9GB    | |
| 80 vCores | 131.68 GB| |

## <a name="service-tier-characteristics"></a>서비스 계층 특성

관리형 인스턴스에는 두 가지 서비스 계층이 있습니다: [범용](sql-database-service-tier-general-purpose.md) 및 [비즈니스 중요.](sql-database-service-tier-business-critical.md) 이러한 계층은 아래 표에 설명된 대로 [서로 다른 기능을](sql-database-service-tiers-general-purpose-business-critical.md)제공합니다.

> [!Important]
> 비즈니스 크리티컬 서비스 계층은 읽기 전용 워크로드에 사용할 수 있는 추가 기본 제공 인스턴스 복사본(보조 복제본)을 제공합니다. 읽기-쓰기 쿼리와 읽기 전용/분석/보고 쿼리를 분리할 수 있는 경우 동일한 가격으로 두 번 vCore및 메모리를 얻게 됩니다. 보조 복제본은 기본 인스턴스보다 몇 초 지연될 수 있으므로 정확한 현재 데이터 상태가 필요하지 않은 보고/분석 워크로드를 오프로드하도록 설계되었습니다. 아래 표에서 **읽기 전용 쿼리는** 보조 복제본에서 실행되는 쿼리입니다.

| **기능** | **범용** | **비즈니스 크리티컬** |
| --- | --- | --- |
| vCore 수\* | Gen4: 8, 16, 24<br/>Gen 5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen 5: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*동일한 수의 vCore가 읽기 전용 쿼리전용입니다. |
| 최대 메모리 | Gen4: 56GB - 168GB(7GB/vCore)<br/>Gen5: 20.4GB - 408GB(5.1GB/vCore)<br/>더 많은 vCore를 추가하여 더 많은 메모리를 가져옵니다. | Gen4: 56GB - 168GB(7GB/vCore)<br/>읽기-쓰기 쿼리를 위한 Gen5: 20.4GB - 408GB(5.1GB/vCore)<br/>+ 읽기 전용 쿼리에 대한 추가 20.4 GB - 408GB (5.1GB / vCore).<br/>더 많은 vCore를 추가하여 더 많은 메모리를 가져옵니다. |
| 최대 인스턴스 저장소 크기(예약됨) | - 4 vCores에 대한 2 TB (Gen5 만)<br/>- 다른 크기의 경우 8TB | Gen4: 1 TB <br/> Gen5: <br/>- 4, 8, 16 vCores용 TB 1개<br/>- 2TB(24개 vCore용)<br/>- 4TB(32, 40, 64, 80개 vCore용) |
| 최대 데이터베이스 크기 | 현재 사용 가능한 인스턴스 크기까지(vCore 수에 따라 최대 2TB - 8TB). | 현재 사용 가능한 인스턴스 크기까지(vCore 수에 따라 최대 1TB - 4TB). |
| 최대 tempDB 크기 | 24GB/vCore(96 ~ 1,920GB)로 제한되며 현재 사용 가능한 인스턴스 저장소 크기입니다.<br/>더 많은 vCore를 추가하여 TempDB 공간을 더 확보합니다.<br/> 로그 파일 크기는 120GB로 제한됩니다.| 현재 사용 가능한 인스턴스 저장소 크기까지입니다. |
| 인스턴스당 최대 데이터베이스 수 | 인스턴스 저장소 크기 제한에 도달하지 않는 한 100입니다. | 인스턴스 저장소 크기 제한에 도달하지 않는 한 100입니다. |
| 인스턴스당 최대 데이터베이스 파일 수 | 인스턴스 저장소 크기 또는 Azure Premium [디스크 할당 공간](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) 제한에 도달하지 않는 한 최대 280입니다. | 인스턴스 저장소 크기 제한에 도달하지 않는 한 데이터베이스당 32,767개의 파일입니다. |
| 최대 데이터 파일 크기 | 현재 사용 가능한 인스턴스 저장소 크기(최대 2TB - 8TB) 및 [Azure 프리미엄 디스크 저장소 할당 공간으로](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files)제한됩니다. | 현재 사용 가능한 인스턴스 저장소 크기(최대 1TB - 4TB)로 제한됩니다. |
| 최대 로그 파일 크기 | 2TB로 제한되며 현재 사용 가능한 인스턴스 저장소 크기입니다. | 2TB로 제한되며 현재 사용 가능한 인스턴스 저장소 크기입니다. |
| 데이터/로그 IOPS(근사치) | 인스턴스당 최대 30-40K IOPS*, 파일당 500 - 7500<br/>\*[파일 크기를 늘려 더 많은 IOPS를 얻습니다.](#file-io-characteristics-in-general-purpose-tier)| 10 K - 200 K (2500 IOPS / vCore)<br/>더 많은 vCore를 추가하여 IO 성능을 향상시려면 |
| 로그 쓰기 처리량 제한(인스턴스당) | vCore당 3MB/초<br/>최대 22MB/s | vCore당 4MB/s<br/>최대 48MB/초 |
| 데이터 처리량(근사치) | 파일당 100~250MB/초<br/>\*[파일 크기를 늘려 IO 성능 향상](#file-io-characteristics-in-general-purpose-tier) | 이에 제한되지 않는다. |
| 스토리지 IO 대기 시간(대략적인) | 5~10ms | 1~2ms |
| 메모리 내 OLTP | 지원되지 않음 | 사용 가능한 [크기는 vCore 수에 따라 다릅니다.](#in-memory-oltp-available-space) |
| 최대 세션수 | 30000 | 30000 |
| [읽기 전용 복제본](sql-database-read-scale-out.md) | 0 | 1 (가격에 포함) |

> [!NOTE]
> - **현재 사용 가능한 인스턴스 저장소 크기는** 예약된 인스턴스 크기와 사용된 저장소 공간의 차이입니다.
> - 사용자 및 시스템 데이터베이스의 데이터 및 로그 파일 크기는 최대 스토리지 크기 제한과 비교되는 인스턴스 스토리지 크기에 포함됩니다. <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> 시스템 뷰를 사용하여 데이터베이스에서 사용되는 총 공간을 확인합니다. 오류 로그는 영구적이지 않으며 크기에 포함되지 않습니다. 백업은 스토리지 크기에 포함되지 않습니다.
> - 범용 계층의 처리량 및 IOPS도 관리되는 인스턴스에 의해 명시적으로 제한되지 않는 [파일 크기에](#file-io-characteristics-in-general-purpose-tier) 따라 달라집니다.
> - 자동 장애 조치 그룹을 사용하여 다른 Azure 지역에서 다른 읽을 수 있는 복제본을 만들 수 있습니다.
> - 최대 인스턴스 IOPS는 파일 레이아웃 및 워크로드 배포에 따라 달라집니다. 예를 들어 최대 5K IOPS가 있는 7x 1TB 파일과 각각 500개의 IOPS가 있는 7개의 작은 파일(128GB 미만)을 생성하는 경우 워크로드가 모든 파일을 사용할 수 있는 경우 인스턴스당 38500 IOPS(7x5000+7x500)를 얻을 수 있습니다. 일부 양의 IOPS는 자동 백업에도 사용됩니다.

> [!NOTE]
> [이 문서에서 관리되는 인스턴스 풀의 리소스 제한에](sql-database-instance-pools.md#instance-pools-resource-limitations)대한 자세한 내용을 알아보십시오.

### <a name="file-io-characteristics-in-general-purpose-tier"></a>범용 계층의 파일 IO 특성

범용 서비스 계층에서 모든 데이터베이스 파일은 파일 크기에 따라 전용 IOPS 및 처리량을 가져옵니다. 더 큰 파일은 더 많은 IOPS 및 처리량을 얻고 있습니다. 데이터베이스 파일의 IO 특성은 다음 표에 나와 있습니다.

| 파일 크기 | >=0 및 <=128 GiB | >128 및 <=256 GiB | >256 및 <= 512 GiB | >0.5 및 <=1 TiB    | >1 및 <=2 TiB    | >2 및 <=4 TiB | >4 및 <=8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| 파일당 IOPS       | 500   | 1100 | 2300              | 5,000              | 7,500              | 7,500              | 12,500   |
| 파일당 처리량 | 100MiB/초 | 125MiB/초 | 150MiB/초 | 200MiB/초 | 250MiB/초 | 250MiB/초 | 480 MiB/s | 

일부 데이터베이스 파일에 높은 IO 대기 시간이 있거나 IOPS/처리량이 한계에 도달하면 [파일 크기를 늘려](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)성능을 향상시킬 수 있습니다.

또한 최대 로그 쓰기 처리량 22MB/s와 같은 인스턴스 수준 제한이 있으므로 인스턴스 처리량 제한에 도달하기 때문에 로그 파일 전체에서 파일에 도달하지 못할 수 있습니다.

## <a name="supported-regions"></a>지원되는 지역

관리되는 인스턴스는 [지원되는 지역에서만](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)만들 수 있습니다. 현재 지원되지 않는 리전에서 관리되는 인스턴스를 만들려면 [Azure 포털을 통해 지원 요청을 보낼](quota-increase-request.md)수 있습니다.

## <a name="supported-subscription-types"></a>지원되는 구독 유형

관리되는 인스턴스는 현재 다음과 같은 구독 유형에서만 배포를 지원합니다.

- [EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [클라우드 서비스 공급자(CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio 구독자에 대한 월별 Azure 크레딧이 있는 구독](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>지역별 리소스 제한

지원되는 구독 유형에는 지역당 제한된 수의 리소스가 포함될 수 있습니다. 관리형 인스턴스에는 Azure 리전당 두 가지 기본 제한이 있습니다(구독 유형 유형에 따라 [Azure Portal에서](quota-increase-request.md) 특별 지원 요청을 만들어 온디맨드로 늘릴 수 있습니다.

- **서브넷 제한**: 관리되는 인스턴스가 단일 지역에 배포되는 서브넷의 최대 수입니다.
- **vCore 단위 제한**: 단일 리전의 모든 인스턴스에 배포할 수 있는 최대 vCore 단위 수입니다. 하나의 GP vCore 는 하나의 vCore 장치를 사용하고 하나의 BC vCore는 4 vCore 단위를 사용합니다. 총 인스턴스 수는 vCore 단위 제한 내에 있는 한 제한되지 않습니다.

> [!Note]
> 이러한 제한은 기본 설정이며 기술적 제한이 아닙니다. 현재 리전에서 더 관리되는 인스턴스가 필요한 경우 [Azure Portal에서](quota-increase-request.md) 특별 지원 요청을 만들어 온디맨드 제한을 늘릴 수 있습니다. 또는 지원 요청을 보내지 않고 다른 Azure 지역에서 새 관리되는 인스턴스를 만들 수 있습니다.

다음 표에서는 지원되는 구독 유형에 대한 **기본 지역 제한을** 보여 주며(아래에 설명된 지원 요청을 사용하여 기본 제한을 확장할 수 있음).

|구독 유형| 관리되는 인스턴스 서브넷의 최대 수 | 최대 vCore 단위 수* |
| :---| :--- | :--- |
|종량제|3|320|
|CSP |8 (일부 지역에서는 15개).)|960 (일부 지역에서는 1440**)|
|종량제 개발/테스트|3|320|
|Enterprise 개발/테스트|3|320|
|EA|8 (일부 지역에서는 15개).)|960 (일부 지역에서는 1440**)|
|Visual Studio Enterprise|2 |64|
|비주얼 스튜디오 전문 및 MSDN 플랫폼|2|32|

\*배포를 계획할 때 BC 주 비즈니스 중요(BC) 서비스 계층에는 범용(GP) 서비스 계층보다 4배 더 많은 vCore 용량이 필요하다는 점을 고려해야 합니다. 예를 들어 1 GP vCore = 1 vCore 단위 및 1 BC vCore = 4 vCore 단위. 기본 한도에 대한 소비 분석을 단순화하려면 관리되는 인스턴스가 배포되는 지역의 모든 서브넷에서 vCore 단위를 요약하고 결과를 구독 유형에 대한 인스턴스 단위 제한과 비교합니다. **최대 vCore 단위** 제한 수는 리전의 각 구독에 적용됩니다. 여러 서브넷에 배포된 모든 vCore의 합계가 **vCore 단위의 최대 수보다**낮거나 같아야 한다는 점을 제외하면 개별 서브넷당 제한은 없습니다.

\*\*더 큰 서브넷 및 vCore 제한은 호주 동부, 미국 동부, 미국 동부 2, 북유럽, 미국 중남부, 동남아시아, 영국 남부, 서유럽, 미국 서부 2 지역에서 사용할 수 있습니다.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>SQL 관리 인스턴스에 대한 할당량 증가 요청

현재 리전에서 더 많은 관리형 인스턴스가 필요한 경우 Azure 포털을 사용하여 할당량을 확장하기 위한 지원 요청을 보냅니다. 자세한 내용은 [Azure SQL 데이터베이스에 대한 할당량 증가를](quota-increase-request.md)요청합니다.

## <a name="next-steps"></a>다음 단계

- 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스란 무엇입니까?](sql-database-managed-instance.md)
- 가격 정보는 [SQL Database 관리 인스턴스 가격 책정을](https://azure.microsoft.com/pricing/details/sql-database/managed/)참조하십시오.
- 첫 번째 관리인스턴스를 만드는 방법에 대해 알아보려면 [빠른 시작 가이드를](sql-database-managed-instance-get-started.md)참조하십시오.
