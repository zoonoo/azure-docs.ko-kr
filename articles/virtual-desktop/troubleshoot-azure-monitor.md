---
title: 모니터 문제 해결 Windows 가상 데스크톱 미리 보기-Azure
description: Windows 가상 데스크톱에 대 한 Azure Monitor 문제를 해결 하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1818dc558ba45e318b71e1443556cc48feaede8b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367676"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Windows 가상 데스크톱 (미리 보기)에 대 한 Azure Monitor 문제 해결

>[!IMPORTANT]
>Windows 가상 데스크톱에 대 한 Azure Monitor는 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Windows 가상 데스크톱 (미리 보기)에 대 한 Azure Monitor의 일반적인 문제에 대 한 알려진 문제 및 해결 방법을 제공 합니다.

## <a name="issues-with-configuration-and-setup"></a>구성 및 설정 문제

구성 통합 문서가 제대로 작동 하지 않아 설치를 자동화 하는 경우 다음 리소스를 사용 하 여 환경을 수동으로 설정할 수 있습니다.

- 진단을 수동으로 사용 하도록 설정 하거나 Log Analytics 작업 영역에 액세스 하려면 [Windows 가상 데스크톱 진단을 Log Analytics에 보내기](diagnostics-log-analytics.md)를 참조 하십시오.
- 호스트에 Log Analytics 확장을 수동으로 설치 하려면 [Windows 용 가상 컴퓨터 확장 Log Analytics](../virtual-machines/extensions/oms-windows.md)을 참조 하세요.
- 새 Log Analytics 작업 영역을 설정 하려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-create-workspace.md)를 참조 하세요.
- 성능 카운터를 추가 하거나 제거 하려면 [성능 카운터 구성](../azure-monitor/platform/data-sources-performance-counters.md)을 참조 하세요.
- Log Analytics 작업 영역에 대 한 이벤트를 구성 하려면 [Log Analytics 에이전트를 사용 하 여 Windows 이벤트 로그 데이터 원본 수집](../azure-monitor/platform/data-sources-windows-events.md)을 참조 하세요.

## <a name="my-data-isnt-displaying-properly"></a>데이터가 제대로 표시 되지 않습니다.

데이터가 제대로 표시 되지 않으면 구성, 사용 권한을 확인 하 고 필요한 IP 주소 차단이 해제 되었는지 확인 합니다. 

- 먼저 [Windows 가상 데스크톱에 대 한 Azure Monitor를 사용 하 여 배포를 모니터링 하는 방법](azure-monitor.md)에 설명 된 대로 구성 통합 문서의 모든 필드를 입력 했는지 확인 합니다. 카운터 또는 이벤트가 없는 경우 연결 된 데이터는 Azure Portal에 표시 되지 않습니다.

- 액세스 권한을 확인 & 리소스 소유자에 게 문의 하 여 누락 된 사용 권한을 요청 합니다. Windows 가상 데스크톱을 모니터링 하는 모든 사용자에 게는 다음 권한이 필요 합니다.

    - Windows 가상 데스크톱 리소스를 보유 하는 Azure 구독에 대 한 읽기 액세스
    - Windows 가상 데스크톱 세션 호스트를 보유 하는 구독의 리소스 그룹에 대 한 읽기 액세스 
    - Log Analytics 작업 영역에 대 한 읽기 액세스

- Azure Monitor 포털에 데이터를 보낼 수 있도록 서버 방화벽에서 나가는 포트를 열어야 할 수 있습니다. [나가는 포트](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)를 참조 하세요. 

- 최근 활동의 데이터가 표시 되지 않나요? 15 분 동안 기다렸다가 피드를 새로 고칠 수 있습니다. Azure Monitor는 로그 데이터를 채우는 데 15 분의 대기 시간이 있습니다. 자세히 알아보려면 [Azure Monitor의 로그 데이터 수집 시간](../azure-monitor/platform/data-ingestion-time.md)을 참조 하세요.

정보가 누락 되지 않았지만 데이터가 여전히 제대로 표시 되지 않는 경우 쿼리 또는 데이터 원본에 문제가 있을 수 있습니다. 알려진 문제 및 제한 사항을 검토 합니다. 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Windows 가상 데스크톱에 대 한 Azure Monitor를 사용자 지정 합니다.

Windows 가상 데스크톱에 대 한 Azure Monitor Azure Monitor 통합 문서를 사용 합니다. 통합 문서를 사용 하면 Windows 가상 데스크톱 통합 문서 템플릿의 복사본을 저장 하 고 사용자 지정을 직접 만들 수 있습니다.

기본적으로 사용자 지정 통합 문서 템플릿은 제품 그룹의 업데이트를 자동으로 도입 하지 않습니다. 자세한 내용은 [통합 문서 기반 정보](../azure-monitor/insights/troubleshoot-workbooks.md) 및 통합 문서 [개요](../azure-monitor/platform/workbooks-overview.md)문제 해결을 참조 하세요.

## <a name="i-cant-interpret-the-data"></a>데이터를 해석할 수 없습니다.

[Windows 가상 데스크톱 용어집에 대 한 Azure Monitor](azure-monitor-glossary.md)에서 데이터 용어에 대해 자세히 알아보세요.

## <a name="the-data-i-need-isnt-available"></a>필요한 데이터를 사용할 수 없습니다.

더 많은 성능 카운터 또는 이벤트를 모니터링 하려는 경우 Log Analytics 작업 영역으로 전송 하 고 호스트 진단: 호스트 브라우저에서 모니터링할 수 있습니다. 

- 성능 카운터를 추가 하려면 [성능 카운터 구성](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#configuring-performance-counters) 을 참조 하세요.
- Windows 이벤트를 추가 하려면 [Windows 이벤트 로그 구성](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events#configuring-windows-event-logs) 을 참조 하세요.

문제를 진단 하는 데 도움이 되는 데이터 요소를 찾을 수 없습니까? 의견을 보내 주세요.

- 피드백을 유지 하는 방법에 [대 한 자세한 내용은 Windows 가상 데스크톱에 대 한 문제 해결 개요, 사용자 의견 및 지원](troubleshoot-set-up-overview.md)을 참조 하세요.
- Windows 가상 데스크톱 [피드백 허브](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) 또는 [UserVoice 포럼](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)에서 windows 가상 데스크톱에 대 한 피드백을 남길 수도 있습니다.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

다음은 현재 인식 하 고 해결 하는 데 사용할 수 있는 문제 및 제한 사항입니다.

- 한 번에 하나의 호스트 풀만 모니터링할 수 있습니다. 

- 즐겨 사용 하는 설정을 저장 하려면 통합 문서의 사용자 지정 템플릿을 저장 해야 합니다. 사용자 지정 템플릿은 제품 그룹의 업데이트를 자동으로 도입 하지 않습니다.

- 일부 오류 메시지는 사용자에 게 친숙 한 방법으로 구를 장르 되지 않으며, 일부 오류 메시지는 설명서에 설명 되어 있지 않습니다.

- Total sessions 성능 카운터는 적은 수의 세션을 초과 하 여 개수를 초과할 수 있으며, 총 세션 수는 최대 세션 한도를 초과 하는 것 처럼 보일 수 있습니다.

- 사용 가능한 세션 수는 호스트 풀에서 크기 조정 정책을 반영 하지 않습니다. 
    
- 드문 경우 지만 연결의 완료 이벤트가 누락 될 수 있으며이로 인해 시간이 지남에 따른 연결과 사용자의 연결 상태와 같은 일부 시각적 개체에 영향을 줄 수 있습니다.  
    
- 구성 통합 문서는 리소스 그룹과 동일한 지역 내에 있는 호스트 구성만 지원 합니다. 

- 연결할 때 사용자가 자격 증명을 입력 하는 데 걸리는 시간이 포함 됩니다. 이는 환경과 관련이 있지만 경우에 따라 잘못 된 최대 수를 표시할 수 있습니다. 
    

## <a name="next-steps"></a>다음 단계

데이터를 해석 하는 방법을 잘 모르겠으면 일반적인 용어에 대 한 자세한 내용을 보려면 [Windows 가상 데스크톱 용어집에 대 한 Azure Monitor](azure-monitor-glossary.md)를 확인 하세요. Windows 가상 데스크톱에 대 한 Azure Monitor를 설정 하 고 사용 하는 방법을 알아보려면 [Windows 가상 데스크톱에 대 한 Azure Monitor를 사용 하 여 배포 모니터링](azure-monitor.md)을 참조 하세요.