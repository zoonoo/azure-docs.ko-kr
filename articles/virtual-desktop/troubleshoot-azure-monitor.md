---
title: Azure Virtual Desktop용 Monitor 문제 해결 - Azure
description: Azure Virtual Desktop용 Azure Monitor 문제를 해결하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 27234cab8ebd6beefdd3766b3dca234ba5b41515
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111755360"
---
# <a name="troubleshoot-azure-monitor-for-azure-virtual-desktop"></a>Azure Virtual Desktop용 Azure Monitor 문제 해결

이 문서에서는 Azure Virtual Desktop용 Azure Monitor의 알려진 문제와 일반적인 문제에 대한 해결 방법을 제공합니다.

## <a name="issues-with-configuration-and-setup"></a>구성 및 설정 관련 이슈

구성 통합 문서가 설치 자동화를 위해 적절히 작동하지 않는 경우 다음 리소스를 사용하여 환경을 수동으로 설정할 수 있습니다.

- 진단을 수동으로 사용하도록 설정하거나 Log Analytics 작업 영역에 액세스하려면 [Log Analytics에 Azure Virtual Desktop 진단 보내기](diagnostics-log-analytics.md)를 참조하세요.
- 세션 호스트에 Log Analytics 확장을 수동으로 설치하려면 [Windows용 Log Analytics 가상 머신 확장](../virtual-machines/extensions/oms-windows.md)을 참조하세요.
- 새 Log Analytics 작업 영역을 설정하려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)를 참조하세요.
- 성능 카운터를 추가, 제거 또는 편집하려면 [성능 카운터 구성](../azure-monitor/agents/data-sources-performance-counters.md)을 참조하세요.
- Log Analytics 작업 영역에 대한 Windows 이벤트 로그를 구성하려면 [Log Analytics 에이전트를 사용하여 Windows 이벤트 로그 데이터 원본 수집](../azure-monitor/agents/data-sources-windows-events.md)을 참조하세요.

## <a name="my-data-isnt-displaying-properly"></a>데이터가 제대로 표시되지 않음

데이터가 제대로 표시되지 않는 경우 다음과 같은 일반적인 해결 방법을 확인합니다.

- 먼저 [Azure Virtual Desktop용 Azure Monitor를 사용하여 배포 모니터링](azure-monitor.md)에 설명된 대로 구성 통합 문서로 올바르게 설정했는지 확인합니다. 카운터 또는 이벤트가 누락된 경우 연결된 데이터가 Azure Portal에 표시되지 않습니다.
- 액세스 권한을 확인하거나 리소스 소유자에게 누락된 권한을 요청하세요. Azure Virtual Desktop을 모니터링하는 모든 사용자는 다음 권한이 필요합니다.
    - Azure Virtual Desktop 리소스를 보유하는 Azure 구독에 대한 읽기 액세스 권한
    - Azure Virtual Desktop 세션 호스트를 보유하는 구독의 리소스 그룹에 대한 읽기 액세스 권한 
    - 사용 중인 Log Analytics 작업 영역에 대한 읽기 액세스 권한
- Azure Monitor 및 Log Analytics에서 Portal로 데이터를 보낼 수 있도록 하려면 서버 방화벽에서 나가는 포트를 열어야 할 수도 있습니다. 이 작업을 수행하는 방법을 알아보려면 다음 문서를 참조하세요.
      - [Azure Monitor 나가는 포트](../azure-monitor/app/ip-addresses.md)
      - [Log Analytics 방화벽 요구 사항](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements) 
- 최근 활동의 데이터가 표시되지 않나요? 15분 동안 기다렸다가 피드를 새로 고칠 수 있습니다. Azure Monitor에서는 로그 데이터를 채우기 위해 15분의 대기 시간이 발생합니다. 자세한 내용은 [Azure Monitor 로그 데이터 수집 시간](../azure-monitor/logs/data-ingestion-time.md)을 참조하세요.

정보가 누락되지 않았지만 데이터가 제대로 표시되지 않는 경우 쿼리 또는 데이터 원본에 문제가 있을 수 있습니다. [알려진 이슈 및 제한 사항](#known-issues-and-limitations)을 검토합니다. 

## <a name="i-want-to-customize-azure-monitor-for-azure-virtual-desktop"></a>Azure Virtual Desktop용 Azure Monitor를 사용자 지정하려고 합니다.

Azure Virtual Desktop용 Azure Monitor는 Azure Monitor 통합 문서를 사용합니다. 통합 문서를 사용하면 Azure Virtual Desktop 통합 문서 템플릿의 복사본을 저장하고 나만의 사용자 지정을 만들 수 있습니다.

기본적으로 사용자 지정 통합 문서 템플릿은 제품 그룹의 업데이트를 자동으로 채택하지 않습니다. 자세한 내용은 [통합 문서 기반 인사이트 문제 해결](../azure-monitor/insights/troubleshoot-workbooks.md) 및 [통합 문서 개요](../azure-monitor/visualize/workbooks-overview.md)를 참조하세요.

## <a name="i-cant-interpret-the-data"></a>데이터를 해석할 수 없음

[Window Virtual Desktop용 Azure Monitor 용어집](azure-monitor-glossary.md)에서 데이터 용어에 대해 자세히 알아보세요.

## <a name="the-data-i-need-isnt-available"></a>필요한 데이터를 사용할 수 없음

더 많은 성능 카운터 또는 Windows 이벤트 로그를 모니터링하려는 경우 Log Analytics 작업 영역에 진단 정보를 보내고 **호스트 진단: 호스트 브라우저** 에서 모니터링하도록 설정할 수 있습니다. 

- 성능 카운터를 추가하려면 [성능 카운터 구성](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)을 참조하세요.
- Windows 이벤트를 추가하려면 [Windows 이벤트 로그 구성](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)을 참조하세요.

문제를 진단하는 데 도움이 되는 데이터 요소를 찾을 수 없나요? 피드백 보내주세요.

- 피드백을 남기는 방법을 알아보려면 [Azure Virtual Desktop에 대한 문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)을 참조하세요.
- [Azure Virtual Desktop 피드백 허브](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)에서 Azure Virtual Desktop에 대한 피드백을 남길 수도 있습니다.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

다음은 인식하고 있으며 해결하기 위해 작업 중인 이슈 및 제한 사항입니다.

- 한 번에 하나의 호스트 풀만 모니터링할 수 있습니다. 
- 즐겨찾는 설정을 저장하려면 통합 문서의 사용자 지정 템플릿을 저장해야 합니다. 사용자 지정 템플릿은 제품 그룹의 업데이트를 자동으로 채택하지 않습니다.
- 경우에 따라 선택 항목을 로드할 때 구성 통합 문서에 "쿼리 실패" 오류가 표시됩니다. 쿼리를 새로 고치고 필요한 경우 선택 항목을 다시 입력하면 오류가 자체적으로 해결됩니다. 
- 일부 오류 메시지는 사용자에게 친숙한 방식으로 표현되지 않으며 설명서에는 일부 오류 메시지만 설명되어 있습니다.
- 총 세션 성능 카운터는 세션 수를 실제보다 약간 더 높게 계산할 수 있으며 총 세션 수는 최대 세션 제한을 초과하는 것처럼 보일 수 있습니다.
- 사용 가능한 세션 수는 호스트 풀의 크기 조정 정책을 반영하지 않습니다.   
- 모순되거나 예기치 않은 연결 시간이 있나요? 드물긴 하지만 연결의 완료 이벤트가 누락될 수 있으며 일부 시각적 개체 및 메트릭에 영향을 줄 수 있습니다.
- 연결 시간에는 사용자가 자격 증명을 입력하는 데 걸리는 시간이 포함됩니다. 이 시간은 환경과 상관 관계가 있지만 경우에 따라 잘못된 최대치를 표시할 수 있습니다. 
    

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Virtual Desktop용 Azure Monitor을 사용하여 배포 모니터링](azure-monitor.md)을 참조하세요.
- 데이터 스토리지 비용을 예측, 측정 및 관리하려면 [예상 Azure Monitor 비용](azure-monitor-costs.md)을 참조하세요.
- Azure Virtual Desktop용 Azure Monitor과 관련된 용어 및 개념에 대해 자세히 알아보려면 [용어집](azure-monitor-glossary.md)을 확인하세요.
