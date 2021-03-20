---
title: 모니터 사용 Windows 가상 데스크톱 모니터 미리 보기-Azure
description: Windows 가상 데스크톱에 Azure Monitor를 사용 하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e9da1071686dafa003a5a49d0864b77644493344
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594465"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>Windows 가상 데스크톱에 대 한 Azure Monitor를 사용 하 여 배포 모니터링 (미리 보기)

>[!IMPORTANT]
>Windows 가상 데스크톱에 대 한 Azure Monitor는 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Windows 가상 데스크톱 (미리 보기)에 대 한 Azure Monitor는 IT 전문가가 Windows 가상 데스크톱 환경을 이해 하는 데 도움이 되는 Azure Monitor 통합 문서를 기반으로 하는 대시보드입니다. 이 항목에서는 windows 가상 데스크톱 환경을 모니터링 하기 위해 Windows 가상 데스크톱에 대 한 Azure Monitor를 설정 하는 방법을 안내 합니다.

## <a name="requirements"></a>요구 사항

Windows 가상 데스크톱에 대 한 Azure Monitor 사용을 시작 하기 전에 다음 사항을 설정 해야 합니다.

- 모니터링 하는 모든 Windows 가상 데스크톱 환경은 Azure Resource Manager 호환 되는 Windows 가상 데스크톱의 최신 릴리스를 기반으로 해야 합니다.

- 하나 이상의 구성 된 Log Analytics 작업 영역.

- Log Analytics 작업 영역에서 다음 항목에 대 한 데이터 수집을 사용 하도록 설정 합니다.
    - 필요한 모든 성능 카운터
    - Windows 가상 데스크톱의 Azure Monitor에 사용 되는 모든 성능 카운터 또는 이벤트
    - 환경에 있는 모든 개체에 대 한 진단 도구의 데이터를 모니터링 합니다.
    - 모니터링할 환경의 Vm (가상 컴퓨터)

사용자 환경에 대 한 Windows 가상 데스크톱에 대 한 Azure Monitor 모니터링 하는 모든 사용자에 게는 다음과 같은 읽기 액세스 권한도 필요 합니다.

- 환경 리소스가 있는 리소스 그룹에 대 한 읽기 액세스입니다.

- 환경의 세션 호스트가 있는 리소스 그룹에 대 한 읽기 액세스

>[!NOTE]
> 읽기 액세스 전용 관리자는 데이터를 볼 수 있습니다. Windows 가상 데스크톱 포털에서 리소스를 관리 하려면 다른 사용 권한이 필요 합니다.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Windows 가상 데스크톱에 대 한 Azure Monitor 열기

다음 방법 중 하나를 사용 하 여 Windows 가상 데스크톱에 대 한 Azure Monitor를 열 수 있습니다.

- [Aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)로 이동 합니다.

- Azure Portal에서 **Windows 가상 데스크톱** 을 검색 하 고 선택한 다음, **Insights** 를 선택 합니다.

- Azure Portal에서 **Azure Monitor** 을 검색 하 고 선택 합니다. **Insights** 에서 **insights 허브** 를 선택 하 고 **기타** 아래에서 **Windows 가상 데스크톱** 을 선택 하 여 Azure Monitor 페이지에서 대시보드를 엽니다.

Windows 가상 데스크톱에 대 한 Azure Monitor를 만들었으면 모니터링 하려는 환경에 따라 **구독**, **리소스 그룹**, **호스트 풀** 및 **시간 범위** 에 레이블이 지정 된 확인란을 하나 이상 선택 합니다.

>[!NOTE]
>Windows 가상 데스크톱은 현재 한 번에 하나의 구독, 리소스 그룹 및 호스트 풀만 모니터링 하도록 지원 합니다. 모니터링 하려는 환경을 찾을 수 없으면 알려진 기능 요청 및 문제에 대 한 [문제 해결 설명서](troubleshoot-azure-monitor.md) 를 참조 하세요.

## <a name="set-up-with-the-configuration-workbook"></a>구성 통합 문서 설정

Windows 가상 데스크톱에 대 한 Azure Monitor를 처음으로 여는 경우 Windows 가상 데스크톱 리소스에 대 한 Azure Monitor를 구성 해야 합니다. 리소스를 구성 하려면:

1. Azure Portal에서 통합 문서를 엽니다.
2. **구성 통합 문서 열기를** 선택 합니다.

구성 통합 문서는 모니터링 환경을 설정 하 고 설치 프로세스를 완료 한 후 구성을 확인할 수 있도록 합니다. 대시보드의 항목이 올바르게 표시 되지 않거나 제품 그룹이 추가 데이터 요소가 필요한 업데이트를 게시 하는 경우 구성을 확인 하는 것이 중요 합니다.

## <a name="host-pool-diagnostic-settings"></a>호스트 풀 진단 설정

이 기능을 지 원하는 Windows 가상 데스크톱 환경 내의 모든 개체에 대해 Azure Monitor 진단 설정을 사용 하도록 설정 해야 합니다.

1. [Aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)에서 Windows 가상 데스크톱에 대 한 Azure Monitor를 열고 **구성 통합 문서** 를 선택 합니다.

2. **구독**, **리소스 그룹** 및 **호스트 풀** 에서 모니터링할 환경을 선택 합니다.

3. **호스트 풀 진단 설정** 에서 호스트 풀에 Windows 가상 데스크톱 진단이 사용 하도록 설정 되어 있는지 확인 합니다. 그렇지 않으면 "선택한 호스트 풀에 대 한 기존 진단 구성이 없습니다." 라는 오류 메시지가 표시 됩니다. 
   
   다음 테이블을 사용 하도록 설정 해야 합니다.

    - 검사점
    - Error
    - 관리
    - 연결
    - HostRegistration

    >[!NOTE]
    > 오류 메시지가 표시 되지 않으면 4 단계를 수행할 필요가 없습니다.

4. **호스트 풀 구성** 을 선택 합니다.

5. **배포** 를 선택합니다.

6. 통합 문서 새로 고침

Windows 가상 데스크톱 환경에서 모든 개체에 대 한 진단을 사용 하도록 설정 하는 방법에 대 한 자세한 내용을 확인 하거나 [Windows 가상 데스크톱 진단 보내기](diagnostics-log-analytics.md)의 Log Analytics 작업 영역에 Log Analytics에 액세스할 수 있습니다.

## <a name="configure-log-analytics"></a>Log Analytics 구성

Windows 가상 데스크톱에 대 한 Azure Monitor 사용을 시작 하려면 모니터링 하려는 환경에서 데이터를 수집 하 고 통합 문서에 제공할 Log Analytics 작업 영역이 하나 이상 필요 합니다. 이미 설정 되어 있는 경우에는 [성능 카운터 설정](#set-up-performance-counters)으로 건너뜁니다. Windows 가상 데스크톱 환경을 포함 하는 Azure 구독에 대 한 새 Log Analytics 작업 영역을 설정 하려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)를 참조 하세요.

>[!NOTE]
>Log Analytics에 대 한 표준 데이터 저장소 요금이 적용 됩니다. 시작 하려면 종 량 제 모델을 선택 하 고 배포 크기를 조정 하 고 더 많은 데이터를 가져오는 것이 좋습니다. 자세히 알아보려면 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조 하세요.

### <a name="set-up-performance-counters"></a>성능 카운터 설정

Log Analytics 작업 영역의 해당 샘플 간격에서 컬렉션에 대 한 특정 성능 카운터를 사용 하도록 설정 해야 합니다. 이러한 성능 카운터는 Windows 가상 데스크톱을 모니터링 하는 데 필요한 유일한 카운터입니다. 다른 모든 항목을 사용 하지 않도록 설정 하 여 비용을 절감할 수 있습니다.

성능 카운터를 이미 사용 하도록 설정한 상태에서 해당 카운터를 제거 하려면 성능 카운터 [구성](../azure-monitor/agents/data-sources-performance-counters.md) 의 지침에 따라 성능 카운터를 다시 구성 합니다. 문서에서 카운터를 추가 하는 방법을 설명 하는 동안 동일한 위치에서 카운터를 제거할 수도 있습니다.

성능 카운터를 아직 설정 하지 않은 경우 Windows 가상 데스크톱에 대 한 Azure Monitor에 대해 구성 하는 방법은 다음과 같습니다.

1. [Aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)로 이동한 다음 창의 **구성 통합 문서** 를 선택 합니다.

2. **Log Analytics 구성** 에서 구독에 대해 설정한 작업 영역을 선택 합니다.

3. **작업 영역 성능 카운터** 에는 모니터링에 필요한 카운터 목록이 표시 됩니다. 해당 목록의 오른쪽에서 **누락 된 카운터** 목록의 항목을 확인 하 여 작업 영역 모니터링을 시작 하는 데 필요한 카운터를 사용 하도록 설정 합니다.

4. **성능 카운터 구성** 을 선택 합니다.

5. **구성 적용** 을 선택 합니다.

6. 구성 통합 문서를 새로 고치고 환경 설정을 계속 합니다.

서비스를 업데이트 하 고 새 모니터링 도구를 필요로 할 때마다 초기 구성 후에 새 성능 카운터를 추가할 수도 있습니다. **누락 된 카운터** 목록에서 필요한 모든 카운터를 선택 하 여 사용할 수 있는지 확인할 수 있습니다.

>[!NOTE]
>입력 지연 성능 카운터는 Windows 10 RS5 이상 또는 Windows Server 2019 이상 에서만 호환 됩니다.

컬렉션에 대해 아직 사용 하도록 설정 되지 않은 성능 카운터를 수동으로 추가 하는 방법에 대 한 자세한 내용은 [성능 카운터 구성](../azure-monitor/agents/data-sources-performance-counters.md)을 참조 하세요.

### <a name="set-up-windows-events"></a>Windows 이벤트 설정

다음으로 Log Analytics 작업 영역에서 컬렉션에 대 한 특정 Windows 이벤트를 사용 하도록 설정 해야 합니다. 이 섹션에서 설명 하는 이벤트는 Windows 가상 데스크톱 요구에 대 한 유일한 Azure Monitor입니다. 다른 모든 항목을 사용 하지 않도록 설정 하 여 비용을 절감할 수 있습니다.

Windows 이벤트를 설정 하려면:

1. Windows 이벤트를 이미 사용 하도록 설정한 상태에서 해당 이벤트를 제거 하려면 구성 통합 문서를 사용 하 여 모니터링에 필요한 집합을 사용 하도록 설정 하기 전에 원하는 이벤트를 제거 합니다.

2. [Aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)의 Windows 가상 데스크톱 Azure Monitor으로 이동한 다음 창의 맨 아래에 있는 **구성 통합 문서** 를 선택 합니다.

3. **Windows 이벤트 구성** 에는 모니터링에 필요한 windows 이벤트 목록이 있습니다. 이 목록 오른쪽에는 작업 영역에 대해 현재 사용 하도록 설정 되지 않은 필수 이벤트 이름 및 이벤트 유형을 찾을 수 있는 **누락 된 이벤트** 목록이 있습니다. 이러한 각 이름을 나중에 기록 합니다.

4. **작업 영역 구성 열기** 를 선택 합니다.

5. **데이터** 를 선택 합니다.

6. **Windows 이벤트 로그** 를 선택 합니다.

7. 3 단계에서 누락 된 이벤트 이름을 추가 하 고 각각에 대해 필요한 이벤트 유형을 추가 합니다.

8. 구성 통합 문서를 새로 고치고 환경 설정을 계속 합니다.

모니터링 도구 업데이트에 새 이벤트를 사용 해야 하는 경우 초기 구성 후 새 Windows 이벤트를 추가할 수 있습니다. 모든 필수 이벤트를 사용 하도록 설정 했는지 확인 하려면 **누락 된 이벤트** 목록으로 돌아가서 누락 된 이벤트를 사용 하도록 설정 합니다.

## <a name="install-the-log-analytics-agent-on-all-hosts"></a>모든 호스트에 Log Analytics 에이전트 설치

마지막으로 호스트에서 선택한 작업 영역으로 데이터를 전송 하려면 호스트 풀의 모든 호스트에 Log Analytics 에이전트를 설치 해야 합니다.

Log Analytics 에이전트를 설치 하려면 다음을 수행 합니다.

1. [Aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)의 Windows 가상 데스크톱 Azure Monitor으로 이동한 다음 창의 맨 아래에 있는 **구성 통합 문서** 를 선택 합니다.

2. 호스트 풀의 모든 호스트에 대해 Log Analytics 구성 되지 않은 경우 "호스트 풀의 일부 호스트가 선택한 Log Analytics 작업 영역으로 데이터를 전송 하지 않습니다." 라는 메시지가 포함 된 Log Analytics 구성 섹션의 맨 아래에 오류가 표시 됩니다. 선택한 호스트를 추가 하려면 **작업 영역에 호스트 추가** 를 선택 합니다. 오류 메시지가 표시 되지 않으면 여기를 중지 합니다.

3. 구성 통합 문서를 새로 고칩니다.

>[!NOTE]
>Log Analytics 확장을 설치 하려면 호스트 컴퓨터를 실행 해야 합니다. 호스트에서 자동 배포가 실패 하는 경우 항상 호스트에 확장을 수동으로 설치할 수 있습니다. 확장을 수동으로 설치 하는 방법에 [대 한 자세한 내용은 Windows 용 가상 머신 확장 Log Analytics](../virtual-machines/extensions/oms-windows.md)을 참조 하세요.

## <a name="optional-configure-alerts"></a>선택 사항: 경고 구성

선택한 구독 내에서 심각한 Azure Monitor 경고가 발생 하는 경우 사용자에 게 알리도록 Windows 가상 데스크톱에 대 한 Azure Monitor를 구성할 수 있습니다. 이렇게 하려면 [Azure Monitor 경고를 사용 하 여 이벤트에 응답](../azure-monitor/alerts/tutorial-response.md)의 지침을 따르세요.

## <a name="diagnostic-and-usage-data"></a>진단 및 사용량 현황 데이터

Microsoft는 Azure Monitor 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는이 데이터를 사용 하 여 서비스의 품질, 보안 및 무결성을 향상 시킵니다.

정확 하 고 효율적인 문제 해결 기능을 제공 하기 위해 수집 된 데이터에는 포털 세션 ID, Azure Active Directory 사용자 ID 및 이벤트가 발생 한 포털 탭의 이름이 포함 됩니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인정보처리방침](https://privacy.microsoft.com/privacystatement)을 참조하세요.

>[!NOTE]
>서비스에서 수집 된 개인 데이터를 보거나 삭제 하는 방법에 대 한 자세한 내용은 [GDPR에 대 한 Azure 데이터 주체 요청](/microsoft-365/compliance/gdpr-dsr-azure)을 참조 하세요. GDPR에 대 한 자세한 내용은 [서비스 신뢰 포털의 GDPR 섹션](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이제 Windows 가상 데스크톱 Azure Portal를 구성 했으므로 다음에 도움이 될 수 있는 몇 가지 리소스가 있습니다.

- Windows 가상 데스크톱에 대 한 Azure Monitor와 관련 된 용어 및 개념에 대 한 자세한 내용은 [용어집](azure-monitor-glossary.md) 을 확인 하세요.
- 문제가 발생 하는 경우 [문제 해결 가이드](troubleshoot-azure-monitor.md) 를 참조 하세요.