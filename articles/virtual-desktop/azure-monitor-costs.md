---
title: Azure Virtual Desktop 비용 가격 책정 예측 모니터링 - Azure
description: Azure Monitor for Azure Virtual Desktop 사용에 대한 비용 및 가격 책정을 예측하는 방법입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 9011e22359e7df72f1754ec4f588f41e5342e868
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745334"
---
# <a name="estimate-azure-monitor-costs"></a>Azure Monitor 비용 예측

Azure Monitor 로그는 사용자 환경에서 생성된 데이터를 수집, 인덱싱 및 저장하는 서비스입니다. 이에 따라 Azure Monitor 가격 책정 모델은 Log Analytics 작업 영역에서 매일 기가바이트 단위로 가져오고 처리(또는 "수집")하는 데이터 양을 기반으로 합니다. Log Analytics 작업 영역의 비용은 수집된 데이터 볼륨 외에도 선택한 Azure 결제 플랜 및 환경에서 생성되는 데이터를 저장하도록 선택한 기간을 기반으로 합니다.

이 문서에서는 Azure Monitor의 가격 책정이 작동하는 방식을 이해하는 데 도움이 되는 다음 항목에 대해 설명합니다.

- 이 기능을 사용하도록 설정하기 전에 데이터 수집 및 스토리지 비용을 미리 예측하는 방법
- 이 기능을 사용하는 경우 비용을 줄이기 위해 데이터 수집 및 스토리지를 측정하고 제어하는 방법

>[!NOTE]
> 이 문서에 나와 있는 모든 크기와 가격 책정은 예측의 작동 방식을 보여 주는 예일 뿐입니다. Azure Monitor Log Analytics 가격 책정 모델 및 Azure 지역에 기반한 더 정확한 평가는 [Azure Monitor 가격](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요.

## <a name="estimate-data-ingestion-and-storage-costs"></a>데이터 수집 및 스토리지 비용 예측

Log Analytics 작업 영역에서 로그로 작성된 미리 정의된 데이터 세트를 사용하는 것이 좋습니다. 다음 예측 예에서는 기본 구성의 청구 가능한 데이터를 살펴보겠습니다.

Azure Monitor for Azure Virtual Desktop에 대해 미리 정의된 데이터 세트는 다음과 같습니다.

- 세션 호스트의 성능 카운터
- 세션 호스트의 Windows 이벤트 로그
- 서비스 인프라의 Azure Virtual Desktop 진단

데이터 수집 및 스토리지 비용은 환경 크기, 상태 및 사용량에 따라 달라집니다. 이 문서에서 예상할 수 있는 비용 범위를 계산하는 데 사용하는 예측 예는 전력 사용량이 적은 정상적인 가상 머신을 기반으로 합니다. 즉, [가상 머신 크기 조정 지침](/windows-server/remote/remote-desktop-services/virtual-machine-recs)에 따라 예상할 수 있는 데이터 수집 및 스토리지 비용의 범위를 계산합니다.

이 예에서 사용하는 전력 사용량이 적은 VM에 포함된 구성 요소는 다음과 같습니다.

- 4개 vCPU, 1개 디스크
- 16개 세션/일
- 2시간(120분) 평균 세션 지속 시간
- 100개 프로세스/세션

이 예에서 사용하는 전력 사용량이 많은 VM에 포함된 구성 요소는 다음과 같습니다.

- 6개 vCPU, 1개 디스크
- 6개 세션/일
- 4시간(240분) 평균 세션 지속 시간
- 200개 프로세스/세션

## <a name="estimating-performance-counter-ingestion"></a>성능 카운터 수집 예측

성능 카운터는 시스템 리소스에서 수행하는 성능을 보여 줍니다. 성능 카운터 데이터 수집은 환경의 크기 및 사용량에 따라 달라집니다. 대부분의 경우 성능 카운터는 Azure Monitor for Azure Virtual Desktop에 대한 데이터 수집의 80~99%를 구성해야 합니다.

예측을 시작하기 전에 각 성능 카운터에서 데이터를 특정 빈도로 보낸다는 것을 이해하는 것이 중요합니다. 분당 기본 샘플 속도를 설정했지만(이 속도는 설정에서 편집할 수도 있음), 해당 속도는 카운터에 따라 서로 다른 곱셈 인수로 적용됩니다. 속도에 영향을 주는 인수는 다음과 같습니다.

- VM(가상 머신)당 인수의 경우 VM이 실행되는 동안 각 카운터에서 사용자 환경의 VM당 데이터를 분당 기본 샘플 속도로 보냅니다. 이러한 카운터에서 하루에 보내는 레코드 수는 분당 기본 샘플 속도와 사용자 환경의 VM 수를 곱한 다음, 해당 숫자와 일당 평균 VM 실행 시간을 곱하여 예측할 수 있습니다.

   요약:

   기본 샘플 속도/분 × VM SKU의 CPU 코어 수 × VM 수 × 평균 VM 실행 시간/일 = 보내는 레코드 수/일

- CPU당 인수의 경우 VM이 실행되는 동안 각 카운터에서 사용자 환경에 있는 각 VM의 vCPU당 분당 기본 샘플 속도로 보냅니다. 카운터에서 하루에 보내는 레코드 수는 분당 기본 샘플 속도와 VM SKU의 CPU 코어 수를 곱한 다음, 해당 숫자와 VM이 실행되는 시간(분) 및 사용자 환경의 VM 수를 곱하여 예측할 수 있습니다.

   요약:
   
   기본 샘플 속도/분 × VM SKU의 CPU 코어 수 × VM 실행 시간(분) × VM 수 = 보내는 레코드 수/일

- 디스크당 인수의 경우 각 카운터에서 데이터를 사용자 환경에 있는 각 VM의 디스크당 기본 샘플 속도로 보냅니다. 이러한 카운터에서 하루에 보내는 레코드 수는 분당 기본 샘플 속도와 VM SKU의 디스크 수를 곱하고, 시간당 60분을 곱하고, 마지막으로 VM의 평균 활성 시간을 곱한 값과 같습니다.

   요약:

   기본 샘플 속도/분 × VM SKU의 디스크 수 × 60분/시간 × VM 수 × 평균 VM 실행 시간/일 = 보내는 레코드 수/일

- 세션당 인수의 경우 세션이 연결되어 있는 동안 각 카운터에서 데이터를 사용자 환경의 각 세션에 대한 기본 샘플 속도로 보냅니다. 이러한 카운터에서 하루에 보내는 레코드 수는 분당 기본 샘플 속도와 일당 평균 세션 수 및 평균 세션 기간을 곱하여 예측할 수 있습니다.

   요약:

   기본 샘플 속도/분 × 세션 수/일 × 평균 세션 기간 = 보내는 레코드 수/일

- 프로세스당 인수의 경우 각 카운터에서 데이터를 사용자 환경에 있는 각 세션의 각 프로세스에 대한 기본 속도로 보냅니다. 이러한 카운터에서 하루에 보내는 레코드 수는 분당 기본 샘플 속도와 일당 평균 세션 수를 곱한 다음, 이 값과 평균 세션 기간 및 세션당 평균 프로세스 수를 곱하여 예측할 수 있습니다.
  
   요약:

   기본 샘플 속도/분 × 세션 수/일 × 평균 세션 기간 × 평균 프로세스 수/세션 = 보내는 레코드 수/일

다음 표에는 Azure Monitor for Azure Virtual Desktop에서 수집하는 20개의 성능 카운터 및 해당 기본 속도가 나와 있습니다.

| 카운터 이름 | 기본 샘플 속도 | 빈도 인수 |
|--------------|---------------------|------------------|
| 논리 디스크(C:)\\% 사용 가능한 공간 | 60초  | 디스크당             |
| 논리 디스크(C:)\\평균 디스크 큐 길이   | 30초    | 디스크당             |
| 논리 디스크(C:)\\평균 디스크 초/전송  | 60초       | 디스크당             |
| 논리 디스크(C:)\\현재 디스크 큐 길이  | 30초      | 디스크당             |
| 메모리(\*)\\사용 가능한 바이트 수 | 30초    | VM당  |
| 메모리(\*)\\페이지 폴트 수/초 | 30초   | VM당  |
| 메모리(\*)\\페이지 수/초 | 30초   | VM당  |
| 메모리(\*)\\% 사용 중인 커밋된 바이트 수 | 30초    | VM당  |
| 실제 디스크(\*)\\평균 디스크 큐 길이 | 30초      | 디스크당             |
| 실제 디스크(\*)\\평균 디스크 초/읽기 | 30초  | 디스크당             |
| 실제 디스크(\*)\\평균 디스크 초/전송 | 30초  | 디스크당             |
| 실제 디스크(\*)\\평균 디스크 초/쓰기 | 30초 | 디스크당             |
| 프로세서 정보(_Total)\\% 프로세서 시간 | 30초 | 코어/CPU당         |
| 터미널 서비스(\*)\\활성 세션 수          | 60초 | VM당  |
| 터미널 서비스(\*)\\비활성 세션 수        | 60초 | VM당  |
| 터미널 서비스(\*)\\총 세션 수 | 60초 | VM당  |
| 프로세스당 사용자 입력 지연(\*)\\최대 입력 지연         | 30초 | 프로세스당          |
| 세션당 사용자 입력 지연(\*)\\최대 입력 지연        | 30초 | 세션당          |
| RemoteFX 네트워크(\*)\\현재 TCP RTT | 30초 | VM당  |
| RemoteFX 네트워크(\*)\\현재 UDP 대역폭     | 30초 | VM당  |

각 레코드 크기를 200바이트로 예상하는 경우 간단한 워크로드를 기본 샘플 속도로 실행하는 예제 VM은 VM당 일당 약 90메가바이트의 성능 카운터 데이터를 보냅니다. 한편 전원 공급 워크로드를 실행하는 예제 VM은 VM당 일당 약 130메가바이트의 성능 카운터 데이터를 보냅니다. 그러나 레코드 크기와 환경 사용량이 다를 수 있으므로 배포에서 사용하는 일당 메가바이트 수가 다를 수 있습니다.

입력 지연 성능 카운터에 대한 자세한 내용은 [사용자 입력 지연 성능 카운터](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)를 참조하세요.

## <a name="estimating-windows-event-log-ingestion"></a>Windows 이벤트 로그 수집 예측

Windows 이벤트 로그는 Windows 가상 머신의 Log Analytics 에이전트에서 수집하는 데이터 원본입니다. 이벤트는 시스템 및 애플리케이션과 같은 표준 로그뿐만 아니라 모니터링해야 하는 애플리케이션에서 만든 사용자 지정 로그에서도 수집할 수 있습니다.

Azure Monitor for Azure Virtual Desktop에 대한 기본 Windows 이벤트는 다음과 같습니다.

- 애플리케이션
- Microsoft-Windows-TerminalServices-RemoteConnectionManager/Admin
- Microsoft-Windows-TerminalServices-LocalSessionManager/Operational
- 시스템
- Microsoft-FSLogix-Apps/Operational
- Microsoft-FSLogix-Apps/Admin

환경에서 이벤트 조건이 충족될 때마다 Windows 이벤트에서 보냅니다. 정상 상태의 컴퓨터는 비정상 상태의 컴퓨터보다 더 적은 이벤트를 보냅니다. 이벤트 수는 예측할 수 없으므로 이 예측 값에 대한 정상 환경의 예를 기준으로 VM당 일당 1,000~10,000개의 이벤트 범위를 사용합니다. 예를 들어 이 예에서 각 이벤트 레코드 크기를 1,500바이트로 예상하는 경우 지정된 환경에 대해 일당 약 2~15메가바이트의 이벤트 데이터가 제공됩니다.

Windows 이벤트에 대한 자세한 내용은 [Windows 이벤트 레코드 속성](../azure-monitor/agents/data-sources-windows-events.md)을 참조하세요.

## <a name="estimating-diagnostics-ingestion"></a>진단 수집 예측

진단 서비스는 사용자 및 관리 작업 모두에 대한 활동 로그를 만듭니다.

진단 카운터에서 추적하는 활동 로그의 이름은 다음과 같습니다.

- WVDCheckpoints
- WVDConnections
- WVDErrors
- WVDFeeds
- WVDManagement
- WVDAgentHealthStatus

이 서비스는 환경에서 레코드를 만드는 데 필요한 조건을 충족할 때마다 진단 정보를 보냅니다. 진단 레코드 수는 예측할 수 없으므로 이 예측 값에 대한 정상 환경의 예를 기준으로 VM당 일당 500~1,000개의 이벤트 범위를 사용합니다.

예를 들어 이 예에서 각 진단 레코드 크기를 200바이트로 예상하는 경우 수집되는 총 데이터는 VM당 일당 1MB 미만입니다.

활동 로그 범주에 대한 자세한 내용은 [Azure Virtual Desktop 진단](diagnostics-log-analytics.md) 참조.

## <a name="estimating-total-costs"></a>총 비용 예측

마지막으로 총 비용을 예측해 보겠습니다. 이 예에서는 이전 섹션의 예 값을 기반으로 하여 다음과 같은 결과를 산출한다고 가정해 보겠습니다.

| 데이터 원본        | 일당 예측 크기(메가바이트)   |
|-------------------------------------|------------------------------------------|
| 성능 카운터   | 90~130 |
| 이벤트    | 2-15 |
| Azure Virtual Desktop 진단 | \< 1 |

이 예에서 Azure Monitor for Azure Virtual Desktop에 대해 수집된 총 데이터는 VM당 일당 92~145MB입니다. 즉, 각 VM은 31일마다 약 3~5GB의 데이터를 수집합니다.

[Log Analytics 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)의 기본 종량제 모델을 사용하면 Azure Monitor 데이터 수집 및 스토리지 비용을 매월 예측할 수 있습니다. 데이터 수집에 따라 Log Analytics 가격 책정의 용량 예약 모델도 고려할 수 있습니다.

## <a name="manage-your-data-ingestion-to-reduce-costs"></a>데이터 수집을 관리하여 비용 절감

이 섹션에서는 비용을 줄이기 위해 데이터 수집을 측정하고 관리하는 방법에 대해 설명합니다.

통합 문서에 대한 권한 및 사용 권한을 관리하는 방법에 대한 자세한 내용은 [액세스 제어](../azure-monitor/visualize/workbooks-access-control.md)를 참조하세요.

>[!NOTE]
>데이터 요소를 제거하면 Azure Monitor for Azure Virtual Desktop의 해당 시각적 개체에 영향을 줍니다.

### <a name="log-analytics-settings"></a>Log Analytics 설정

Log Analytics 설정을 최적화하여 데이터 수집을 관리하기 위한 몇 가지 제안 사항은 다음과 같습니다.

- Azure Virtual Desktop 리소스에 대해 지정된 Log Analytics 작업 영역을 사용하여 Log Analytics에서 Azure Virtual Desktop 배포의 가상 머신에 대한 성능 카운터 및 이벤트만 수집하도록 합니다.
- Log Analytics 스토리지 설정을 조정하여 비용을 관리합니다. 보존 기간을 줄이거나, 고정 스토리지 가격 책정 계층이 더 비용 효율적인지 여부를 평가하거나, 비정상 배포의 영향을 제한하기 위해 수집할 수 있는 데이터 양에 대한 경계를 설정할 수 있습니다. 자세한 내용은 [Azure Monitor 로그의 사용량 및 비용 관리](../azure-monitor/logs/manage-cost-storage.md)를 참조하세요.

### <a name="remove-excess-data"></a>초과 데이터 제거

기본 구성은 Azure Monitor for Azure Virtual Desktop에 권장되는 유일한 데이터 세트입니다. 항상 추가 데이터 요소를 추가하고 호스트 진단: 호스트 브라우저에서 보거나 사용자 지정 차트를 작성할 수 있지만, 데이터가 추가되면 Log Analytics 비용이 증가합니다. 이러한 데이터는 비용을 절감하기 위해 제거할 수 있습니다.

### <a name="measure-and-manage-your-performance-counter-data"></a>성능 카운터 데이터 측정 및 관리

실제 모니터링 비용은 환경 크기, 사용량 및 상태에 따라 달라집니다. Log Analytics 작업 영역에서 데이터 수집을 측정하는 방법을 이해하려면 [수집된 로그 데이터 볼륨 파악](../azure-monitor/logs/manage-cost-storage.md#understanding-ingested-data-volume)을 참조하세요.

세션 호스트에서 사용하는 성능 카운터는 Azure Monitor for Azure Virtual Desktop에 대해 수집된 데이터의 가장 큰 원본일 수 있습니다. Log Analytics 작업 영역에 대한 다음 사용자 지정 쿼리 템플릿은 마지막 하루 동안 수집된 성능 카운터당 빈도 및 메가바이트를 추적할 수 있습니다.

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
>템플릿의 자리 표시자 값을 환경에서 사용하는 값으로 바꿔야 합니다. 그렇지 않으면 쿼리가 작동하지 않습니다.

이 쿼리는 Azure Monitor for Azure Virtual Desktop에 대한 기본 카운터뿐만 아니라 환경에서 사용하도록 설정한 모든 성능 카운터를 표시합니다. 이 정보는 카운터의 빈도를 줄이거나 카운터를 완전히 제거하는 것처럼 비용을 절감하기 위해 대상으로 지정할 영역을 파악하는 데 도움이 됩니다.

또한 성능 카운터를 제거하여 비용을 절감할 수도 있습니다. 성능 카운터를 제거하거나 빈도를 줄이도록 기존 카운터를 편집하는 방법에 대한 자세한 내용은 [성능 카운터 구성](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)을 참조하세요.

### <a name="manage-windows-event-logs"></a>Windows 이벤트 로그 관리

모든 호스트가 정상 상태인 경우 Windows 이벤트에서 데이터 수집이 급증할 가능성이 낮습니다. 비정상 호스트는 로그에 보내는 이벤트 수를 늘릴 수 있지만 이 정보는 호스트의 문제를 해결하는 데 중요할 수 있습니다. 유지하는 것이 좋습니다. Windows 이벤트 로그를 관리하는 방법에 대한 자세한 내용은 [Windows 이벤트 로그 구성](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)을 참조하세요.

### <a name="manage-diagnostics"></a>진단 관리

Azure Virtual Desktop 진단은 데이터 스토리지 비용의 1% 미만을 차지하므로 제거하지 않는 것이 좋습니다. Azure Virtual Desktop 진단을 관리하려면 [Log Analytics를 진단 기능에 사용하세요](diagnostics-log-analytics.md).

## <a name="next-steps"></a>다음 단계

다음 문서에서 Azure Monitor for Azure Virtual Desktop에 대해 자세히 알아보세요.

- [Azure Monitor for Azure Virtual Desktop을 사용하여 배포를 모니터링](azure-monitor.md)합니다.
- [용어집](azure-monitor-glossary.md)을 사용하여 용어와 개념에 대해 자세히 알아봅니다.
- 문제가 발생하면 [문제 해결 가이드](troubleshoot-azure-monitor.md)를 참조하여 도움을 받습니다.
- [Azure Monitor에서 사용량 및 예상 비용 모니터링](../azure-monitor/usage-estimated-costs.md)을 확인하여 모니터링 비용을 관리하는 방법을 자세히 알아봅니다.