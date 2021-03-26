---
title: '저장소: 성능 모범 사례 & 지침'
description: Azure VM (가상 머신)에서 SQL Server의 성능을 최적화 하기 위한 저장소 모범 사례 및 지침을 제공 합니다.
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 001a9a15c259d0b0d73eec9c9a39ad7c27f26721
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572522"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>저장소: Azure Vm의 SQL Server에 대 한 성능 모범 사례
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure Virtual Machines (Vm)에서 SQL Server의 성능을 최적화 하기 위한 저장소 모범 사례 및 지침을 제공 합니다.

일반적으로 비용에 대한 최적화와 성능에 대한 최적화 간의 절충이 있습니다. 이 성능 모범 사례 시리즈는 Azure Virtual Machines에서 SQL Server에 대 한 *최상의* 성능을 얻는 데 중점을 두었습니다. 워크 로드가 더 까다로운 경우 모든 권장 최적화를 요구 하지 않을 수 있습니다. 이러한 권장 사항을 평가할 때 성능 요구 사항, 비용 및 작업 패턴을 고려하세요.

자세히 알아보려면이 시리즈의 다른 문서 ( [성능 검사 목록](performance-guidelines-best-practices-checklist.md), [VM 크기](performance-guidelines-best-practices-vm-size.md)및 [기준 수집](performance-guidelines-best-practices-collect-baseline.md))를 참조 하세요. 

## <a name="checklist"></a>검사 목록

이 문서의 나머지 부분에서 자세히 설명 하는 저장소 모범 사례에 대 한 간략 한 개요를 보려면 다음 검사 목록을 검토 하세요. 

- 응용 프로그램을 모니터링 하 고 디스크 유형을 선택 하기 전에 SQL Server 데이터, 로그 및 tempdb 파일에 대 한 [저장소 대역폭 및 대기 시간 요구 사항을 확인](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) 합니다. 
- 저장소 성능을 최적화 하려면 사용 가능한 최고 캐시 되지 않은 IOPS를 계획 하 고, [가상 컴퓨터 및 디스크 50,](../../../virtual-machines/premium-storage-performance.md#throttling)을 방지 하는 동시에 데이터 읽기에 대 한 성능 기능으로 데이터 캐싱을 사용 합니다.
- 데이터, 로그 및 tempdb 파일을 별도의 드라이브에 저장 합니다.
    - 데이터 드라이브의 경우 [Premium P30 및 P40 디스크만](../../../virtual-machines/disks-types.md#premium-ssd) 사용 하 여 캐시 지원의 가용성을 보장 합니다.
    - [프리미엄 P30-P80 디스크](../../../virtual-machines/disks-types.md#premium-ssd) 를 평가 하는 동안 용량 및 테스트 성능과 비용에 대 한 로그 드라이브 계획
      - Submillisecond 저장소 대기 시간이 필요한 경우 트랜잭션 로그에 대해 [Azure ultra disks](../../../virtual-machines/disks-types.md#ultra-disk) 를 사용 합니다. 
      - M 시리즈 가상 머신 배포의 경우 Azure ultra disks를 사용 하는 [write accelerator](../../../virtual-machines/how-to-enable-write-accelerator.md) 를 고려 합니다.
    - [](/sql/relational-databases/databases/tempdb-database) `D:\` 최적의 VM 크기를 선택한 후에 대부분의 SQL Server 작업에 대해 로컬 임시 SSD 드라이브에 tempdb를 넣습니다. 
      - 로컬 드라이브의 용량이 tempdb에 충분 하지 않은 경우 VM의 크기를 조정 하는 것이 좋습니다. 자세한 내용은 [데이터 파일 캐싱 정책](#data-file-caching-policies) 을 참조 하세요.
- [저장소 공간](/windows-server/storage/storage-spaces/overview) 을 사용 하 여 여러 Azure 데이터 디스크를 스트라이프 하 여 대상 가상 머신의 IOPS 및 처리량 제한까지 i/o 대역폭을 늘립니다.
- 데이터 파일 디스크에 대해 [호스트 캐싱을](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) 읽기 전용으로 설정 합니다.
- 로그 파일 디스크에 대해 [호스트 캐싱을](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) none으로 설정 합니다.
    - SQL Server 파일을 포함 하는 디스크에서 읽기/쓰기 캐싱을 사용 하도록 설정 하지 마십시오. 
    - 디스크의 캐시 설정을 변경 하기 전에 항상 SQL Server 서비스를 중지 합니다.
- 개발 및 테스트 워크 로드와 장기 백업 보관에는 standard storage를 사용 하는 것이 좋습니다. 프로덕션 워크 로드에 표준 HDD/SDD를 사용 하지 않는 것이 좋습니다.
- [신용 기반 디스크 버스트](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20)는 소규모 개발/테스트 워크 로드 및 부서 시스템에만 고려 되어야 합니다.
- SQL Server VM와 동일한 지역에 저장소 계정을 프로 비전 합니다. 
- Azure 지역 중복 저장소 (지역에서 복제)를 사용 하지 않도록 설정 하 고 저장소 계정에서 LRS (로컬 중복 저장소)를 사용 합니다.
- 임시 드라이브가 아닌 드라이브에 배치 된 모든 데이터 파일에 대해 64 KB 할당 단위 크기를 사용 하도록 데이터 디스크의 형식을 지정 `D:\` 합니다 (기본값은 4kb). Azure Marketplace를 통해 배포 된 SQL Server Vm은 할당 단위 크기 및 64로 설정 된 저장소 풀의 인터리브로 포맷 된 데이터 디스크와 함께 제공 됩니다. 

저장소 검사 목록을 다른 목록과 비교 하려면 포괄적인 [성능 모범 사례 검사 목록](performance-guidelines-best-practices-checklist.md)을 참조 하세요. 

## <a name="overview"></a>개요

Azure VM에서 SQL Server 작업에 대 한 가장 효율적인 구성을 찾으려면 먼저 [비즈니스 응용 프로그램의 저장소 성능을 측정](performance-guidelines-best-practices-collect-baseline.md#storage)합니다. 저장소 요구 사항을 파악 한 후에는 적절 한 메모리-vCore 비율을 사용 하 여 필요한 IOPS 및 처리량을 지 원하는 가상 머신을 선택 합니다. 

워크 로드에 대 한 저장소 확장성이 충분 하 고 비즈니스의 용량 및 성능 요구 사항을 충족 하는 디스크 (일반적으로 저장소 풀)를 혼합 하 여 VM 크기를 선택 합니다. 

디스크 유형은 디스크에서 호스트 되는 파일 유형과 최고 성능 요구 사항에 따라 달라 집니다.

> [!TIP]
> Azure Portal를 통해 SQL Server VM를 프로 비전 하면 저장소 구성 프로세스를 안내 하 고, 데이터 및 로그 파일에 대 한 별도의 저장소 풀 만들기, 드라이브에 tempdb 대상 지정, `D:\` 최적의 캐싱 정책 사용과 같은 대부분의 저장소 모범 사례를 구현할 수 있습니다. 저장소를 프로 비전 하 고 구성 하는 방법에 대 한 자세한 내용은 [SQL VM 저장소 구성](storage-configuration.md)을 참조 하세요. 

## <a name="vm-disk-types"></a>VM 디스크 유형

디스크의 성능 수준에서 선택할 수 있습니다. 기본 저장소로 사용할 수 있는 관리 디스크 유형 (성능 향상 기능으로 표시 됨)은 표준 HDD (하드 디스크 드라이브), 표준 Ssd, 프리미엄 반도체 드라이브 (SSD) 및 ultra disks입니다. 

디스크의 성능은 용량이 4 GiB 인 P1 및 120 IOPS와 같은 [프리미엄 디스크 레이블을](../../../virtual-machines/disks-types.md#premium-ssd) 사용 하 여 저장소 및 2만 Iops의 P80을 32 사용 하 여 디스크를 늘립니다. Premium storage는 일부 워크 로드에 대 한 읽기 및 쓰기 성능을 개선 하는 데 도움이 되는 저장소 캐시를 지원 합니다. 자세한 내용은 [Managed disks 개요](../../../virtual-machines/managed-disks-overview.md)를 참조 하세요. 

Azure VM에서 SQL Server (OS 디스크, 임시 디스크 및 데이터 디스크)에 대해 고려해 야 할 세 가지 주요 [디스크 유형도](../../../virtual-machines/managed-disks-overview.md#disk-roles) 있습니다. 운영 체제 드라이브 및 임시 임시 드라이브에 저장 된 항목을 신중 하 게 선택 `(C:\)` `(D:\)` 합니다. 

### <a name="operating-system-disk"></a>운영 체제 디스크

운영 체제 디스크는 운영 체제의 실행 중인 버전으로 부팅 하 고 탑재할 수 있는 VHD 이며 드라이브로 레이블이 지정 됩니다 `C:\` . Azure 가상 컴퓨터를 만들 때 플랫폼은 운영 체제 디스크에 대 한 하나 이상의 디스크를 VM에 연결 합니다. `C:\`드라이브는 응용 프로그램 설치 및 파일 구성에 대 한 기본 위치입니다. 

프로덕션 SQL Server 환경의 경우 데이터 파일, 로그 파일, 오류 로그에 운영 체제 디스크를 사용 하지 마십시오. 

### <a name="temporary-disk"></a>임시 디스크

많은 Azure virtual machines는 임시 디스크 (드라이브로 레이블이 지정 됨) 라는 다른 디스크 유형을 포함 `D:\` 합니다. Virtual machine 시리즈 및 크기에 따라이 디스크의 용량은 달라질 수 있습니다. 임시 디스크는 사용 후 삭제 됩니다. 즉, 가상 컴퓨터를 다시 시작 하거나 다른 호스트 (예: [서비스 복구](/troubleshoot/azure/virtual-machines/understand-vm-reboot)의 경우)로 이동 하는 경우에는 디스크 저장소가 다시 만들어지고 (에서와 같이 할당 취소 및 할당 됨) 

임시 저장소 드라이브는 원격 저장소에 유지 되지 않으므로 사용자 데이터베이스 파일, 트랜잭션 로그 파일 또는 보존 되어야 하는 모든 항목을 저장 해서는 안 됩니다. 

`D:\`로컬 캐시를 사용 하는 데 문제가 있는 경우를 제외 하 고 SQL Server 작업에 대 한 로컬 임시 SSD 드라이브에 tempdb를 넣습니다. [임시 디스크가](../../../virtual-machines/azure-vms-no-temp-disk.md) 없는 가상 컴퓨터를 사용 하는 경우 캐시를 읽기 전용으로 설정 하 여 자체 격리 된 디스크 또는 저장소 풀에 tempdb를 저장 하는 것이 좋습니다. 자세한 내용은 [tempdb 데이터 캐싱 정책](performance-guidelines-best-practices-storage.md#data-file-caching-policies)을 참조 하세요.

### <a name="data-disks"></a>데이터 디스크

데이터 디스크는 단일 디스크에서 가상 머신에 제공할 수 있는 용량과 성능을 초과 하기 위해 [저장소 풀](/windows-server/storage/storage-spaces/overview) 에서 자주 만들어지는 원격 저장소 디스크입니다.

작업 부하의 IOPS, 처리량 및 용량 요구 사항을 충족 하는 최소 디스크 수를 연결 합니다. 크기를 조정 하려는 가장 작은 가상 컴퓨터의 최대 데이터 디스크 수를 초과 하지 마십시오.

성능 요구 사항에 가장 적합 하도록 프로 비전 된 데이터 디스크에 데이터 및 로그 파일을 저장 합니다. 

임시 드라이브가 아닌 드라이브에 배치 된 모든 데이터 파일에 대해 64 KB 할당 단위 크기를 사용 하도록 데이터 디스크의 형식을 지정 `D:\` 합니다 (기본값은 4kb). Azure Marketplace를 통해 배포 된 SQL Server Vm은 할당 단위 크기 및 64로 설정 된 저장소 풀의 인터리브로 포맷 된 데이터 디스크와 함께 제공 됩니다. 

## <a name="premium-disks"></a>프리미엄 디스크

프로덕션 SQL Server 작업에 대 한 데이터 및 로그 파일에 프리미엄 SSD 디스크를 사용 합니다. 프리미엄 SSD IOPS 및 대역폭은 [디스크 크기와 유형에](../../../virtual-machines/disks-types.md)따라 달라 집니다. 

프로덕션 워크 로드의 경우 SQL Server 데이터 파일에 대 한 P30 및/또는 P40 디스크를 사용 하 여 캐싱을 지원 하 고 SQL Server 트랜잭션 로그 파일에 대해 P80까지 P30를 사용 합니다.  최대 소유 비용은 데이터 및 로그 파일에 대 한 P30s (5000 IOPS/200mbps)부터 시작 하 여 가상 머신 디스크 수를 제어 해야 할 때 더 높은 용량을 선택 합니다.

OLTP 워크 로드의 경우 최대 사용 시간 및 성능 카운터를 사용 하 여 작업을 사용 하는 성능 요구 사항과 디스크 (또는 저장소 풀) 당 대상 IOPS를 일치 시킵니다 `Disk Reads/sec`  +  `Disk Writes/sec` . 데이터 웨어하우스 및 보고 워크 로드의 경우 최대 사용 시간 및에서 작업을 사용 하 여 대상 처리량을 일치 시킵니다 `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` . 

저장소 공간을 사용 하 여 최적의 성능을 실현 하 고 두 개의 풀, 즉 로그 파일 및 데이터 파일용 풀을 구성 합니다. 디스크 스트라이프를 사용 하지 않는 경우 별도의 드라이브에 매핑되는 두 개의 프리미엄 SSD 디스크를 사용 합니다. 여기서 한 드라이브에는 로그 파일이 포함 되 고 다른 하나에는 데이터가 포함 됩니다.

저장소 풀의 일부로 사용 되는, 디스크당 [프로 비전 된 IOPS 및 처리량](../../../virtual-machines/disks-types.md#premium-ssd) 입니다. 디스크의 결합 된 IOPS 및 처리량 기능은 가상 컴퓨터의 처리량 제한까지 최대 용량입니다.

가장 좋은 방법은 IOPS (및 처리량) 및 용량에 대 한 최소 요구 사항을 충족 하면서 최대한 적은 수의 디스크를 사용 하는 것입니다. 그러나 적은 수의 작은 디스크가 아닌 많은 작은 디스크를 사용 하는 경우 가격과 성능의 균형을 유지 하는 것이 좋습니다.

### <a name="scaling-premium-disks"></a>프리미엄 디스크 크기 조정

Azure 관리 디스크를 처음 배포 하는 경우 해당 디스크의 성능 계층은 프로 비전 된 디스크 크기를 기준으로 합니다. 배포 시 성능 계층을 지정 하거나 디스크 크기를 변경 하지 않고 나중에 변경 합니다. 요구가 늘어나면 비즈니스 요구에 맞게 성능 수준을 높일 수 있습니다. 

성능 계층을 변경 하면 관리자가 [디스크 버스트](../../../virtual-machines/disk-bursting.md#credit-based-bursting)에 의존 하지 않고 높은 수요를 준비 하 고 충족할 수 있습니다. 

저장소 성능 계층에 맞게 요금이 청구 되는 경우 필요에 따라 더 높은 성능을 사용할 수 있습니다. 용량을 늘리지 않고 성능 요구 사항과 일치 하도록 계층을 업그레이드 합니다. 추가 성능이 더 이상 필요 하지 않은 경우 원래 계층으로 돌아갑니다.

이 비용 효율적이 고 일시적인 성능 확장은 쇼핑, 성능 테스트, 학습 이벤트 및 단기에 대 한 성능 향상이 필요한 기타 간단한 창과 같은 대상 이벤트의 경우 강력한 사용 사례입니다. 

자세한 내용은 [managed disks의 성능 계층](../../../virtual-machines/disks-change-performance.md)을 참조 하세요. 

## <a name="azure-ultra-disk"></a>Azure ultra disk

대기 시간이 submillisecond 응답 시간이 필요한 경우에는 SQL Server 로그 드라이브에는 [Azure ultra disk](../../../virtual-machines/disks-types.md#ultra-disk) 를 사용 하는 것이 좋습니다. 또는 i/o 대기 시간에 매우 중요 한 응용 프로그램의 경우에는 데이터 드라이브를 사용 하는 것이 좋습니다. 

용량 및 IOPS를 독립적으로 확장할 수 있는 Ultra disk를 구성할 수 있습니다. 울트라 디스크 관리자는 응용 프로그램 요구에 따라 용량, IOPS 및 처리량 요구 사항으로 디스크를 프로 비전 할 수 있습니다. 

Ultra disk는 모든 VM 시리즈에서 지원 되지 않으며 지역 가용성, 중복성 및 Azure Backup 지원과 같은 기타 제한 사항이 있습니다. 자세한 내용은 [Azure ultra disks를 사용 하 여](../../../virtual-machines/disks-enable-ultra-ssd.md) 전체 제한 사항 목록을 참조 하세요. 

## <a name="standard-hdds-and-ssds"></a>표준 Hdd 및 Ssd

[표준 hdd](../../../virtual-machines/disks-types.md#standard-hdd) 및 ssd는 다양 한 대기 시간 및 대역폭을 가지 며 개발/테스트 워크 로드에만 권장 됩니다. 프로덕션 워크로드에는 프리미엄 SSD를 사용해야 합니다. 표준 SSD (개발/테스트 시나리오)를 사용 하는 경우 [VM 크기](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) 에서 지 원하는 최대 데이터 디스크 수를 추가 하 고 최상의 성능을 위해 저장소 공간을 사용 하 여 디스크 스트라이프를 사용 하는 것이 좋습니다.

## <a name="caching"></a>캐싱

Premium storage 캐싱을 지 원하는 가상 머신은 Azure BlobCache 또는 호스트 캐싱 이라는 추가 기능을 활용 하 여 가상 머신의 IOPS 및 처리량 기능을 확장할 수 있습니다. Premium storage 및 premium storage 캐싱에 사용 하도록 설정 된 가상 컴퓨터에는 저장소 성능을 향상 시키기 위해 함께 사용할 수 있는 두 가지 저장소 대역폭 제한이 있습니다.

캐싱을 사용 하지 않는 IOPS 및 MBps 처리량은 가상 머신의 캐시 되지 않은 디스크 처리량 한도에 대해 계산 됩니다. 캐시 된 최대 한도는 증가 및 예기치 않은 최대 사용량을 해결 하는 데 도움이 되는 읽기 버퍼를 추가로 제공 합니다.

추가 비용 없이 데이터 드라이브에 대 한 읽기 성능을 크게 향상 시키기 위해 옵션이 지원 될 때마다 프리미엄 캐싱을 사용 하도록 설정 합니다. 

Azure BlobCache (캐시 된 IOPS 및 처리량)에 대 한 읽기 및 쓰기는 가상 머신의 캐시 되지 않은 IOPS 및 처리량 한도에 대해 계산 되지 않습니다.

> [!NOTE]
> 디스크 캐싱은 디스크 4 TiB 이상 (P50 이상)에 대해 지원 되지 않습니다. 여러 디스크가 VM에 연결되어 있는 경우 4TiB보다 작은 디스크는 캐싱을 지원합니다. 자세한 내용은 [디스크 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching)을 참조 하세요. 

### <a name="uncached-throughput"></a>캐시 되지 않은 처리량

최대 캐시 되지 않은 디스크 IOPS 및 처리량은 가상 컴퓨터에서 처리할 수 있는 최대 원격 저장소 한도입니다. 이 제한은 가상 컴퓨터에서 정의 되며 기본 디스크 저장소의 제한이 아닙니다. 이 제한은 임시 드라이브 ( `D:\` 드라이브) 또는 OS 드라이브에 대 한 로컬 i/o가 아닌 VM에 원격으로 연결 된 데이터 드라이브에 대 한 i/o에만 적용 됩니다.

VM에 사용할 수 있는 캐시 되지 않은 IOPS 및 처리량의 양은 가상 머신에 대 한 설명서에서 확인할 수 있습니다.

예를 들어, [M 시리즈](../../../virtual-machines/m-series.md) 설명서는 Standard_M8ms VM에 대 한 최대 캐시 되지 않은 처리량이 5000 IOPS 및 125 MBps 캐시 되지 않은 디스크 처리량 임을 보여 줍니다. 

![M 시리즈 캐시 되지 않은 디스크 처리량 설명서를 보여 주는 스크린샷](./media/performance-guidelines-best-practices/m-series-table.png)

마찬가지로 Standard_M32ts에서 2만 캐시 되지 않은 disk IOPS 및 500 MBps 캐시 되지 않은 디스크 처리량을 지원 하는지 확인할 수 있습니다. 이 제한은 기본 프리미엄 디스크 저장소에 관계 없이 가상 머신 수준에서 관리 됩니다.

자세한 내용은 [캐시 되지 않은 및 캐시 된 제한](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits)을 참조 하세요.


### <a name="cached-and-temp-storage-throughput"></a>캐시 된 저장소 및 임시 저장소 처리량

최대 캐시 및 임시 저장소 처리량 제한은 가상 컴퓨터에 대 한 캐시 되지 않은 처리량 제한과는 별개의 제한입니다. Azure BlobCache는 가상 컴퓨터 호스트의 임의 액세스 메모리와 로컬로 연결 된 SSD의 조합으로 구성 됩니다. `D:\`가상 컴퓨터 내의 임시 드라이브 (드라이브)도이 로컬 SSD에서 호스팅됩니다.

캐시 된 최대 및 임시 저장소 처리량 제한은 `D:\` 호스트 캐싱이 사용 되는 **경우에만** 로컬 임시 드라이브 (드라이브)와 Azure blobcache에 대 한 i/o를 제어 합니다. 

Premium storage에서 캐싱이 설정 된 경우 가상 머신은 원격 저장소 캐시 되지 않은 VM IOPS 및 처리량 제한의 제한을 초과 하 여 확장할 수 있습니다.  

특정 가상 머신만 premium storage 및 premium storage 캐싱 (가상 머신 설명서에서 확인 해야 함)을 모두 지원 합니다. 예를 들어, [M 시리즈](../../../virtual-machines/m-series.md) 설명서는 premium storage 및 premium storage 캐싱이 모두 지원 됨을 나타냅니다. 

![M 시리즈 Premium Storage 지원을 보여 주는 스크린샷](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

캐시 제한은 가상 컴퓨터 크기에 따라 달라 집니다. 예를 들어 Standard_M8ms VM은 1만 캐시 된 디스크 IOPS 및 1000 MBps 캐시 된 디스크 처리량을 793 GiB의 총 캐시 크기와 함께 지원 합니다. 마찬가지로 Standard_M32ts VM은 4만 캐시 된 디스크 IOPS 및 400 MBps 캐시 된 디스크 처리량을 3174 GiB의 총 캐시 크기로 지원 합니다. 

![M 시리즈 캐시 된 디스크 처리량 설명서를 보여 주는 스크린샷](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

기존 VM에서 호스트 캐싱을 수동으로 사용 하도록 설정할 수 있습니다. 가상 컴퓨터의 캐싱 정책을 변경 하기 전에 모든 응용 프로그램 작업 및 SQL Server 서비스를 중지 합니다. 가상 컴퓨터 캐시 설정을 변경 하면 설정이 적용 된 후 대상 디스크가 분리 되어 다시 첨부 됩니다.

### <a name="data-file-caching-policies"></a>데이터 파일 캐싱 정책

저장소 캐싱 정책은 드라이브에서 호스트 되는 SQL Server 데이터 파일의 유형에 따라 달라 집니다. 

다음 표에서는 SQL Server 데이터 유형에 따라 권장 되는 캐싱 정책을 요약 하 여 보여 줍니다. 

|디스크 SQL Server |권장 |
|---------|---------|
| **데이터 디스크** | `Read-only`SQL Server 데이터 파일을 호스트 하는 디스크에 캐싱을 사용 하도록 설정 합니다. <br/> 캐시에서 읽기는 데이터 디스크에서 캐시 되지 않은 읽기 보다 빠릅니다. <br/> 캐시 되지 않은 IOPS 및 처리량 외에도 캐시 된 IOPS 및 처리량은 Vm 제한 내에서 가상 머신에서 사용할 수 있는 총 성능을 제공 하지만 실제 성능은 캐시를 사용 하는 작업의 기능 (캐시 적중률)에 따라 달라 집니다. <br/>|
|**트랜잭션 로그 디스크**|`None`트랜잭션 로그를 호스팅하는 디스크에 대 한 캐싱 정책을로 설정 합니다.  트랜잭션 로그 디스크에 대해 캐싱을 사용 하도록 설정 하는 경우 성능상의 이점을 얻을 수 없습니다. 즉, `Read-only` `Read/Write` 로그 드라이브에서 또는 캐싱을 사용 하도록 설정 하면 드라이브에 대 한 쓰기 성능이 저하 되 고 데이터 드라이브에서 읽기에 사용할 수 있는 캐시의 양이 줄어듭니다.  |
|**운영 체제 디스크** | 기본 캐싱 정책은 `Read-only` `Read/write` OS 드라이브에 대해 또는 일 수 있습니다. <br/> OS 드라이브의 캐싱 수준을 변경 하지 않는 것이 좋습니다.  |
| **tempdb**| 용량이 부족 하 여 임시 드라이브에 tempdb를 배치할 수 없는 경우 `D:\` 더 큰 임시 드라이브를 가져오도록 가상 머신의 크기를 조정 하거나 캐싱이 구성 된 별도의 데이터 드라이브에 tempdb를 배치 하십시오 `Read-only` . <br/> 가상 컴퓨터 캐시와 임시 드라이브 모두 로컬 SSD를 사용 하므로 tempdb i/o로 크기를 조정 하면 임시 드라이브에 호스팅될 때 캐시 된 IOPS 및 처리량 가상 컴퓨터 제한에 대해이를 염두에 두어야 합니다.| 
| | | 


자세히 알아보려면 [디스크 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching)을 참조 하세요. 


## <a name="disk-striping"></a>디스크 스트라이프

SQL 데이터 파일에 필요한 처리량 및 대역폭을 분석 하 여 로그 파일 및 tempdb를 비롯 한 데이터 디스크의 수를 확인 합니다. 처리량 및 대역폭 제한은 VM 크기에 따라 달라 집니다. 자세히 알아보려면 [VM 크기](../../../virtual-machines/sizes.md) 를 참조 하세요.

추가 처리량을 위해 데이터 디스크를 추가 하 고 디스크 스트라이프를 사용 합니다. 예를 들어 12000 IOPS 및 180 m b/초 처리량을 요구 하는 응용 프로그램은 3 개의 스트라이프 P30 디스크를 사용 하 여 15000 IOPS 및 600 MB/s 처리량을 제공할 수 있습니다. 

디스크 스트라이프를 구성 하려면 [디스크 스트라이프](storage-configuration.md#disk-striping)를 참조 하세요. 

## <a name="disk-capping"></a>디스크 50, 

디스크 및 가상 머신 수준 모두에서 처리량 제한이 있습니다. VM 당 및 디스크당 최대 IOPS 제한은 서로 다르며 서로 독립적입니다.

이러한 한도를 초과 하는 리소스를 사용 하는 응용 프로그램은 제한 됩니다 (더 이상 사용 되지 않음). 응용 프로그램 요구 사항을 충족 하는 디스크 스트라이프에서 가상 컴퓨터 및 디스크 크기를 선택 하면 50, 제한이 발생 하지 않습니다. 50,를 해결 하려면 캐싱을 사용 하거나, 더 짧은 처리량이 필요 하도록 응용 프로그램을 조정 합니다.

예를 들어 12000 IOPS 및 180 m b/초를 필요로 하는 응용 프로그램은 다음을 수행할 수 있습니다. 
- 최대 캐시 되지 않은 디스크 처리량이 2만 IOPS 및 500 MBps 인 [Standard_M32ms](../../../virtual-machines/m-series.md) 를 사용 합니다.
- 3 개의 P30 디스크를 스트라이프 하 여 15000 IOPS 및 600mb/s 처리량을 제공 합니다.
- [Standard_M16ms](../../../virtual-machines/m-series.md) 가상 컴퓨터를 사용 하 고 호스트 캐싱을 사용 하 여 처리량을 소비 하는 동안 로컬 캐시를 활용 합니다. 

높은 사용률의 시간 동안 강화 하도록 구성 된 가상 머신은 최대 VM 크기를 지원할 수 있는 충분 한 IOPS 및 처리량을 가진 저장소를 프로 비전 하 고, 사용 대상으로 지정 된 가장 작은 VM SKU에서 지원 되는 최대 개수 보다 작거나 같은 전체 디스크 수를 유지 해야 합니다.

디스크 50, 제한 사항에 대 한 자세한 내용 및 50,을 방지 하기 위해 캐싱 사용에 대 한 자세한 내용은 [DISK IO 50,](../../../virtual-machines/disks-performance.md)를 참조 하세요.

> [!NOTE] 
> 일부 디스크 50, 사용자에 게 만족할 만한 성능을 초래할 수 있습니다. 대규모 VM으로 크기를 조정 하는 대신 작업을 조정 하 고 유지 관리 하 여 비즈니스에 대 한 비용 및 성능 관리를 분산 합니다. 


## <a name="write-acceleration"></a>쓰기 가속

쓰기 가속화는 [M 시리즈](https://docs.microsoft.com/azure/virtual-machines/m-series) Virtual Machines (vm)에만 사용할 수 있는 디스크 기능입니다. 쓰기 가속화의 목적은 대량 업무에 중요 한 OLTP 작업 또는 데이터 웨어하우스 환경으로 인해 단일 숫자 i/o 대기 시간이 필요한 경우 Azure Premium Storage에 대 한 쓰기의 i/o 대기 시간을 개선 하는 것입니다. 

쓰기 가속화를 사용 하 여 로그 파일을 호스트 하는 드라이브에 대 한 쓰기 대기 시간을 향상 시킵니다. SQL Server 데이터 파일에 대해 쓰기 가속을 사용 하지 마십시오. 

쓰기 가속기 디스크는 가상 머신과 동일한 IOPS 제한을 공유 합니다. 연결 된 디스크는 VM에 대 한 쓰기 가속기 IOPS 제한을 초과할 수 없습니다.  

다음 표에서는 가상 머신 당 지원 되는 데이터 디스크 및 IOPS의 수를 간략하게 설명 합니다. 

| VM SKU  | 디스크 쓰기 가속기  | VM 당 디스크 IOPS 쓰기 가속기  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2, M208s_v2  | 8  | 10000  |
| M64ms, M64ls, M64s  |  8 | 10000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5,000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

쓰기 가속 사용에 대 한 몇 가지 제한 사항이 있습니다. 자세히 알아보려면 [쓰기 가속기 사용 시 제한 사항](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator)을 참조 하세요.


### <a name="comparing-to-azure-ultra-disk"></a>Azure ultra disk와 비교

쓰기 가속화와 Azure ultra disks의 가장 큰 차이점은 쓰기 가속화는 M 시리즈에만 사용할 수 있는 가상 머신 기능이 며 Azure ultra disks는 저장소 옵션입니다. 쓰기 가속화는 가상 머신 크기에 따라 고유한 제한이 있는 쓰기에 최적화 된 캐시입니다. Azure ultra disks는 Azure Virtual Machines에 대 한 짧은 대기 시간 디스크 저장소 옵션입니다. 

가능 하면 트랜잭션 로그 디스크에 대해 ultra disks에 대해 쓰기 가속화를 사용 합니다. 쓰기 가속을 지원 하지 않지만 트랜잭션 로그에 짧은 대기 시간이 필요한 가상 컴퓨터의 경우 Azure ultra disks를 사용 합니다. 

## <a name="monitor-storage-performance"></a>저장소 성능 모니터링

저장소 요구를 평가 하 고 저장소를 얼마나 잘 수행 하는지 확인 하려면 측정할 내용과 해당 지표의 의미를 이해 해야 합니다. 

[IOPS (초당 입력/출력 수)](../../../virtual-machines/premium-storage-performance.md#iops) 는 응용 프로그램이 저장소에서 초당 저장소로 만드는 요청 수입니다. 성능 모니터 카운터 및을 사용 하 여 IOPS `Disk Reads/sec` 를 측정 `Disk Writes/sec` 합니다. [OLTP (온라인 트랜잭션 처리)](/azure/architecture/data-guide/relational-data/online-transaction-processing) 응용 프로그램은 최적의 성능을 얻기 위해 높은 IOPS를 구동 해야 합니다. 결제 처리 시스템, 온라인 쇼핑 및 소매 pos 시스템과 같은 응용 프로그램은 모두 OLTP 응용 프로그램의 예입니다.

[처리량](../../../virtual-machines/premium-storage-performance.md#throughput) 은 기본 저장소로 전송 되는 데이터의 볼륨이 며 종종 초당 메가바이트 단위로 측정 됩니다. 성능 모니터 카운터 및을 사용 하 여 처리량을 측정 `Disk Read Bytes/sec` `Disk Write Bytes/sec` 합니다. [데이터 웨어하우징](/azure/architecture/data-guide/relational-data/data-warehousing) 은 IOPS를 통한 처리량을 최대화 하는 것과 관련 하 여 최적화 되었습니다. 분석, 보고, ETL 작업 및 기타 비즈니스 인텔리전스 대상에 대 한 데이터 저장소와 같은 응용 프로그램은 모두 데이터 웨어하우징 응용 프로그램의 예입니다.

I/o 단위 크기는 iops 및 처리량 기능에 영향을 주므로 더 작은 i/o 크기는 더 높은 IOPS를 생성 하 고 더 큰 i/o 크기는 더 높은 처리량을 생성 합니다. SQL Server 최적의 i/o 크기를 자동으로 선택 합니다. 에 대 한 자세한 내용은 [응용 프로그램에 대 한 IOPS, 처리량 및 대기 시간 최적화](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance)를 참조 하세요. 

가상 컴퓨터 및 디스크 수준에서 50,를 검색 하는 데 중요 한 특정 Azure Monitor 메트릭과 AzureBlob 캐시의 사용량과 상태가 있습니다. 모니터링 솔루션 및 Azure Portal 대시보드에 추가할 주요 카운터를 식별 하려면 [저장소 사용률 메트릭](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics)을 참조 하세요. 

> [!NOTE]
> Azure Monitor는 현재 임시 임시 드라이브에 대 한 디스크 수준 메트릭을 제공 하지 않습니다 `(D:\)` . 사용 된 VM 캐시 된 IOPS 비율 및 사용 된 VM 캐시 된 대역폭 백분율에는 모두 임시 드라이브 및 호스트 캐싱의 IOPS 및 처리량이 반영 됩니다 `(D:\)` .


## <a name="next-steps"></a>다음 단계

성능 모범 사례에 대해 자세히 알아보려면이 시리즈의 다른 문서를 참조 하세요.
- [빠른 검사 목록](performance-guidelines-best-practices-checklist.md)
- [VM 크기](performance-guidelines-best-practices-vm-size.md)
- [기준선 수집](performance-guidelines-best-practices-collect-baseline.md)

보안 모범 사례는 [Azure Virtual Machines에서 SQL Server에 대 한 보안 고려 사항](security-considerations-best-practices.md)을 참조 하세요.

TPC-E 및 TPC_C 벤치마크를 사용하여 Azure VM에서 SQL Server 성능을 자세히 테스트하려면 블로그 [OLTP 성능 최적화](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794)를 참조하세요.

[Azure Virtual Machines의 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)에서 다른 SQL Server 가상 머신 문서를 검토하세요. SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](frequently-asked-questions-faq.md)을 참조하세요.
