---
title: Windows 가상 데스크톱 비용 가격 예측 모니터링-Azure
description: Windows 가상 데스크톱에 대 한 Azure Monitor 사용에 대 한 비용 및 가격 책정을 계산 하는 방법입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 099def5a29c8a9b46733ba435befed7210756012
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709981"
---
# <a name="estimate-azure-monitor-costs"></a>예상 Azure Monitor 비용

Azure Monitor 로그는 사용자 환경에서 생성 되는 데이터를 수집, 인덱스 및 저장 하는 서비스입니다. 이로 인해 Azure Monitor 가격 책정 모델은 Log Analytics 작업 영역에서 매일 (또는 "수집") 가져와 처리 하 고 처리 하는 데이터의 양을 기준으로 합니다. Log Analytics 작업 영역의 비용은 수집 된 데이터의 양을 기반으로 하는 것이 아니라 선택한 Azure 결제 계획 및 사용자 환경에서 생성 하는 데이터를 저장 하도록 선택 하는 기간을 기반으로 합니다.

이 문서에서는 Azure Monitor의 가격은 어떻게 작동 하는지 이해 하는 데 도움이 되는 다음 사항을 설명 합니다.

- 이 기능을 사용 하도록 설정 하기 전에 데이터 수집 및 저장소 비용을 예측 하는 방법
- 이 기능을 사용할 때 비용을 줄이기 위해 수집 및 저장소를 측정 하 고 제어 하는 방법

>[!NOTE]
> 이 문서에 나와 있는 모든 크기와 가격은 예측의 작동 방식을 보여 주는 예입니다. Azure Monitor Log Analytics 가격 책정 모델 및 Azure 지역에 따라 보다 정확 하 게 평가 하려면 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조 하세요.

## <a name="estimate-data-ingestion-and-storage-costs"></a>데이터 수집 및 저장소 비용 예측

Log Analytics 작업 영역에서 로그로 작성 된 미리 정의 된 데이터 집합을 사용 하는 것이 좋습니다. 다음 예에서는 기본 구성에서 청구 가능한 데이터를 살펴보겠습니다.

Windows 가상 데스크톱의 Azure Monitor에 대해 미리 정의 된 데이터 집합은 다음과 같습니다.

- 세션 호스트의 성능 카운터
- 세션 호스트의 Windows 이벤트 로그
- 서비스 인프라의 Windows 가상 데스크톱 진단

데이터 수집 및 저장소 비용은 사용자 환경 크기, 상태 및 사용에 따라 달라 집니다. 이 문서에서 사용 하는 예상 비용 범위를 계산 하기 위해이 문서에서 사용 하는 예상 비용 범위는 [가상 머신 크기 조정 지침에 따라 가상 머신 크기 조정 지침](/remote/remote-desktop-services/virtual-machine-recs)에 따라 전원 사용을 지 원하는 정상 가상 머신을 기반으로 하 여 예상 되는 데이터 수집 및 저장소 비용을 계산 합니다.

이 예제에서 사용 하는 광원 사용 VM에는 다음 구성 요소가 포함 됩니다.

- 4 개 vCPUs, 1 개 디스크
- 일별 세션 16 개
- 평균 세션 기간 2 시간 (120 분)
- 세션당 100 프로세스

이 예제에서 사용 하는 전원 사용 VM에는 다음 구성 요소가 포함 됩니다.

- 6 개 vCPUs, 1 개 디스크
- 일별 세션 6 개
- 평균 세션 기간 (4 시간) (240 분)
- 세션당 200 프로세스

## <a name="estimating-performance-counter-ingestion"></a>성능 카운터 수집 예측

성능 카운터는 시스템 리소스를 수행 하는 방법을 보여 줍니다. 성능 카운터 데이터 수집은 사용자 환경 크기 및 사용량에 따라 다릅니다. 대부분의 경우 성능 카운터는 Windows 가상 데스크톱의 Azure Monitor에 대 한 데이터 수집의 80 ~ 99%를 구성 해야 합니다.

예측을 시작 하기 전에 각 성능 카운터가 특정 빈도로 데이터를 전송 한다는 것을 이해 하는 것이 중요 합니다. 기본 샘플 속도 (분 단위)를 설정 합니다 (설정에서이 속도를 편집할 수도 있음) .이 속도는 카운터에 따라 서로 다른 곱하기 요소에 적용 됩니다. 이 비율에 영향을 주는 요인은 다음과 같습니다.

- Vm (가상 머신)의 경우 각 카운터는 vm이 실행 되는 동안 분당 기본 샘플링 주기 속도로 사용자 환경에서 VM 당 데이터를 보냅니다. 분당 기본 샘플링 주기를 사용자 환경에 있는 Vm 수와 곱하여 해당 숫자를 일일 평균 VM 수로 곱하여 이러한 카운터에서 하루에 전송 하는 레코드 수를 예측할 수 있습니다.

   요약:

   분당 기본 샘플링 주기 x VM SKU의 CPU 코어 수 x vm 수/일별 평균 VM 실행 시간 = 하루에 보낸 레코드 수

- CPU 당 계수의 경우 각 카운터는 VM이 실행 되는 동안 사용자 환경의 각 VM에서 vCPU 당 분당 기본 샘플 속도를 보냅니다. 분당 기본 샘플링 주기를 VM SKU의 CPU 코어 수와 곱하여 해당 숫자를 VM이 실행 되는 시간 (분) 및 사용자 환경의 Vm 수와 곱하여 하루에 전송할 레코드 수를 예측할 수 있습니다.

   요약:
   
   분당 기본 샘플링 주기 x VM의 CPU 코어 수 x vm이 실행 되는 시간 (분) x vm 수 = 일별 보낸 레코드 수

- 디스크당 각 카운터의 경우 각 카운터는 사용자 환경의 각 VM에 있는 각 디스크에 대 한 기본 샘플링 속도로 데이터를 전송 합니다. 이러한 카운터는 매일 전송 되는 레코드 수는 VM SKU의 디스크 수를 곱한 값으로 분당 기본 샘플링 주기, 시간당 60 분 곱하여 마지막으로 VM에 대 한 평균 활성 시간을 곱합니다.

   요약:

   분당 기본 샘플링 주기 수 x VM SKU의 디스크 수 × 60 시간/시간 × vm 수 × 평균 VM 실행 시간 = 하루에 보낸 레코드 수

- 세션당 각 카운터는 세션이 연결 된 동안 사용자 환경의 각 세션에 대 한 기본 샘플링 속도로 데이터를 보냅니다. 분당 평균 세션 수와 평균 세션 기간을 곱하여 기본 샘플링 주기를 분당 전송 하는 레코드 수를 예상할 수 있습니다.

   요약:

   분당 기본 샘플링 주기/일별 세션 수 × 평균 세션 기간 = 하루에 보낸 레코드 수

- 프로세스별 요소에 대해 각 카운터는 사용자 환경의 각 세션에서 각 프로세스에 대 한 기본 속도로 데이터를 보냅니다. 분당 기본 샘플링 주기를 일별 평균 세션 수로 곱하여 평균 세션 기간 및 세션당 평균 프로세스 수를 곱하여 매일이 카운터가 전송 하는 레코드 수를 예측할 수 있습니다.
  
   요약:

   분당 기본 샘플링 주기 (분당 세션 수) × 평균 세션 기간 x 평균 프로세스 수 = 세션당 전송 된 레코드 수

다음 표에는 Windows 가상 데스크톱 수집 및 기본 요금에 대 한 20 개의 성능 카운터가 Azure Monitor 나와 있습니다.

| 카운터 이름 | 기본 샘플링 주기 | 빈도 비율 |
|--------------|---------------------|------------------|
| 논리 디스크 (C:) \\ % 사용 가능한 공간 | 60초  | 디스크당             |
| 논리 디스크 (C:)의 \\ 평균 디스크 큐 길이   | 30초    | 디스크당             |
| 논리 디스크 (C: \\ Avg. Disk sec/Transfer)  | 60초       | 디스크당             |
| 논리 디스크 (C:) \\ 현재 디스크 큐 길이  | 30초      | 디스크당             |
| Memory ( \* ) \\ 사용 가능한 공간 (mb) | 30초    | VM 당  |
| Memory ( \* ) \\ 페이지 폴트/초 | 30초   | VM 당  |
| Memory ( \* ) \\ Pages/sec | 30초   | VM 당  |
| Memory ( \* ) \\ % 사용 중인 커밋된 바이트 수 | 30초    | VM 당  |
| PhysicalDisk ( \* ) \\ Avg. Disk Queue Length | 30초      | 디스크당             |
| PhysicalDisk ( \* ) \\ Avg. Disk Sec/Read | 30초  | 디스크당             |
| PhysicalDisk ( \* ) \\ Avg. Disk Sec/Transfer | 30초  | 디스크당             |
| PhysicalDisk ( \* ) \\ Avg. Disk Sec/Write | 30초 | 디스크당             |
| 프로세서 정보 (_Total) \\ % 프로세서 시간 | 30초 | 코어/CPU 당         |
| 터미널 서비스 ( \* ) \\ 활성 세션          | 60초 | VM 당  |
| 터미널 서비스 ( \* ) \\ 비활성 세션        | 60초 | VM 당  |
| 터미널 서비스 ( \* ) \\ 총 세션 | 60초 | VM 당  |
| 프로세스 당 사용자 입력 지연 ( \* ) \\ 최대 입력 지연         | 30초 | 프로세스별          |
| 세션당 사용자 입력 지연 ( \* ) \\ 최대 입력 지연        | 30초 | 세션당          |
| RemoteFX 네트워크 ( \* ) \\ 현재 TCP RTT | 30초 | VM 당  |
| RemoteFX 네트워크 ( \* ) \\ 현재 UDP 대역폭     | 30초 | VM 당  |

각 레코드 크기를 200 바이트로 예상 하는 경우, 기본 샘플링 주기에서 가벼운 워크 로드를 실행 하는 예제 VM은 VM 당 하루에 약 90 메가바이트의 성능 카운터 데이터를 전송 합니다. 한편, 전원 워크 로드를 실행 하는 예제 VM은 VM 당 하루에 약 130 메가바이트의 성능 카운터 데이터를 전송 합니다. 그러나 레코드 크기와 환경 사용이 다를 수 있으므로 배포에서 사용 하는 일별 메가바이트는 다를 수 있습니다.

입력 지연 성능 카운터에 대 한 자세한 내용은 [사용자 입력 지연 성능 카운터](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)를 참조 하세요.

## <a name="estimating-windows-event-log-ingestion"></a>Windows 이벤트 로그 수집 예측

Windows 이벤트 로그는 Windows 가상 컴퓨터의 Log Analytics 에이전트에서 수집 하는 데이터 원본입니다. 모니터링 해야 하는 응용 프로그램에서 만든 사용자 지정 로그 뿐만 아니라 시스템 및 응용 프로그램과 같은 표준 로그에서 이벤트를 수집할 수 있습니다.

Windows 가상 데스크톱의 Azure Monitor에 대 한 기본 Windows 이벤트는 다음과 같습니다.

- 애플리케이션
- Microsoft-windows-terminalservices-gateway-RemoteConnectionManager/Admin
- Microsoft-windows-terminalservices-gateway-LocalSessionManager/Operational
- 시스템
- Microsoft-FSLogix-앱/운영
- Microsoft-FSLogix-앱/관리자

환경에서 이벤트의 조건이 충족 될 때마다 Windows 이벤트에서 보냅니다. 정상 상태의 컴퓨터는 비정상 상태의 컴퓨터 보다 더 작은 이벤트를 전송 합니다. 이벤트 수는 예측할 수 없기 때문에이 추정치의 정상적인 환경에서 예제를 기준으로 일별 VM 당 1000의 범위 1만를 사용 합니다. 예를 들어이 예에서 각 이벤트 레코드 크기를 1500 바이트로 추정 하는 경우 지정 된 환경에 대해 하루에 약 2 ~ 15mb의 이벤트 데이터를 제공 합니다.

Windows 이벤트에 대해 자세히 알아보려면 [windows 이벤트 레코드 속성](../azure-monitor/agents/data-sources-windows-events.md)을 참조 하세요.

## <a name="estimating-diagnostics-ingestion"></a>진단 수집 예측

진단 서비스는 사용자 및 관리 작업 모두에 대 한 활동 로그를 만듭니다.

진단 카운터가 추적 하는 활동 로그의 이름은 다음과 같습니다.

- WVDCheckpoints
- WVDConnections
- WVDErrors
- WVDFeeds
- WVDManagement
- WVDAgentHealthStatus

이 서비스는 환경이 레코드를 만드는 데 필요한 조건을 충족할 때마다 진단 정보를 보냅니다. 진단 레코드 수는 예측할 수 없기 때문에이 예측에 대 한 정상 환경의 예제를 기준으로 일별 VM 당 500 ~ 1000 이벤트의 범위를 사용 합니다.

예를 들어이 예에서 각 진단 레코드 크기를 200 바이트로 추정 하는 경우 총 수집 데이터는 일별 VM 당 1mb 미만입니다.

활동 로그 범주에 대 한 자세한 내용은 [Windows 가상 데스크톱 진단](diagnostics-log-analytics.md)을 참조 하세요.

## <a name="estimating-total-costs"></a>총 비용 예측

마지막으로, 총 비용을 예측 해 보겠습니다. 이 예제에서는 이전 섹션의 예제 값을 기반으로 다음과 같은 결과를 얻을 수 있다고 가정 합니다.

| 데이터 원본        | 일별 예상 크기 (mb)   |
|-------------------------------------|------------------------------------------|
| 성능 카운터   | 90-130 |
| 이벤트    | 2-15 |
| Windows 가상 데스크톱 진단 | \< 1(sp1) |

이 예제에서 Windows 가상 데스크톱에 대 한 Azure Monitor의 총 수집 데이터는 일별 VM 당 92 ~ 145 메가바이트입니다. 즉, 각 VM은 31 일 마다 약 3 ~ 5gb의 데이터를 수집 합니다.

[가격 책정](https://azure.microsoft.com/pricing/details/monitor/)에 대 Log Analytics 한 기본 종 량 제 모델을 사용 하 여 매월 Azure Monitor 데이터 수집 및 저장소 비용을 예측할 수 있습니다. 데이터 수집에 따라 Log Analytics 가격 책정에 대 한 용량 예약 모델을 고려할 수도 있습니다.

## <a name="manage-your-data-ingestion-to-reduce-costs"></a>데이터 수집을 관리 하 여 비용 절감

이 섹션에서는 비용을 절감 하기 위해 데이터 수집을 측정 하 고 관리 하는 방법을 설명 합니다.

통합 문서에 대 한 권한 및 사용 권한을 관리 하는 방법에 대 한 자세한 내용은 [액세스 제어](../azure-monitor/visualize/workbooks-access-control.md)를 참조 하세요.

>[!NOTE]
>데이터 요소를 제거 하면 Windows 가상 데스크톱에 대 한 Azure Monitor의 해당 시각적 개체에 영향을 줍니다.

### <a name="log-analytics-settings"></a>Log Analytics 설정

다음은 데이터 수집을 관리 하기 위해 Log Analytics 설정을 최적화 하기 위한 몇 가지 제안 사항입니다.

- Windows 가상 데스크톱 리소스에 대해 지정 된 Log Analytics 작업 영역을 사용 하 여 Log Analytics에서 Windows 가상 데스크톱 배포의 가상 컴퓨터에 대 한 성능 카운터와 이벤트만 수집 하도록 합니다.
- Log Analytics 저장소 설정을 조정 하 여 비용을 관리 합니다. 보존 기간을 줄이거나, 고정 저장소 가격 책정 계층이 더 비용 효율적인 지 여부를 평가 하거나, 비정상 배포의 영향을 제한 하기 위해 수집할 수 있는 데이터의 양에 대 한 경계를 설정할 수 있습니다. 자세히 알아보려면 [Azure Monitor 로그의 사용량 및 비용 관리](../azure-monitor/platform/manage-cost-storage.md)를 참조 하세요.

### <a name="remove-excess-data"></a>초과 데이터 제거

기본 구성은 Windows 가상 데스크톱에 대 한 Azure Monitor에 권장 되는 유일한 데이터 집합입니다. 데이터 요소를 추가 하 고 호스트 진단: 호스트 브라우저에서 볼 수 있는 옵션이 항상 있습니다. 그러나 데이터를 추가 하면 Log Analytics 비용이 증가 합니다. 비용 절감을 위해 제거할 수 있습니다.

### <a name="measure-and-manage-your-performance-counter-data"></a>성능 카운터 데이터 측정 및 관리

실제 모니터링 비용은 사용자 환경 크기, 사용량 및 상태에 따라 달라 집니다. Log Analytics 작업 영역에서 데이터 수집을 측정 하는 방법을 이해 하려면 [수집 로그 데이터 볼륨 이해](../azure-monitor/logs/manage-cost-storage.md#understanding-ingested-data-volume)를 참조 하세요.

세션에서 사용 하는 성능 카운터는 Windows 가상 데스크톱의 Azure Monitor에 대 한 수집 데이터의 가장 큰 원본 일 수 있습니다. Log Analytics 작업 영역에 대 한 다음 사용자 지정 쿼리 템플릿에서는 마지막 날 동안의 빈도 및 성능 카운터 당 메가바이트 수집을 추적할 수 있습니다.

```azure
let WVDHosts = dynamic(['Host1.MyCompany.com', 'Host2.MyCompany.com']);
Perf
| where TimeGenerated > ago(1d)
| where Computer in (WVDHosts)
| extend PerfCounter = strcat(ObjectName, ":", CounterName)
| summarize Records = count(TimeGenerated), InstanceNames = dcount(InstanceName), Bytes=sum(_BilledSize) by PerfCounter
| extend Billed_MBytes = Bytes / (1024 * 1024), BytesPerRecord = Bytes / Records
| sort by Records desc
```

>[!NOTE]
>템플릿의 자리 표시자 값을 사용자 환경에서 사용 하는 값으로 대체 해야 합니다. 그렇지 않으면 쿼리가 작동 하지 않습니다.

이 쿼리는 환경에서 사용 하도록 설정한 모든 성능 카운터를 표시 합니다. Windows 가상 데스크톱의 Azure Monitor에 대 한 기본값 뿐입니다. 이 정보는 카운터의 빈도 감소 또는 완전히 제거와 같이 비용을 줄이기 위해 대상 영역을 파악 하는 데 도움이 될 수 있습니다.

성능 카운터를 제거 하 여 비용을 줄일 수도 있습니다. 성능 카운터를 제거 하거나 빈도를 줄이기 위해 기존 카운터를 편집 하는 방법을 알아보려면 [성능 카운터 구성](../azure-monitor/platform/data-sources-performance-counters.md#configuring-performance-counters)을 참조 하세요.

### <a name="manage-windows-event-logs"></a>Windows 이벤트 로그 관리

Windows 이벤트는 모든 호스트가 정상 상태 이면 데이터 수집에 스파이크가 발생할 가능성이 낮습니다. 비정상 호스트는 로그로 전송 되는 이벤트 수를 늘릴 수 있지만 호스트의 문제를 해결 하는 데 중요 한 정보를 사용할 수 있습니다. 유지 하는 것이 좋습니다. Windows 이벤트 로그를 관리 하는 방법에 대 한 자세한 내용은 [Windows 이벤트 로그 구성](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)을 참조 하세요.

### <a name="manage-diagnostics"></a>진단 관리

Windows 가상 데스크톱 진단은 데이터 저장소 비용의 1%를 차지 하므로 제거 하지 않는 것이 좋습니다. Windows 가상 데스크톱 진단을 관리 하려면 [진단 기능에 Log Analytics를 사용](diagnostics-log-analytics.md)합니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 Windows 가상 데스크톱에 대 한 Azure Monitor에 대해 자세히 알아보세요.

- [Windows 가상 데스크톱에 대 한 Azure Monitor를 사용 하 여 배포를 모니터링할 수](azure-monitor.md)있습니다.
- 용어 및 개념에 대 한 자세한 내용을 보려면 [용어집](azure-monitor-glossary.md) 을 사용 하세요.
- 문제가 발생 하는 경우 [문제 해결 가이드](troubleshoot-azure-monitor.md) 를 참조 하세요.
- 모니터링 비용을 관리 하는 방법에 대 한 자세한 내용은 [Azure Monitor의 모니터링 사용 및 예상 비용](../azure-monitor/usage-estimated-costs.md) 을 확인 하세요.
