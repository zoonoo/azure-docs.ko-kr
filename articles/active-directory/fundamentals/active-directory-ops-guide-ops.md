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
ms.openlocfilehash: d039373d3e70076149da2b970a234b59d7aa661a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422953"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Azure Active Directory 일반 작업 가이드 참조

[Azure AD 작업 참조 가이드의](active-directory-ops-guide-intro.md) 이 섹션에서는 Azure Active Directory(Azure AD)의 일반 작업을 최적화하기 위해 수행해야 하는 검사 및 작업에 대해 설명합니다.

> [!NOTE]
> 이러한 권장 사항은 게시 날짜를 기준으로 최신이지만 시간이 지남에 따라 변경될 수 있습니다. Microsoft 제품 및 서비스가 시간이 지남에 따라 발전함에 따라 조직은 운영 관행을 지속적으로 평가해야 합니다.

## <a name="key-operational-processes"></a>주요 운영 프로세스

### <a name="assign-owners-to-key-tasks"></a>주요 작업에 소유자 할당

Azure Active Directory를 관리하려면 롤아웃 프로젝트의 일부가 아닐 수 있는 주요 운영 작업 및 프로세스를 지속적으로 실행해야 합니다. 환경을 최적화하기 위해 이러한 작업을 설정하는 것은 여전히 중요합니다. 주요 작업 및 권장 소유자는 다음과 같습니다.

| Task | 소유자 |
| :- | :- |
| ID 보안 점수에 대한 개선 사항 | 인포섹 운영 팀 |
| Azure AD 연결 서버 유지 관리 | IAM 운영 팀 |
| IdFix 보고서를 정기적으로 실행하고 심사합니다. | IAM 운영 팀 |
| 동기화 및 AD FS에 대 한 Azure AD 연결 상태 경고 심사 | IAM 운영 팀 |
| Azure AD Connect Health를 사용하지 않는 경우 고객은 사용자 지정 인프라를 모니터링하는 동일한 프로세스 및 도구가 있습니다. | IAM 운영 팀 |
| AD FS를 사용하지 않는 경우 고객은 사용자 지정 인프라를 모니터링하는 동일한 프로세스와 도구를 가지고 있습니다. | IAM 운영 팀 |
| 하이브리드 로그 모니터링: Azure AD 앱 프록시 커넥터 | IAM 운영 팀 |
| 하이브리드 로그 모니터링: 통과 인증 에이전트 | IAM 운영 팀 |
| 하이브리드 로그 모니터링: 암호 쓰기 백 서비스 | IAM 운영 팀 |
| 하이브리드 로그 모니터링: 온-프레미스 암호 보호 게이트웨이 | IAM 운영 팀 |
| 하이브리드 로그 모니터링: Azure MFA NPS 확장(해당하는 경우) | IAM 운영 팀 |

목록을 검토할 때 소유자가 없는 작업에 대해 소유자를 할당하거나 위의 권장 사항과 일치하지 않는 소유자와 작업에 대한 소유권을 조정해야 할 수 있습니다.

#### <a name="owners-recommended-reading"></a>소유자는 독서를 권장

- [Azure Active Directory에서 관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure에서 거버넌스](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>하이브리드 관리

### <a name="recent-versions-of-on-premises-components"></a>온-프레미스 구성 요소의 최신 버전

온-프레미스 구성 요소의 최신 버전을 사용하면 고객에게 환경을 더욱 단순화하는 데 도움이 되는 모든 최신 보안 업데이트, 성능 향상 및 기능을 제공합니다. 대부분의 구성 요소에는 업그레이드 프로세스가 자동화되는 자동 업그레이드 설정이 있습니다.

이러한 구성 요소는 다음과 같습니다.

- Azure AD Connect
- Azure AD 애플리케이션 프록시 커넥터
- Azure AD 통과 인증 에이전트
- Azure AD 연결 상태 에이전트

설정되지 않은 경우 이러한 구성 요소를 업그레이드하는 프로세스를 정의하고 가능하면 자동 업그레이드 기능을 사용해야 합니다. 6개월 이상 뒤에 있는 구성 요소를 발견하면 가능한 한 빨리 업그레이드해야 합니다.

#### <a name="hybrid-management-recommended-reading"></a>하이브리드 관리 권장 읽기

- [Azure AD Connect: 자동 업그레이드](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [Azure AD 응용 프로그램 프록시 커넥터 이해 | 자동 업데이트](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD 연결 상태 경고 기준

조직은 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health) 및 AD FS의 모니터링 및 보고를 위해 Azure AD Connect 상태를 배포해야 합니다. Azure AD 연결 및 AD FS는 수명 주기 관리 및 인증을 중단하여 중단될 수 있는 중요한 구성 요소입니다. Azure AD Connect Health를 사용하면 온-프레미스 ID 인프라를 모니터링하고 통찰력을 얻을 수 있으므로 환경의 안정성을 보장할 수 있습니다.

![Azure AD Connect 히스 아키텍처](./media/active-directory-ops-guide/active-directory-ops-img16.png)

환경의 상태를 모니터링할 때 심각도가 높은 경고를 즉시 해결한 다음 심각도 가중도가 낮은 경고를 처리해야 합니다.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD 연결 상태 권장 읽기

- [Azure AD Connect Health Agent 설치](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>온-프레미스 에이전트 로그

일부 ID 및 액세스 관리 서비스에는 하이브리드 시나리오를 사용하도록 설정하기 위해 온-프레미스 에이전트가 필요합니다. 예를 들어 암호 재설정, 통과 인증(PTA), Azure AD 응용 프로그램 프록시 및 Azure MFA NPS 확장을 예로 들 수 있습니다. 운영 팀이 시스템 센터 운영 관리자 또는 SIEM과 같은 솔루션을 사용하여 구성 요소 에이전트 로그를 보관하고 분석하여 이러한 구성 요소의 상태를 기준화하고 모니터링하는 것이 중요합니다. Infosec 운영 팀이나 헬프 데스크에서 오류 패턴 문제를 해결하는 방법을 이해하는 것 또한 똑같이 중요합니다.

#### <a name="on-premises-agents-logs-recommended-reading"></a>온-프레미스 에이전트로그 권장 읽기

- [애플리케이션 프록시 문제 해결](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [셀프 서비스 암호 재설정 문제 해결 - Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [Azure AD 응용 프로그램 프록시 커넥터 이해](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD 연결: 통과 인증 문제 해결](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [Azure MFA NPS 확장에 대한 오류 코드 문제 해결](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>온-프레미스 에이전트 관리

모범 사례를 채택하면 온-프레미스 에이전트의 최적의 운영에 도움이 될 수 있습니다. 다음 모범 사례를 고려하십시오.

- 커넥터 그룹당 여러 Azure AD 응용 프로그램 프록시 커넥터는 프록시 응용 프로그램에 액세스할 때 단일 실패 지점을 방지하여 원활한 부하 분산 및 고가용성을 제공하는 것이 좋습니다. 현재 프로덕션 환경에서 응용 프로그램을 처리하는 커넥터 그룹에 커넥터가 하나만 있는 경우 중복성을 위해 두 개 이상의 커넥터를 배포해야 합니다.
- 디버깅을 위해 앱 프록시 커넥터 그룹을 만들고 사용하는 것은 시나리오 문제를 해결하는 데 유용하며 새 온-프레미스 응용 프로그램을 온보딩할 때 유용할 수 있습니다. 또한 커넥터 컴퓨터에 메시지 분석기 및 Fiddler와 같은 네트워킹 도구를 설치하는 것이 좋습니다.
- 인증 흐름 중에 단일 실패 지점을 방지하여 원활한 로드 분산 및 고가용성을 제공하기 위해 여러 통과 인증 에이전트를 사용하는 것이 좋습니다. 중복성을 위해 두 개 이상의 통과 인증 에이전트를 배포해야 합니다.

#### <a name="on-premises-agents-management-recommended-reading"></a>온-프레미스 에이전트 관리 권장 읽기

- [Azure AD 응용 프로그램 프록시 커넥터 이해](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Azure AD 통과 인증 - 빠른 시작](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>규모의 관리

### <a name="identity-secure-score"></a>ID 보안 점수

[ID 보안 점수는](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score) 조직의 보안 상태를 정량화할 수 있는 척도를 제공합니다. 보고된 결과를 지속적으로 검토하고 해결하며 가능한 가장 높은 점수를 갖기 위해 노력하는 것이 중요합니다. 점수는 다음에 도움이 됩니다.

- ID 보안 상태를 객관적으로 측정
- ID 보안 개선 계획
- 개선 계획의 성공 여부 검토

![보안 점수](./media/active-directory-ops-guide/active-directory-ops-img17.png)

현재 조직에 ID 보안 점수의 변경 내용을 모니터링할 프로그램이 없는 경우 계획을 구현하고 소유자에게 개선 작업을 모니터링하고 실행하도록 지정하는 것이 좋습니다. 조직은 점수 영향이 30보다 높은 개선 작업을 가능한 한 빨리 수정해야 합니다.

### <a name="notifications"></a>공지

Microsoft는 관리자에게 전자 메일 통신을 전송하여 서비스의 다양한 변경 사항, 필요한 구성 업데이트 및 관리자 개입이 필요한 오류를 알립니다. 모든 알림을 승인하고 조치를 취하여 적절한 팀 구성원에게 알림을 보내도록 고객이 알림 이메일 주소를 설정하는 것이 중요합니다. [Office 365 메시지 센터에](https://docs.microsoft.com/office365/admin/manage/message-center) 여러 받는 사람을 추가하고 Azure AD Connect 상태 알림을 포함한 알림이 메일 그룹 또는 공유 사서함으로 전송되도록 요청하는 것이 좋습니다. 이메일 주소가 있는 글로벌 관리자 계정이 하나만 있는 경우 이메일이 가능한 계정을 두 개 이상 구성해야 합니다.

Azure AD에서 사용하는 두 개의 "보낸 <o365mc@email2.microsoft.com>보낸" 주소가 있습니다. 및 <azure-noreply@microsoft.com>및, 관련 알림을 보냅니다.

- [Azure AD 액세스 검토](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD ID 보호](/azure/active-directory/identity-protection/howto-identity-protection-configure-notifications)
- [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [엔터프라이즈 앱 인증서 만료 알림](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- 엔터프라이즈 앱 서비스 프로비저닝 알림

전송되는 알림의 유형과 해당 알림의 위치를 확인하려면 다음 표를 참조하십시오.

| 알림 소스 | 전송되는 내용 | 확인 위치 |
|:-|:-|:-|
| 기술 연락처 | 동기화 오류 | Azure 포털 - 속성 블레이드 |
| Office 365 메시지 센터 | ID 서비스 및 O365 백 엔드 서비스의 인시던트 및 성능 저하 알림 | 사무실 포털 |
| ID 보호 주간 다이제스트 | ID 보호 다이제스트 | Azure AD ID 보호 블레이드 |
| Azure AD Connect Health | 경고 알림 | Azure 포털 - Azure AD 연결 상태 블레이드 |
| 엔터프라이즈 응용 프로그램 알림 | 인증서가 만료될 때 알림 및 프로비저닝 오류 | Azure 포털 - 엔터프라이즈 응용 프로그램 블레이드(각 앱에는 자체 전자 메일 주소 설정이 있음) |

#### <a name="notifications-recommended-reading"></a>알림 권장 읽기

- [조직의 주소, 기술 담당자 등을 변경 - Office 365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>작동 표면적

### <a name="ad-fs-lockdown"></a>AD FS 잠금

Azure AD에 직접 인증하도록 응용 프로그램을 구성하는 조직은 [Azure AD 스마트 잠금의](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)이점을 누릴 수 있습니다. Windows Server 2012 R2에서 AD FS를 사용하는 경우 AD FS [엑스트라넷 잠금 보호를](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)구현합니다. Windows Server 2016 이상에서 AD FS를 사용하는 경우 [엑스트라넷 스마트 잠금을 구현합니다.](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016) 최소한 엑스트라넷 잠금을 사용하도록 설정하면 온-프레미스 Active Directory에 대한 무차별 암호 대입 공격의 위험이 포함되는 것이 좋습니다. 그러나 Windows 2016 이상에 AD FS가 있는 경우 [암호 스프레이](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) 공격을 완화하는 데 도움이 되는 엑스트라넷 스마트 잠금도 활성화해야 합니다.

AD FS가 Azure AD 페더레이션에만 사용되는 경우 공격 노출 영역을 최소화하기 위해 해제할 수 있는 몇 가지 끝점이 있습니다. 예를 들어 AD FS가 Azure AD에만 사용되는 경우 **사용자 이름 혼합** 및 **windowstransport에**대해 사용하도록 설정된 끝점이 아닌 WS-Trust 끝점을 사용하지 않도록 설정해야 합니다.

### <a name="access-to-machines-with-on-premises-identity-components"></a>온-프레미스 ID 구성 요소가 있는 컴퓨터에 대한 액세스

조직은 온-프레미스 도메인과 동일한 방식으로 온-프레미스 하이브리드 구성 요소를 사용하여 컴퓨터에 대한 액세스를 잠가야 합니다. 예를 들어 백업 운영자 또는 Hyper-V 관리자는 Azure AD Connect Server에 로그인하여 규칙을 변경할 수 없습니다.

Active Directory 관리 계층 모델은 환경(계층 0)과 공격자가 자주 손상시키는 고위험 워크스테이션 자산 사이의 버퍼 영역 집합을 사용하여 ID 시스템을 보호하도록 설계되었습니다. ![계층 모델의 세 가지 계층을 보여 주는 다이어그램](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[계층 모델은](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) 세 가지 수준으로 구성되며 표준 사용자 계정이 아닌 관리 계정만 포함합니다.

- **계층 0** - 환경에서 엔터프라이즈 ID를 직접 제어합니다. 계층 0에는 Active Directory 포리스트, 도메인 또는 도메인 컨트롤러와 그 속의 모든 자산에 대한 직접 또는 간접 관리 권한을 가진 계정, 그룹 및 기타 자산이 포함됩니다. 모든 계층 0 자산은 서로를 효과적으로 제어하므로 보안 민감도가 동일합니다.
- **계층 1** - 엔터프라이즈 서버 및 응용 프로그램 제어. 계층 1 자산에는 서버 운영 체제, 클라우드 서비스 및 엔터프라이즈 애플리케이션이 포함됩니다. 계층 1 관리자 계정은 이러한 자산에서 호스트되는 상당한 양의 비즈니스 가치에 대한 관리 권한이 있습니다. 일반적인 역할 예로는 모든 엔터프라이즈 서비스에 영향을 줄 수 있는 기능으로 이러한 운영 체제를 유지 관리하는 서버 관리자가 있습니다.
- **계층 2** - 사용자 워크스테이션 및 장치 제어. 계층 2 관리자 계정은 사용자 워크스테이션 및 디바이스에서 호스트되는 상당한 양의 비즈니스 가치에 대한 관리 권한이 있습니다. 예를 들어 지원 센터 및 컴퓨터 지원 관리자가 여기에 포함됩니다. 이들은 거의 모든 사용자 데이터의 무결성에 영향을 줄 수 있기 때문입니다.

Azure AD Connect, AD FS 및 SQL 서비스와 같은 온-프레미스 ID 구성 요소에 대한 액세스를 도메인 컨트롤러와 동일한 방식으로 잠급합니다.

## <a name="summary"></a>요약

보안 ID 인프라에는 7가지 측면이 있습니다. 이 목록은 Azure Active Directory(Azure AD)에 대한 작업을 최적화하기 위해 수행해야 하는 작업을 찾는 데 도움이 됩니다.

- 주요 작업에 소유자를 할당합니다.
- 온-프레미스 하이브리드 구성 요소에 대한 업그레이드 프로세스를 자동화합니다.
- Azure AD Connect 및 AD FS의 모니터링 및 보고를 위해 Azure AD 연결 상태를 배포합니다.
- 시스템 센터 운영 관리자 또는 SIEM 솔루션을 사용하여 구성 요소 에이전트 로그를 보관하고 분석하여 온-프레미스 하이브리드 구성 요소의 상태를 모니터링합니다.
- ID 보안 점수로 보안 상태를 측정하여 보안 개선 사항을 구현합니다.
- AD FS를 잠급합니다.
- 온-프레미스 ID 구성 요소가 있는 컴퓨터에 대한 액세스를 잠급니다.

## <a name="next-steps"></a>다음 단계

배포하지 않은 기능에 대한 구현 세부 정보는 [Azure AD 배포 계획을](active-directory-deployment-plans.md) 참조하십시오.
