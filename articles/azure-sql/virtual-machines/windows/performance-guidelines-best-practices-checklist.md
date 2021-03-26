---
title: '검사 목록: 성능 모범 사례 & 지침'
description: Azure VM (가상 머신)에서 SQL Server의 성능을 최적화 하기 위한 모범 사례 및 지침을 검토 하는 빠른 검사 목록을 제공 합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 0b88884576a47db871c78b874104d4973ee9ba9a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572457"
---
# <a name="checklist-performance-best-practices-for-sql-server-on-azure-vms"></a>검사 목록: Azure Vm의 SQL Server에 대 한 성능 모범 사례
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure Virtual Machines (Vm)에서 SQL Server의 성능을 최적화 하기 위한 일련의 모범 사례 및 지침을 제공 합니다. 

자세한 내용은이 시리즈의 다른 문서 ( [VM 크기](performance-guidelines-best-practices-vm-size.md), [저장소](performance-guidelines-best-practices-storage.md), [기준 수집](performance-guidelines-best-practices-collect-baseline.md))를 참조 하세요. 


## <a name="overview"></a>개요

Azure Virtual Machines에서 SQL Server를 실행 하는 동안 온-프레미스 서버 환경에서 SQL Server에 적용 되는 것과 동일한 데이터베이스 성능 튜닝 옵션을 계속 사용 합니다. 그러나 공용 클라우드의 관계형 데이터베이스 성능은 가상 머신의 크기 및 데이터 디스크의 구성과 같은 많은 요인에 따라 달라 집니다.

일반적으로 비용에 대한 최적화와 성능에 대한 최적화 간의 절충이 있습니다. 이 성능 모범 사례 시리즈는 Azure Virtual Machines에서 SQL Server에 대 한 *최상의* 성능을 얻는 데 중점을 두었습니다. 워크 로드가 더 까다로운 경우 모든 권장 최적화를 요구 하지 않을 수 있습니다. 이러한 권장 사항을 평가할 때 성능 요구 사항, 비용 및 작업 패턴을 고려하세요.

## <a name="vm-size"></a>VM 크기

다음은 Azure VM에서 SQL Server를 실행 하기 위한 VM 크기 모범 사례에 대 한 빠른 검사 목록입니다. 

- [Standard_M8-4ms](/../../virtual-machines/m-series), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series), [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 이상 등 4 개 이상의 vcpu가 포함 된 VM 크기를 사용 합니다. 
- 메모리 액세스에 [최적화](../../../virtual-machines/sizes-memory.md) 된 가상 머신 크기를 사용 하 여 SQL Server 워크 로드의 성능을 극대화 합니다. 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) 시리즈, [M-](../../../virtual-machines/m-series.md)및 [Mv2](../../../virtual-machines/mv2-series.md) 시리즈는 OLTP 워크 로드에 필요한 최적의 메모리-vcore 비율을 제공 합니다. M 시리즈 Vm은 업무에 중요 한 워크 로드에 필요한 메모리-vCore의 최고 비율을 제공 하며, 데이터 웨어하우스 워크 로드에도 적합 합니다. 
- 중요 업무용 및 데이터 웨어하우스 워크 로드에 대 한 메모리-vCore 비율을 높이는 것이 좋습니다. 
- SQL Server 설정 및 저장소 옵션이 최적의 SQL Server 성능을 위해 구성 되므로 Azure Virtual Machine marketplace 이미지를 사용 합니다. 
- 대상 워크 로드의 성능 특성을 수집 하 고이를 사용 하 여 비즈니스에 적합 한 VM 크기를 결정 합니다.

자세히 알아보려면 포괄적인 [VM 크기 모범 사례](performance-guidelines-best-practices-vm-size.md)를 참조 하세요. 

## <a name="storage"></a>Storage

다음은 Azure VM에서 SQL Server를 실행 하기 위한 저장소 구성 모범 사례에 대 한 빠른 검사 목록입니다. 

- 응용 프로그램을 모니터링 하 고 디스크 유형을 선택 하기 전에 SQL Server 데이터, 로그 및 tempdb 파일에 대 한 [저장소 대역폭 및 대기 시간 요구 사항을 확인](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) 합니다. 
- 저장소 성능을 최적화 하려면 사용 가능한 최고 캐시 되지 않은 IOPS를 계획 하 고, [가상 머신 및 디스크 50,/제한을](../../../virtual-machines/premium-storage-performance.md#throttling)방지 하면서 데이터 읽기에 대 한 성능 기능으로 데이터 캐싱을 사용 합니다.
- 데이터, 로그 및 tempdb 파일을 별도의 드라이브에 저장 합니다.
    - 데이터 드라이브의 경우 [Premium P30 및 P40 디스크만](../../../virtual-machines/disks-types.md#premium-ssd) 사용 하 여 캐시 지원의 가용성을 보장 합니다.
    - [프리미엄 P30-P80 디스크](../../../virtual-machines/disks-types.md#premium-ssd)를 평가 하는 동안 용량 및 테스트 성능과 비용에 대 한 로그 드라이브 계획
      - Submillisecond 저장소 대기 시간이 필요한 경우 트랜잭션 로그에 대해 [Azure ultra disks](../../../virtual-machines/disks-types.md#ultra-disk) 를 사용 합니다. 
      - M 시리즈 가상 머신 배포의 경우 Azure ultra disks를 사용 하 여 [쓰기 가속기](../../../virtual-machines/how-to-enable-write-accelerator.md) 하는 것이 좋습니다.
    - [](/sql/relational-databases/databases/tempdb-database) `D:\` 최적의 VM 크기를 선택한 후에 대부분의 SQL Server 작업에 대해 로컬 임시 SSD 드라이브에 tempdb를 넣습니다. 
      - 로컬 드라이브의 용량이 tempdb에 충분 하지 않은 경우 VM의 크기를 조정 하는 것이 좋습니다. 자세한 내용은 [데이터 파일 캐싱 정책](performance-guidelines-best-practices-storage.md#data-file-caching-policies) 을 참조 하세요.
- [저장소 공간](/windows-server/storage/storage-spaces/overview) 을 사용 하 여 여러 Azure 데이터 디스크를 스트라이프 하 여 대상 가상 머신의 IOPS 및 처리량 제한까지 i/o 대역폭을 늘립니다.
- 데이터 파일 디스크에 대해 [호스트 캐싱을](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) 읽기 전용으로 설정 합니다.
- 로그 파일 디스크에 대해 [호스트 캐싱을](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) none으로 설정 합니다.
    - SQL Server 파일을 포함 하는 디스크에서 읽기/쓰기 캐싱을 사용 하도록 설정 하지 마십시오. 
    - 디스크의 캐시 설정을 변경 하기 전에 항상 SQL Server 서비스를 중지 합니다.
- 개발 및 테스트 워크 로드의 경우 standard storage를 사용 하는 것이 좋습니다. 프로덕션 워크 로드에 표준 HDD/SDD를 사용 하지 않는 것이 좋습니다.
- [신용 기반 디스크 버스트](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20)는 소규모 개발/테스트 워크 로드 및 부서 시스템에만 고려 되어야 합니다.
- SQL Server VM와 동일한 지역에 저장소 계정을 프로 비전 합니다. 
- Azure 지역 중복 저장소 (지역에서 복제)를 사용 하지 않도록 설정 하 고 저장소 계정에서 LRS (로컬 중복 저장소)를 사용 합니다.
- 임시 드라이브가 아닌 드라이브에 배치 된 모든 데이터 파일에 대해 64 KB 할당 단위 크기를 사용 하도록 데이터 디스크의 형식을 지정 `D:\` 합니다 (기본값은 4kb). Azure Marketplace를 통해 배포 된 SQL Server Vm은 할당 단위 크기 및 64로 설정 된 저장소 풀의 인터리브로 포맷 된 데이터 디스크와 함께 제공 됩니다. 

자세히 알아보려면 포괄적인 [저장소 모범 사례](performance-guidelines-best-practices-storage.md)를 참조 하세요. 


## <a name="azure--sql-feature-specific"></a>Azure & SQL 기능 관련

다음은 Azure VM에서 SQL Server을 실행할 때 SQL Server 및 Azure 관련 구성에 대 한 모범 사례에 대 한 빠른 검사 목록입니다. 

- [SQL IaaS 에이전트 확장](sql-agent-extension-manually-register-single-vm.md) 에 등록 하 여 다양 한 [기능 혜택](sql-server-iaas-agent-extension-automate-management.md#feature-benefits)의 잠금을 해제 합니다. 
- 데이터베이스 페이지 압축을 사용하도록 설정합니다.
- 데이터 파일에 대해 즉시 파일 초기화를 사용하도록 설정합니다.
- 데이터베이스의 자동 증가를 제한 합니다.
- 데이터베이스의 자동 축소를 사용 하지 않도록 설정 합니다.
- 시스템 데이터베이스를 포함하여 모든 데이터베이스를 데이터 디스크로 이동합니다.
- SQL Server 오류 로그 및 추적 파일 디렉터리를 데이터 디스크로 이동합니다.
- 기본 백업 및 데이터베이스 파일 위치를 구성 합니다.
- [메모리에 잠긴 페이지를 사용하도록 설정합니다](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).
- 설치 된 SQL Server 버전에 대 한 [최신 누적 업데이트](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) 를 평가 하 고 적용 합니다.
- Azure Blob storage에 직접 백업 합니다.
- 여러 [tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) 파일, 코어 당 1 개의 파일, 최대 8 개의 파일을 사용 합니다.



## <a name="next-steps"></a>다음 단계

자세히 알아보려면이 시리즈의 다른 문서를 참조 하세요.
- [VM 크기](performance-guidelines-best-practices-vm-size.md)
- [스토리지](performance-guidelines-best-practices-storage.md)
- [기준선 수집](performance-guidelines-best-practices-collect-baseline.md)

보안 모범 사례는 [Azure Virtual Machines에서 SQL Server에 대 한 보안 고려 사항](security-considerations-best-practices.md)을 참조 하세요.

[Azure Virtual Machines의 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)에서 다른 SQL Server 가상 머신 문서를 검토하세요. SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](frequently-asked-questions-faq.md)을 참조하세요.
