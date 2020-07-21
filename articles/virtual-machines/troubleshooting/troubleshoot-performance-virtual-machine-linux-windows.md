---
title: Linux 또는 Windows에서 Azure 가상 머신 성능 문제 해결
description: 이 문서에서는 병목 현상 모니터링 및 관찰을 통해 VM (가상 컴퓨터) 일반 성능 문제를 설명 하 고 발생할 수 있는 문제에 대 한 가능한 수정을 제공 합니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 53fd2332224d903c5a4b33563470cf3569f82b13
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526659"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Linux 또는 Windows에서 Azure 가상 머신 성능 문제 해결

이 문서에서는 병목 현상 모니터링 및 관찰을 통해 VM (가상 컴퓨터) 일반 성능 문제를 설명 하 고 발생할 수 있는 문제에 대 한 가능한 수정을 제공 합니다. 모니터링 외에도 모범 사례 권장 사항을 포함 하는 보고서를 제공할 수 있는 Perfinsights 및 IO/CPU/메모리와 관련 된 주요 병목 상태를 사용할 수 있습니다. Perfinsights는 Azure의 [Windows](./how-to-use-perfinsights.md) 및 [Linux](./how-to-use-perfinsights-linux.md) VM 모두에서 사용할 수 있습니다.

이 문서에서는 모니터링을 사용 하 여 성능 병목 상태를 진단 하는 과정을 안내 합니다.

## <a name="enabling-monitoring"></a>모니터링 사용

### <a name="azure-iaas-virtual-machine-monitoring"></a>Azure IAAS 가상 머신 모니터링

게스트 VM을 모니터링 하려면 Azure VM 모니터링을 사용 합니다. 그러면 특정 개략적인 리소스 조건에 대해 경고를 표시 합니다. VM 진단을 사용 하도록 설정 했는지 여부를 확인 하려면 [Azure 리소스 로그 개요](../../azure-monitor/learn/tutorial-resource-logs.md)를 참조 하세요. 다음이 표시 되는 경우 진단이 사용 하도록 설정 되어 있지 않을 가능성이 높습니다.

![모니터링 사용 안 함](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Microsoft Azure Portal를 통해 VM 진단을 사용 하도록 설정

VM 진단을 사용 하도록 설정 하려면:

1. VM으로 이동
2. **진단 설정** 을 클릭 합니다.
3. 저장소 계정을 선택 하 고 **게스트 수준 모니터링 사용**을 클릭 합니다.

   ![설정, 진단을 차례로 클릭 합니다.](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

진단 **설정**의 **에이전트** 탭에서 진단 설정에 사용 되는 저장소 계정을 확인할 수 있습니다.

![저장소 계정 확인](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Azure Portal를 통해 저장소 계정 진단을 사용 하도록 설정

저장소는 Azure에서 가상 머신에 대 한 IO 성능을 분석 하려는 경우 매우 중요 한 계층입니다. 저장소 관련 메트릭의 경우 추가 단계로 진단을 사용 하도록 설정 해야 합니다. 저장소 관련 카운터만 분석 하려는 경우에도이 기능을 사용 하도록 설정할 수 있습니다.

1. Vm을 선택 하 여 VM에서 사용 하는 저장소 계정 (또는 계정)을 식별 합니다. **설정**을 클릭 한 다음 **디스크**를 클릭 합니다.

   ![설정, 디스크를 차례로 클릭 합니다.](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. 포털에서 VM에 대 한 저장소 계정으로 이동 하 고 다음 단계를 수행 합니다.

   1. 위의 단계에서 찾은 저장소 계정에 대 한 개요를 클릭 합니다.
   2. 기본 메트릭이 표시 됩니다. 

    ![기본 메트릭](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. 메트릭을 클릭 하 여 메트릭을 구성 하 고 추가 하는 옵션이 더 포함 된 다른 블레이드를 표시 합니다.

   ![메트릭 추가](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

이러한 옵션을 구성 하려면:

1.  **메트릭**을 선택합니다.
2.  **리소스** (저장소 계정)를 선택 합니다.
3.  **네임 스페이스** 선택
4.  **메트릭**을 선택 합니다.
5.  **집계** 유형을 선택 합니다.
6.  이 보기를 대시보드에 고정할 수 있습니다.

## <a name="observing-bottlenecks"></a>병목 상태 관찰

필요한 메트릭에 대 한 초기 설정 프로세스를 진행 하 고 VM 및 관련 저장소 계정에 대 한 진단을 사용 하도록 설정한 후에는 분석 단계로 전환할 수 있습니다.

### <a name="accessing-the-monitoring"></a>모니터링에 액세스

조사 하려는 Azure VM을 선택 하 고 **모니터링**을 선택 합니다.

![모니터링 선택](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>관찰 타임 라인

리소스 병목 현상이 있는지 확인 하려면 데이터를 검토 합니다. 컴퓨터가 정상적으로 실행 되 고 있는 것을 확인 했지만 성능이 최근에 저하 된 것으로 보고 된 경우 문제 발생 전후에 보고 된 변경 전에 성능 메트릭 데이터를 포함 하는 데이터의 시간 범위를 검토 합니다.

### <a name="check-for-cpu-bottleneck"></a>CPU 병목 상태 확인

![CPU 병목 상태 확인](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. 그래프를 편집 합니다.
2. 시간 범위를 설정 합니다.
3. 그런 다음 카운터: CPU 비율 게스트 OS를 추가 해야 합니다.
4. 저장합니다.

### <a name="cpu-observe-trends"></a>CPU 관찰 추세

성능 문제를 살펴보면 추세를 파악 하 고이에 영향을 주는지 파악 해야 합니다. 다음 섹션에서는 포털의 모니터링 그래프를 사용 하 여 추세를 보여 줍니다. 동일한 기간의 상호 참조 차이 리소스 동작에도 유용할 수 있습니다. 그래프를 사용자 지정 하려면 [Azure Monitor data platform](../../azure-monitor/platform/data-platform.md)을 클릭 합니다.

의 급격 –의 급격은 예약 된 작업/알려진 이벤트와 관련 될 수 있습니다. 작업을 식별할 수 있는 경우 작업이 필요한 성능 수준에서 실행 되는지 확인 합니다. 성능을 허용할 수 있는 경우 리소스를 늘릴 필요가 없습니다.

스파이크 및 Constant – 새 작업을 표시 하는 경우가 많습니다. 인식 된 작업이 아니면 VM에서 모니터링을 사용 하도록 설정 하 여 동작을 발생 시키는 프로세스 (또는 프로세스)를 확인 합니다. 프로세스가 인식 되 면 증가 된 소비를 비효율적인 코드 또는 정상적인 소비로 인해 발생 하는지 확인 합니다. 정상적인 소비 인 경우 프로세스가 필요한 성능 수준에서 작동 하는지 여부를 결정 합니다.

상수 – VM이 항상이 수준에서 실행 되는지 또는 진단이 사용 하도록 설정 된 이후 해당 수준 에서만 실행 되 고 있는지를 확인 합니다. 이 경우 문제의 원인이 되는 프로세스를 식별 하 고 해당 리소스를 더 추가 하는 것이 좋습니다.

꾸준히 증가 – 지속적인 소비는 비효율적 이거나 더 많은 사용자 작업을 수행 하는 프로세스입니다.

### <a name="high-cpu-utilization-remediation"></a>높은 CPU 사용률 수정

응용 프로그램 또는 프로세스가 올바른 성능 수준에서 실행 되 고 있지 않고 95% + CPU 사용률 상수가 표시 되는 경우 다음 작업 중 하나를 수행할 수 있습니다.

* 즉시 릴리프의 경우-더 많은 코어가 있는 크기로 VM 크기를 늘립니다.
* 문제 이해 – 응용 프로그램/프로세스를 찾아 문제를 해결 합니다.

VM을 증가 시키고 CPU가 여전히 95%를 실행 하는 경우이 설정이 적절 한 수준으로 더 나은 성능을 제공 하는지 또는 응용 프로그램 처리량을 더 높게 제공 하는지 확인 합니다. 그렇지 않은 경우 개별 application\process.의 문제를 해결 합니다.

[Windows](./how-to-use-perfinsights.md) 또는 [Linux](./how-to-use-perfinsights-linux.md) 용 Perfinsights을 사용 하 여 CPU 사용량을 제어 하는 프로세스를 분석할 수 있습니다. 

## <a name="check-for-memory-bottleneck"></a>메모리 병목 상태 확인

메트릭을 보려면 다음을 수행 합니다.

1. 섹션을 추가 합니다.
2. 타일을 추가 합니다.
3. 갤러리를 엽니다.
4. 메모리 사용량을 선택 하 고 끕니다. 타일이 도킹 되 면 마우스 오른쪽 단추를 클릭 하 고 **6x4**를 선택 합니다.

### <a name="memory-observe-trends"></a>메모리 관찰 추세

메모리 사용에는 VM에서 사용 되는 메모리의 양이 표시 됩니다. 추세와 문제가 표시 된 시간에 매핑되는지 여부를 이해 합니다. 항상 100 MB 이상의 사용 가능한 메모리가 있어야 합니다.

스파이크 및 상수/상수를 사용 하는 경우, 관계형 데이터베이스 엔진과 같은 일부 응용 프로그램에서 많은 양의 메모리를 할당 하 고이 사용률이 중요 하지 않을 수 있으므로 메모리 사용률이 높으면 잘못 된 성능의 원인이 아닐 수 있습니다. 그러나 메모리를 많이 소비 하는 응용 프로그램이 여러 개 있는 경우 메모리 경합으로 인해 디스크에 대 한 트리밍 및 페이징/스와핑의 성능이 저하 될 수 있습니다. 이러한 성능 저하는 종종 응용 프로그램 성능에 미치는 영향을 크게 초래 합니다.

꾸준히 증가 – 가능한 응용 프로그램 ' 준비 '는 이러한 소비는 시작 되는 데이터베이스 엔진 사이에서 자주 발생 합니다. 그러나 애플리케이션의 메모리 누수 징후일 수도 있습니다. 응용 프로그램을 식별 하 고 동작이 예상 되는지 여부를 파악 합니다.

페이지 또는 스왑 파일 사용 – Windows 페이징 파일을 사용 하 고 있는지 확인 \) 합니다 (에 있는 D: 또는 Linux 스왑 파일 (에 있음 `/dev/sdb` ). 이러한 파일을 제외 하 고 이러한 볼륨에 아무 것도 없는 경우 해당 디스크에 대 한 읽기/쓰기의 높은를 확인 합니다. 이 문제는 메모리 부족 상태를 나타냅니다.

### <a name="high-memory-utilization-remediation"></a>높은 메모리 사용률 수정

높은 메모리 사용률을 해결 하려면 다음 작업 중 하나를 수행 합니다.

* 즉시 릴리프 나 페이지 또는 스왑 파일 사용의 경우 더 많은 메모리를 사용 하는 VM 크기를 1 씩 늘리고 모니터 합니다.
* 문제 이해 – 소비 중인 메모리 응용 프로그램을 식별 하기 위한 응용 프로그램/프로세스 및 문제를 찾습니다.
* 응용 프로그램을 알고 있는 경우 메모리 할당을 제거할 수 있는지 여부를 참조 하세요.

큰 VM으로 업그레이드 한 후에도 여전히 지속적으로 100%까지 지속적으로 증가 하는 것을 알 수 있습니다.

[Windows](./how-to-use-perfinsights.md) 또는 [Linux](./how-to-use-perfinsights-linux.md) 용 Perfinsights를 사용 하 여 메모리 소비를 구동 하는 프로세스를 분석할 수 있습니다. 

## <a name="check-for-disk-bottleneck"></a>디스크 병목 상태 확인

VM에 대 한 저장소 하위 시스템을 확인 하려면 VM 진단의 카운터와 저장소 계정 진단을 사용 하 여 Azure VM 수준에서 진단을 확인 하세요.

VM 특정 문제 해결에서 [Windows](./how-to-use-perfinsights.md) 또는 [Linux](./how-to-use-perfinsights-linux.md)에 대해 Perfinsights를 사용 하 여 IO를 구동 하는 프로세스를 분석할 수 있습니다. 

영역 중복 및 Premium Storage 계정에 대 한 카운터가 없습니다. 이러한 카운터와 관련 된 문제의 경우 지원 사례를 발생 시킵니다.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>모니터링에서 저장소 계정 진단 보기

아래 항목에 대 한 작업을 수행 하려면 포털에서 VM에 대 한 저장소 계정으로 이동 합니다.

![모니터링에서 저장소 계정 진단 보기](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. 모니터링 그래프를 편집 합니다.
2. 시간 범위를 설정 합니다.
3. 아래 단계에 설명 된 카운터를 추가 합니다.
4. 변경 내용을 저장합니다.

### <a name="disk-observe-trends-standard-storage-only"></a>디스크 관찰 추세 (표준 저장소에만 해당)

저장소 문제를 식별 하려면 저장소 계정 진단 및 VM 진단에서 성능 메트릭을 확인 합니다.

아래 각 검사에 대해 문제의 시간 범위 내에서 문제가 발생 하는 경우 주요 추세를 확인 합니다.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Azure storage 가용성 확인 – 저장소 계정 메트릭: 가용성을 추가 합니다.

가용성에 대 한 삭제가 표시 되는 경우 플랫폼에 문제가 있을 수 있습니다. [Azure 상태](https://azure.microsoft.com/status/)를 확인 하세요. 문제가 표시 되지 않으면 새 지원 요청을 발생 시킵니다.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Azure storage 시간 제한 확인-저장소 계정 메트릭을 추가 합니다.

* ClientTimeOutError
* ServerTimeOutError
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

* TimeOutError 메트릭의 값은 IO 작업이 너무 오래 걸려서 시간이 초과 되었음을 표시 합니다. 다음 단계를 수행 하면 잠재적 원인을 파악 하는 데 도움이 됩니다.

TimeOutErrors는 플랫폼 문제가 AverageServerLatency 동시에 더 늘어납니다. 이 경우 새 지원 요청을 발생 시킵니다.

AverageE2ELatency은 클라이언트 대기 시간을 나타냅니다. 응용 프로그램에서 IOPS를 수행 하는 방법을 확인 합니다. 증가 또는 지속적으로 높은 TotalRequests 메트릭을 찾습니다. 이 메트릭은 IOPS를 나타냅니다. 저장소 계정 또는 단일 VHD의 한도에 도달 하기 시작 하는 경우에는 제한에 대 한 대기 시간을 조정할 수 있습니다.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Azure storage 제한 확인-저장소 계정 메트릭 추가: ThrottlingError

제한 값은 사용자가 저장소 계정 수준에서 제한 되 고 있음을 나타냅니다. 즉, 계정에 대 한 IOPS 제한에 도달 하 고 있음을 나타냅니다. **TotalRequests**메트릭을 확인 하 여 IOPs 임계값에 도달 하 고 있는지 여부를 확인할 수 있습니다.

각 VHD의 제한은 500 IOPS 또는 60 메가 비트 이지만 저장소 계정 당 초당 2만 IOPS의 누적 한도에 의해 바인딩됩니다.

이 메트릭을 사용 하 여 제한을 유발 하는 blob 및 그에 의해 영향을 받는 blob을 확인할 수 없습니다. 그러나 저장소 계정의 IOPS 또는 수신/송신 제한에 도달 하는 중입니다.

IOPS 제한에 도달 하 고 있는지 확인 하려면 Storage 계정 진단으로 이동 하 여 TotalRequests를 확인 하 고 2만 TotalRequests에 도달 했는지 확인 합니다. 첫 번째 제한이 있는지 여부 또는 특정 시간에이 제한이 발생 하는지 여부에 관계 없이 패턴의 변경 내용 중 하나를 식별 합니다.

Standard storage에서 새로운 디스크 제품을 사용 하는 경우 IOPS 및 처리량 한도가 다를 수 있지만 표준 저장소 계정의 누적 제한은 2만 IOPS (Premium storage는 계정 또는 디스크 수준에서 다른 한도)입니다. 다른 standard storage 디스크 제공 및 디스크당 제한 사항에 대해 자세히 알아보세요.

* [Windows의 VM 디스크에 대 한 확장성 및 성능 목표](../windows/disk-scalability-targets.md)

#### <a name="references"></a>참고 자료

* [프리미엄 페이지 blob storage 계정에 대 한 확장성 및 성능 목표](../../storage/blobs/scalability-targets-premium-page-blobs.md)

저장소 계정의 대역폭은 저장소 계정 메트릭: TotalIngress 및 Totalingress로 측정 됩니다. 중복성 및 지역 유형에 따라 대역폭에 대해 서로 다른 임계값이 있습니다.

* [표준 스토리지 계정의 확장성 및 성능 목표](../../storage/common/scalability-targets-standard-account.md)

저장소 계정 중복 유형 및 지역에 대 한 수신 및 발신 한도에 대해 TotalIngress 및 Totalingress를 확인 합니다.

VM에 연결 된 Vhd의 처리량 한도를 확인 합니다. VM 메트릭 디스크 읽기 및 쓰기를 추가 합니다.

Standard storage의 새 디스크 제공에는 IOPS 및 처리량 한도가 다릅니다 (IOPS는 VHD 당 노출 되지 않음). 데이터를 확인 하 여 디스크 읽기 및 쓰기를 사용 하는 VM 수준에서 VHD의 총 용량 (MB)에 대 한 제한에 도달 했는지 확인 한 다음 VM 저장소 구성을 최적화 하 여 지난 단일 VHD 제한을 확장 합니다. 다른 standard storage 디스크 제공 및 디스크당 제한 사항에 대해 자세히 알아보세요.

* [Windows의 VM 디스크에 대 한 확장성 및 성능 목표](../windows/disk-scalability-targets.md)

### <a name="high-disk-utilizationlatency-remediation"></a>높은 디스크 사용률/대기 시간 재구성

클라이언트 대기 시간을 줄이고 VM IO를 최적화 하 여 VHD 제한 이전 크기 조정

* [Azure에서 Windows 용 IO 최적화](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md?toc=/azure/virtual-machines/windows/toc.json)

* [Azure에서 Linux에 대 한 IO 최적화](/archive/blogs/igorpag/azure-storage-secrets-and-linux-io-optimizations)

#### <a name="reduce-throttling"></a>제한 줄이기

에서 저장소 계정에 대 한 상한 제한을 초과 하는 경우 저장소 계정 간에 Vhd의 균형을 다시 조정 합니다. [Azure Storage 확장성 및 성능 목표](../../storage/common/scalability-targets-standard-account.md)를 참조 하세요.

### <a name="increase-throughput-and-reduce-latency"></a>처리량 증가 및 대기 시간 단축

대기 시간이 중요 한 응용 프로그램이 있고 높은 처리량이 필요한 경우에는 DS 및 GS 시리즈 VM을 사용 하 여 Vhd를 Azure Premium storage로 마이그레이션합니다.

다음 문서에서는 특정 시나리오에 대해 설명 합니다.

* [Azure Premium Storage로 마이그레이션](../windows/migrate-to-managed-disks.md)

* [SQL Server에서 Azure Premium Storage 사용](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-premium-storage)

## <a name="next-steps"></a>다음 단계

이 문서의 어느 시점에서 든 도움이 필요한 경우 [MSDN azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에 게 문의 하세요.

또는 Azure 지원 인시던트를 파일에 제공 합니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
