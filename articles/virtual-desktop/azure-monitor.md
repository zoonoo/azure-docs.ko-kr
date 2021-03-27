---
title: 모니터 사용 Windows 가상 데스크톱 모니터 미리 보기-Azure
description: Windows 가상 데스크톱에 Azure Monitor를 사용 하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1c87763cb2ca482fc8ee15588d7287f0d9275fff
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627169"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>Windows 가상 데스크톱에 대 한 Azure Monitor를 사용 하 여 배포 모니터링 (미리 보기)

>[!IMPORTANT]
>Windows 가상 데스크톱에 대 한 Azure Monitor는 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Windows 가상 데스크톱 (미리 보기)에 대 한 Azure Monitor는 IT 전문가가 Windows 가상 데스크톱 환경을 이해 하는 데 도움이 되는 Azure Monitor 통합 문서를 기반으로 하는 대시보드입니다. 이 문서에서는 windows 가상 데스크톱 환경을 모니터링 하기 위해 Windows 가상 데스크톱 Azure Monitor를 설정 하는 방법을 안내 합니다.

## <a name="requirements"></a>요구 사항

Windows 가상 데스크톱에 대 한 Azure Monitor 사용을 시작 하기 전에 다음 사항을 설정 해야 합니다.

- 모니터링 하는 모든 Windows 가상 데스크톱 환경은 Azure Resource Manager 호환 되는 Windows 가상 데스크톱의 최신 릴리스를 기반으로 해야 합니다.
- 하나 이상의 구성 된 Log Analytics 작업 영역. Windows 가상 데스크톱 세션 호스트에 대해 지정 된 Log Analytics 작업 영역을 사용 하 여 Windows 가상 데스크톱 배포의 세션 호스트 에서만 성능 카운터 및 이벤트를 수집 하도록 합니다.
- Log Analytics 작업 영역에서 다음 항목에 대 한 데이터 수집을 사용 하도록 설정 합니다.
    - Windows 가상 데스크톱 환경의 진단
    - Windows 가상 데스크톱 세션 호스트의 권장 되는 성능 카운터
    - Windows 가상 데스크톱 세션 호스트의 권장 Windows 이벤트 로그

 이 문서에서 설명 하는 데이터 설치 프로세스는 Windows 가상 데스크톱을 모니터링 하는 데만 필요 합니다. Log Analytics 작업 영역에 데이터를 보내는 다른 모든 항목을 사용 하지 않도록 설정 하 여 비용을 절감할 수 있습니다.

사용자 환경에 대 한 Windows 가상 데스크톱에 대 한 모든 Azure Monitor 모니터링에는 다음 읽기 액세스 권한도 필요 합니다.

- Windows 가상 데스크톱 리소스를 보유 하는 Azure 구독에 대 한 읽기 액세스
- Windows 가상 데스크톱 세션 호스트를 보유 하는 구독의 리소스 그룹에 대 한 읽기 액세스
- Log Analytics 작업 영역 또는 작업 영역에 대 한 읽기 권한

>[!NOTE]
> 읽기 액세스 전용 관리자는 데이터를 볼 수 있습니다. Windows 가상 데스크톱 포털에서 리소스를 관리 하려면 다른 사용 권한이 필요 합니다.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Windows 가상 데스크톱에 대 한 Azure Monitor 열기

다음 방법 중 하나를 사용 하 여 Windows 가상 데스크톱에 대 한 Azure Monitor를 열 수 있습니다.

- [Aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)로 이동 합니다.
- Azure Portal에서 **Windows 가상 데스크톱** 을 검색 하 고 선택한 다음, **Insights** 를 선택 합니다.
- Azure Portal에서 **Azure Monitor** 을 검색 하 고 선택 합니다. **Insights** 에서 **insights 허브** 를 선택 하 고 **Windows 가상 데스크톱** 을 선택 합니다.
페이지가 열리면 모니터링 하려는 환경에 대 한 **구독**, **리소스 그룹**, **호스트 풀** 및 **시간 범위** 를 입력 합니다.

>[!NOTE]
>Windows 가상 데스크톱은 현재 한 번에 하나의 구독, 리소스 그룹 및 호스트 풀만 모니터링 하도록 지원 합니다. 모니터링 하려는 환경을 찾을 수 없으면 알려진 기능 요청 및 문제에 대 한 [문제 해결 설명서](troubleshoot-azure-monitor.md) 를 참조 하세요.

## <a name="log-analytics-settings"></a>Log Analytics 설정

Windows 가상 데스크톱에 대 한 Azure Monitor 사용을 시작 하려면 하나 이상의 Log Analytics 작업 영역이 필요 합니다. Windows 가상 데스크톱 세션 호스트에 대해 지정 된 Log Analytics 작업 영역을 사용 하 여 Windows 가상 데스크톱 배포에서 성능 카운터 및 이벤트가 수집 된 양식 세션 호스트만 있는지 확인 합니다. 작업 영역을 이미 설정한 경우 [구성 통합 문서를 사용 하 여 설정](#set-up-using-the-configuration-workbook)으로 건너뜁니다. 하나를 설정 하려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)를 참조 하세요.

>[!NOTE]
>Log Analytics에 대 한 표준 데이터 저장소 요금이 적용 됩니다. 시작 하려면 종 량 제 모델을 선택 하 고 배포 크기를 조정 하 고 더 많은 데이터를 가져오는 것이 좋습니다. 자세히 알아보려면 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조 하세요.

## <a name="set-up-using-the-configuration-workbook"></a>구성 통합 문서를 사용 하 여 설정

Windows 가상 데스크톱에 대 한 Azure Monitor를 처음으로 여는 경우 Windows 가상 데스크톱 환경에 대 한 Azure Monitor를 설정 해야 합니다. 리소스를 구성 하려면:

1. [Aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)의 Azure Portal에서 Windows 가상 데스크톱에 대 한 Azure Monitor를 열고 **구성 통합 문서** 를 선택 합니다.
2. **구독**, **리소스 그룹** 및 **호스트 풀** 에서 구성할 환경을 선택 합니다.

구성 통합 문서는 모니터링 환경을 설정 하 고 설치 프로세스를 완료 한 후 구성을 확인할 수 있도록 합니다. 대시보드의 항목이 올바르게 표시 되지 않거나 제품 그룹이 새 설정이 필요한 업데이트를 게시 하는 경우 구성을 확인 하는 것이 중요 합니다.

### <a name="resource-diagnostic-settings"></a>리소스 진단 설정

Windows 가상 데스크톱 인프라에서 정보를 수집 하려면 Windows 가상 데스크톱 호스트 풀 및 작업 영역에서 몇 가지 진단 설정을 사용 하도록 설정 해야 합니다 (Log Analytics 작업 영역이 아닌 Windows 가상 데스크톱 작업 영역). 호스트 풀, 작업 영역 및 기타 Windows 가상 데스크톱 리소스 개체에 대해 자세히 알아보려면 [환경 가이드](environment-setup.md)를 참조 하세요.

Windows 가상 데스크톱 진단 및 [Windows 가상 데스크톱 진단 전송](diagnostics-log-analytics.md)에서 지원 되는 진단 테이블에 대 한 자세한 내용은 Log Analytics에 대해 자세히 알아볼 수 있습니다.

구성 통합 문서에서 리소스 진단 설정을 설정 하려면 다음을 수행 합니다. 

1. 구성 통합 문서에서 **리소스 진단 설정** 탭을 선택 합니다. 
2. **Log Analytics 작업 영역** 을 선택 하 여 Windows 가상 데스크톱 진단을 보냅니다. 

#### <a name="host-pool-diagnostic-settings"></a>호스트 풀 진단 설정

구성 문서의 리소스 진단 설정 섹션을 사용 하 여 호스트 풀 진단을 설정 하려면 다음을 수행 합니다.

1. **호스트 풀** 에서 Windows 가상 데스크톱 진단 사용 여부를 확인 합니다. 그렇지 않으면 "선택한 호스트 풀에 대 한 기존 진단 구성이 없습니다." 라는 오류 메시지가 표시 됩니다. 다음 지원 되는 진단 테이블을 사용 하도록 설정 해야 합니다.

    - 검사점
    - Error
    - 관리
    - 연결
    - HostRegistration
    - AgentHealthStatus
    
    >[!NOTE]
    > 오류 메시지가 표시 되지 않으면 2 ~ 4 단계를 수행할 필요가 없습니다.

2. **호스트 풀 구성** 을 선택 합니다.
3. **배포** 를 선택합니다.
4. 구성 통합 문서를 새로 고칩니다.

#### <a name="workspace-diagnostic-settings"></a>작업 영역 진단 설정 

구성 문서의 리소스 진단 설정 섹션을 사용 하 여 작업 영역 진단을 설정 하려면 다음을 수행 합니다.

 1. **작업 영역** 에서 windows 가상 데스크톱 진단 작업 영역에 대해 Windows 가상 데스크톱 진단이 사용 하도록 설정 되어 있는지 확인 합니다. 그렇지 않으면 "선택한 작업 영역에 대 한 기존 진단 구성이 없습니다." 라는 오류 메시지가 표시 됩니다. 다음 지원 되는 진단 테이블을 사용 하도록 설정 해야 합니다.
 
    - 검사점
    - Error
    - 관리
    - 피드
    
    >[!NOTE]
    > 오류 메시지가 표시 되지 않으면 2-4 단계를 수행 하지 않아도 됩니다.

2. **작업 영역 구성** 을 선택 합니다.
3. **배포** 를 선택합니다.
4. 구성 통합 문서를 새로 고칩니다.

### <a name="session-host-data-settings"></a>세션 호스트 데이터 설정

Windows 가상 데스크톱 세션 호스트에 대 한 정보를 수집 하려면 호스트 풀의 모든 세션 호스트에 Log Analytics 에이전트를 설치 하 고, 세션 호스트가 Log Analytics 작업 영역으로 전송 되 고 있는지 확인 하 고, 성능 데이터 및 Windows 이벤트 로그를 수집 하도록 Log Analytics 에이전트 설정을 구성 해야 합니다.

세션 호스트 데이터를 전송 하는 Log Analytics 작업 영역은 진단 데이터를 전송 하는 것과 동일 하지 않아도 됩니다. Azure 세션 호스트가 Windows 가상 데스크톱 환경 외부에 있는 경우 Windows 가상 데스크톱 세션 호스트에 대해 지정 된 Log Analytics 작업 영역을 지정 하는 것이 좋습니다. 

세션 호스트 데이터를 수집 하려는 Log Analytics 작업 영역을 설정 하려면 다음을 수행 합니다. 

1. 구성 통합 문서에서 **세션 호스트 데이터 설정** 탭을 선택 합니다. 
2. 세션 호스트 데이터를 전송 하려는 **Log Analytics 작업 영역** 을 선택 합니다. 

#### <a name="session-hosts"></a>세션 호스트

호스트 풀의 모든 세션 호스트에 Log Analytics 에이전트를 설치 하 고 해당 호스트에서 선택한 Log Analytics 작업 영역으로 데이터를 전송 해야 합니다. 호스트 풀의 모든 세션 호스트에 대해 Log Analytics 구성 되지 않은 경우 "호스트 풀의 일부 호스트가 선택한 Log Analytics 작업 영역으로 데이터를 전송 하지 않습니다." 라는 메시지와 함께 세션 **호스트 데이터 설정** 맨 위에 **세션 호스트** 섹션이 표시 됩니다.

>[!NOTE]
> **세션 호스트** 섹션 또는 오류 메시지가 표시 되지 않으면 모든 세션 호스트가 올바르게 설정 된 것입니다. [작업 영역 성능 카운터](#workspace-performance-counters)에 대 한 지침 설정으로 건너뜁니다.

구성 통합 문서를 사용 하 여 남은 세션 호스트를 설정 하려면 다음을 수행 합니다.

1. **작업 영역에 호스트 추가를** 선택 합니다. 
2. 구성 통합 문서를 새로 고칩니다.

>[!NOTE]
>Log Analytics 확장을 설치 하려면 호스트 컴퓨터를 실행 해야 합니다. 자동 배포가 작동 하지 않는 경우 대신 호스트에 확장을 수동으로 설치할 수 있습니다. 확장을 수동으로 설치 하는 방법에 [대 한 자세한 내용은 Windows 용 가상 머신 확장 Log Analytics](../virtual-machines/extensions/oms-windows.md)을 참조 하세요.

#### <a name="workspace-performance-counters"></a>작업 영역 성능 카운터

특정 성능 카운터를 사용 하도록 설정 하 여 세션 호스트에서 성능 정보를 수집 하 고 Log Analytics 작업 영역에 전송 해야 합니다.

성능 카운터를 이미 사용 하도록 설정한 상태에서 해당 카운터를 제거 하려면 [성능 카운터 구성](../azure-monitor/agents/data-sources-performance-counters.md)의 지침을 따르세요. 동일한 위치에서 성능 카운터를 추가 하 고 제거할 수 있습니다.

구성 통합 문서를 사용 하 여 성능 카운터를 설정 하려면: 

1. 구성 문서의 **작업 영역 성능 카운터** 에서 **구성 된 카운터** 를 확인 하 여 이미 Log Analytics 작업 영역으로 보내도록 설정 된 카운터를 확인 합니다. **누락 된 카운터** 를 확인 하 여 필요한 모든 카운터를 사용 하도록 설정 했는지 확인 합니다.
2. 누락 된 카운터가 있는 경우 **성능 카운터 구성** 을 선택 합니다.
3. **구성 적용** 을 선택 합니다.
4. 구성 통합 문서를 새로 고칩니다.
5. **누락 된 카운터** 목록을 확인 하 여 필요한 모든 카운터를 사용 하도록 설정 했는지 확인 합니다. 

#### <a name="configure-windows-event-logs"></a>Windows 이벤트 로그 구성

또한 특정 Windows 이벤트 로그를 사용 하도록 설정 하 여 세션 호스트에서 오류, 경고 및 정보를 수집 하 고 Log Analytics 작업 영역으로 전송 해야 합니다.

Windows 이벤트 로그를 이미 사용 하도록 설정 했 고 제거 하려면 [Windows 이벤트 로그 구성](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)의 지침을 따르세요.  동일한 위치에서 Windows 이벤트 로그를 추가 하 고 제거할 수 있습니다.

구성 통합 문서를 사용 하 여 Windows 이벤트 로그를 설정 하려면:

1. **Windows 이벤트 로그 구성** 에서 **구성 된 이벤트 로그** 를 확인 하 여 이미 Log Analytics 작업 영역으로 보낼 수 있도록 설정한 이벤트 로그를 확인 합니다. **누락 된 이벤트 로그** 를 확인 하 여 모든 Windows 이벤트 로그를 사용 하도록 설정 했는지 확인 합니다.
2. Windows 이벤트 로그가 없는 경우 **이벤트 구성** 을 선택 합니다.
3. **배포** 를 선택합니다.
4. 구성 통합 문서를 새로 고칩니다.
5. **누락 된 이벤트 로그** 목록을 확인 하 여 필요한 모든 Windows 이벤트 로그를 사용 하도록 설정 했는지 확인 합니다. 

>[!NOTE]
>자동 이벤트 배포가 실패 하는 경우 구성 통합 문서에서 **에이전트 구성 열기** 를 선택 하 여 누락 된 Windows 이벤트 로그를 수동으로 추가 합니다. 

## <a name="optional-configure-alerts"></a>선택 사항: 경고 구성

Windows 가상 데스크톱에 대 한 Azure Monitor를 사용 하면 Windows 가상 데스크톱 데이터의 컨텍스트에서 선택한 구독 내에서 발생 하는 경고 Azure Monitor 모니터링할 수 있습니다. Azure Monitor 경고는 Azure 구독에 대 한 선택적 기능이 며 Windows 가상 데스크톱에 대 한 Azure Monitor와 별도로 설정 해야 합니다. Azure Monitor alerts 프레임 워크를 사용 하 여 Windows 가상 데스크톱 이벤트, 진단 및 리소스에 대 한 사용자 지정 경고를 설정할 수 있습니다. Azure Monitor 경고에 대해 자세히 알아보려면 [Azure Monitor 경고를 사용 하 여 이벤트에 응답](../azure-monitor/alerts/tutorial-response.md)을 참조 하세요.

## <a name="diagnostic-and-usage-data"></a>진단 및 사용량 현황 데이터

Microsoft는 Azure Monitor 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는이 데이터를 사용 하 여 서비스의 품질, 보안 및 무결성을 향상 시킵니다.

정확 하 고 효율적인 문제 해결 기능을 제공 하기 위해 수집 된 데이터에는 포털 세션 ID, Azure Active Directory 사용자 ID 및 이벤트가 발생 한 포털 탭의 이름이 포함 됩니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인정보처리방침](https://privacy.microsoft.com/privacystatement)을 참조하세요.

>[!NOTE]
>서비스에서 수집 된 개인 데이터를 보거나 삭제 하는 방법에 대 한 자세한 내용은 [GDPR에 대 한 Azure 데이터 주체 요청](/microsoft-365/compliance/gdpr-dsr-azure)을 참조 하세요. GDPR에 대 한 자세한 내용은 [서비스 신뢰 포털의 GDPR 섹션](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이제 Windows 가상 데스크톱 환경에 대 한 Azure Monitor를 구성 했으므로 환경 모니터링을 시작 하는 데 도움이 될 수 있는 몇 가지 리소스가 있습니다.

- Windows 가상 데스크톱에 대 한 Azure Monitor와 관련 된 용어 및 개념에 대 한 자세한 내용은 [용어집](azure-monitor-glossary.md) 을 확인 하세요.
- 문제가 발생 하는 경우 문제 [해결 가이드](troubleshoot-azure-monitor.md) 를 확인 하 고 알려진 문제를 확인 하세요.
