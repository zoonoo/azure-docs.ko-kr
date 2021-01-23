---
title: Azure의 SQL Server에 대한 성능 지침 | Microsoft Docs
description: Microsoft Azure Virtual Machines에서 SQL Server 성능을 최적화 하는 방법에 대 한 지침을 제공 합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6ba30436b363353ad183396e07111b33ca912dbf
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737441"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines에서 SQL Server의 성능 지침
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Microsoft Azure Virtual Machines의 SQL Server 성능 최적화에 대 한 지침을 제공 합니다.

## <a name="overview"></a>개요

Azure Virtual Machines에서 SQL Server를 실행 하는 동안 온-프레미스 서버 환경에서 SQL Server에 적용 되는 것과 동일한 데이터베이스 성능 튜닝 옵션을 계속 사용 하는 것이 좋습니다. 그러나 퍼블릭 클라우드의 관계형 데이터베이스 성능은 가상 머신의 크기 및 데이터 디스크의 구성과 같은 많은 요인에 따라 달라집니다.

[Azure Portal에서 프로 비전 된 SQL Server 이미지는](sql-vm-create-portal-quickstart.md) 일반적인 저장소 [구성 모범 사례](storage-configuration.md)를 따릅니다. 프로비전 후에는 이 문서에서 설명하는 다른 최적화를 적용해 보는 방안을 고려해 봅니다. 워크로드에 따라 선택하고 테스트를 통해 확인합니다.

> [!TIP]
> 일반적으로 비용에 대한 최적화와 성능에 대한 최적화 간의 절충이 있습니다. 이 문서는 Azure Virtual Machines에서 SQL Server에 대 한 *최상의* 성능을 얻는 데 중점을 두었습니다. 작업이 적은 경우 아래 나열된 모든 최적화 사항이 필요하지 않을 수 있습니다. 이러한 권장 사항을 평가할 때 성능 요구 사항, 비용 및 작업 패턴을 고려하세요.

## <a name="quick-checklist"></a>빠른 검사 목록

다음은 Azure Virtual Machines에서 SQL Server의 최적의 성능을 위한 빠른 검사 목록입니다.

| 영역 | 최적화 |
| --- | --- |
| [VM 크기](#vm-size-guidance) | - [Standard_M8-4ms](../../../virtual-machines/m-series.md), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series), [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 이상 등 4 개 이상의 vcpu가 포함 된 VM 크기를 사용 합니다. <br/><br/> -메모리 액세스에 [최적화](../../../virtual-machines/sizes-memory.md) 된 가상 머신 크기를 사용 하 여 SQL Server 워크 로드의 성능을 극대화 합니다. <br/><br/> - [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) 시리즈, [M-](../../../virtual-machines/m-series.md)및 [Mv2](../../../virtual-machines/mv2-series.md) 시리즈는 OLTP 워크 로드에 필요한 최적의 메모리-vcore 비율을 제공 합니다. M 시리즈 Vm은 업무에 중요 한 워크 로드에 필요한 메모리-vCore의 최고 비율을 제공 하며, 데이터 웨어하우스 워크 로드에도 적합 합니다. <br/><br/> -중요 업무용 및 데이터 웨어하우스 워크 로드에 대해 더 높은 메모리-vCore 비율이 필요할 수 있습니다. <br/><br/> -SQL Server 설정 및 저장소 옵션이 최적의 SQL Server 성능을 위해 구성 되어 있으므로 Azure Virtual Machine marketplace 이미지를 활용 합니다. <br/><br/> -대상 워크 로드의 성능 특성을 수집 하 고이를 사용 하 여 비즈니스에 적합 한 VM 크기를 결정 합니다.|
| [스토리지](#storage-guidance) | -Azure Virtual Machines에서 SQL Server 성능에 대 한 자세한 테스트 및 TPC_C 벤치 마크에 대 한 자세한 내용은 [OLTP 성능 최적화](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)블로그를 참조 하세요. <br/><br/> - 최고의 가격/성능 이점을 위해 [프리미엄 SSD](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)를 사용합니다. 데이터 파일에 대 한 [읽기 전용 캐시](../../../virtual-machines/premium-storage-performance.md#disk-caching) 를 구성 하 고 로그 파일에 대 한 캐시는 구성 하지 않습니다. <br/><br/> -작업에 1 밀리초 미만의 저장소 대기 시간이 필요한 경우에는 [Ultra Disks](../../../virtual-machines/disks-types.md#ultra-disk) 를 사용 합니다. 자세히 알아보려면 [ultra 디스크로 마이그레이션](storage-migrate-to-ultradisk.md) 을 참조 하세요. <br/><br/> - 디스크 유형을 선택하기 전에 [애플리케이션을 모니터링](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)하여 SQL Server 데이터, 로그 및 임시 DB 파일의 스토리지 대기 시간 요구 사항을 수집합니다. 1 밀리초의 저장소 대기 시간이 필요한 < 경우에는 울트라 디스크를 사용 하 고 그렇지 않으면 프리미엄 SSD를 사용 합니다. 짧은 대기 시간이 로그 파일에만 필요하고 데이터 파일에는 필요하지 않은 경우 로그 파일에 대해서만 필요한 IOPS 및 처리량 수준에서 [Ultra Disk를 프로비저닝](../../../virtual-machines/disks-enable-ultra-ssd.md)합니다. <br/><br/>  - 표준 스토리지는 개발 및 테스트 용도나 백업 파일에만 권장되며 프로덕션 워크로드에는 사용하면 안 됩니다. <br/><br/> - [스토리지 계정](../../../storage/common/storage-account-create.md)과 SQL Server VM을 동일한 Azure 지역에 유지합니다.<br/><br/> - 스토리지 계정의 Azure [지역 중복 스토리지](../../../storage/common/storage-redundancy.md)(지역에서 복제)를 사용하지 않도록 설정합니다.  |
| [디스크](#disks-guidance) | - 최소 2개의 [프리미엄 SSD 디스크](../../../virtual-machines/disks-types.md#premium-ssd)를 사용합니다(로그 파일용 1개 및 데이터 파일용 1개). <br/><br/> -1 밀리초 IO 대기 시간 < 필요한 워크 로드의 경우 M 시리즈에 대해 write accelerator를 사용 하도록 설정 하 고 Es 및 DS 시리즈에 대해 울트라 SSD 디스크를 사용 하는 것이 좋습니다 <br/><br/> - 데이터 파일을 호스트하는 디스크에서 [읽기 전용 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching)을 사용하도록 설정합니다.<br/><br/> - [SQL Server 데이터, 로그 및 TempDB 파일에 대 한 저장소를 구성할](storage-configuration.md) 때 워크 로드에 필요한 것 보다 20% 프리미엄 IOPS/처리량 용량을 추가 합니다. <br/><br/> - 운영 체제 또는 임시 디스크를 데이터베이스 저장 또는 로깅에 사용하지 마세요.<br/><br/> - 로그 파일을 호스팅하는 디스크에서 캐싱을 사용하지 마세요.  **중요**: Azure Virtual Machines 디스크에 대 한 캐시 설정을 변경 하는 경우 SQL Server 서비스를 중지 합니다.<br/><br/> - 스토리지 처리량이 증가하도록 여러 Azure 데이터 디스크를 스트라이프합니다.<br/><br/> - 문서화된 할당 크기로 포맷합니다. <br/><br/> - TempDB는 중요 업무를 위한 SQL Server 워크로드용 로컬 SSD `D:\` 드라이브에 넣습니다(올바른 VM 크기를 선택한 후). Azure Portal 또는 Azure 빠른 시작 템플릿에서 VM을 만들고 [임시 DB를 로컬 디스크에 저장](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)하는 경우에는 추가 작업이 필요 하지 않습니다. 다른 모든 경우에는를 다시 시작한 후 오류를 방지 하기 위해  [ssd를 사용 하 여 TempDB를 저장](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) 하기 위한 블로그의 단계를 따르세요. 로컬 드라이브의 용량이 임시 DB 크기에 충분하지 않은 경우 [읽기 전용 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching)을 사용하여 프리미엄 SSD 디스크에 [스트라이프된](../../../virtual-machines/premium-storage-performance.md) 스토리지 풀에 임시 DB를 넣습니다. |
| [I/O](#io-guidance) |- 데이터베이스 페이지 압축을 사용하도록 설정합니다.<br/><br/> - 데이터 파일에 즉시 파일 초기화를 사용하도록 설정합니다.<br/><br/> - 데이터베이스의 자동 확장을 제한합니다.<br/><br/> - 데이터베이스의 자동 축소를 해제합니다.<br/><br/> - 시스템 데이터베이스를 포함하여 모든 데이터베이스를 데이터 디스크로 이동합니다.<br/><br/> - SQL Server 오류 로그 및 추적 파일 디렉터리를 데이터 디스크로 이동합니다.<br/><br/> - 기본 백업 및 데이터베이스 파일 위치를 구성합니다.<br/><br/> - [메모리에 잠긴 페이지를 사용하도록 설정합니다](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).<br/><br/> -설치 된 SQL Server 버전에 대 한 [최신 누적 업데이트](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) 를 평가 하 고 적용 합니다. |
| [기능별](#feature-specific-guidance) | -Azure Blob storage에 직접 백업 합니다.<br/><br/>- 12TB보다 큰 데이터베이스에 [파일 스냅샷 백업](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)을 사용합니다. <br/><br/>- 여러 개의 임시 DB 파일, 코어당 1개의 파일, 최대 8개의 파일을 사용합니다.<br/><br/>- 운영 체제에 대해 최대 서버 메모리를 90% 또는 최대 50GB 정도 남게 설정합니다. <br/><br/>- 소프트 NUMA를 사용하도록 설정합니다. |


<br/>
이러한 최적화를 수행하는 *방법* 및 *이유* 에 대한 자세한 내용은 다음 섹션에 나와 있는 세부 정보 및 지침을 참조하세요.
<br/><br/>

## <a name="getting-started"></a>시작하기

Azure VM에 새 SQL Server를 만들고 현재 원본 시스템을 마이그레이션하지 않는 경우 공급 업체 요구 사항에 따라 새 SQL Server VM을 만듭니다.  SQL Server VM에 대 한 공급 업체 요구 사항은 온-프레미스에서 배포 하는 것과 동일 합니다. 

클라우드 용으로 빌드된 새 응용 프로그램을 사용 하 여 새 SQL Server VM을 만드는 경우 데이터 및 사용 요구 사항이 발전 함에 따라 SQL Server VM 크기를 쉽게 지정할 수 있습니다.
하위 계층 D 시리즈, B 시리즈 또는 Av2 시리즈를 사용 하 여 개발 환경을 시작 하 고 시간이 지남에 따라 환경을 확장 합니다. 

프로덕션 OLTP 환경에 권장 되는 최소값은 4 vCore, 32 GB의 메모리 및 메모리-vCore 비율 8입니다. 새 환경의 경우 데이터 및 계산 요구 사항이 변경 될 때 4 개의 vCore 컴퓨터에서 시작 하 고 8, 16, 32 Vcore 이상으로 확장 합니다. OLTP 처리량의 경우 모든 vCore에 대해 5000 IOPS를 포함 하는 Vm을 대상으로 SQL Server 합니다. 

포털의 저장소 구성과 함께 SQL Server VM marketplace 이미지를 사용 합니다. 이렇게 하면 워크 로드에 필요한 크기, IOPS 및 처리량을 얻는 데 필요한 저장소 풀을 보다 쉽게 만들 수 있습니다. Premium storage 및 premium storage 캐싱을 지 원하는 Vm SQL Server 선택 하는 것이 중요 합니다. 자세히 알아보려면 [저장소](#storage-guidance) 섹션을 참조 하세요. 

데이터 웨어하우스와 업무상 중요 한 환경 SQL Server는 메모리 대 vCore 비율 8 개 이상으로 규모를 확장 해야 하는 경우가 많습니다. 중간 규모 환경의 경우, 큰 데이터 웨어하우스 환경에 대해 16 개의 코어-메모리 비율과 32의 코어-메모리 비율을 선택할 수 있습니다. 

데이터 웨어하우스 환경 SQL Server 큰 컴퓨터의 병렬 처리를 활용 하는 경우가 많습니다. 이러한 이유로 M 시리즈 및 Mv2 시리즈는 대규모 데이터 웨어하우스 환경에서 강력한 옵션입니다.

## <a name="vm-size-guidance"></a>VM 크기 지침

현재 온-프레미스 SQL Server 데이터베이스를 Azure Vm의 SQL Server으로 마이그레이션하기 위한 기준으로 원본 컴퓨터의 vCPU 및 메모리 구성을 사용 합니다. Azure에 대 한 핵심 라이선스를 사용 하 여 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 를 활용 하 고 SQL Server 라이선스 비용을 절감할 수 있습니다.

**프로덕션 SQL Server 작업에 대 한 출발점으로 8의 메모리-vCore 비율을 권장 합니다.** 비-프로덕션 작업에는 더 작은 비율을 사용할 수 있습니다. 

작업 (OLTP 또는 데이터 웨어하우스)을 기반으로 SQL Server 성능에 가장 적합 한 [메모리 최적화](../../../virtual-machines/sizes-memory.md), [범용](../../../virtual-machines/sizes-general.md), [저장소 최적화](../../../virtual-machines/sizes-storage.md)또는 [제한 된 vcore](../../../virtual-machines/constrained-vcpu.md) 가상 머신 크기를 선택 합니다. 

### <a name="memory-optimized"></a>메모리 최적화

메모리 액세스에 [최적화 된 가상 머신 크기](../../../virtual-machines/sizes-memory.md) 는 SQL Server vm의 기본 목표 이며 Microsoft에서 권장 하는 선택입니다. 메모리 액세스에 최적화 된 가상 머신은 더 강력한 메모리와 CPU 비율을 제공 하 고 중간 규모에서 크게 캐시 옵션을 제공 합니다. 

#### <a name="m-and-mv2-series"></a>M 및 Mv2 시리즈

[M 시리즈](../../../virtual-machines/m-series.md) 는 가장 큰 SQL Server 워크 로드 중 일부에 대 한 vcore 개수와 메모리를 제공 합니다.  

[Mv2 시리즈](../../../virtual-machines/mv2-series.md) 는 최고 vcore 수 및 메모리를 포함 하 고 있으며 중요 업무용 및 데이터 웨어하우스 워크 로드에 권장 됩니다. Mv2 시리즈 인스턴스는 관계형 데이터베이스 서버, 큰 캐시 및 메모리 내 분석에 적합 한 메모리 내 대규모 데이터베이스와 워크 로드에 대 한 높은 메모리와 CPU의 비율을 지원 하기 위해 뛰어난 계산 성능을 제공 하는 메모리 액세스에 최적화 된 VM 크기입니다.

예를 들어 [Standard_M64ms](../../../virtual-machines/m-series.md) 는 28 메모리-vcore 비율을 갖습니다.

M 및 Mv2 SQL Server 시리즈의 기능 중 일부에는 [premium storage](../../../virtual-machines/premium-storage-performance.md) 및 [premium storage 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching) 지원, [울트라 디스크](../../../virtual-machines/disks-enable-ultra-ssd.md) 지원 및 [쓰기 가속이](../../../virtual-machines/how-to-enable-write-accelerator.md)포함 됩니다.

#### <a name="edsv4-series"></a>Edsv4 시리즈

[Edsv4 시리즈](../../../virtual-machines/edv4-edsv4-series.md) 는 메모리 집약적 응용 프로그램을 위해 설계 되었습니다. 이러한 Vm에는 큰 로컬 저장소 SSD 용량, 강력한 로컬 디스크 IOPS, 최대 504 GiB RAM, Gen2 Vm을 사용 하는 이전 Ev3/Esv3 크기와 비교 하 여 향상 된 계산 등이 있습니다. 이러한 가상 컴퓨터에서 메모리-vCore 비율 8이 거의 일치 하며, 표준 SQL Server 워크 로드에 이상적입니다. 

이 VM 시리즈는 대기 시간이 짧고 고속 로컬 저장소를 활용 하는 메모리 집약적 엔터프라이즈 응용 프로그램 및 응용 프로그램에 적합 합니다.

Edsv4 시리즈 가상 머신은 [premium storage](../../../virtual-machines/premium-storage-performance.md)및 [premium storage 캐싱을](../../../virtual-machines/premium-storage-performance.md#disk-caching)지원 합니다.

#### <a name="dsv2-series-11-15"></a>DSv2 시리즈 11-15

[DSv2 시리즈 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 에는 이전 D 시리즈와 동일한 메모리 및 디스크 구성이 있습니다. 이 시리즈는 모든 가상 머신에서 7의 메모리 대 CPU 비율을 일관성 있게 유지 합니다. 

[DSv2 시리즈 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 는 [premium storage](../../../virtual-machines/premium-storage-performance.md) 및 [premium storage 캐싱을](../../../virtual-machines/premium-storage-performance.md#disk-caching)지원 합니다 .이는 최적의 성능을 위해 적극 권장 됩니다.

### <a name="general-purpose"></a>범용

범용 [가상 머신 크기](../../../virtual-machines/sizes-general.md) 는 개발 및 테스트, 웹 서버 및 더 작은 데이터베이스 서버와 같은 더 작은 항목 수준 워크 로드에 대해 분산 된 메모리-vcore 비율을 제공 하도록 설계 되었습니다. 

범용 가상 컴퓨터를 사용 하는 메모리 대 vCore 비율이 작을수록 메모리 기반 성능 카운터를 신중 하 게 모니터링 하 SQL Server에서 필요한 버퍼 캐시 메모리를 얻을 수 있는지 확인 하는 것이 중요 합니다. 자세한 내용은 [메모리 성능 기준](#memory) 을 참조 하세요. 

프로덕션 워크 로드에 대 한 시작 권장 사항은 메모리-vCore 비율 8 이기 때문에 SQL Server를 실행 하는 범용 VM에 대 한 최소 권장 구성은 4 개의 Vcore 및 32 GB의 메모리입니다. 

#### <a name="ddsv4-series"></a>Ddsv4 시리즈

[Ddsv4 시리즈](../../../virtual-machines/ddv4-ddsv4-series.md) 는 vcpu, 메모리 및 임시 디스크의 공평 한 조합을 제공 하지만 메모리와 vcpu를 더 적게 지원 합니다. 

Ddsv4 Vm에는 짧은 대기 시간과 고속 로컬 저장소가 포함 됩니다.

이러한 컴퓨터는 side-by-side SQL 및 임시 저장소 및 부서별 관계형 데이터베이스에 빠르게 액세스 해야 하는 앱 배포에 적합 합니다. 이 시리즈의 모든 가상 컴퓨터에 대해 표준 메모리-vCore 비율 4가 있습니다. 

이러한 이유로이 시리즈에서는 8 개의 vCores 및 32 Gb의 메모리를 포함 하는 스타터 가상 머신으로 D8ds_v4를 활용 하는 것이 좋습니다. 가장 큰 컴퓨터는 64 vCores 및 256 Gb의 메모리를 포함 하는 D64ds_v4입니다.

[Ddsv4 시리즈](../../../virtual-machines/ddv4-ddsv4-series.md) 가상 머신은 [premium storage](../../../virtual-machines/premium-storage-performance.md) 및 [premium storage 캐싱을](../../../virtual-machines/premium-storage-performance.md#disk-caching)지원 합니다.

> [!NOTE]
> [Ddsv4 시리즈](../../../virtual-machines/ddv4-ddsv4-series.md) 는 SQL Server 워크 로드에 권장 되는 8의 메모리-vcore 비율을 포함 하지 않습니다. 따라서 더 작은 응용 프로그램 및 개발 워크 로드에만 이러한 가상 컴퓨터를 사용 하는 것을 고려 합니다.

#### <a name="b-series"></a>B 시리즈

[안정적인 B 시리즈](../../../virtual-machines/sizes-b-series-burstable.md) 가상 머신 크기는 개념 증명 및 매우 작은 응용 프로그램 및 개발 서버와 같이 일관 된 성능을 필요로 하지 않는 워크 로드에 이상적입니다. 

[안정적인 B 시리즈](../../../virtual-machines/sizes-b-series-burstable.md) 가상 머신 크기의 대부분은 메모리-vcore 비율 4입니다. 이러한 컴퓨터 중 가장 큰 값은 20 개 vCores 및 80 GB의 메모리가 있는 [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) 입니다.

이 시리즈는 컴퓨터 크기에 따라 변화 하는 안정화 크레딧을 사용 하 여 업무 시간 동안 **버스트** 하는 기능을 제공 하므로 고유 합니다. 

크레딧을 모두 사용 하는 경우 VM은 기준 컴퓨터 성능으로 돌아옵니다.

B 시리즈의 혜택은 다른 시리즈의 다른 VM 크기와 비교 하 여 달성할 수 있는 계산 절감 액 이며 특히 하루 종일 처리 능력을 많이 필요로 하는 경우에 해당 합니다.

이 시리즈는 [premium storage](../../../virtual-machines/premium-storage-performance.md)를 지원 하지만 [premium storage 캐싱은](../../../virtual-machines/premium-storage-performance.md#disk-caching) **지원 하지** 않습니다.

> [!NOTE] 
> SQL Server 워크 로드에 권장 되는 8의 메모리-vCore 비율을 포함 [하지 않습니다.](../../../virtual-machines/sizes-b-series-burstable.md) 따라서 더 작은 응용 프로그램, 웹 서버 및 개발 워크 로드에만 이러한 가상 컴퓨터를 사용 하는 것이 좋습니다.

#### <a name="av2-series"></a>Av2 시리즈

[Av2 시리즈](../../../virtual-machines/av2-series.md) vm은 개발 및 테스트, 낮은 트래픽 웹 서버, 중소 규모의 앱 데이터베이스, 개념 증명 등의 항목 수준 작업에 가장 적합 합니다.

[Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 개 vcores 및 메모리 16GBs), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 개 vcores 및 32GBs of memory) 및 [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (Vcores 8 개, 메모리 64GBs)는 이러한 상위 3 개 가상 컴퓨터에 대 한 메모리-vcores 비율 8의 양호한 비율을 갖습니다. 

이러한 가상 머신은 소규모 개발 및 테스트 SQL Server 컴퓨터에서 모두 좋은 옵션입니다. 

8 vCore [Standard_A8m_v2](../../../virtual-machines/av2-series.md) 작은 응용 프로그램 및 웹 서버에 대 한 좋은 옵션 일 수도 있습니다.

> [!NOTE] 
> Av2 시리즈는 premium storage를 지원 하지 않으므로, 메모리-vCore 비율이 8 인 가상 머신을 사용 하더라도 프로덕션 SQL Server 워크 로드에는 권장 되지 않습니다.

### <a name="storage-optimized"></a>Storage에 최적화

[저장소에 최적화 된 VM 크기](../../../virtual-machines/sizes-storage.md) 는 특정 사용 사례에 대 한 것입니다. 이러한 가상 머신은 최적화 된 디스크 처리량 및 IO로 특별히 설계 되었습니다. 이 가상 머신 시리즈는 빅 데이터 시나리오, 데이터 웨어하우징 및 대규모 트랜잭션 데이터베이스를 대상으로 합니다. 

#### <a name="lsv2-series"></a>Lsv2 시리즈

[Lsv2 시리즈](../../../virtual-machines/lsv2-series.md) 는 높은 처리량, 짧은 대기 시간 및 로컬 NVMe 저장소 기능을 제공 합니다. Lsv2 시리즈 Vm은 내구성이 있는 데이터 디스크를 사용 하는 대신 VM에 직접 연결 된 노드에서 로컬 디스크를 사용 하도록 최적화 되어 있습니다. 

이러한 가상 머신은 빅 데이터, 데이터 웨어하우스, 보고 및 ETL 워크 로드에 대 한 강력한 옵션입니다. 로컬 NVMe 저장소의 높은 처리량 및 IOPs는 데이터베이스에 로드 되는 파일을 처리 하는 데 적합 한 사용 사례 이며 원본 시스템이 나 Azure Blob 저장소 또는 Azure Data Lake 같은 다른 리포지토리에서 원본 데이터를 다시 만들 수 있는 기타 시나리오입니다. [Lsv2 시리즈](../../../virtual-machines/lsv2-series.md) Vm은 한 번에 최대 30 분 동안 디스크 성능을 버스트 할 수도 있습니다.

이러한 가상 머신은 vCPU 당 8 GiB 메모리를 포함 하는 8 개에서 80 vCPU, 8 개 Vcpu 마다 1.92 TB의 NVMe SSD를 포함 합니다. 즉,이 시리즈의 가장 큰 VM, [L80s_v2](../../../virtual-machines/lsv2-series.md), 10 배 1.92 Tb의 NVMe 저장소를 사용 하는 80 vcpu 및 640 연령의 메모리를 사용 합니다.  모든 가상 컴퓨터에서 8의 메모리 대 vCore 비율이 일관 됩니다.

NVMe 저장소는 가상 컴퓨터를 다시 시작 하는 경우 이러한 디스크에서 데이터가 손실 되는 것을 의미 합니다.

Lsv2 및 Ls 시리즈는 premium [storage를 지원 하지만](../../../virtual-machines/premium-storage-performance.md)프리미엄 저장소 캐싱은 지원 하지 않습니다. IOPs를 늘리기 위해 로컬 캐시를 만드는 것은 지원 되지 않습니다. 

> [!WARNING]
> 임시 NVMe 저장소에 데이터 파일을 저장 하면 VM 할당이 취소 될 때 데이터 손실이 발생할 수 있습니다. 

### <a name="constrained-vcores"></a>제약이 있는 vCores

높은 성능의 SQL Server 워크 로드에는 더 높은 vCore 수가 없는 많은 양의 메모리, IO 및 처리량이 필요 합니다. 

대부분의 OLTP 워크 로드는 많은 수의 작은 트랜잭션으로 구동 되는 응용 프로그램 데이터베이스입니다. OLTP 워크 로드를 사용 하는 경우 소량의 데이터를 읽거나 수정 하지만 사용자 수를 기반으로 하는 트랜잭션 볼륨이 훨씬 더 높습니다. 캐시 계획에 사용할 수 있는 SQL Server 메모리가 있어야 하 고, 최근에 액세스 한 데이터를 성능에 저장 하 고, 물리적 읽기를 메모리에 빠르게 읽을 수 있는지 확인 하는 것이 중요 합니다. 

이러한 OLTP 환경에는 더 많은 양의 메모리, 빠른 저장소 및 최적으로 수행 하는 데 필요한 i/o 대역폭이 필요 합니다. 

더 높은 SQL Server 라이선스 비용 없이이 수준의 성능을 유지 하기 위해 Azure는 [제한 된 vCPU](../../../virtual-machines/constrained-vcpu.md)수를 포함 하는 VM 크기를 제공 합니다. 

그러면 부모 가상 머신의 동일한 메모리, 저장소 및 i/o 대역폭을 유지 하면서 사용 가능한 vCores를 줄임으로써 라이선스 비용을 제어할 수 있습니다.

VCPU 수는 원래 VM 크기의 1/2 ~ 1 사분기로 제한 될 수 있습니다. 가상 컴퓨터에 사용할 수 있는 Vcores를 줄이면 메모리 대 Vcores 비율을 높일 수 있습니다.

이러한 새 VM 크기에는 더 쉽게 식별할 수 있도록 활성 vCPUs 수를 지정 하는 접미사가 있습니다. 

예를 들어 [M64](../../../virtual-machines/constrained-vcpu.md) 에는 [M64ms](../../../virtual-machines/m-series.md) 의 메모리, IO 및 처리량을 포함 하는 vcores 32 SQL Server vcores가 필요 하 고 [M64-16ms](../../../virtual-machines/constrained-vcpu.md) 에는 16 개 vcores만 라이선스를 요구 합니다.  [M64-16ms](../../../virtual-machines/constrained-vcpu.md) 에는 M64ms의 SQL Server 라이선스 비용에 대 한 분기가 있지만 가상 머신의 계산 비용은 동일 합니다.

> [!NOTE] 
> - 중간에서 대규모 데이터 웨어하우스 워크 로드는 제약이 있는 [vCore vm](../../../virtual-machines/constrained-vcpu.md)을 활용할 수 있지만, 데이터 웨어하우스 워크 로드는 병렬 실행 되는 쿼리 계획을 통해 더 많은 양의 데이터를 처리 하는 더 작은 사용자 및 프로세스에 의해 일반적으로 특징을 갖습니다. 
> - 운영 체제 라이선스를 포함 하는 계산 비용은 부모 가상 머신과 동일 하 게 유지 됩니다. 

## <a name="storage-guidance"></a>스토리지 지침

TPC-E 및 TPC-C 벤치 마크를 사용 하 여 Azure Virtual Machines에서 SQL Server 성능에 대 한 자세한 테스트는 [OLTP 성능 최적화](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)블로그를 참조 하세요. 

프리미엄 SSD를 사용하는 Azure Blob 캐시는 모든 프로덕션 워크로드에 권장됩니다. 

> [!WARNING]
> 표준 HDD 및 SSD는 대기 시간 및 대역폭이 다양하므로 개발/테스트 워크로드에만 권장됩니다. 프로덕션 워크로드에는 프리미엄 SSD를 사용해야 합니다.

또한 전송 지연을 줄이기 위해 SQL Server 가상 머신과 동일한 데이터 센터에서 Azure Storage 계정을 만드는 것이 좋습니다. 스토리지 계정을 만들 때 여러 디스크 간에 일관된 쓰기 순서가 보장되지 않기 때문에 지역에서 복제를 사용하지 않도록 설정합니다. 대신 두 Azure 데이터 센터 간에 SQL Server 재해 복구 기술을 구성하는 것이 좋습니다. 자세한 내용은 [Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구](business-continuity-high-availability-disaster-recovery-hadr-overview.md)를 참조하세요.

## <a name="disks-guidance"></a>디스크 지침

Azure virtual machines에는 세 가지 주요 디스크 유형이 있습니다.

* **OS 디스크**: Azure 가상 머신을 만들 때 플랫폼은 운영 체제 디스크에 대 한 VM에 하나 이상의 디스크 ( **C** 드라이브로 레이블이 지정 됨)를 연결 합니다. 이 디스크는 스토리지에 페이지 Blob으로 저장된 VHD입니다.
* **임시 디스크**: Azure 가상 머신은 임시 디스크(**D**: 드라이브로 레이블이 지정됨)라는 다른 디스크를 포함합니다. 이는 스크래치 공간에 사용할 수 있는 노드의 디스크입니다.
* **데이터 디스크**: 추가 디스크는 가상 머신에 데이터 디스크로 연결될 수도 있으며 스토리지에 페이지 Blob으로 저장됩니다.

다음 섹션에서는 이러한 서로 다른 디스크를 사용하기 위한 권장 사항을 설명합니다.

### <a name="operating-system-disk"></a>운영 체제 디스크

운영 체제 디스크는 운영 체제의 실행 중인 버전으로 부팅 하 고 탑재할 수 있는 VHD 이며 **C** 드라이브로 레이블이 지정 됩니다.

운영 체제 디스크의 기본 캐싱 정책은 **읽기/쓰기** 입니다. 성능이 중요한 애플리케이션의 경우 운영 체제 디스크 대신에 데이터 디스크를 사용하는 것이 좋습니다. 아래의 데이터 디스크에 관한 섹션을 참조하세요.

### <a name="temporary-disk"></a>임시 디스크

**D** 드라이브로 레이블이 지정 된 임시 저장소 드라이브는 Azure Blob storage에 유지 되지 않습니다. 사용자 데이터베이스 파일 또는 사용자 트랜잭션 로그 파일은 **D**: 드라이브에 저장하지 않도록 합니다.

TempDB는 중요 업무를 위한 SQL Server 워크로드용 로컬 SSD `D:\` 드라이브에 넣습니다(올바른 VM 크기를 선택한 후). Azure Portal 또는 Azure 빠른 시작 템플릿에서 VM을 만들고 [임시 DB를 로컬 디스크에 저장](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)하는 경우에는 추가 작업이 필요 하지 않습니다. 다른 모든 경우에는를 다시 시작한 후 오류를 방지 하기 위해  [ssd를 사용 하 여 TempDB를 저장](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) 하기 위한 블로그의 단계를 따르세요. 로컬 드라이브의 용량이 임시 DB 크기에 충분하지 않은 경우 [읽기 전용 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching)을 사용하여 프리미엄 SSD 디스크에 [스트라이프된](../../../virtual-machines/premium-storage-performance.md) 스토리지 풀에 임시 DB를 넣습니다.

프리미엄 SSD를 지원하는 VM의 경우 읽기 캐싱을 사용하도록 설정된 프리미엄 SSD를 지원하는 디스크에도 TempDB를 저장할 수 있습니다.


### <a name="data-disks"></a>데이터 디스크

* **데이터 및 로그 파일에 프리미엄 SSD 디스크 사용**: 디스크 스트라이프를 사용하지 않는 경우 프리미엄 SSD 디스크 2개(로그 파일용 1개 및 데이터용 1개)를 사용합니다. 각 프리미엄 SSD는 [디스크 유형 선택](../../../virtual-machines/disks-types.md) 문서에 설명된 대로 해당 크기에 따라 여러 IOPS 및 대역폭(MB/초)을 제공합니다. 스토리지 공간과 같은 디스크 스트라이프 기술을 사용하는 경우 로그 파일 및 데이터 파일에 대한 다른 두 개의 풀을 소유함으로써 최적의 성능을 얻습니다. 그러나 SQL Server FCI(장애 조치(failover) 클러스터 인스턴스)를 사용하려는 경우 하나의 풀을 구성하거나 [프리미엄 파일 공유](failover-cluster-instance-premium-file-share-manually-configure.md)를 대신 활용해야 합니다.

   > [!TIP]
   > - 다양 한 디스크 및 워크 로드 구성에 대 한 테스트 결과는 [Azure Virtual Machines에서 SQL Server에 대 한 저장소 구성 지침](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm)블로그 게시물을 참조 하세요.
   > - ~50,000 IOPS가 필요한 중요 업무용 고성능 SQL Server의 경우 -P30 디스크 10개를 울트라 SSD로 바꾸는 방안을 고려해 보세요. 자세한 내용은 블로그 게시물 [울트라 SSD를 사용하는 중요 업무용 고성능](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/)을 참조하세요.

   > [!NOTE]
   > 포털에서 SQL Server VM을 프로비전하는 경우 스토리지 구성을 편집하는 옵션이 있습니다. 구성에 따라 Azure에서는 하나 이상의 디스크를 구성합니다. 여러 디스크를 제거하여 단일 스토리지 풀로 결합합니다. 이 구성에서는 데이터 및 로그 파일이 함께 배치됩니다. 자세한 내용은 [SQL Server VM에 대한 스토리지 구성](storage-configuration.md)을 참조하세요.

* **디스크 스트라이프**: 더 많은 처리량이 필요한 경우 추가 데이터 디스크를 추가 하 고 디스크 스트라이프를 사용할 수 있습니다. 데이터 디스크 수를 확인하려면 로그 파일과 데이터 및 TempDB 파일에 필요한 IOPS 및 대역폭 수를 분석해야 합니다. VM 크기에 따라 IOPs 및 대역폭 수에 대한 제한이 다릅니다. [VM 크기](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)별 IOPS에 대한 표를 참조하세요. 다음 지침을 사용하세요.

  * Windows 8/Windows Server 2012 이상인 경우 다음 지침을 통해 [스토리지 공간](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11))을 사용합니다.

      1. 파티션 잘못 맞춤으로 인한 영향이 성능에 미치지 않도록 하려면 OLTP 워크로드에는 64KB(65,536바이트), 데이터 웨어하우징 워크로드에는 256KB(262,144바이트)로 인터리빙(스트라이프 크기)을 설정합니다. 이는 PowerShell로 설정되어야 합니다.
      2. 열 수를 실제 디스크 수로 설정합니다. 8개 이상의 디스크(서버 관리자 UI 아님)를 구성하는 경우 PowerShell을 사용합니다. 

    예를 들어 다음 PowerShell은 인터리브 크기가 64 KB이 고 저장소 풀의 실제 디스크 양과 동일한 열 수를 사용 하 여 새 저장소 풀을 만듭니다.

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Windows 2008 R2 또는 이전 버전에서는 동적 디스크(OS 스트라이프 볼륨)를 사용할 수 있으며 스트라이프 크기는 항상 64KB입니다. 이 옵션은 Windows 8/Windows Server 2012부터 사용되지 않습니다. 자세한 내용은 [가상 디스크 서비스가 Windows 스토리지 관리 API로 전환](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api)에 있는 지원 설명을 참조하세요.

  * [SQL Server 장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-storage-spaces-direct-manually-configure.md)와 함께 [S2D(스토리지 공간 다이렉트)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm)를 사용하는 경우 단일 풀을 구성해야 합니다. 해당 단일 풀에서 여러 볼륨을 만들 수 있지만 모두 동일한 캐싱 정책 등의 동일한 특성을 공유합니다.

  * 예상되는 부하에 따라 스토리지 풀에 연결되는 디스크 수를 결정합니다. VM 크기가 다르면 연결된 데이터 디스크 수도 다를 수 있다는 점에 유의하세요. 자세한 내용은 [가상 컴퓨터의 크기](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조 하세요.

  * Premium Ssd (개발/테스트 시나리오)를 사용 하지 않는 경우 [VM 크기](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 에서 지 원하는 최대 데이터 디스크 수를 추가 하 고 디스크 스트라이프를 사용 하는 것이 좋습니다.

* **캐싱 정책**: 스토리지 구성에 따라 캐싱 정책에 대한 다음 권장 사항을 확인합니다.

  * 데이터 및 로그 파일에 별도의 디스크를 사용하는 경우 데이터 파일 및 TempDB 데이터 파일을 호스트하는 데이터 디스크에서 읽기 캐싱을 사용하도록 설정합니다. 이렇게 하면 성능이 대폭 향상될 수 있습니다. 로그 파일을 보관하는 디스크에서 캐시를 사용하도록 설정하면 성능이 약간 저하되므로 설정하지 마세요.

  * 단일 스토리지 풀에서 디스크 스트라이프를 사용하는 경우 대부분의 워크로드에서 읽기 캐싱의 이점이 있습니다. 로그 및 데이터 파일에 대한 별도 스토리지 풀이 있는 경우 데이터 파일에 대한 스토리지 풀에만 읽기 캐싱을 활성화합니다. 쓰기 워크로드가 대량으로 있는 경우 캐싱을 사용하지 않을 때 성능이 향상될 수 있습니다. 이는 테스트를 통해서만 확인할 수 있습니다.

  * 이전 권장 사항은 프리미엄 SSD에 적용됩니다. 프리미엄 SSD를 사용하지 않는 경우 데이터 디스크에서 캐싱을 사용하도록 설정하지 마세요.

  * 디스크 캐싱 구성에 대한 지침은 다음 문서를 참조하세요. 클래식(ASM) 배포 모델의 경우 다음을 참조하세요. [Set-AzureOSDisk](/previous-versions/azure/jj152847(v=azure.100)) 및 [Set-AzureDataDisk](/previous-versions/azure/jj152851(v=azure.100)). Azure Resource Manager 배포 모델의 경우 다음을 참조하세요. [Set-AzOSDisk](/powershell/module/az.compute/set-azvmosdisk) 및 [Set-AzVMDataDisk](/powershell/module/az.compute/set-azvmdatadisk)

     > [!WARNING]
     > 데이터베이스 손상 가능성을 방지 하기 위해 Azure Virtual Machines 디스크의 캐시 설정을 변경 하는 경우 SQL Server 서비스를 중지 합니다.

* **NTFS 할당 단위 크기**: 데이터 디스크를 포맷할 때 TempDB뿐만 아니라 데이터 및 로그 파일에 대해 64KB 할당 단위 크기를 사용하는 것이 좋습니다. TempDB가 임시 디스크에 배치 되는 경우 (D:\ 드라이브)이 드라이브를 활용 하 여 얻은 성능은 64 KB 할당 단위 크기에 대 한 필요성 보다 큽니다. 

* **디스크 관리 모범 사례**: 데이터 디스크를 제거하거나 캐시 유형을 변경할 때 변경하는 동안 SQL Server 서비스를 중지합니다. OS 디스크에 대한 캐싱 설정이 변경되면 Azure는 VM을 중지하고, 캐시 유형을 변경하고 VM을 다시 시작합니다. 데이터 디스크의 캐시 설정이 변경될 경우 변경 중에 VM은 중지되지 않지만 데이터 디스크가 VM에서 분리되었다가 다시 연결됩니다.

  > [!WARNING]
  > 이러한 작업 중에 SQL Server 서비스를 중지하지 못하면 데이터베이스가 손상될 수 있습니다.


## <a name="io-guidance"></a>I/O 지침

* 프리미엄 SSD를 사용하여 최상의 결과를 얻으려면 애플리케이션과 요청을 병렬 처리합니다. 프리미엄 SSD는 IO 큐 크기가 1보다 큰 시나리오에 맞게 설계되었기 때문에 스토리지를 많이 사용하더라도 단일 스레드 직렬 요청에 대한 성능 이점이 거의 없거나 전혀 없습니다. 예를 들어 이는 SQLIO와 같은 성능 분석 도구의 단일 스레드 테스트 결과에 영향을 줄 수 있습니다.

* [데이터베이스 페이지 압축](/sql/relational-databases/data-compression/data-compression)을 사용하면 I/O가 많은 작업의 성능이 향상될 수 있습니다. 그러나 데이터 압축은 데이터베이스 서버의 CPU 사용량을 늘릴 수 있습니다.

* 초기 파일 할당에 필요한 시간을 줄이기 위해 즉시 파일 초기화를 사용하도록 설정하는 것이 좋습니다. 즉시 파일 초기화를 이용하려면 SE_MANAGE_VOLUME_NAME으로 SQL Server(MSSQLSERVER) 서비스 계정을 부여하고 이를 **볼륨 유지 관리 작업 수행** 보안 정책에 추가합니다. Azure용 SQL Server 플랫폼 이미지를 사용하면 기본 서비스 계정(NT Service\MSSQLSERVER)이 **볼륨 유지 관리 작업 수행** 보안 정책에 추가되지 않습니다. 즉, 즉시 파일 초기화는 SQL Server Azure 플랫폼 이미지에서 사용하도록 설정되어 있지 않습니다. SQL Server 서비스 계정을 **볼륨 유지 관리 작업 수행** 보안 정책에 추가한 후 SQL Server 서비스를 다시 시작합니다. 이 기능을 사용하기 위한 보안 고려 사항이 있을 수 있습니다. 자세한 내용은 [데이터베이스 파일 초기화](/sql/relational-databases/databases/database-instant-file-initialization)를 참조하세요.

* 자동 **증가** 는 예기치 않은 증가에 대 한 단순한 대체 방법으로 간주 됩니다. 일상적으로 자동 증가를 사용하여 사용자 데이터 및 로그 증가를 관리하지 마세요. 자동 증가를 사용하면 Size 스위치를 사용하여 파일을 사전에 증가시킵니다.

* **자동 축소** 를 사용하지 않도록 설정하여 성능에 부정적인 영향을 미칠 수 있는 불필요한 오버헤드를 방지합니다.

* 시스템 데이터베이스를 포함하여 모든 데이터베이스를 데이터 디스크로 이동합니다. 자세한 내용은 [시스템 데이터베이스 이동](/sql/relational-databases/databases/move-system-databases)을 참조하세요.

* SQL Server 오류 로그 및 추적 파일 디렉터리를 데이터 디스크로 이동합니다. SQL Server 구성 관리자에서 SQL Server 인스턴스를 마우스 오른쪽 단추로 클릭하고 속성을 선택하여 이를 수행할 수 있습니다. 오류 로그 및 추적 파일 설정은 **시작 매개 변수** 탭에서 변경할 수 있습니다. 덤프 디렉터리는 **고급** 탭에서 지정합니다. 다음 스크린샷에서는 오류 로그 시작 매개 변수를 찾을 위치를 보여 줍니다.

    ![SQL 오류 로그 스크린샷](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* 기본 백업 및 데이터베이스 파일 위치를 설정 합니다. 이 문서의 권장 사항을 사용하여 서버 속성 창의 설정을 변경합니다. 지침은 [데이터 및 로그 파일의 기본 위치 보기 또는 변경(SQL Server Management Studio)](/sql/database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files)을 참조하세요. 다음 스크린샷에서는 이러한 변경 작업을 수행하는 위치를 보여 줍니다.

    ![SQL 데이터 로그 및 Backup 파일](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* 잠긴 페이지를 사용하도록 설정하여 IO 및 페이징 작업을 줄입니다. 자세한 내용은 [메모리 내 페이지 잠금 옵션 사용(Windows)](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows)을 참조하세요.

* SQL Server 2012를 실행 중인 경우 서비스 팩 1 누적 업데이트 10을 설치합니다. 이 업데이트에는 SQL Server 2012에서 임시 테이블에 대한 select 문을 실행할 때 I/O 성능 저하에 대한 픽스가 포함되어 있습니다. 자세한 내용은 [기술 자료 문서](https://support.microsoft.com/kb/2958012)를 참조하세요.

* 모든 데이터 파일은 Azure에서 송수신할 때 압축하는 것이 좋습니다.

## <a name="feature-specific-guidance"></a>기능별 지침

더 많은 고급 구성 기술을 사용하면 일부 배포에서 추가적인 성능 이점을 얻을 수 있습니다. 다음 목록에는 성능 개선에 도움이 되는 일부 SQL Server 기능이 나와 있습니다.

### <a name="back-up-to-azure-storage"></a>Azure Storage에 백업
Azure Virtual Machines에서 실행 되는 SQL Server에 대 한 백업을 수행 하는 경우 [URL에 백업 SQL Server](/sql/relational-databases/backup-restore/sql-server-backup-to-url)사용할 수 있습니다. 이 기능은 SQL Server 2012 SP1 CU2부터 사용할 수 있으며 연결된 데이터 디스크에 백업하는 것이 좋습니다. Azure Storage에서 백업/복원 하는 경우 [URL에 대 한 백업 모범 사례 및 문제 해결 및 Azure Storage에 저장 된 백업에서 복원에서 SQL Server](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)제공 하는 권장 사항을 따르세요. [Azure Virtual Machines에서 SQL Server에 대 한 자동화 된 백업을](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)사용 하 여 이러한 백업을 자동화할 수도 있습니다.

SQL Server 2012 이전 버전에서는 [Azure에 SQL Server Backup 도구](https://www.microsoft.com/download/details.aspx?id=40740)를 사용할 수 있습니다. 이 도구는 여러 백업 스트라이프 대상을 사용하여 백업 처리량을 늘릴 수 있습니다.

### <a name="sql-server-data-files-in-azure"></a>Azure의 SQL Server 데이터 파일

SQL Server 2014부터 새로운 기능인 [Azure의 SQL Server 데이터 파일](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)을 사용할 수 있습니다. Azure에서 데이터 파일로 SQL Server를 실행하면 Azure 데이터 디스크를 사용하는 것과 견줄 만한 성능 특성을 보여 줍니다.

### <a name="failover-cluster-instance-and-storage-spaces"></a>장애 조치(Failover) 클러스터 및 스토리지 공간

저장소 공간을 사용 하는 경우 **확인** 페이지에서 클러스터에 노드를 추가 하는 경우 **클러스터에 사용할 수 있는 모든 저장소를 추가** 합니다. 확인란의 선택을 취소 합니다. 

![적격 스토리지 선택 취소](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

만약 스토리지 공간을 사용하면서 **클러스터에 사용할 수 있는 모든 스토리지를 추가하세요.** 확인란을 선택 취소하지 않으면 Windows에서 클러스터링 프로세스 도중 가상 디스크를 분리합니다. 그 결과, 스토리지 공간이 클러스터에서 제거되고 PowerShell을 사용하여 다시 연결할 때까지 디스크 관리자 또는 탐색기에 표시되지 않습니다. 스토리지 공간은 여러 디스크를 스토리지 풀로 그룹화합니다. 자세한 내용은 [스토리지 공간](/windows-server/storage/storage-spaces/overview)을 참조하세요.

## <a name="multiple-instances"></a>여러 인스턴스 

단일 가상 머신에 여러 SQL Server 인스턴스를 배포 하는 경우 다음 모범 사례를 고려 합니다. 

- 운영 체제에 대해 남아 있는 메모리가 있는지 확인 하 여 각 SQL Server 인스턴스에 대 한 최대 서버 메모리를 설정 합니다. 가상 컴퓨터에 할당 된 메모리 양을 변경 하는 경우 SQL Server 인스턴스에 대 한 메모리 제한을 업데이트 해야 합니다. 
- 데이터, 로그 및 TempDB에는 서로 다른 워크 로드 패턴이 있으며 서로에 영향을 주지 않기 때문에 별도의 Lun이 있어야 합니다. 
- 응용 프로그램 Sla 내에서 최대 작업량을 처리할 수 있도록 프로덕션과 유사한 워크 로드에서 환경을 철저히 테스트 합니다. 

오버 로드 된 시스템의 부호에는 작업자 스레드 고갈, 저속 응답 시간 및/또는 지연 된 디스패처 시스템 메모리가 포함 될 수 있습니다. 



## <a name="collect-performance-baseline"></a>성능 기준선 수집

보다 규범적인 접근법을 위해 PerfMon/LogMan를 사용 하 여 성능 카운터를 수집 하 고 SQL Server 대기 통계를 캡처하여 원본 환경의 일반적인 pressures 및 잠재적 병목 상태를 보다 잘 이해할 수 있습니다. 

[응용 프로그램 성능 검사 목록](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)에 따라 최대 사용 시간에 원본 워크 로드의 CPU, 메모리, [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), [처리량](../../../virtual-machines/premium-storage-performance.md#throughput)및 [대기 시간](../../../virtual-machines/premium-storage-performance.md#latency) 을 수집 하 여 시작 합니다. 

일반적인 업무 시간 동안 워크 로드와 같이 사용량이 많은 시간 동안 데이터를 수집 하 고, 하루 종일 처리 및 주말 ETL 워크 로드와 같은 기타 높은 부하 프로세스를 수행 합니다. 분기 간 처리와 같은 많은 작업을 atypically 하는 리소스를 확장 한 다음, 워크 로드가 완료 되 면 확장을 수행 하는 것이 좋습니다. 

성능 분석을 사용 하 여 워크 로드의 성능 요구 사항에 맞게 확장할 수 있는 [VM 크기](../../../virtual-machines/sizes-memory.md) 를 선택 합니다.


### <a name="iops-and-throughput"></a>IOPS 및 처리량

SQL Server 성능은 i/o 하위 시스템에 따라 크게 달라 집니다. 데이터베이스가 실제 메모리에 적합 하지 않을 경우 SQL Server는 데이터베이스 페이지를 버퍼 풀에 계속 해 서 제공 합니다. SQL Server에 대 한 데이터 파일은 다르게 처리 되어야 합니다. TempDB를 비롯 한 데이터 파일에 임의로 액세스 되는 트랜잭션을 롤백해야 하는 경우를 제외 하 고 로그 파일에 대 한 액세스는 순차적으로 실행 됩니다. 저속 i/o 하위 시스템이 있는 경우 사용자에 게는 속도가 느리거나 시간 초과로 인해 완료 되지 않는 작업과 같은 성능 문제가 발생할 수 있습니다. 

Azure Marketplace 가상 컴퓨터에는 기본적으로 데이터 파일과 별도의 실제 디스크에 로그 파일이 있습니다. TempDB 데이터 파일 수 및 크기는 모범 사례를 충족 하 고 임시 드라이브.. 

다음 PerfMon 카운터는 SQL Server에 필요한 IO 처리량의 유효성을 검사 하는 데 도움이 될 수 있습니다. 
* **\LogicalDisk\Disk Reads/Sec** (읽기 및 쓰기 IOPS)
* **\LogicalDisk\Disk Writes/Sec** (읽기 및 쓰기 IOPS) 
* **\LogicalDisk\Disk Bytes/Sec** (데이터, 로그 및 TempDB 파일에 대 한 처리량 요구 사항)

최고 수준에서 IOPS 및 처리량 요구 사항을 사용 하 여 사용자 측정의 용량과 일치 하는 VM 크기를 평가 합니다. 

작업에 20 K 읽기 IOPS 및 10K write IOPS가 필요한 경우 저장소 공간을 사용 하 여 2 개의 P30 디스크 스트라이프를 사용 하 여 E16s_v3 (최대 32 K 및 25600 캐시 되지 않은 IOPS 사용) 또는 M16_s (최대 20 K 캐시 및 10K 캐시 되지 않은 IOPS 사용)를 선택할 수 있습니다. 

Vm은 IOPS 및 처리량에 대 한 확장 제한이 서로 다르므로 워크 로드의 처리량 및 IOPS 요구 사항을 모두 이해 해야 합니다.

### <a name="memory"></a>메모리

SQL Server에서 내부적으로 사용 하는 메모리 뿐만 아니라 OS에서 사용 하는 외부 메모리를 모두 추적 합니다. 어느 구성 요소에 대 한 압력을 식별 하는 것은 가상 머신의 크기를 조정 하는 데 도움이 되며, 

다음 PerfMon 카운터는 SQL Server 가상 컴퓨터의 메모리 상태를 확인 하는 데 도움이 될 수 있습니다. 
* [\Memory\사용 가능한 MB](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: Memory Manager\Target Server Memory (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: Memory Manager\Total Server Memory (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: Buffer Manager\Lazy writes/sec](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: Buffer Manager\Page 수명 예상](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

### <a name="compute--processing"></a>계산/처리

Azure에서 계산은 온-프레미스와 다르게 관리 됩니다. 온-프레미스 서버는 관리 오버 헤드 및 새 하드웨어 획득 비용으로 인해 업그레이드 하지 않고 지난 몇 년간 빌드됩니다. 가상화는 이러한 문제 중 일부를 완화 하지만 응용 프로그램은 기본 하드웨어의 장점을 최대한 활용 하도록 최적화 되어 있습니다. 즉, 리소스 소비에 대 한 중요 한 변경 사항은 전체 물리적 환경을 균형 있게 조정 해야 합니다. 

이는 다른 하드웨어의 새 가상 컴퓨터와 다른 지역 에서도 쉽게 달성할 수 있는 Azure의 과제가 아닙니다. 

Azure에서는 가상 머신 리소스를 최대한 활용 하고자 하므로 워크 로드에 영향을 주지 않고 최대한 높은 평균 CPU를 유지 하도록 Azure 가상 머신을 구성 해야 합니다. 

다음 PerfMon 카운터는 SQL Server 가상 컴퓨터의 계산 상태를 확인 하는 데 도움이 될 수 있습니다.
* **\Processor Information (_Total) \% 프로세서 시간**
* **\Process (sqlservr.exe) \% 프로세서 시간**

> [!NOTE] 
> 80% 이상의 계산을 사용 하는 것이 가장 좋지만, 90%를 초과 하지만 지속적으로 지속 되는 기간에는 100%에 도달 하지 않습니다. 기본적으로, 응용 프로그램에 필요한 계산을 프로 비전 하 고 비즈니스에 필요한 만큼 규모를 확장 하거나 축소할 계획을 세워야 합니다. 

## <a name="next-steps"></a>다음 단계

보안 모범 사례는 [Azure Virtual Machines에서 SQL Server에 대 한 보안 고려 사항](security-considerations-best-practices.md)을 참조 하세요.

[Azure Virtual Machines의 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)에서 다른 SQL Server 가상 머신 문서를 검토하세요. SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](frequently-asked-questions-faq.md)을 참조하세요.