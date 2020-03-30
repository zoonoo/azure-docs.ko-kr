---
title: Linux 또는 Windows에서 Azure 가상 컴퓨터 성능 문제 해결
description: 이 문서에서는 병목 현상 모니터링 및 관찰을 통해 VM(가상 시스템) 일반 성능 문제 해결에 대해 설명하고 발생할 수 있는 문제에 대한 가능한 해결 을 제공합니다.
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
ms.openlocfilehash: 176b0634fe2c7ee2f47162e439c4ea16bde77a8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772621"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Linux 또는 Windows에서 Azure 가상 컴퓨터 성능 문제 해결

이 문서에서는 병목 현상 모니터링 및 관찰을 통해 VM(가상 시스템) 일반 성능 문제 해결에 대해 설명하고 발생할 수 있는 문제에 대한 가능한 해결 을 제공합니다. 모니터링 외에도 Perfinsights를 사용하여 IO/CPU/메모리와 관련된 모범 사례 권장 사항 및 주요 병목 현상이 포함된 보고서를 제공할 수 있습니다. 퍼프인사이트는 [Azure에서 Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) VM 모두에서 사용할 수 있습니다.

이 문서에서는 모니터링을 사용하여 성능 병목 현상을 진단합니다.

## <a name="enabling-monitoring"></a>모니터링 사용

### <a name="azure-iaas-virtual-machine-monitoring"></a>Azure IAAS 가상 시스템 모니터링

게스트 VM을 모니터링하려면 Azure VM 모니터링을 사용하여 특정 상위 수준의 리소스 조건에 대해 경고합니다. VM 진단을 사용하도록 설정했는지 확인하려면 [Azure 리소스 로그 개요를](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs)참조하십시오. 다음이 표시되면 진단 프로그램을 사용하도록 설정하지 않았을 가능성이 큽입니다.

![모니터링이 활성화되지 않았습니다.](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>마이크로소프트 Azure 포털을 통해 VM 진단 사용

VM 진단을 사용하려면 다음을 수행합니다.

1. VM으로 이동
2. **진단 설정을 클릭합니다.**
3. 저장소 계정을 선택하고 **게스트 수준 모니터링 활성화를**클릭합니다.

   ![설정을 클릭한 다음 진단](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

**진단 설정에서** **에이전트** 탭에서 진단 설정에 사용되는 저장소 계정을 확인할 수 있습니다.

![저장소 계정 확인](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Azure 포털을 통한 저장소 계정 진단 사용

Azure의 가상 시스템에 대한 IO 성능을 분석하려는 경우 저장소는 매우 중요한 계층입니다. 저장소 관련 메트릭의 경우 추가 단계로 진단을 사용하도록 설정해야 합니다. 저장소 관련 카운터만 분석하려는 경우에도 이 옵션을 사용할 수 있습니다.

1. VM을 선택하여 VM이 사용하는 저장소 계정(또는 계정)을 식별합니다. **설정을**클릭한 다음 **디스크를 클릭합니다.**

   ![설정을 클릭한 다음 디스크](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. 포털에서 VM의 저장소 계정(또는 계정)으로 이동하여 다음 단계를 수행합니다.

   1. 위의 단계를 통해 찾은 저장소 계정에 대한 개요를 클릭합니다.
   2. 기본 메트릭이 표시됩니다. 

    ![기본 메트릭](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. 메트릭을 클릭하면 메트릭을 구성하고 추가할 수 있는 옵션이 더 많은 다른 블레이드가 표시됩니다.

   ![메트릭 추가](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

이러한 옵션을 구성하려면 다음을 수행하십시오.

1.  **메트릭을 선택합니다.**
2.  **리소스(저장소** 계정)를 선택합니다.
3.  **네임스페이스** 선택
4.  **메트릭**을 선택합니다.
5.  **집계** 유형 선택
6.  대시보드에서 이 보기를 고정할 수 있습니다.

## <a name="observing-bottlenecks"></a>병목 현상 관찰

필요한 메트릭에 대한 초기 설정 프로세스를 거치고 VM 및 관련 저장소 계정에 대한 진단을 사용하도록 설정한 후 분석 단계로 전환할 수 있습니다.

### <a name="accessing-the-monitoring"></a>모니터링 에 액세스

조사할 Azure VM을 선택하고 **모니터링을**선택합니다.

![모니터링 선택](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>관찰 일정

리소스 병목 현상이 있는지 확인하려면 데이터를 검토합니다. 컴퓨터가 잘 실행되었지만 최근에 성능이 저하되었다고 보고된 경우 보고된 시간, 문제 중 및 이후에 보고된 성능 메트릭 데이터를 포함하는 데이터의 시간 범위를 검토합니다.

### <a name="check-for-cpu-bottleneck"></a>CPU 병목 현상 확인

![CPU 병목 현상 확인](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. 그래프를 편집합니다.
2. 시간 범위를 설정합니다.
3. 그런 다음 카운터에 추가해야 합니다: CPU 백분율 게스트 OS
4. 저장합니다.

### <a name="cpu-observe-trends"></a>CPU는 추세를 관찰

성능 문제를 볼 때는 추세를 인식하고 추세에 영향을 미치는지 이해합니다. 다음 섹션에서는 포털의 모니터링 그래프를 사용하여 추세를 표시합니다. 또한 같은 기간에 차이 리소스 동작을 상호 참조하는 데 유용할 수도 있습니다. 그래프를 사용자 지정하려면 [Azure Monitor 데이터 플랫폼을 클릭합니다.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)

스파이크 – 스파이크는 예약된 작업/알려진 이벤트와 관련이 있을 수 있습니다. 작업을 식별할 수 있는 경우 작업이 필요한 성능 수준에서 실행되는지 여부를 확인합니다. 성능이 허용되는 경우 리소스를 늘릴 필요가 없습니다.

스파이크 업 및 상수 – 종종 새 워크로드를 나타냅니다. 인식된 워크로드가 아닌 경우 VM에서 모니터링을 사용하여 동작의 원인(또는 프로세스)을 확인할 수 있습니다. 프로세스가 인식되면 비효율적인 코드 또는 일반 소비로 인해 소비가 증가했는지 여부를 확인합니다. 정상적인 소비인 경우 프로세스가 필요한 성능 수준에서 작동하는지 여부를 결정합니다.

상수 – VM이 항상 이 수준에서 실행되었는지 또는 진단이 활성화된 이후 해당 수준에서만 실행되고 있는지 확인합니다. 그렇다면 문제를 일으키는 프로세스(또는 프로세스)를 식별하고 해당 리소스를 더 추가하는 것이 좋습니다.

꾸준히 증가 - 사용량이 지속적으로 증가하는 것은 비효율적인 코드이거나 더 많은 사용자 워크로드를 처리하는 프로세스입니다.

### <a name="high-cpu-utilization-remediation"></a>높은 CPU 사용률 개선

응용 프로그램 또는 프로세스가 올바른 성능 수준에서 실행되지 않고 95% + CPU 사용률이 일정하다면 다음 작업 중 하나를 수행할 수 있습니다.

* 즉각적인 완화를 위해 - 코어가 더 많은 크기로 VM 크기를 늘립니다.
* 문제 이해 - 응용 프로그램/프로세스를 찾고 그에 따라 문제를 해결합니다.

VM을 늘렸고 CPU가 여전히 95%를 실행 중인 경우 이 설정이 더 나은 성능을 제공하는지 또는 더 높은 응용 프로그램 처리량을 허용 가능한 수준으로 제공하는지 확인합니다. 그렇지 않은 경우 해당 개별 응용 프로그램\프로세스문제를 해결합니다.

[Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) 또는 [Linux용](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) Perfinsights를 사용하여 CPU 소비를 유도하는 프로세스를 분석할 수 있습니다. 

## <a name="check-for-memory-bottleneck"></a>메모리 병목 현상 확인

메트릭을 보려면 다음을 수행하십시오.

1. 단면을 추가합니다.
2. 타일을 추가합니다.
3. 갤러리를 엽니다.
4. 메모리 사용량을 선택하고 드래그합니다. 타일이 도킹되면 마우스 오른쪽 단추로 클릭하고 **6x4를**선택합니다.

### <a name="memory-observe-trends"></a>메모리 관찰 동향

메모리 사용량은 VM에서 사용 중인 메모리의 양을 보여 주며, 이 사용법은 VM에서 사용 중인 메모리의 양을 보여줍니다. 추세를 이해하고 문제가 표시되는 시간에 매핑되는지 여부를 파악합니다. 항상 사용 가능한 메모리가 100MB 이상이어야 합니다.

스파이크 및 상수/일정 한 꾸준한 소비 - 관계형 데이터베이스 엔진과 같은 일부 응용 프로그램에서는 많은 양의 메모리를 할당하므로 메모리 사용률이 높으면 성능이 저하되지 않을 수 있으며 이 사용률은 중요하지 않을 수 있습니다. 그러나 메모리가 부족한 응용 프로그램이 여러 개 있는 경우 메모리 경합으로 인해 디스크로 트리밍 및 페이징/스와핑이 발생할 수 있습니다. 이러한 성능 저하는 종종 응용 프로그램 성능에 영향을 미치는 눈에 띄는 원인입니다.

꾸준히 소비 증가 – 가능한 응용 프로그램 '워밍업', 이 소비는 시작 데이터베이스 엔진 중 일반적이다. 그러나 애플리케이션의 메모리 누수 징후일 수도 있습니다. 응용 프로그램을 식별하고 동작이 예상되는지 여부를 이해합니다.

페이지 또는 파일 사용 스왑 - Windows 페이징 파일(D:\) 또는 Linux Swap `/dev/sdb`파일(에 있음)을 사용하고 있는지 확인합니다. 이러한 파일을 제외한 이러한 볼륨에 아무 것도 없는 경우 해당 디스크에서 높은 읽기/쓰기를 확인합니다. 이 문제는 메모리 부족 상태를 나타냅니다.

### <a name="high-memory-utilization-remediation"></a>높은 메모리 사용률 수정

높은 메모리 사용률을 해결하려면 다음 작업 중 한 가지 를 수행하십시오.

* 즉각적인 릴리프 또는 페이지 또는 파일 사용 스왑 - VM 크기를 메모리가 더 많은 VM 크기로 늘린 다음 모니터링합니다.
* 문제 이해 – 응용 프로그램/프로세스를 찾고 고소비 메모리 응용 프로그램을 식별하기 위한 문제 해결.
* 응용 프로그램을 알고 있는 경우 메모리 할당을 제한할 수 있는지 확인합니다.

더 큰 VM으로 업그레이드한 후에도 100%까지 꾸준히 증가하는 것을 발견하면 응용 프로그램/프로세스및 문제 해결을 식별할 수 있습니다.

[Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) 또는 [Linux용](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) Perfinsights를 사용하여 메모리 소비를 유도하는 프로세스를 분석할 수 있습니다. 

## <a name="check-for-disk-bottleneck"></a>디스크 병목 현상 확인

VM의 저장소 하위 시스템을 확인하려면 VM 진단및 저장소 계정 진단의 카운터를 사용하여 Azure VM 수준에서 진단을 확인합니다.

VM 특정 문제 해결의 경우 [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) 또는 [Linux용](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux)Perfinsights를 사용하여 IO를 구동하는 프로세스를 분석하는 데 도움이 될 수 있습니다. 

영역 중복 및 프리미엄 저장소 계정에 대한 카운터는 없습니다. 이러한 카운터와 관련된 문제의 경우 지원 사례를 발생시.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>모니터링에서 스토리지 계정 진단 보기

아래 항목에 대해 작업하려면 포털의 VM에 대한 저장소 계정으로 이동합니다.

![모니터링에서 저장소 계정 진단 보기](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. 모니터링 그래프를 편집합니다.
2. 시간 범위를 설정합니다.
3. 아래 단계에 설명된 카운터를 추가합니다.
4. 변경 내용을 저장합니다.

### <a name="disk-observe-trends-standard-storage-only"></a>디스크 관찰 추세(표준 저장소만)

저장소 문제를 식별하려면 저장소 계정 진단 및 VM 진단의 성능 메트릭을 확인합니다.

아래의 각 확인에 대해 문제의 시간 범위 내에서 문제가 발생하는 주요 추세를 찾습니다.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Azure 저장소 가용성 확인 - 저장소 계정 메트릭 추가: 가용성

가용성이 떨어지면 플랫폼에 문제가 있을 수 있으므로 [Azure 상태를](https://azure.microsoft.com/status/)확인합니다. 문제가 표시되지 않으면 새 지원 요청을 발생시면

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Azure 저장소 시간 시간 확인 - 저장소 계정 메트릭 추가:

* 클라이언트 타임아웃 오류
* 서버 타임아웃 오류
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

*TimeOutError 메트릭의 값은 IO 작업이 너무 오래 걸리고 시간이 오래 걸렸음을 나타냅니다. 다음 단계를 통해 작업하면 잠재적인 원인을 식별하는 데 도움이 됩니다.

타임아웃 오류에서 동시에 평균 ServerLatency 증가는 플랫폼 문제가 될 수 있습니다. 이 경우 새 지원 요청을 발생시.

평균E2ELatency는 클라이언트 대기 시간을 나타냅니다. 응용 프로그램에서 IOPS가 수행되는 방식을 확인합니다. TotalRequests 증가 또는 지속적으로 높은 TotalRequests 메트릭을 찾습니다. 이 메트릭은 IOPS를 나타냅니다. 저장소 계정 또는 단일 VHD의 한도에 도달하기 시작하면 대기 시간이 제한과 관련될 수 있습니다.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Azure 저장소 제한 확인 - 저장소 계정 메트릭 추가: 제한오류

제한 값은 저장소 계정 수준에서 제한되고 있음을 나타내므로 계정의 IOPS 한도에 도달했습니다. 메트릭 **TotalRequests를**확인하여 IoP 임계값에 도달했는지 여부를 확인할 수 있습니다.

각 VHD는 500 IOPS 또는 60MBits의 제한을 가지지만 저장소 계정당 누적 한도인 20000 IOPS에 구속됩니다.

이 메트릭에서는 제한을 일으키는 Blob과 그 영향을 받는 Blob을 알 수 없습니다. 그러나 저장소 계정의 IOPS 또는 정보/송신 제한에 부딪히게 됩니다.

IOPS 한도에 도달했는지 확인하려면 저장소 계정 진단으로 이동하여 TotalRequests를 확인하여 20,000TotalRequests에 접근하는지 확인합니다. 패턴의 변경 중 하나, 처음 제한을 볼 수 있는지 여부 또는 이 제한이 특정 시간에 발생하는지 여부를 식별합니다.

표준 저장소에서 새 디스크 오퍼링을 사용하면 IOPS 및 처리량 한도가 다를 수 있지만 표준 저장소 계정의 누적 한도는 20000 IOPS입니다(프리미엄 저장소는 계정 또는 디스크 수준에서 다른 제한이 있음). 다양한 표준 스토리지 디스크 제품 및 디스크 제한당에 대해 자세히 알아보기:

* [Windows의 VM 디스크에 대한 확장성 및 성능 대상](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets)

#### <a name="references"></a>참조

* [프리미엄 페이지 Blob 저장소 계정에 대한 확장성 및 성능 목표](../../storage/blobs/scalability-targets-premium-page-blobs.md)

저장소 계정의 대역폭은 저장소 계정 메트릭인 총계 및 TotalEgress에 의해 측정됩니다. 중복 성 및 영역 유형에 따라 대역폭에 대한 임계값이 다릅니다.

* [표준 스토리지 계정의 확장성 및 성능 목표](../../storage/common/scalability-targets-standard-account.md)

저장소 계정 중복 유형 및 지역에 대한 송신 및 송신 제한에 대해 TotalIngress 및 TotalEgress를 확인합니다.

VM에 연결된 VHD의 처리량 제한을 확인합니다. VM 메트릭 디스크 읽기 및 쓰기를 추가합니다.

표준 스토리지의 새로운 디스크 오퍼링에는 IOPS 및 처리량 제한이 다릅니다(IOPS는 VHD당 노출되지 않음). 데이터를 확인하여 디스크 읽기 및 쓰기를 사용하여 VM 수준에서 VHD의 결합 처리량 MB의 한계를 초과하는지 확인한 다음 VM 스토리지 구성을 최적화하여 단일 VHD 제한을 초과하는 경우를 확인합니다. 다양한 표준 스토리지 디스크 제품 및 디스크 제한당에 대해 자세히 알아보기:

* [Windows의 VM 디스크에 대한 확장성 및 성능 대상](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets)

### <a name="high-disk-utilizationlatency-remediation"></a>높은 디스크 사용률/대기 시간 수정

클라이언트 대기 시간을 줄이고 VM IO를 최적화하여 VHD 제한을 초과하는 확장

* [Azure에서 Windows용 IO 최적화](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [Azure에서 Linux용 IO 최적화](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>제한 감소

저장소 계정의 상한에 부딪히면 저장소 계정 간에 VHD의 균형을 다시 조정합니다. Azure [저장소 확장성 및 성능 목표를](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/)참조하십시오.

### <a name="increase-throughput-and-reduce-latency"></a>처리량을 늘리고 대기 시간을 줄입니다.

대기 시간에 민감한 응용 프로그램이 있고 높은 처리량이 필요한 경우 DS 및 GS 시리즈 VM을 사용하여 VHD를 Azure Premium 저장소로 마이그레이션합니다.

이 문서에서는 특정 시나리오에 대해 설명합니다.

* [Azure Premium Storage로 마이그레이션](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [SQL 서버와 함께 Azure 프리미엄 저장소 사용](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>다음 단계

이 문서의 어느 시점에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼의 Azure 전문가에게 문의하십시오.](https://azure.microsoft.com/support/forums/)

또는 Azure 지원 인시던트를 파일로 제출합니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.
