---
title: Azure Active Directory 일반 작업 가이드 참조
description: 이 작업 참조 가이드에서는 일반 작업을 보호하기 위해 수행해야 하는 검사 및 작업에 대해 설명합니다.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 74410025e148eb60438a020164837dac486d2ad3
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111955006"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Azure Active Directory 일반 작업 가이드 참조

[Azure AD 작업 참조 가이드](active-directory-ops-guide-intro.md)의 이 섹션에서는 Azure Active Directory(Azure AD)의 일반 작업을 최적화하기 위해 수행해야 하는 검사 및 작업에 대해 설명합니다.

> [!NOTE]
> 이 권장 사항은 게시 날짜를 기준으로 최신이지만 시간이 지나면 변경될 수 있습니다. 시간이 지남에 따라 Microsoft 제품 및 서비스가 발전하므로 조직은 운영 사례를 지속해서 평가해야 합니다.

## <a name="key-operational-processes"></a>주요 운영 프로세스

### <a name="assign-owners-to-key-tasks"></a>주요 작업에 소유자 할당

Azure Active Directory를 관리하려면 출시 프로젝트의 일부가 아닐 수 있는 주요 운영 작업 및 프로세스를 지속해서 실행해야 합니다. 환경을 최적화하기 위해 이 작업을 설정하는 것도 여전히 중요합니다. 핵심 작업과 권장 소유자는 다음과 같습니다.

| Task | 소유자 |
| :- | :- |
| ID 보안 점수 개선 추진 | InfoSec 운영 팀 |
| Azure AD Connect 서버 유지 관리 | IAM 운영 팀 |
| 정기적으로 IdFix 보고서 실행 및 심사 | IAM 운영 팀 |
| 동기화 및 AD FS에 대한 Azure AD Connect Health 경고 심사 | IAM 운영 팀 |
| Azure AD Connect Health를 사용하지 않는 경우 고객에게 사용자 지정 인프라를 모니터링하는 동일한 프로세스 및 도구가 있습니다. | IAM 운영 팀 |
| AD FS를 사용하지 않는 경우 고객에게 사용자 지정 인프라를 모니터링하는 동일한 프로세스 및 도구가 있습니다. | IAM 운영 팀 |
| 하이브리드 로그 모니터링: Azure AD 앱 프록시 커넥터 | IAM 운영 팀 |
| 하이브리드 로그 모니터링: 통과 인증 에이전트 | IAM 운영 팀 |
| 하이브리드 로그 모니터링: 비밀번호 쓰기 저장 서비스 | IAM 운영 팀 |
| 하이브리드 로그 모니터링: 온-프레미스 암호 보호 게이트웨이 | IAM 운영 팀 |
| 하이브리드 로그 모니터링: Azure AD MFA NPS 확장(해당하는 경우) | IAM 운영 팀 |

목록을 검토할 때 소유자가 누락된 작업에 대해 소유자를 할당하거나 위의 권장 사항에 맞지 않는 소유자가 있는 작업에 대한 소유권을 조정해야 할 수도 있습니다.

#### <a name="owners-recommended-reading"></a>소유자 권장 참조 항목

- [Azure Active Directory에서 관리자 역할 할당](../roles/permissions-reference.md)
- [Azure에서 거버넌스](../../governance/index.yml)

## <a name="hybrid-management"></a>하이브리드 관리

### <a name="recent-versions-of-on-premises-components"></a>최신 버전의 온-프레미스 구성 요소

최신 버전의 온-프레미스 구성 요소를 사용하면 고객에게 환경을 더욱 간소화하는 데 도움이 되는 기능뿐만 아니라 모든 최신 보안 업데이트, 성능 향상을 제공합니다. 대부분의 구성 요소에는 업그레이드 프로세스를 자동화하는 자동 업그레이드 설정이 있습니다.

이러한 구성 요소는 다음과 같습니다.

- Azure AD Connect
- Azure AD 애플리케이션 프록시 커넥터
- Azure AD 통과 인증 에이전트
- Azure AD Connect Health 에이전트

구성 요소가 설정되지 않은 경우 이러한 구성 요소를 업그레이드하는 프로세스를 정의하고 가능하면 자동 업그레이드 기능을 사용해야 합니다. 6개월 이상 지난 구성 요소를 찾으면 최대한 빨리 업그레이드해야 합니다.

#### <a name="hybrid-management-recommended-reading"></a>하이브리드 관리 권장 참조 항목

- [Azure AD Connect: 자동 업그레이드](../hybrid/how-to-connect-install-automatic-upgrade.md)
- [Azure AD 애플리케이션 프록시 커넥터 이해 | 자동 업데이트](../app-proxy/application-proxy-connectors.md#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health 경고 기준

조직은 Azure AD Connect 및 AD FS의 모니터링 및 보고를 위해 [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health)를 배포해야 합니다. Azure AD Connect 및 AD FS는 수명 주기 관리 및 인증을 중단하여 작동이 중단될 수 있는 중요한 구성 요소입니다. Azure AD Connect Health를 사용하면 온-프레미스 ID 인프라를 모니터링하고 파악할 수 있으므로 사용자 환경의 신뢰성을 보장합니다.

![Azure AD Connect Health 아키텍처](./media/active-directory-ops-guide/active-directory-ops-img16.png)

환경의 상태를 모니터링할 때 심각도가 높은 경고를 즉시 처리한 다음 심각도가 낮은 경고를 처리해야 합니다.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health 권장 참조 항목

- [Azure AD Connect Health Agent 설치](../hybrid/how-to-connect-health-agent-install.md)

### <a name="on-premises-agents-logs"></a>온-프레미스 에이전트 로그

일부 ID 및 액세스 관리 서비스에는 하이브리드 시나리오를 사용하기 위해 온-프레미스 에이전트가 필요합니다. 예를 들어 암호 재설정, PTA(통과 인증), Azure AD 애플리케이션 프록시 및 Azure AD MFA NPS 확장이 있습니다. 운영 팀은 System Center Operations Manager 또는 SIEM과 같은 솔루션을 사용하여 구성 요소 에이전트 로그를 보관 및 분석하여 이러한 구성 요소의 상태를 기준으로 설정하고 모니터링하는 것이 중요합니다. Infosec 운영 팀이나 지원 센터에서 오류 패턴 문제를 해결하는 방법을 이해하는 것도 마찬가지로 중요합니다.

#### <a name="on-premises-agents-logs-recommended-reading"></a>온-프레미스 에이전트 로그 권장 참조 항목

- [애플리케이션 프록시 문제 해결](../app-proxy/application-proxy-troubleshoot.md)
- [셀프 서비스 암호 재설정 문제 해결 - Azure Active Directory](../authentication/troubleshoot-sspr.md)
- [Azure AD 애플리케이션 프록시 커넥터 이해](../app-proxy/application-proxy-connectors.md)
- [Azure AD Connect: 통과 인증 문제 해결](../hybrid/tshoot-connect-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)
- [Azure AD MFA NPS 확장에 대한 오류 코드 문제 해결](../authentication/howto-mfa-nps-extension-errors.md)

### <a name="on-premises-agents-management"></a>온-프레미스 에이전트 관리

모범 사례를 채택하면 온-프레미스 에이전트의 최적 운영에 도움이 될 수 있습니다. 다음 모범 사례를 고려합니다.

- 프록시 애플리케이션에 액세스할 때 단일 실패 지점을 방지하여 원활한 부하 분산 및 고가용성을 제공하려면 커넥터 그룹당 여러 Azure AD 애플리케이션 프록시 커넥터를 사용하는 것이 좋습니다. 현재 프로덕션에서 애플리케이션을 처리하는 커넥터 그룹에 커넥터가 하나만 있는 경우 중복도를 위해 두 개 이상의 커넥터를 배포해야 합니다.
- 디버깅 목적으로 앱 프록시 커넥터 그룹을 만들고 사용하면 문제 해결 시나리오와 새로운 온-프레미스 애플리케이션을 온보딩할 때 유용할 수 있습니다. 또한 커넥터 시스템에 Message Analyzer 및 Fiddler와 같은 네트워킹 도구를 설치하는 것이 좋습니다.
- 인증 흐름 중 단일 실패 지점을 방지하여 원활한 부하 분산 및 고가용성을 제공하려면 여러 통과 인증 에이전트를 사용하는 것이 좋습니다. 중복도를 위해 두 개 이상의 통과 인증 에이전트를 배포해야 합니다.

#### <a name="on-premises-agents-management-recommended-reading"></a>온-프레미스 에이전트 관리 권장 참조 항목

- [Azure AD 애플리케이션 프록시 커넥터 이해](../app-proxy/application-proxy-connectors.md)
- [Azure AD 통과 인증 - 빠른 시작](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>대규모 관리

### <a name="identity-secure-score"></a>ID 보안 점수

[ID 보안 점수](./identity-secure-score.md)는 조직의 보안 태세에 대한 정량적 측정값을 제공합니다. 보고된 결과를 지속적으로 검토하여 해결하고 가능한 최고 점수를 받기 위해 노력하는 것이 중요합니다. 점수는 다음에 도움이 됩니다.

- ID 보안 상태를 객관적으로 측정
- ID 보안 개선 계획
- 개선 계획의 성공 여부 검토

![보안 점수](./media/active-directory-ops-guide/active-directory-ops-img17.png)

조직에 현재 ID 보안 점수의 변경 사항을 모니터링하는 프로그램이 없는 경우 계획을 구현하고 모니터링 및 개선 조치를 추진할 소유자를 지정하는 것이 좋습니다. 조직은 점수 영향이 30보다 높은 개선 작업을 가능한 한 빨리 수정해야 합니다.

### <a name="notifications"></a>공지

Microsoft는 서비스의 다양한 변경 사항, 필요한 구성 업데이트 및 관리자 개입이 필요한 오류를 알리기 위해 관리자에게 이메일 통신을 보냅니다. 모든 알림을 확인하고 조치를 취할 수 있는 적절한 팀 구성원에게 알림이 전송되도록 고객이 알림 이메일 주소를 설정하는 것이 중요합니다. 여러 수신자를 [메시지 센터](/office365/admin/manage/message-center)에 추가하고 알림(Azure AD Connect Health 알림 포함)을 배포 목록이나 공유 사서함으로 보내도록 요청하는 것이 좋습니다. 이메일 주소가 있는 글로벌 관리자 계정이 하나만 있는 경우 이메일 가능 계정을 두 개 이상 구성해야 합니다.

Azure AD에서 사용하는 두 개의 "보낸 사람" 주소는 메시지 센터 알림을 보내는 <o365mc@email2.microsoft.com>과 관련된 알림을 보내는 <azure-noreply@microsoft.com>입니다.

- [Azure AD 액세스 검토](../governance/access-reviews-overview.md)
- [Azure AD Connect Health](../hybrid/how-to-connect-health-operations.md#enable-email-notifications)
- [Azure AD ID 보호](../identity-protection/howto-identity-protection-configure-notifications.md)
- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-email-notifications.md)
- [엔터프라이즈 앱 인증서 만료 알림](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)
- 엔터프라이즈 앱 서비스 프로비저닝 알림

전송되는 알림의 유형과 알림을 확인할 위치를 알아보려면 다음 표를 참조하세요.

| 알림 원본 | 전달 내용 | 확인할 위치 |
|:-|:-|:-|
| 기술 담당자 | 동기화 오류 | Azure Portal - 속성 블레이드 |
| 메시지 센터 | ID 서비스 및 Microsoft 365 백 엔드 서비스의 인시던트 및 성능 저하 알림 | Office 포털 |
| ID 보호 주별 다이제스트 | ID 보호 다이제스트 | Azure AD ID 보호 블레이드 |
| Azure AD Connect Health | 경고 알림 | Azure Portal - Azure AD Connect Health 블레이드 |
| 엔터프라이즈 애플리케이션 알림 | 인증서 만료 및 프로비저닝 오류 알림 | Azure Portal - 엔터프라이즈 애플리케이션 블레이드(각 앱에 고유한 이메일 주소 설정이 있음) |

#### <a name="notifications-recommended-reading"></a>알림 권장 참조 항목

- [조직의 주소, 기술 담당자 등 변경](/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>작업 노출 영역

### <a name="ad-fs-lockdown"></a>AD FS 잠금

Azure AD에 직접 인증하도록 애플리케이션을 구성하는 조직은 [Azure AD 스마트 잠금](../authentication/concept-sspr-howitworks.md)의 이점을 활용합니다. Windows Server 2012 R2에서 AD FS를 사용하는 경우 AD FS [엑스트라넷 잠금 보호](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)를 구현합니다. Windows Server 2016 이상에서 AD FS를 사용하는 경우 [엑스트라넷 스마트 잠금](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)을 구현합니다. 최소한 온-프레미스 Active Directory에 대한 무차별 암호 대입 공격의 위험을 포함하도록 엑스트라넷 잠금을 사용하는 것이 좋습니다. 그러나 Windows 2016 이상에 AD FS가 있는 경우 [암호 스프레이](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) 공격을 완화하는 데 도움이 되는 엑스트라넷 스마트 잠금도 사용해야 합니다.

AD FS가 Azure AD 페더레이션에만 사용되는 경우 공격 노출 영역을 최소화하기 위해 해제할 수 있는 몇 가지 엔드포인트가 있습니다. 예를 들어 AD FS가 Azure AD에만 사용되는 경우 **usernamemixed** 및 **windowstransport** 에 대해 사용하도록 설정된 엔드포인트가 아닌 WS-Trust 엔드포인트를 사용하지 않아야 합니다.

### <a name="access-to-machines-with-on-premises-identity-components"></a>온-프레미스 ID 구성 요소가 있는 컴퓨터에 대한 액세스

조직은 온-프레미스 도메인과 동일한 방식으로 온-프레미스 하이브리드 구성 요소가 있는 컴퓨터에 대한 액세스를 잠가야 합니다. 예를 들어 백업 운영자 또는 Hyper-V 관리자는 Azure AD Connect 서버에 로그인하여 규칙을 변경할 수 없어야 합니다.

Active Directory 관리 계층 모델은 환경의 전체 제어(계층 0)와 공격자가 자주 손상시킬 위험이 높은 워크스테이션 자산 간의 버퍼 영역 집합을 사용하여 ID 시스템을 보호하도록 설계되었습니다. ![계층 모델의 세 가지 계층을 보여 주는 다이어그램](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[계층 모델](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)은 세 가지 수준으로 구성되며 관리 계정만 포함하고 표준 사용자 계정은 포함하지 않습니다.

- **계층 0** - 환경 내 엔터프라이즈 ID를 직접 제어합니다. 계층 0에는 Active Directory 포리스트, 도메인 또는 도메인 컨트롤러와 그 속의 모든 자산에 대한 직접 또는 간접 관리 권한을 가진 계정, 그룹 및 기타 자산이 포함됩니다. 모든 계층 0 자산은 서로를 효과적으로 제어하므로 보안 민감도가 동일합니다.
- **계층 1** - 엔터프라이즈 서버 및 애플리케이션을 제어합니다. 계층 1 자산에는 서버 운영 체제, 클라우드 서비스 및 엔터프라이즈 애플리케이션이 포함됩니다. 계층 1 관리자 계정은 이러한 자산에서 호스트되는 상당한 양의 비즈니스 가치에 대한 관리 권한이 있습니다. 일반적인 역할 예로는 모든 엔터프라이즈 서비스에 영향을 줄 수 있는 기능으로 이러한 운영 체제를 유지 관리하는 서버 관리자가 있습니다.
- **계층 2** - 사용자 워크스테이션 및 디바이스를 제어합니다. 계층 2 관리자 계정은 사용자 워크스테이션 및 디바이스에서 호스트되는 상당한 양의 비즈니스 가치에 대한 관리 권한이 있습니다. 예를 들어 지원 센터 및 컴퓨터 지원 관리자가 여기에 포함됩니다. 이들은 거의 모든 사용자 데이터의 무결성에 영향을 줄 수 있기 때문입니다.

도메인 컨트롤러에 대해 수행하는 것과 동일한 방식으로 Azure AD Connect, AD FS 및 SQL 서비스와 같은 온-프레미스 ID 구성 요소에 대한 액세스를 잠급니다.

## <a name="summary"></a>요약

보안 ID 인프라에는 7가지 측면이 있습니다. 이 목록은 Azure AD(Azure Active Directory) 작업을 최적화하기 위해 수행해야 하는 작업을 찾는 데 도움이 됩니다.

- 주요 작업에 소유자를 할당합니다.
- 온-프레미스 하이브리드 구성 요소에 대한 업그레이드 프로세스를 자동화합니다.
- Azure AD Connect 및 AD FS의 모니터링 및 보고를 위해 Azure AD Connect Health를 배포합니다.
- System Center Operations Manager 또는 SIEM 솔루션을 사용하여 구성 요소 에이전트 로그를 보관 및 분석하여 온-프레미스 하이브리드 구성 요소의 상태를 모니터링합니다.
- ID 보안 점수를 통해 보안 태세를 측정하여 보안 향상을 구현합니다.
- AD FS를 잠급니다.
- 온-프레미스 ID 구성 요소가 있는 컴퓨터에 대한 액세스를 잠급니다.

## <a name="next-steps"></a>다음 단계

배포하지 않은 기능에 대한 구현 세부 정보는 [Azure AD 배포 계획](active-directory-deployment-plans.md)을 참조하세요.