---
title: 'VM 크기: 성능 모범 사례 & 지침'
description: Vm 크기 지침과 모범 사례를 제공 하 여 Azure VM (가상 머신)에서 SQL Server의 성능을 최적화 합니다.
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 9427ae1b9bd68f63df40d24122cc13b5460fbc27
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572499"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>VM 크기: Azure Vm의 SQL Server에 대 한 성능 모범 사례
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure Virtual Machines (Vm)에서 SQL Server의 성능을 최적화 하기 위한 일련의 모범 사례 및 지침을 제공 하는 VM 크기 지침을 제공 합니다.

일반적으로 비용에 대한 최적화와 성능에 대한 최적화 간의 절충이 있습니다. 이 성능 모범 사례 시리즈는 Azure Virtual Machines에서 SQL Server에 대 한 *최상의* 성능을 얻는 데 중점을 두었습니다. 워크 로드가 더 까다로운 경우 모든 권장 최적화를 요구 하지 않을 수 있습니다. 이러한 권장 사항을 평가할 때 성능 요구 사항, 비용 및 작업 패턴을 고려하세요.


## <a name="checklist"></a>검사 목록

이 문서의 나머지 부분에서 자세히 설명 하는 VM 크기 모범 사례에 대 한 간략 한 개요를 보려면 다음 검사 목록을 검토 하세요. 

- [Standard_M8-4ms](/../../virtual-machines/m-series), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series), [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 이상 등 4 개 이상의 vcpu가 포함 된 VM 크기를 사용 합니다. 
- 메모리 액세스에 [최적화](../../../virtual-machines/sizes-memory.md) 된 가상 머신 크기를 사용 하 여 SQL Server 워크 로드의 성능을 극대화 합니다. 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) 시리즈, [M-](../../../virtual-machines/m-series.md)및 [Mv2](../../../virtual-machines/mv2-series.md) 시리즈는 OLTP 워크 로드에 필요한 최적의 메모리-vcore 비율을 제공 합니다. M 시리즈 Vm은 업무에 중요 한 워크 로드에 필요한 메모리-vCore의 최고 비율을 제공 하며, 데이터 웨어하우스 워크 로드에도 적합 합니다. 
- 중요 업무용 및 데이터 웨어하우스 워크 로드에 대 한 메모리-vCore 비율을 높이는 것이 좋습니다. 
- SQL Server 설정 및 저장소 옵션이 최적의 SQL Server 성능을 위해 구성 되어 있으므로 Azure Virtual Machine marketplace 이미지를 활용 하세요. 
- 대상 워크 로드의 성능 특성을 수집 하 고이를 사용 하 여 비즈니스에 적합 한 VM 크기를 결정 합니다.

VM 크기 검사 목록을 다른 항목과 비교 하려면 포괄적인 [성능 모범 사례 검사 목록](performance-guidelines-best-practices-checklist.md)을 참조 하세요. 

## <a name="overview"></a>개요

Azure VM에서 SQL Server를 만들 때 필요한 작업 유형을 신중 하 게 고려해 야 합니다. 기존 환경을 마이그레이션하는 경우 Azure VM 요구 사항에 대 한 SQL Server를 확인 하 [는 성능 기준선을 수집](performance-guidelines-best-practices-collect-baseline.md) 합니다. 새 VM 인 경우 공급 업체 요구 사항에 따라 새 SQL Server VM를 만듭니다. 

클라우드 용으로 빌드된 새 응용 프로그램을 사용 하 여 새 SQL Server VM을 만드는 경우 데이터 및 사용 요구 사항이 발전 함에 따라 SQL Server VM 크기를 쉽게 지정할 수 있습니다.
하위 계층 D 시리즈, B 시리즈 또는 Av2 시리즈를 사용 하 여 개발 환경을 시작 하 고 시간이 지남에 따라 환경을 확장 합니다. 

포털의 저장소 구성과 함께 SQL Server VM marketplace 이미지를 사용 합니다. 이렇게 하면 워크 로드에 필요한 크기, IOPS 및 처리량을 얻는 데 필요한 저장소 풀을 보다 쉽게 만들 수 있습니다. Premium storage 및 premium storage 캐싱을 지 원하는 Vm SQL Server 선택 하는 것이 중요 합니다. 자세히 알아보려면 [저장소](performance-guidelines-best-practices-storage.md) 문서를 참조 하세요. 

프로덕션 OLTP 환경에 권장 되는 최소값은 4 vCore, 32 GB의 메모리 및 메모리-vCore 비율 8입니다. 새 환경의 경우 데이터 및 계산 요구 사항이 변경 될 때 4 개의 vCore 컴퓨터에서 시작 하 고 8, 16, 32 Vcore 이상으로 확장 합니다. OLTP 처리량의 경우 모든 vCore에 대해 5000 IOPS를 포함 하는 Vm을 대상으로 SQL Server 합니다. 

데이터 웨어하우스와 업무상 중요 한 환경 SQL Server는 메모리 대 vCore 비율 8 개 이상으로 규모를 확장 해야 하는 경우가 많습니다. 중간 규모 환경의 경우에는 더 큰 데이터 웨어하우스 환경에 대해 16 개의 메모리-vCore 비율 및 32 메모리-vCore 비율을 선택 하는 것이 좋습니다. 

데이터 웨어하우스 환경 SQL Server 큰 컴퓨터의 병렬 처리를 활용 하는 경우가 많습니다. 이러한 이유로 M 시리즈 및 Mv2 시리즈는 대규모 데이터 웨어하우스 환경에서 강력한 옵션입니다.

현재 온-프레미스 SQL Server 데이터베이스를 Azure Vm의 SQL Server으로 마이그레이션하기 위한 기준으로 원본 컴퓨터의 vCPU 및 메모리 구성을 사용 합니다. Azure에 대 한 핵심 라이선스를 사용 하 여 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 를 활용 하 고 SQL Server 라이선스 비용을 절감할 수 있습니다.

## <a name="memory-optimized"></a>메모리에 최적화

메모리 액세스에 [최적화 된 가상 머신 크기](../../../virtual-machines/sizes-memory.md) 는 SQL Server vm의 기본 목표 이며 Microsoft에서 권장 하는 선택입니다. 메모리 액세스에 최적화 된 가상 머신은 더 강력한 메모리와 CPU 비율을 제공 하 고 중간 규모에서 크게 캐시 옵션을 제공 합니다. 

### <a name="m-mv2-and-mdsv2-series"></a>M, Mv2 및 Mdsv2 시리즈

[M 시리즈](../../../virtual-machines/m-series.md) 는 가장 큰 SQL Server 워크 로드 중 일부에 대 한 vcore 개수와 메모리를 제공 합니다.  

[Mv2 시리즈](../../../virtual-machines/mv2-series.md) 는 최고 vcore 수 및 메모리를 포함 하 고 있으며 중요 업무용 및 데이터 웨어하우스 워크 로드에 권장 됩니다. Mv2 시리즈 인스턴스는 관계형 데이터베이스 서버, 큰 캐시 및 메모리 내 분석에 적합 한 메모리 내 대규모 데이터베이스와 워크 로드에 대 한 높은 메모리와 CPU의 비율을 지원 하기 위해 뛰어난 계산 성능을 제공 하는 메모리 액세스에 최적화 된 VM 크기입니다.

예를 들어 [Standard_M64ms](../../../virtual-machines/m-series.md) 는 28 메모리-vcore 비율을 갖습니다.

[Mdsv2 Medium memory 시리즈](../../..//virtual-machines/msv2-mdsv2-series.md) 는 현재 [미리 보기로](https://aka.ms/Mv2MedMemoryPreview) 제공 되는 새로운 m 시리즈로, 중간 계층 메모리 제품을 포함 하는 여러 개의 m 시리즈 수준 Azure virtual machines를 제공 합니다. 이러한 컴퓨터는 최소 10 개의 메모리-vCore 지원이 최대 30 개 있는 SQL Server 작업에 적합 합니다.

M 및 Mv2 SQL Server 시리즈의 기능 중 일부에는 [premium storage](../../../virtual-machines/premium-storage-performance.md) 및 [premium storage 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching) 지원, [울트라 디스크](../../../virtual-machines/disks-enable-ultra-ssd.md) 지원 및 [쓰기 가속이](../../../virtual-machines/how-to-enable-write-accelerator.md)포함 됩니다.

### <a name="edsv4-series"></a>Edsv4 시리즈

[Edsv4 시리즈](../../../virtual-machines/edv4-edsv4-series.md) 는 메모리 집약적 응용 프로그램을 위해 설계 되었습니다. 이러한 Vm에는 최대 504 GiB RAM의 큰 로컬 저장소 SSD 용량, 강력한 로컬 디스크 IOPS가 있습니다. 이러한 가상 머신 대부분에서 vCore 당 GiB 8 개의 메모리를 거의 일관 되 게 사용할 수 있으며,이는 표준 SQL Server 워크 로드에 이상적입니다. 

이 그룹에는 80 Vcores, 504 Gb 메모리를 제공 하는 [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) 와 메모리 대 vcores 비율 6이 포함 되어 있습니다. 이 가상 머신은 [격리](../../../virtual-machines/isolation.md) 되어 있으므로 호스트에서 실행 되는 유일한 가상 머신으로 보장 되므로 다른 고객 워크 로드와 격리 되기 때문에 중요 합니다. 이는 SQL Server에 권장 되는 것 보다 낮은 메모리 대 vCore 비율을 가지 므로, 격리가 필요한 경우에만 사용 해야 합니다.

Edsv4 시리즈 가상 머신은 [premium storage](../../../virtual-machines/premium-storage-performance.md)및 [premium storage 캐싱을](../../../virtual-machines/premium-storage-performance.md#disk-caching)지원 합니다.

### <a name="dsv2-series-11-15"></a>DSv2 시리즈 11-15

[DSv2 시리즈 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 에는 이전 D 시리즈와 동일한 메모리 및 디스크 구성이 있습니다. 이 시리즈는 모든 가상 머신에서 7의 메모리 대 CPU 비율을 일관성 있게 유지 합니다. 이는 메모리 최적화 계열의 가장 작은 값으로, 항목 수준 SQL Server 워크 로드에 대 한 좋은 저렴 한 옵션입니다.

[DSv2 시리즈 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 는 [premium storage](../../../virtual-machines/premium-storage-performance.md) 및 [premium storage 캐싱을](../../../virtual-machines/premium-storage-performance.md#disk-caching)지원 합니다 .이는 최적의 성능을 위해 적극 권장 됩니다.

## <a name="general-purpose"></a>범용 가상 컴퓨터

범용 [가상 머신 크기](../../../virtual-machines/sizes-general.md) 는 개발 및 테스트, 웹 서버 및 더 작은 데이터베이스 서버와 같은 더 작은 항목 수준 워크 로드에 대해 분산 된 메모리-vcore 비율을 제공 하도록 설계 되었습니다. 

범용 가상 컴퓨터를 사용 하는 메모리 대 vCore 비율이 작을수록 메모리 기반 성능 카운터를 신중 하 게 모니터링 하 SQL Server에서 필요한 버퍼 캐시 메모리를 얻을 수 있는지 확인 하는 것이 중요 합니다. 자세한 내용은 [메모리 성능 기준](performance-guidelines-best-practices-collect-baseline.md#memory) 을 참조 하세요. 

프로덕션 워크 로드에 대 한 시작 권장 사항은 메모리-vCore 비율 8 이기 때문에 SQL Server를 실행 하는 범용 VM에 대 한 최소 권장 구성은 4 개의 Vcore 및 32 GB의 메모리입니다. 

### <a name="ddsv4-series"></a>Ddsv4 시리즈

[Ddsv4 시리즈](../../../virtual-machines/ddv4-ddsv4-series.md) 는 vcpu, 메모리 및 임시 디스크의 공평 한 조합을 제공 하지만 메모리와 vcpu를 더 적게 지원 합니다. 

Ddsv4 Vm에는 짧은 대기 시간과 고속 로컬 저장소가 포함 됩니다.

이러한 컴퓨터는 side-by-side SQL 및 임시 저장소 및 부서별 관계형 데이터베이스에 빠르게 액세스 해야 하는 앱 배포에 적합 합니다. 이 시리즈의 모든 가상 컴퓨터에 대해 표준 메모리-vCore 비율 4가 있습니다. 

이러한 이유로이 시리즈에서는 8 개의 vCores 및 32 Gb의 메모리를 포함 하는 스타터 가상 머신으로 D8ds_v4를 활용 하는 것이 좋습니다. 가장 큰 컴퓨터는 64 vCores 및 256 Gb의 메모리를 포함 하는 D64ds_v4입니다.

[Ddsv4 시리즈](../../../virtual-machines/ddv4-ddsv4-series.md) 가상 머신은 [premium storage](../../../virtual-machines/premium-storage-performance.md) 및 [premium storage 캐싱을](../../../virtual-machines/premium-storage-performance.md#disk-caching)지원 합니다.

> [!NOTE]
> [Ddsv4 시리즈](../../../virtual-machines/ddv4-ddsv4-series.md) 는 SQL Server 워크 로드에 권장 되는 8의 메모리-vcore 비율을 포함 하지 않습니다. 따라서 더 작은 응용 프로그램 및 개발 워크 로드에만 이러한 가상 컴퓨터를 사용 하는 것을 고려 합니다.

### <a name="b-series"></a>B 시리즈

[안정적인 B 시리즈](../../../virtual-machines/sizes-b-series-burstable.md) 가상 머신 크기는 개념 증명 및 매우 작은 응용 프로그램 및 개발 서버와 같이 일관 된 성능을 필요로 하지 않는 워크 로드에 이상적입니다. 

[안정적인 B 시리즈](../../../virtual-machines/sizes-b-series-burstable.md) 가상 머신 크기의 대부분은 메모리-vcore 비율 4입니다. 이러한 컴퓨터 중 가장 큰 값은 20 개 vCores 및 80 GB의 메모리가 있는 [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) 입니다.

이 시리즈는 컴퓨터 크기에 따라 변화 하는 안정화 크레딧을 사용 하 여 업무 시간 동안 **버스트** 하는 기능을 제공 하므로 고유 합니다. 

크레딧을 모두 사용 하는 경우 VM은 기준 컴퓨터 성능으로 돌아옵니다.

B 시리즈의 혜택은 다른 시리즈의 다른 VM 크기와 비교 하 여 달성할 수 있는 계산 절감 액 이며 특히 하루 종일 처리 능력을 많이 필요로 하는 경우에 해당 합니다.

이 시리즈는 [premium storage](../../../virtual-machines/premium-storage-performance.md)를 지원 하지만 [premium storage 캐싱은](../../../virtual-machines/premium-storage-performance.md#disk-caching) **지원 하지** 않습니다.

> [!NOTE] 
> SQL Server 워크 로드에 권장 되는 8의 메모리-vCore 비율을 포함 [하지 않습니다.](../../../virtual-machines/sizes-b-series-burstable.md) 따라서 더 작은 응용 프로그램, 웹 서버 및 개발 워크 로드에만 이러한 가상 컴퓨터를 사용 하는 것이 좋습니다.

### <a name="av2-series"></a>Av2 시리즈

[Av2 시리즈](../../../virtual-machines/av2-series.md) vm은 개발 및 테스트, 낮은 트래픽 웹 서버, 중소 규모의 앱 데이터베이스, 개념 증명 등의 항목 수준 작업에 가장 적합 합니다.

[Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 개 vcores 및 메모리 16GBs), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 개 vcores 및 32GBs of memory) 및 [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (Vcores 8 개, 메모리 64GBs)는 이러한 상위 3 개 가상 컴퓨터에 대 한 메모리-vcores 비율 8의 양호한 비율을 갖습니다. 

이러한 가상 머신은 소규모 개발 및 테스트 SQL Server 컴퓨터에서 모두 좋은 옵션입니다. 

8 vCore [Standard_A8m_v2](../../../virtual-machines/av2-series.md) 작은 응용 프로그램 및 웹 서버에 대 한 좋은 옵션 일 수도 있습니다.

> [!NOTE] 
> Av2 시리즈는 premium storage를 지원 하지 않으므로, 메모리-vCore 비율이 8 인 가상 머신을 사용 하더라도 프로덕션 SQL Server 워크 로드에는 권장 되지 않습니다.

## <a name="storage-optimized"></a>Storage에 최적화

[저장소에 최적화 된 VM 크기](../../../virtual-machines/sizes-storage.md) 는 특정 사용 사례에 대 한 것입니다. 이러한 가상 머신은 최적화 된 디스크 처리량 및 IO로 특별히 설계 되었습니다. 

### <a name="lsv2-series"></a>Lsv2 시리즈

[Lsv2 시리즈](../../../virtual-machines/lsv2-series.md) 는 높은 처리량, 짧은 대기 시간 및 로컬 NVMe 저장소 기능을 제공 합니다. Lsv2 시리즈 Vm은 내구성이 있는 데이터 디스크를 사용 하는 대신 VM에 직접 연결 된 노드에서 로컬 디스크를 사용 하도록 최적화 되어 있습니다. 

이러한 가상 머신은 빅 데이터, 데이터 웨어하우스, 보고 및 ETL 워크 로드에 대 한 강력한 옵션입니다. 로컬 NVMe 저장소의 높은 처리량 및 IOPS는 데이터베이스에 로드 되는 파일을 처리 하는 데 적합 한 사용 사례 이며, 데이터를 원본 시스템이 나 Azure Blob 저장소 또는 Azure Data Lake 같은 다른 리포지토리에서 다시 만들 수 있는 기타 시나리오입니다. [Lsv2 시리즈](../../../virtual-machines/lsv2-series.md) Vm은 한 번에 최대 30 분 동안 디스크 성능을 버스트 할 수도 있습니다.

이러한 가상 머신은 vCPU 당 8 GiB 메모리를 포함 하는 8 개에서 80 vCPU, 8 개 Vcpu 마다 1.92 TB의 NVMe SSD를 포함 합니다. 즉,이 시리즈의 가장 큰 VM, [L80s_v2](../../../virtual-machines/lsv2-series.md), 10 배 1.92 Tb의 NVMe 저장소를 사용 하는 80 vcpu 및 640 연령의 메모리를 사용 합니다.  모든 가상 컴퓨터에서 8의 메모리 대 vCore 비율이 일관 됩니다.

NVMe 저장소는 가상 컴퓨터의 할당을 취소 하거나 서비스 복구를 위해 다른 호스트로 이동 하는 경우 이러한 디스크에서 데이터가 손실 될 수 있다는 것을 의미 합니다.

Lsv2 및 Ls 시리즈는 premium [storage를 지원 하지만](../../../virtual-machines/premium-storage-performance.md)프리미엄 저장소 캐싱은 지원 하지 않습니다. IOPs를 늘리기 위해 로컬 캐시를 만드는 것은 지원 되지 않습니다. 

> [!WARNING]
> 임시 NVMe 저장소에 데이터 파일을 저장 하면 VM 할당이 취소 될 때 데이터 손실이 발생할 수 있습니다. 

## <a name="constrained-vcores"></a>제약이 있는 vCores

높은 성능의 SQL Server 워크 로드에는 더 높은 vCore 수가 없는 많은 양의 메모리, i/o 및 처리량이 필요 하기도 합니다. 

대부분의 OLTP 워크 로드는 많은 수의 작은 트랜잭션으로 구동 되는 응용 프로그램 데이터베이스입니다. OLTP 워크 로드를 사용 하는 경우 소량의 데이터를 읽거나 수정 하지만 사용자 수를 기반으로 하는 트랜잭션 볼륨이 훨씬 더 높습니다. 캐시 계획에 사용할 수 있는 SQL Server 메모리가 있어야 하 고, 최근에 액세스 한 데이터를 성능에 저장 하 고, 물리적 읽기를 메모리에 빠르게 읽을 수 있는지 확인 하는 것이 중요 합니다. 

이러한 OLTP 환경에는 더 많은 양의 메모리, 빠른 저장소 및 최적으로 수행 하는 데 필요한 i/o 대역폭이 필요 합니다. 

더 높은 SQL Server 라이선스 비용 없이이 수준의 성능을 유지 하기 위해 Azure는 [제한 된 vCPU](../../../virtual-machines/constrained-vcpu.md)수를 포함 하는 VM 크기를 제공 합니다. 

그러면 부모 가상 머신의 동일한 메모리, 저장소 및 i/o 대역폭을 유지 하면서 사용 가능한 vCores를 줄임으로써 라이선스 비용을 제어할 수 있습니다.

VCPU 수는 원래 VM 크기의 1/2 ~ 1 사분기로 제한 될 수 있습니다. 가상 머신에서 사용할 수 있는 Vcores를 줄이면 메모리 대 Vcores 비율을 높일 수 있지만 계산 비용은 동일 하 게 유지 됩니다.

이러한 새 VM 크기에는 더 쉽게 식별할 수 있도록 활성 vCPUs 수를 지정 하는 접미사가 있습니다. 

예를 들어 [M64](../../../virtual-machines/constrained-vcpu.md) 는 [M64ms](../../../virtual-machines/m-series.md) 의 메모리, i/o 및 처리량을 포함 하는 vcores SQL Server 32의 라이선스만 필요 하 고, [M64-16ms](../../../virtual-machines/constrained-vcpu.md) 에는 vcores 16 개만 사용 하면 됩니다.  [M64-16ms](../../../virtual-machines/constrained-vcpu.md) 에는 M64ms의 SQL Server 라이선스 비용에 대 한 분기가 있지만 가상 머신의 계산 비용은 동일 합니다.

> [!NOTE] 
> - 중간에서 대규모 데이터 웨어하우스 워크 로드는 제약이 있는 [vCore vm](../../../virtual-machines/constrained-vcpu.md)을 활용할 수 있지만, 데이터 웨어하우스 워크 로드는 병렬 실행 되는 쿼리 계획을 통해 더 많은 양의 데이터를 처리 하는 더 작은 사용자 및 프로세스에 의해 일반적으로 특징을 갖습니다. 
> - 운영 체제 라이선스를 포함 하는 계산 비용은 부모 가상 머신과 동일 하 게 유지 됩니다. 



## <a name="next-steps"></a>다음 단계

자세히 알아보려면이 시리즈의 다른 문서를 참조 하세요.
- [빠른 검사 목록](performance-guidelines-best-practices-checklist.md)
- [스토리지](performance-guidelines-best-practices-storage.md)
- [기준선 수집](performance-guidelines-best-practices-collect-baseline.md)

보안 모범 사례는 [Azure Virtual Machines에서 SQL Server에 대 한 보안 고려 사항](security-considerations-best-practices.md)을 참조 하세요.

[Azure Virtual Machines의 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)에서 다른 SQL Server 가상 머신 문서를 검토하세요. SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](frequently-asked-questions-faq.md)을 참조하세요.
