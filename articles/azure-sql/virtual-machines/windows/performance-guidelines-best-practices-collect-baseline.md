---
title: '기준 수집: 성능 모범 사례 & 지침'
description: Azure VM (가상 컴퓨터)에서 SQL Server의 성능을 최적화 하기 위한 지침으로 성능 기준선을 수집 하는 단계를 제공 합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1909d5fd5e3c02f104c73acb515740cb6ff65f6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572448"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>기준 수집: Azure VM의 SQL Server에 대 한 성능 모범 사례
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure Virtual Machines (Vm)에서 SQL Server의 성능을 최적화 하기 위한 일련의 모범 사례 및 지침으로 성능 기준선을 수집 하는 정보를 제공 합니다.

일반적으로 비용에 대한 최적화와 성능에 대한 최적화 간의 절충이 있습니다. 이 성능 모범 사례 시리즈는 Azure Virtual Machines에서 SQL Server에 대 한 *최상의* 성능을 얻는 데 중점을 두었습니다. 워크 로드가 더 까다로운 경우 모든 권장 최적화를 요구 하지 않을 수 있습니다. 이러한 권장 사항을 평가할 때 성능 요구 사항, 비용 및 작업 패턴을 고려하세요.

## <a name="overview"></a>개요

규범적인 접근 방식을 위해 PerfMon/LogMan를 사용 하 여 성능 카운터를 수집 하 고 SQL Server 대기 통계를 캡처하여 원본 환경의 일반적인 pressures 및 잠재적 병목 상태를 보다 잘 이해할 수 있도록 합니다. 

[응용 프로그램 성능 검사 목록](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)에 따라 최대 사용 시간에 원본 워크 로드의 CPU, 메모리, [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), [처리량](../../../virtual-machines/premium-storage-performance.md#throughput)및 [대기 시간](../../../virtual-machines/premium-storage-performance.md#latency) 을 수집 하 여 시작 합니다. 

일반적인 업무 시간 동안 워크 로드와 같이 사용량이 많은 시간 동안 데이터를 수집 하 고, 하루 종일 처리 및 주말 ETL 워크 로드와 같은 기타 높은 부하 프로세스를 수행 합니다. 분기 간 처리와 같은 많은 작업을 atypically 하는 리소스를 확장 한 다음, 워크 로드가 완료 되 면 확장을 수행 하는 것이 좋습니다. 

성능 분석을 사용 하 여 워크 로드의 성능 요구 사항에 맞게 확장할 수 있는 [VM 크기](../../../virtual-machines/sizes-memory.md) 를 선택 합니다.


## <a name="storage"></a>Storage

SQL Server 성능은 i/o 하위 시스템에 따라 다르며 저장소 성능은 IOPS 및 처리량으로 측정 됩니다. 데이터베이스가 실제 메모리에 적합 하지 않을 경우 SQL Server는 데이터베이스 페이지를 버퍼 풀에 계속 해 서 제공 합니다. SQL Server에 대 한 데이터 파일은 다르게 처리 되어야 합니다. Tempdb를 비롯 한 데이터 파일에 임의로 액세스 되는 트랜잭션을 롤백해야 하는 경우를 제외 하 고 로그 파일에 대 한 액세스는 순차적으로 실행 됩니다. 저속 i/o 하위 시스템이 있는 경우 사용자에 게는 속도가 느리거나 시간 초과로 인해 완료 되지 않는 작업과 같은 성능 문제가 발생할 수 있습니다. 

Azure Marketplace 가상 컴퓨터에는 기본적으로 데이터 파일과 별도의 실제 디스크에 로그 파일이 있습니다. Tempdb 데이터 파일 수 및 크기는 모범 사례를 충족 하며 임시 드라이브를 대상으로 `D:\` 합니다. 

다음 PerfMon 카운터는 SQL Server에 필요한 IO 처리량의 유효성을 검사 하는 데 도움이 될 수 있습니다. 
* **\LogicalDisk\Disk Reads/Sec** (읽기 IOPS)
* **\LogicalDisk\Disk Writes/Sec** (쓰기 IOPS) 
* **\LogicalDisk\Disk Read Bytes/Sec** (데이터, 로그 및 tempdb 파일에 대 한 읽기 처리량 요구 사항)
* **\LogicalDisk\Disk Write Bytes/Sec** (데이터, 로그 및 tempdb 파일에 대 한 쓰기 처리량 요구 사항)

최고 수준에서 IOPS 및 처리량 요구 사항을 사용 하 여 사용자 측정의 용량과 일치 하는 VM 크기를 평가 합니다. 

워크 로드에 20K 읽기 IOPS 및 10K write IOPS가 필요한 경우 저장소 공간을 사용 하 여 2 개의 P30 디스크 스트라이프를 사용 하 여 E16s_v3 (최대 32K 캐시 및 25600 캐시 되지 않은 IOPS 포함) 또는 M16_s (최대 20K 캐시 및 10K 캐시 되지 않은 IOPS 사용)를 선택할 수 있습니다. 

Vm은 IOPS 및 처리량에 대 한 확장 제한이 서로 다르므로 워크 로드의 처리량 및 IOPS 요구 사항을 모두 이해 해야 합니다.

## <a name="memory"></a>메모리

SQL Server에서 내부적으로 사용 하는 메모리 뿐만 아니라 OS에서 사용 하는 외부 메모리를 모두 추적 합니다. 어느 구성 요소에 대 한 압력을 식별 하는 것은 가상 머신의 크기를 조정 하는 데 도움이 되며, 

다음 PerfMon 카운터는 SQL Server 가상 컴퓨터의 메모리 상태를 확인 하는 데 도움이 될 수 있습니다. 
* [\Memory\사용 가능한 MB](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: Memory Manager\Target Server Memory (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: Memory Manager\Total Server Memory (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: Buffer Manager\Lazy writes/sec](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: Buffer Manager\Page 수명 예상](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>Compute

Azure에서 계산은 온-프레미스와 다르게 관리 됩니다. 온-프레미스 서버는 관리 오버 헤드 및 새 하드웨어 획득 비용으로 인해 업그레이드 하지 않고 지난 몇 년간 빌드됩니다. 가상화는 이러한 문제 중 일부를 완화 하지만 응용 프로그램은 기본 하드웨어의 장점을 최대한 활용 하도록 최적화 되어 있습니다. 즉, 리소스 소비에 대 한 중요 한 변경 사항은 전체 물리적 환경을 균형 있게 조정 해야 합니다. 

이는 다른 하드웨어의 새 가상 컴퓨터와 다른 지역 에서도 쉽게 달성할 수 있는 Azure의 과제가 아닙니다. 

Azure에서는 가상 머신 리소스를 최대한 활용 하고자 하므로 워크 로드에 영향을 주지 않고 최대한 높은 평균 CPU를 유지 하도록 Azure 가상 머신을 구성 해야 합니다. 

다음 PerfMon 카운터는 SQL Server 가상 컴퓨터의 계산 상태를 확인 하는 데 도움이 될 수 있습니다.
* **\Processor Information (_Total) \% 프로세서 시간**
* **\Process (sqlservr.exe) \% 프로세서 시간**

> [!NOTE] 
> 80% 이상의 계산을 사용 하는 것이 가장 좋지만, 90%를 초과 하지만 지속적으로 지속 되는 기간에는 100%에 도달 하지 않습니다. 기본적으로, 응용 프로그램에 필요한 계산을 프로 비전 하 고 비즈니스에 필요한 만큼 규모를 확장 하거나 축소할 계획을 세워야 합니다. 


## <a name="next-steps"></a>다음 단계

자세히 알아보려면이 시리즈의 다른 문서를 참조 하세요.
- [빠른 검사 목록](performance-guidelines-best-practices-checklist.md)
- [VM 크기](performance-guidelines-best-practices-vm-size.md)
- [스토리지](performance-guidelines-best-practices-storage.md)


보안 모범 사례는 [Azure Virtual Machines에서 SQL Server에 대 한 보안 고려 사항](security-considerations-best-practices.md)을 참조 하세요.

[Azure Virtual Machines의 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)에서 다른 SQL Server 가상 머신 문서를 검토하세요. SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](frequently-asked-questions-faq.md)을 참조하세요.
