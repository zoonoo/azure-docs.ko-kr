---
title: Windows 가상 데스크톱 미리 보기 용어 모니터링-Azure
description: Windows 가상 데스크톱에 대 한 Azure Monitor 관련 된 용어 및 개념입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 3/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7b824bc13bc4f553d22358b69237173effb51594
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627135"
---
# <a name="azure-monitor-for-windows-virtual-desktop-preview-glossary"></a>Windows 가상 데스크톱 (미리 보기) 용어집에 대 한 Azure Monitor

>[!IMPORTANT]
>Windows 가상 데스크톱에 대 한 Azure Monitor는 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Windows 가상 데스크톱 (미리 보기)에 대 한 Azure Monitor와 관련 된 주요 용어 및 개념을 나열 하 고 간략하게 설명 합니다.

## <a name="alerts"></a>경고

구독에서 구성 하 고 [심각도 0](#severity-0-alerts) 으로 분류 한 활성 Azure Monitor 경고가 개요 페이지에 표시 됩니다. 경고를 설정 하는 방법에 대 한 자세한 내용은 [Azure Monitor 경고를 사용 하 여 이벤트에 응답](../azure-monitor/alerts/tutorial-response.md)을 참조 하세요.

## <a name="available-sessions"></a>사용 가능한 세션

사용 가능한 세션 호스트 풀에서 사용할 수 있는 세션 수를 표시 합니다. 이 서비스는 가상 머신 (Vm) 수를 가상 머신 당 허용 되는 최대 세션 수와 곱하여 총 세션을 빼서이 수를 계산 합니다.

## <a name="connection-success"></a>연결 성공

이 항목은 연결 상태를 표시 합니다. "연결 성공"은 해당 가상 컴퓨터의 스택에서 확인 된 대로 연결에서 호스트에 연결할 수 있음을 의미 합니다. 연결에 실패 하면 연결에서 호스트에 연결할 수 없음을 의미 합니다.

## <a name="daily-active-users-dau"></a>일일 활성 사용자 (6AU)

지난 24 시간 동안 세션을 시작한 총 사용자 수입니다.

## <a name="daily-alerts"></a>일별 경고

매일 트리거된 총 경고 수입니다.

## <a name="daily-connections-and-reconnections"></a>매일 연결 및 재연결

최근 24 시간 이내에 시작 되거나 완료 된 총 연결 및 횟수 수입니다.

## <a name="daily-connected-hours"></a>일별 연결 시간

최근 24 시간 동안의 사용자 간에 세션에 연결 된 총 시간입니다.

## <a name="diagnostics-and-errors"></a>진단 및 오류

Windows 가상 데스크톱에 대 한 Azure Monitor에 오류 또는 경고가 표시 되 면 다음 세 가지 항목으로 분류 됩니다.

- 활동 유형:이 범주는 Windows 가상 데스크톱 진단에서 오류를 분류 하는 방법입니다. 범주는 관리 활동, 피드, 연결, 호스트 등록, 오류 및 검사점입니다. [진단 기능을 사용 하 여 Log Analytics 사용](diagnostics-log-analytics.md)에서 이러한 범주에 대해 자세히 알아보세요.

- Kind:이 범주는 오류 위치를 표시 합니다. 

     - Windows 가상 데스크톱 서비스에서 "service" 또는 "ServiceError = TRUE"로 표시 된 오류가 발생 했습니다.
     - Windows 가상 데스크톱 서비스 외부에서 "배포" 또는 태그가 지정 된 "ServiceError = FALSE"로 표시 된 오류가 발생 했습니다.
     - ServiceError 태그에 대해 자세히 알아보려면 [일반적인 오류 시나리오](diagnostics-role-service.md#common-error-scenarios)를 참조 하세요.

- 원본:이 범주는 오류가 발생 한 위치에 대 한 보다 구체적인 설명을 제공 합니다.

     - 진단: 서비스 활동 모니터링 및 보고를 담당 하는 서비스 역할로, 사용자가 배포 문제를 관찰 하 고 진단할 수 있습니다.

     - RDBroker: 배포 작업을 오케스트레이션 하 고, 개체의 상태를 유지 관리 하 고, 인증의 유효성을 검사 하는 작업을 담당 하는 서비스 역할입니다.

     - RDGateway: 최종 사용자와 가상 컴퓨터 간의 네트워크 연결 처리를 담당 하는 서비스 역할입니다.

     - RDStack: Vm에 설치 되어 Windows 가상 데스크톱 서비스와 통신할 수 있도록 하는 소프트웨어 구성 요소입니다.

     - 클라이언트: Windows 가상 데스크톱 서비스에 대 한 인터페이스를 제공 하는 최종 사용자 컴퓨터에서 실행 되는 소프트웨어입니다. 선택한 후 게시 된 리소스 목록을 표시 하 고 원격 데스크톱 연결을 호스팅합니다.

각 진단 문제 또는 오류에는 발생 한 문제를 설명 하는 메시지가 포함 됩니다. 오류 문제 해결에 대 한 자세한 내용은 [Windows 가상 데스크톱 문제 식별 및 진단](diagnostics-role-service.md)을 참조 하세요.

## <a name="input-delay"></a>입력 지연

Windows 가상 데스크톱에 대 한 Azure Monitor의 "입력 지연"은 각 세션에 대 한 프로세스 당 입력 지연 성능 카운터를 의미 합니다. [Aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)의 호스트 성능 페이지에서이 성능 카운터는 30 초 마다 한 번씩 보고서를 서비스로 보내도록 구성 되어 있습니다. 이러한 30 초 간격을 "samples" 라고 하며, 해당 창에서 최악의 경우를 보고 합니다. 중앙값과 p95 값은 모든 샘플에서 중앙값 및 95 번째 백분위 수를 반영 합니다.

**호스트의 입력 지연** 에서 세션 호스트 행을 선택 하 여 해당 호스트에 대 한 페이지의 다른 모든 시각적 개체를 필터링 할 수 있습니다. 프로세스 이름을 선택 하 여 시간에 따른 입력 지연 시간 차트를 필터링 할 수도 있습니다.

다음 범주에 지연이 발생 합니다.

- 양호: 150 밀리초 미만.
- 허용: 150-500 밀리초.
- 나쁨: 500-2000 밀리초 (2 초 미만)
- 불량: 2000 밀리초 (2 초 이상)

입력 지연 카운터가 작동 하는 방법에 대 한 자세한 내용은 [사용자 입력 지연 성능 카운터](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)를 참조 하세요.

##  <a name="monthly-active-users-mau"></a>MAU(월간 활성 사용자)

지난 28 일 동안 세션을 시작한 총 사용자 수입니다. 30 일 이내에 데이터를 저장 하는 경우 데이터를 28 일 미만으로 사용할 수 있는 기간 동안 예상 되는 MAU 및 연결 값이 표시 될 수 있습니다.

## <a name="performance-counters"></a>성능 카운터

성능 카운터는 하드웨어 구성 요소, 운영 체제 및 응용 프로그램의 성능을 보여 줍니다.

다음 표에는 Windows 가상 데스크톱에 사용 되는 Azure Monitor 권장 성능 카운터와 시간 간격이 나열 되어 있습니다.

|성능 카운터 이름|시간 간격|
|---|---|
|논리 디스크 (C:)의 \\ 평균 디스크 큐 길이|30초|
|논리 디스크 (C: \\ Avg. Disk sec/Transfer)|60초|
|논리 디스크 (C:) \\ 현재 디스크 큐 길이|30초|
|Memory ( \* ) \\ 사용 가능한 공간 (mb)|30초|
|Memory ( \* ) \\ 페이지 폴트/초|30초|
|Memory ( \* ) \\ Pages/sec|30초|
|Memory ( \* ) \\ % 사용 중인 커밋된 바이트 수|30초|
|PhysicalDisk ( \* ) \\ Avg. Disk Queue Length|30초|
|PhysicalDisk ( \* ) \\ Avg. Disk Sec/Read|30초|
|PhysicalDisk ( \* ) \\ Avg. Disk Sec/Transfer|30초|
|PhysicalDisk ( \* ) \\ Avg. Disk Sec/Write|30초|
|프로세서 정보 (_Total) \\ % 프로세서 시간|30초|
|터미널 서비스 ( \* ) \\ 활성 세션|60초|
|터미널 서비스 ( \* ) \\ 비활성 세션|60초|
|터미널 서비스 ( \* ) \\ 총 세션|60초|
|\*프로세스 당 사용자 입력 지연 ( \* ) \\ 최대 입력 지연|30초|
|\*세션당 사용자 입력 지연 ( \* ) \\ 최대 입력 지연|30초|
|RemoteFX 네트워크 ( \* ) \\ 현재 TCP RTT|30초|
|RemoteFX 네트워크 ( \* ) \\ 현재 UDP 대역폭|30초|

성능 카운터를 읽는 방법에 대 한 자세한 내용은 [성능 카운터 구성](../azure-monitor/agents/data-sources-performance-counters.md)을 참조 하세요.

입력 지연 성능 카운터에 대 한 자세한 내용은 [사용자 입력 지연 성능 카운터](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)를 참조 하세요.

## <a name="potential-connectivity-issues"></a>잠재적 연결 문제

잠재적 연결 문제는 연결 실패율이 높은 호스트, 사용자, 게시 된 리소스 및 클라이언트를 보여 줍니다. "보고서 기준" 필터를 선택 하면 다음 열의 값을 확인 하 여 문제의 심각도를 평가할 수 있습니다.

- 시도 횟수 (연결 시도 횟수)
- 리소스 (게시 된 앱 또는 데스크톱 수)
- 호스트 (Vm 수)
- 클라이언트

예를 들어 **사용자** 필터를 선택 하는 경우 **시도** 열에서 각 사용자의 연결 시도를 확인할 수 있습니다.

여러 호스트, 사용자, 리소스 또는 클라이언트에 연결 문제가 있는 경우 전체 시스템에 문제가 있을 수 있습니다. 그렇지 않으면 낮은 우선 순위의 문제입니다.

항목을 선택 하 여 추가 정보를 볼 수도 있습니다. 문제와 관련 된 호스트, 리소스 및 클라이언트 버전을 확인할 수 있습니다. 또한 연결 시도 중에 보고 된 모든 오류가 표시 됩니다.

## <a name="round-trip-time-rtt"></a>RTT (왕복 시간)

RTT (왕복 시간)는 최종 사용자의 위치와 세션 호스트의 Azure 지역 간에 연결의 왕복 시간을 예상 하는 것입니다. 대기 시간이 가장 긴 위치를 확인 하려면 [Windows 가상 데스크톱 환경 평가기 도구](https://azure.microsoft.com/services/virtual-desktop/assessment/)에서 원하는 위치를 조회 합니다.

## <a name="session-history"></a>세션 기록

**세션** 항목은 모든 세션의 상태를 표시 하 고 연결 된 연결을 해제 합니다. **유휴 세션** 은 연결 되지 않은 세션만 표시 합니다.

## <a name="severity-0-alerts"></a>심각도 0 경고

즉시 처리 해야 하는 가장 긴급 한 항목입니다. 이러한 문제를 해결 하지 않으면 Windows 가상 데스크톱 배포의 작동이 중지 될 수 있습니다.

## <a name="time-to-connect"></a>연결 시간

연결할 때 사용자가 세션을 시작 하 고 서비스에 로그인 하는 것으로 간주 될 때 까지의 시간입니다. 새 연결을 설정 하면 기존 연결을 재설정 하는 것 보다 시간이 오래 걸립니다.

## <a name="user-report"></a>사용자 보고서

사용자 보고서 페이지에서는 특정 사용자의 연결 기록과 진단 정보를 볼 수 있습니다. 각 사용자 보고서에는 사용 패턴, 사용자 피드백 및 사용자가 세션 중에 발생 한 오류가 표시 됩니다. 사용자 의견을 통해 가장 작은 문제를 해결할 수 있습니다. 자세히 검토 해야 하는 경우 특정 연결 ID 또는 시간에 대 한 정보를 필터링 할 수도 있습니다.

## <a name="users-per-core"></a>코어 당 사용자 수

각 가상 컴퓨터 코어의 사용자 수입니다. 시간에 따른 코어 당 최대 사용자 수를 추적 하면 환경에서 코어 당 높음, 낮음 또는 변동의 사용자 수를 일관 되 게 실행할지 여부를 쉽게 파악할 수 있습니다. 활성 상태인 사용자 수를 알면 환경을 효율적으로 리소스 및 크기를 조정 하는 데 도움이 됩니다.

## <a name="windows-event-logs"></a>Windows 이벤트 로그

Windows 이벤트 로그는 Windows 가상 컴퓨터의 Log Analytics 에이전트에서 수집 하는 데이터 원본입니다. 모니터링 해야 하는 응용 프로그램에서 만든 사용자 지정 로그 뿐만 아니라 시스템 및 응용 프로그램과 같은 표준 로그에서 이벤트를 수집할 수 있습니다.

다음 표에서는 Windows 가상 데스크톱에 대 한 Azure Monitor에 필요한 Windows 이벤트 로그를 보여 줍니다.

|이벤트 이름|이벤트 유형|
|---|---|
|애플리케이션|오류 및 경고|
|Microsoft-windows-terminalservices-gateway-RemoteConnectionManager/Admin|오류, 경고 및 정보|
|Microsoft-windows-terminalservices-gateway-LocalSessionManager/Operational|오류, 경고 및 정보|
|시스템|오류 및 경고|
| Microsoft-FSLogix-앱/운영|오류, 경고 및 정보|
|Microsoft-FSLogix-앱/관리자|오류, 경고 및 정보|

Windows 이벤트 로그에 대 한 자세한 내용은 [Windows 이벤트 레코드 속성](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Windows 가상 데스크톱에 대 한 Azure Monitor를 시작 하려면 다음 문서를 확인 하세요.

- [Windows 가상 데스크톱에 대 한 Azure Monitor를 사용 하 여 배포 모니터링](azure-monitor.md)
- [Windows 가상 데스크톱 문제 해결을 위한 Azure Monitor](troubleshoot-azure-monitor.md)

일반적인 문제를 해결 하거나 방지 하는 방법을 파악 하는 데 도움이 되는 Azure Advisor을 설정할 수도 있습니다. [Windows 가상 데스크톱에서 Azure Advisor 사용](azure-advisor.md)에 대해 자세히 알아보세요.

도움이 필요 하거나 질문이 있는 경우 커뮤니티 리소스를 확인 하세요.

- [Windows 가상 데스크톱 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서 질문 하거나 커뮤니티에 대 한 제안을 해 보세요.
   
- 피드백을 유지 하는 방법에 [대 한 자세한 내용은 Windows 가상 데스크톱에 대 한 문제 해결 개요, 사용자 의견 및 지원](troubleshoot-set-up-overview.md#report-issues)을 참조 하세요.

- Windows 가상 데스크톱 [피드백 허브](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) 에서 Windows 가상 데스크톱에 대 한 피드백을 남길 수도 있습니다.
