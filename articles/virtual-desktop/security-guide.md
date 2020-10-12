---
title: Windows 가상 데스크톱 보안 모범 사례-Azure
description: Windows 가상 데스크톱 환경을 안전 하 게 유지 하기 위한 모범 사례입니다.
author: heidilohr
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a194074b75a404e5a28e86015b0d0bcea2619fc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88002298"
---
# <a name="security-best-practices"></a>보안 모범 사례

Windows 가상 데스크톱은 조직을 안전 하 게 유지 하기 위한 다양 한 보안 기능을 포함 하는 관리 되는 가상 데스크톱 서비스입니다. Windows 가상 데스크톱 배포에서 Microsoft는 고객을 대신 하 여 서비스의 일부를 관리 합니다. 서비스에는 역방향 연결과 같은 여러 가지 기본 제공 고급 보안 기능이 있으며,이를 통해 어디서 나 원격 데스크톱에 액세스할 수 있는 것과 관련 된 위험을 줄일 수 있습니다.

이 문서에서는 고객의 Windows 가상 데스크톱 배포를 안전 하 게 유지 하기 위해 관리자 권한으로 수행할 수 있는 추가 단계에 대해 설명 합니다.

## <a name="security-responsibilities"></a>보안 책임

기존 온-프레미스 VDIs (가상 데스크톱 인프라)와 다른 클라우드 서비스를 사용 하는 것은 보안 업무를 처리 하는 방법입니다. 예를 들어 기존의 온-프레미스 VDI에서 고객은 보안의 모든 측면을 담당 합니다. 그러나 대부분의 클라우드 서비스에서 이러한 책임은 고객과 회사 간에 공유 됩니다.

Windows 가상 데스크톱을 사용 하는 경우 일부 구성 요소는 사용자 환경에 대해 이미 보안을 유지 하지만 조직의 보안 요구 사항에 맞게 다른 영역을 직접 구성 해야 한다는 점을 이해 하는 것이 중요 합니다.

Windows 가상 데스크톱 배포에서 담당 하는 보안 요구 사항은 다음과 같습니다.

| 보안 요구 | 고객이이를 담당 하나요? |
|---------------|:-------------------------:|
|ID|예|
|사용자 장치 (모바일 및 PC)|예|
|앱 보안|예|
|세션 호스트 OS|예|
|배포 구성|예|
|네트워크 컨트롤|예|
|가상화 제어 평면|아니요|
|물리적 호스트|아니요|
|실제 네트워크|아니요|
|물리적 데이터 센터|아니요|

고객이 책임을 지지 않는 보안 요구는 Microsoft에서 처리 합니다.

## <a name="azure-security-best-practices"></a>Azure 보안 모범 사례

Windows 가상 데스크톱은 Azure의 서비스입니다. Windows 가상 데스크톱 배포의 안전을 최대화 하려면 주변 Azure 인프라 및 관리 평면도 보호 해야 합니다. 인프라를 보호 하려면 Windows 가상 데스크톱이 더 큰 Azure 에코 시스템에 어떻게 부합 하는지 고려 합니다. Azure 에코 시스템에 대해 자세히 알아보려면 [azure 보안 모범 사례 및 패턴](../security/fundamentals/best-practices-and-patterns.md)을 참조 하세요.

이 섹션에서는 Azure 에코 시스템을 보호 하기 위한 모범 사례를 설명 합니다.

### <a name="enable-azure-security-center"></a>Azure Security Center 활성화

구독, 가상 컴퓨터, 키 자격 증명 모음 및 저장소 계정에 Azure Security Center Standard를 사용 하도록 설정 하는 것이 좋습니다.

Azure Security Center Standard를 사용 하 여 다음을 수행할 수 있습니다.

* 취약성 관리.
* PCI와 같은 일반적인 프레임 워크를 사용 하 여 규정 준수를 평가 합니다.
* 환경에 대 한 전반적인 보안을 강화 합니다.

자세히 알아보려면 [Security Center Standard에 Azure 구독 등록을](../security-center/security-center-get-started.md)참조 하세요.

### <a name="improve-your-secure-score"></a>보안 점수 향상

보안 점수는 전체 보안을 개선 하기 위한 권장 사항 및 모범 사례 조언을 제공 합니다. 이러한 권장 사항은 가장 중요 한 문제를 선택 하는 데 도움이 되도록 우선 순위를 지정 하 고 빠른 수정 옵션을 통해 잠재적 취약성을 신속 하 게 해결할 수 있습니다. 이러한 권장 사항은 시간이 지남에 따라 업데이트 되므로 환경의 보안을 유지 하는 가장 좋은 방법에 대해 최신 상태를 유지할 수 있습니다. 자세히 알아보려면 [Azure Security Center의 보안 점수 향상](../security-center/security-center-secure-score.md)을 참조 하세요.

## <a name="windows-virtual-desktop-security-best-practices"></a>Windows 가상 데스크톱 보안 모범 사례

Windows 가상 데스크톱에는 여러 가지 기본 제공 보안 컨트롤이 있습니다. 이 섹션에서는 사용자와 데이터를 안전 하 게 유지 하는 데 사용할 수 있는 보안 컨트롤에 대해 알아봅니다.

### <a name="require-multi-factor-authentication"></a>다단계 인증 필요

Windows 가상 데스크톱의 모든 사용자 및 관리자에 대해 multi-factor authentication을 요구 하면 전체 배포의 보안이 향상 됩니다. 자세한 내용은 [Windows 가상 데스크톱에 대 한 Azure Multi-Factor Authentication 사용](set-up-mfa.md)을 참조 하세요.

### <a name="enable-conditional-access"></a>조건부 액세스 사용

[조건부 액세스](../active-directory/conditional-access/best-practices.md) 를 사용 하면 사용자에 게 Windows 가상 데스크톱 환경에 대 한 액세스 권한을 부여 하기 전에 위험을 관리할 수 있습니다. 에 대 한 액세스 권한을 부여할 사용자를 결정할 때 사용자가 누구 인지, 로그인 하는 방법 및 사용 중인 장치를 고려 하는 것이 좋습니다.

### <a name="collect-audit-logs"></a>감사 로그 수집

감사 로그 수집을 사용 하도록 설정 하면 Windows 가상 데스크톱과 관련 된 사용자 및 관리자 활동을 볼 수 있습니다. 키 감사 로그의 몇 가지 예는 다음과 같습니다.

-   [Azure 활동 로그](../azure-monitor/platform/activity-log-collect.md)
-   [활동 로그 Azure Active Directory](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [세션 호스트](../azure-monitor/platform/agent-windows.md)
-   [Windows 가상 데스크톱 진단 로그](../virtual-desktop/diagnostics-log-analytics.md)
-   [Key Vault 로그](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>Remoteapp 사용

배포 모델을 선택할 때 원격 사용자에 게 전체 가상 데스크톱에 대 한 액세스 권한을 제공 하거나 응용 프로그램만 선택할 수 있습니다. 원격 응용 프로그램 또는 Remoteapp는 사용자가 가상 데스크톱에서 앱을 사용할 때 원활한 환경을 제공 합니다. 응용 프로그램에서 노출 하는 원격 컴퓨터의 하위 집합을 사용 하 여 사용자만 작업 하도록 허용 하 여 위험을 Remoteapp 합니다.

### <a name="monitor-usage-with-azure-monitor"></a>Azure Monitor 사용 모니터링

[Azure Monitor](https://azure.microsoft.com/services/monitor/)를 사용 하 여 Windows 가상 데스크톱 서비스의 사용 현황 및 가용성을 모니터링 합니다. 서비스에 영향을 주는 이벤트가 있을 때마다 알림을 받도록 Windows 가상 데스크톱 서비스에 대 한 [서비스 상태 경고](../service-health/alerts-activity-log-service-notifications.md) 를 만드는 것이 좋습니다.

## <a name="session-host-security-best-practices"></a>세션 호스트 보안 모범 사례

세션 호스트는 Azure 구독 및 가상 네트워크 내에서 실행 되는 가상 머신입니다. Windows 가상 데스크톱 배포의 전반적인 보안은 세션 호스트에 배치 하는 보안 제어에 따라 달라 집니다. 이 섹션에서는 세션 호스트를 안전 하 게 유지 하기 위한 모범 사례를 설명 합니다.

### <a name="enable-endpoint-protection"></a>Endpoint protection 사용

알려진 악성 소프트웨어에서 배포를 보호 하려면 모든 세션 호스트에서 endpoint protection을 사용 하도록 설정 하는 것이 좋습니다. Windows Defender 바이러스 백신 또는 타사 프로그램을 사용할 수 있습니다. 자세히 알아보려면 [VDI 환경에서 Windows Defender 바이러스 백신 배포 가이드](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus)를 참조 하세요.

FSLogix 또는 VHD 파일을 탑재 하는 기타 솔루션과 같은 프로필 솔루션의 경우 VHD 파일 확장명을 제외 하는 것이 좋습니다.

### <a name="install-an-endpoint-detection-and-response-product"></a>끝점 검색 및 응답 제품 설치

고급 검색 및 응답 기능을 제공 하기 위해 EDR (끝점 검색 및 응답) 제품을 설치 하는 것이 좋습니다. [Azure Security Center](../security-center/security-center-services.md) 사용 하도록 설정 된 서버 운영 체제의 경우 edr 제품을 설치 하면 Defender ATP가 배포 됩니다. 클라이언트 운영 체제의 경우 해당 끝점에 [DEFENDER ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) 또는 타사 제품을 배포할 수 있습니다.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>위협 및 취약성 관리 평가 사용

운영 체제 및 응용 프로그램에 존재 하는 소프트웨어 취약점을 식별 하는 것은 환경을 안전 하 게 유지 하는 데 중요 합니다. Azure Security Center는 서버 운영 체제에 대 한 취약성 평가를 통해 문제 지점을 식별 하는 데 도움이 될 수 있습니다. 데스크톱 운영 체제에 대 한 위협 및 취약성 관리를 제공 하는 Defender ATP를 사용할 수도 있습니다. Azure Security Center 및 Defender ATP를 사용 하는 것이 좋지만, 그렇다면 타사 제품을 사용할 수도 있습니다.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>사용자 환경에서 소프트웨어 취약성 패치

취약점을 식별 한 후에는 패치를 받아야 합니다. 이는 실행 중인 운영 체제, 그 안에 배포 된 응용 프로그램 및 새 컴퓨터를 만드는 이미지를 포함 하는 가상 환경에도 적용 됩니다. 공급 업체 패치 알림 통신을 따르고 적시에 패치를 적용 합니다. 새로 배포 된 컴퓨터를 최대한 안전 하 게 유지 하기 위해 매월 기본 이미지를 패치 하는 것이 좋습니다.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>최대 비활성 시간 및 연결 끊기 정책 설정

비활성 상태일 때 사용자를 서명 하면 리소스가 유지 되 고 권한이 없는 사용자가 액세스할 수 없게 됩니다. 제한 시간을 사용 하 여 리소스 사용과 사용자의 생산성을 분산 하는 것이 좋습니다. 상태 비저장 응용 프로그램과 상호 작용 하는 사용자의 경우 컴퓨터를 끄고 리소스를 유지 하는 보다 적극적인 정책을 고려 하세요. 시뮬레이션 또는 CAD 렌더링과 같이 사용자가 유휴 상태일 경우 계속 실행 되는 장기 실행 응용 프로그램을 분리 하면 사용자의 작업이 중단 되 고 컴퓨터를 다시 시작 해야 할 수도 있습니다.

### <a name="set-up-screen-locks-for-idle-sessions"></a>유휴 세션의 화면 잠금 설정

유휴 시간에 컴퓨터 화면을 잠그고 인증을 사용 하 여 잠금을 해제 하도록 Windows 가상 데스크톱을 구성 하 여 원치 않는 시스템 액세스를 방지할 수 있습니다.

### <a name="establish-tiered-admin-access"></a>계층화 된 관리자 액세스 설정

사용자에 게 가상 데스크톱에 대 한 관리자 액세스 권한을 부여 하지 않는 것이 좋습니다. 소프트웨어 패키지가 필요한 경우 Microsoft Endpoint Manager와 같은 구성 관리 유틸리티를 통해 사용할 수 있도록 설정 하는 것이 좋습니다. 다중 세션 환경에서는 사용자가 소프트웨어를 직접 설치할 수 없도록 하는 것이 좋습니다.

### <a name="consider-which-users-should-access-which-resources"></a>어떤 사용자가 어떤 리소스에 액세스 해야 하는지 고려

세션 호스트를 기존 데스크톱 배포의 확장으로 간주 합니다. 네트워크 조각화 및 필터링을 사용 하는 등 환경의 다른 데스크톱에서와 동일한 방법으로 네트워크 리소스에 대 한 액세스를 제어 하는 것이 좋습니다. 기본적으로 세션 호스트는 인터넷의 모든 리소스에 연결할 수 있습니다. Azure 방화벽, 네트워크 가상 어플라이언스 또는 프록시를 사용 하는 등의 여러 가지 방법으로 트래픽을 제한할 수 있습니다. 트래픽을 제한 해야 하는 경우 Windows 가상 데스크톱이 제대로 작동할 수 있도록 적절 한 규칙을 추가 해야 합니다.

### <a name="manage-office-pro-plus-security"></a>Office Pro Plus 보안 관리

세션 호스트를 보호 하는 것 외에도 내부에서 실행 되는 응용 프로그램을 보호 하는 것이 중요 합니다. Office Pro Plus는 세션 호스트에 배포 된 가장 일반적인 응용 프로그램 중 하나입니다. Office 배포 보안을 향상 시키려면 enterprise 용 Microsoft 365 앱에 대 한 [보안 정책 관리자](/DeployOffice/overview-of-security-policy-advisor) 를 사용 하는 것이 좋습니다. 이 도구는 더 많은 보안을 위해 배포에 적용할 수 있는 정책을 식별 합니다. 보안 정책 관리자는 보안과 생산성에 미치는 영향에 따라 정책을 권장 합니다.

### <a name="other-security-tips-for-session-hosts"></a>세션 호스트에 대 한 기타 보안 팁

운영 체제 기능을 제한 하 여 세션 호스트의 보안을 강화할 수 있습니다. 수행할 수 있는 몇 가지 작업은 다음과 같습니다.

- 원격 데스크톱 세션에서 드라이브, 프린터 및 USB 장치를 사용자의 로컬 장치로 리디렉션하여 장치 리디렉션을 제어 합니다. 보안 요구 사항을 평가 하 고 이러한 기능을 사용 하지 않도록 설정 해야 하는지 확인 하는 것이 좋습니다.

- 로컬 및 원격 드라이브 매핑을 숨겨서 Windows 탐색기 액세스를 제한 합니다. 이렇게 하면 사용자가 시스템 구성 및 사용자에 대 한 원치 않는 정보를 검색할 수 없습니다.

- 사용자 환경에서 세션 호스트에 대 한 직접 RDP 액세스를 방지 합니다. 관리 또는 문제 해결을 위해 RDP에 직접 액세스 해야 하는 경우 [just-in-time](../security-center/security-center-just-in-time.md) 액세스를 사용 하 여 세션 호스트에서 잠재적 공격 노출 영역을 제한할 수 있습니다.

- 로컬 및 원격 파일 시스템에 액세스할 때 사용자에 게 제한 된 권한을 부여 합니다. 로컬 및 원격 파일 시스템에서 최소 권한으로 액세스 제어 목록을 사용 하도록 하 여 권한을 제한할 수 있습니다. 이러한 방식으로 사용자는 필요한 항목에만 액세스할 수 있으며 중요 한 리소스를 변경 하거나 삭제할 수 없습니다.

- 세션 호스트에서 원치 않는 소프트웨어가 실행 되지 않도록 합니다. 세션 호스트에서 추가 보안을 위해 앱 보관을 사용 하도록 설정 하 여 허용 되는 앱만 호스트에서 실행 되도록 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Multi-factor authentication을 사용 하도록 설정 하는 방법을 알아보려면 [multi-factor Authentication 설정](set-up-mfa.md)을 참조 하세요.