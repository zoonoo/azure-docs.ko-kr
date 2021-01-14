---
title: 온-프레미스 공격 으로부터 Microsoft 365 보호
description: 온-프레미스 공격에 영향을 주지 않도록 하는 방법에 대 한 지침 Microsoft 365
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97893dece068dfdde85159f734095401288231d2
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201351"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>온-프레미스 공격 으로부터 Microsoft 365 보호

많은 고객이 자신의 개인 회사 네트워크를 Microsoft 365에 연결 하 여 사용자, 장치 및 응용 프로그램을 활용 합니다. 그러나 이러한 개인 네트워크를 손상 시킬 수 있는 잘 문서화 된 여러 가지 방법이 있습니다. Microsoft 365은 많은 조직에서 "불안해 시스템"으로 작동 하므로 손상 된 온-프레미스 인프라에서 보호 하는 것이 중요 합니다.

이 문서에서는 온-프레미스 손상 으로부터 Microsoft 365 클라우드 환경을 보호 하도록 시스템을 구성 하는 방법을 보여 줍니다. Azure ad 테 넌 트 구성 설정, Azure AD 테 넌 트가 온-프레미스 시스템에 안전 하 게 연결 되는 방법 및 온-프레미스 손상 으로부터 클라우드 시스템을 보호 하는 방식으로 시스템을 운영 하는 데 필요한 장단점에 중점을 두고 있습니다.

Microsoft 365 클라우드 환경을 보호 하기 위해이 지침을 구현 하는 것이 좋습니다.
> [!NOTE]
> 이 문서는 처음에 블로그 게시물로 게시 되었습니다. 수명 및 유지 관리를 위해 여기로 이동 되었습니다. <br>
이 문서의 오프 라인 버전을 만들려면 브라우저의 PDF 인쇄 기능을 사용 합니다. 업데이트는 여기에서 자주 확인 하세요.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>손상 된 온-프레미스 환경의 기본 위협 벡터


Microsoft 365 클라우드 환경은 광범위 한 모니터링 및 보안 인프라를 활용 합니다. 전 세계의 트래픽에 대 한 기계 학습 및 인간 인텔리전스를 사용 하면 공격을 신속 하 게 감지 하 고 거의 실시간으로 다시 구성할 수 있습니다. 온-프레미스 인프라를 Microsoft 365에 연결 하는 하이브리드 배포에서 많은 조직은 중요 한 인증 및 디렉터리 개체 상태 관리 결정을 위해 온-프레미스 구성 요소에 신뢰를 위임 합니다.
아쉽게도 온-프레미스 환경이 손상 되는 경우 이러한 트러스트 관계로 인해 공격자가 Microsoft 365 환경을 손상 시킬 수 있습니다.

두 가지 주요 위협 벡터는 **페더레이션 트러스트 관계** 와 **계정 동기화입니다.** 두 벡터 모두 공격자에 게 클라우드에 대 한 관리 액세스 권한을 부여할 수 있습니다.

* SAML 인증과 같은 **페더레이션된 트러스트 관계** 는 온-프레미스 id 인프라를 통해 Microsoft 365에 인증 하는 데 사용 됩니다. SAML 토큰 서명 인증서가 손상 되 면 페더레이션에서 해당 인증서를 가진 모든 사용자가 클라우드의 모든 사용자를 가장할 수 있습니다. **가능 하면 Microsoft 365 인증을 위해 페더레이션 트러스트 관계를 사용 하지 않도록 설정 하는 것이 좋습니다.**

* **계정 동기화** 를 사용 하 여 권한 있는 사용자 (자격 증명 포함) 또는 Microsoft 365에서 관리 권한을 부여 받은 그룹을 수정할 수 있습니다. 동기화 된 개체는 직접 또는 신뢰할 수 있는 역할 또는 그룹에 포함 하 여 **Microsoft 365의 사용자 외에 권한을 보유 하 고 있는지 확인 하는 것이 좋습니다** . 이러한 개체에는 신뢰할 수 있는 클라우드 역할 또는 그룹에 직접 또는 중첩 된 할당이 없는지 확인 하세요.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>온-프레미스 손상 으로부터 Microsoft 365 보호


위에서 설명한 위협 벡터를 해결 하려면 아래에 설명 된 원칙을 따르는 것이 좋습니다.

![Microsoft 365 보호를 위한 참조 아키텍처 ](media/protect-m365/protect-m365-principles.png)

*  **Microsoft 365 관리자 계정을 완전히 격리 합니다.** 이는

    * Azure AD에서 마스터 되었습니다.

     * MFA (Multi-factor authentication)를 사용 하 여 인증 되었습니다.

     *  Azure AD 조건부 액세스를 통해 보안이 유지 됩니다.

     *  Azure 관리 워크스테이션을 사용 하 여 액세스 합니다.

제한 된 사용 계정입니다. **Microsoft 365에서 관리 권한이 있는 온-프레미스 계정이 없어야 합니다.** 자세한 내용은이 [개요 Microsoft 365 관리자 역할 개요](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)를 참조 하세요.
또한 [Azure Active Directory에서 Microsoft 365에 대 한 역할을](../roles/m365-workload-docs.md)참조 하세요.

*  **Microsoft 365에서 장치를 관리 합니다.** Azure AD 조인 및 클라우드 기반 MDM (모바일 장치 관리)을 사용 하 여 장치 및 보안 제어를 손상 시킬 수 있는 온-프레미스 장치 관리 인프라에 대 한 종속성을 제거 합니다.

* **Microsoft 365에 대 한 승격 된 권한이 있는 온-프레미스 계정이 없습니다.**
    NTLM, LDAP 또는 Kerberos 인증을 요구 하는 온-프레미스 응용 프로그램에 액세스 하는 계정은 조직의 온-프레미스 id 인프라에 계정이 필요 합니다. 서비스 계정을 비롯 한 이러한 계정이 권한 있는 클라우드 역할 또는 그룹에 포함 되어 있지 않은지와 이러한 계정에 대 한 변경 내용이 클라우드 환경의 무결성에 영향을 주지 않는지 확인 합니다. 권한 있는 온-프레미스 소프트웨어는 권한 있는 계정 또는 역할 Microsoft 365에 영향을 줄 수 없습니다.

*  **AZURE AD 클라우드 인증을 사용** 하 여 온-프레미스 자격 증명에 대 한 종속성을 제거 합니다. Windows Hello, FIDO, Microsoft Authenticator 또는 Azure AD MFA와 같은 강력한 인증을 항상 사용 합니다.

## <a name="specific-recommendations"></a>특정 권장 사항


다음 섹션에서는 위에서 설명한 원칙을 구현 하는 방법에 대 한 구체적인 지침을 제공 합니다.

### <a name="isolate-privileged-identities"></a>권한 있는 id 격리


Azure AD에서 관리자와 같은 권한 있는 역할을 가진 사용자는 나머지 환경의 빌드 및 관리를 위한 신뢰의 루트입니다. 손상의 영향을 최소화 하기 위해 다음 사례를 구현 합니다.

* Azure AD 및 Microsoft 365 권한 있는 역할에 대 한 클라우드 전용 계정을 사용 합니다.

* 권한 있는 액세스 [장치](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles) 를 배포 하 여 Microsoft 365 및 Azure AD를 관리 합니다.

*  권한 있는 역할이 있는 모든 인간 계정에 JIT (just-in-time) 액세스를 위한 PIM ( [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) )을 배포 하 고, 역할을 활성화 하려면 강력한 인증이 필요 합니다.

* 관리 역할에 [작업을 수행할 수 있는 최소 권한을](../roles/delegate-by-task.md)제공 합니다.

* 동시에 위임 및 여러 역할을 포함 하는 보다 풍부한 역할 할당 환경을 사용 하도록 설정 하려면 Azure AD 보안 그룹 또는 Microsoft 365 그룹 (통칭 "클라우드 그룹")을 사용 하 고 [역할 기반 액세스 제어를 사용 하도록 설정](../roles/groups-assign-role.md)하는 것이 좋습니다. [관리 단위](../roles/administrative-units.md) 를 사용 하 여 역할의 범위를 조직 일부분으로 제한할 수도 있습니다.

* [응급 액세스 계정을](../roles/security-emergency-access.md) 배포 하 고 온-프레미스 암호 자격 증명 모음을 사용 하 여 자격 증명을 저장 하지 않습니다.

자세한 내용은이 항목에 대 한 자세한 지침이 포함 된 [권한 있는 액세스 보안](https://aka.ms/SPA)을 참조 하세요. 또한 [AZURE AD의 관리자를 위한 보안 액세스 방법](../roles/security-planning.md)을 참조 하세요.

### <a name="use-cloud-authentication"></a>클라우드 인증 사용 

자격 증명은 기본 공격 벡터입니다. 자격 증명을 보다 안전 하 게 만들려면 다음 방법을 구현 합니다.

* 암호 없는 [인증 배포](../authentication/howto-authentication-passwordless-deployment.md): 암호 없는 자격 증명을 배포 하 여 가능한 한 많은 암호 사용을 줄입니다. 이러한 자격 증명은 클라우드에서 기본적으로 관리 되 고 유효성이 검사 됩니다. 다음 중에서 선택합니다.

   * [비즈니스용 Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Authenticator 앱](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 보안 키](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Multi-Factor Authentication 배포](https://aka.ms/deploymentplans/mfa): [Azure AD MFA를 사용 하 여 여러 강력한 자격 증명](../fundamentals/resilience-in-credentials.md)을 프로 비전 합니다. 이렇게 하면 클라우드 리소스에 액세스할 때 조작할 수 있는 온-프레미스 암호 외에도 Azure AD에서 관리 되는 자격 증명이 필요 합니다.

   * 자세한 내용은 [Azure Active Directory를 사용 하 여 복원 력 있는 액세스 제어 관리 전략 만들기](https://aka.ms/resilientaad)를 참조 하세요.

**제한 사항 및 장단점**

* 하이브리드 계정 암호 관리에는 암호 보호 에이전트 및 비밀 번호 쓰기 저장 에이전트와 같은 하이브리드 구성 요소가 필요 합니다. 온-프레미스 인프라가 손상 된 경우 공격자는 이러한 에이전트가 상주 하는 컴퓨터를 제어할 수 있습니다. 이로 인해 클라우드 인프라가 손상 되지 않지만 클라우드 계정은 온-프레미스 손상 으로부터 이러한 구성 요소를 보호 하지 않습니다.

*  Active Directory에서 동기화 된 온-프레미스 계정은 온-프레미스 AD 암호 정책으로 완화 될 것 이라는 가정 하에 Azure AD에서 만료 되지 않음으로 표시 됩니다. 온-프레미스 AD가 손상 되 고 AD connect의 동기화를 사용 하지 않도록 설정 해야 하는 경우 [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md)옵션을 설정 해야 합니다.

## <a name="provision-user-access-from-the-cloud"></a>클라우드에서 사용자 액세스 프로 비전

프로 비전은 응용 프로그램 또는 id 공급자에서 사용자 계정 및 그룹 만들기를 나타냅니다.

![프로 비전 아키텍처 다이어그램](media/protect-m365/protect-m365-provision.png)

* **클라우드 HR 앱에서 AZURE AD로 프로 비전 합니다.** 이렇게 하면 클라우드 HR 앱에서 Azure AD로의 Leaver 주기를 방해 하지 않고 온-프레미스 손상을 격리할 수 있습니다.

* **클라우드 응용 프로그램:** 가능 하면 온-프레미스 프로 비전 솔루션과 달리 [Azure AD 앱 프로 비전](../app-provisioning/user-provisioning.md) 을 배포 합니다. 이렇게 하면 온-프레미스 위반으로 인해 일부 SaaS 앱이 악성 사용자 프로필로 포이즌 되지 않도록 보호 합니다. 

* **외부 id:** [AZURE AD B2B 공동 작업](../external-identities/what-is-b2b.md)을 사용 합니다.
    이렇게 하면 파트너, 고객 및 공급자와의 외부 공동 작업을 위한 온-프레미스 계정 종속성이 줄어듭니다. 다른 id 공급자와의 직접 페더레이션을 신중 하 게 평가 합니다. B2B 게스트 계정은 다음과 같은 방법으로 제한 하는 것이 좋습니다.

   *  디렉터리의 검색 그룹 및 기타 속성에 대 한 게스트 액세스를 제한 합니다. 외부 공동 작업 설정을 사용 하 여 구성원이 아닌 그룹을 읽는 게스트 기능을 제한할 수 있습니다. 

    *   Azure Portal에 대 한 액세스를 차단 합니다. 거의 필요한 예외를 만들 수 있습니다.  모든 게스트 및 외부 사용자를 포함 하는 조건부 액세스 정책을 만든 다음 [액세스를 차단 하는 정책을 구현](/azure/role-based-access-control/conditional-access-azure-management)합니다. 

* **연결 되지 않은 포리스트:** [AZURE AD 클라우드 프로 비전](../cloud-provisioning/what-is-cloud-provisioning.md)을 사용 합니다. 이렇게 하면 연결이 끊어진 포리스트에 연결 하 여 포리스트 간 연결 또는 트러스트를 설정할 필요가 없으므로 온-프레미스 위반의 영향을 넓힐 수 있습니다. * 
 
**제한 사항:**

* 하이브리드 계정을 프로 비전 하는 데 사용 하는 경우 클라우드 HR 시스템의 Azure AD는 온-프레미스 동기화를 사용 하 여 AD에서 Azure AD로의 데이터 흐름을 완료 합니다. 동기화가 중단 되 면 Azure AD에서 새 직원 레코드를 사용할 수 없게 됩니다.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>공동 작업 및 액세스에 클라우드 그룹 사용

클라우드 그룹을 사용 하 여 온-프레미스 인프라에서 공동 작업과 액세스를 분리할 수 있습니다.

* **공동 작업:** 최신 공동 작업을 위해 Microsoft 365 그룹과 Microsoft 팀을 사용 합니다. Outlook에서 온-프레미스 메일 그룹의 서비스를 해제 하 고 [배포 목록을 Microsoft 365 그룹으로 업그레이드](https://docs.microsoft.com/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide)합니다.

* **액세스:** Azure ad 보안 그룹 또는 Microsoft 365 그룹을 사용 하 여 Azure AD에서 응용 프로그램에 대 한 액세스 권한을 부여 합니다.
* **Office 365 라이선스:** 그룹 기반 라이선스를 사용 하 여 클라우드 전용 그룹을 사용 하 여 Office 365에 프로 비전 합니다. 이 분리 온-프레미스 인프라에서 그룹 멤버 자격을 제어 합니다.

액세스에 사용 되는 그룹 소유자는 온-프레미스 손상의 멤버 자격 인수 방지 하기 위해 권한 있는 id로 간주 되어야 합니다.
가져오기-온-프레미스의 그룹 멤버 자격 또는 Microsoft 365의 동적 그룹 멤버 자격에 영향을 줄 수 있는 온-프레미스 특성 조작을 직접 조작 하는 작업이 포함 됩니다.

## <a name="manage-devices-from-the-cloud"></a>클라우드에서 장치 관리


Azure AD 기능을 사용 하 여 장치를 안전 하 게 관리 합니다.

-   **Windows 10 워크스테이션 사용:** MDM 정책을 사용 하 여 [Azure AD 가입](../devices/azureadjoin-plan.md) 장치를 배포 합니다. 완전 자동화 된 프로 비전 환경을 위해 [Windows Autopilot](https://docs.microsoft.com/mem/autopilot/windows-autopilot) 을 사용 하도록 설정 합니다.

    -   사용 중단 Windows 8.1 및 이전 컴퓨터

    -   서버 OS 컴퓨터를 워크스테이션으로 배포 하지 않습니다.

    -   모든 장치 관리 작업의 권한 원본으로 [Microsoft Intune](https://www.microsoft.com/en/microsoft-365/enterprise-mobility-security/microsoft-intune) 를 사용 합니다.

-   권한 있는 액세스 [**장치를 배포**](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles) 하 여 Microsoft 365 및 Azure AD를 관리 합니다.

 ## <a name="workloads-applications-and-resources"></a>작업, 응용 프로그램 및 리소스 

-   **온-프레미스 SSO 시스템:** 모든 온-프레미스 페더레이션 및 웹 액세스 관리 인프라를 사용 중단 하 고 Azure AD를 사용 하도록 응용 프로그램을 구성 합니다.  

-   **최신 인증 프로토콜을 지 원하는 SaaS 및 LOB 응용 프로그램:** [SINGLE SIGN-ON에 Azure AD를 사용](../manage-apps/what-is-single-sign-on.md)합니다. 인증에 Azure AD를 사용 하도록 구성 하는 앱이 많을 수록 온-프레미스 손상의 경우에는 위험이 줄어듭니다.


* **레거시 응용 프로그램** 

   * 최신 인증을 지원 하지 않는 레거시 응용 프로그램에 대 한 인증, 권한 부여 및 원격 액세스는 [Azure AD 응용 프로그램 프록시](../manage-apps/application-proxy.md)를 통해 사용 하도록 설정할 수 있습니다. 또한  [보안 하이브리드 액세스 파트너 통합](../manage-apps/secure-hybrid-access.md)을 사용 하 여 네트워크 또는 응용 프로그램 배달 컨트롤러 솔루션을 통해 사용 하도록 설정할 수 있습니다.   

   * 최신 인증을 지 원하는 VPN 공급 업체를 선택 하 고 해당 인증을 Azure AD와 통합 합니다. Anon-프레미스 손상의 경우 Azure AD를 사용 하 여 VPN을 사용 하지 않도록 설정 하 여 액세스를 사용 하지 않도록 설정 하거나 차단할 수 있습니다.

*  **응용 프로그램 및 워크 로드 서버**

   * 필요한 서버를 Azure IaaS로 마이그레이션할 수 있는 응용 프로그램 또는 리소스를 사용 하 고 [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) (azure AD DS)를 사용 하 여 AD 온-프레미스에 대 한 트러스트 및 종속성을 분리할 수 있습니다. 이러한 분리를 위해 Azure AD DS에 사용 되는 가상 네트워크는 회사 네트워크에 연결 되어 있지 않아야 합니다.

   * [자격 증명 계층화](https://aka.ms/TierModel)의 지침을 따릅니다. 응용 프로그램 서버는 일반적으로 계층 1 자산으로 간주 됩니다.

 ## <a name="conditional-access-policies"></a>조건부 액세스 정책

Azure AD 조건부 액세스를 사용 하 여 신호를 해석 하 고 그에 따라 인증 결정을 내립니다. 자세한 내용은 [조건부 액세스 배포 계획](https://aka.ms/deploymentplans/ca) 을 참조 하세요.

* [레거시 인증 프로토콜](../fundamentals/auth-sync-overview.md): 조건부 액세스를 사용 하 여 가능한 경우 [레거시 인증](../conditional-access/howto-conditional-access-policy-block-legacy.md) 프로토콜을 차단 합니다. 또한 응용 프로그램 관련 구성을 사용 하 여 응용 프로그램 수준에서 레거시 인증 프로토콜을 사용 하지 않도록 설정 합니다.

   * [Exchange online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) 및 [SharePoint online](https://docs.microsoft.com/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps)에 대 한 특정 세부 정보를 참조 하세요.

* 권장 되는 [id 및 장치 액세스 구성을 구현 합니다.](https://docs.microsoft.com/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide)

* 조건부 액세스를 포함 하지 않는 Azure AD의 버전을 사용 하는 경우 [AZURE ad 보안 기본값](../fundamentals/concept-fundamentals-security-defaults.md)을 사용 하 고 있는지 확인 합니다.

   * Azure AD 기능 라이선스에 대 한 자세한 내용은 [AZURE ad 가격 책정 가이드](https://azure.microsoft.com/pricing/details/active-directory/)를 참조 하세요.

## <a name="monitoring"></a>모니터링 

온-프레미스 손상 으로부터 Microsoft 365을 보호 하도록 환경을 구성한 후에는 환경을 사전에 [모니터링](../reports-monitoring/overview-monitoring.md) 합니다.
### <a name="scenarios-to-monitor"></a>모니터링할 시나리오

조직과 관련 된 시나리오 외에도 다음과 같은 주요 시나리오를 모니터링 합니다. 예를 들어 비즈니스에 중요 한 응용 프로그램 및 리소스에 대 한 액세스를 사전에 모니터링 해야 합니다.

* **의심 스러운 활동**: 의심 스러운 활동에 대해 모든 [Azure AD 위험 이벤트](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection#risk-detection-and-remediation) 를 모니터링 해야 합니다. [Azure AD ID 보호](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) 은 기본적으로 Azure Security Center와 통합 됩니다.

   * 위치 기반 신호의 소음 검색을 방지 하기 위해 네트워크 [명명 된 위치](../reports-monitoring/quickstart-configure-named-locations.md) 를 정의 합니다. 
*  **UEBA (사용자 엔터티 동작 분석) 경고** UEBA를 사용 하 여 변칙 검색에 대 한 정보를 얻습니다.
   * MCAS (Microsoft 클라우드 앱 검색) [는 클라우드에서 Ueba](https://docs.microsoft.com/cloud-app-security/tutorial-ueba)를 제공 합니다.

   * [AZURE ATP에서 온-프레미스 UEBA를 통합할](https://docs.microsoft.com/defender-for-identity/install-step2)수 있습니다. MCAS는 Azure AD ID 보호에서 신호를 읽습니다. 

* **응급 액세스 계정 작업**: [응급 액세스 계정을](../roles/security-emergency-access.md) 사용 하는 모든 액세스를 모니터링 하 고 조사를 위해 생성 된 경고를 생성 해야 합니다. 이 모니터링에는 다음이 포함 되어야 합니다. 

   * 로그인. 

   * 자격 증명 관리. 

   * 그룹 멤버 자격에 대 한 모든 업데이트 

   *    응용 프로그램 할당. 
* **권한 있는 역할 활동**: [Azure AD PIM에서 생성 된 보안 경고](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-configure-security-alerts?tabs=new#security-alerts)를 구성 하 고 검토 합니다.
    사용자가 직접 할당 될 때마다 경고를 생성 하 여 PIM 외부의 권한 있는 역할에 대 한 직접 할당을 모니터링 합니다.
* **AZURE AD 테 넌 트 전체** 구성: 테 넌 트 전체 구성에 대 한 변경 내용은 시스템에서 경고를 생성 해야 합니다. 여기에는 다음이 포함 되지만이에 국한 되지 않습니다.
  *  사용자 지정 도메인 업데이트  

  * Azure AD B2B 허용/차단 목록 변경 내용입니다.
  * Azure AD B2B는 id 공급자 (직접 페더레이션 또는 소셜 로그인을 통해 SAML IDPs)를 허용 합니다.  
  * 조건부 액세스 또는 위험 정책 변경 

* **응용 프로그램 및 서비스 주체 개체**:
   * 조건부 액세스 정책이 필요할 수 있는 새 응용 프로그램 또는 서비스 주체입니다. 

   * 서비스 주체에 추가 자격 증명이 추가 되었습니다.
   * 응용 프로그램 동의 활동입니다. 

* **사용자 지정 역할**:
   * 사용자 지정 역할 정의를 업데이트 합니다. 

   * 새 사용자 지정 역할을 만들었습니다. 

### <a name="log-management"></a>로그 관리

Azure 센티널, 일반적인 쿼리, 조사 및 법적 고가 플레이 북과 같은 SIEM 시스템과 같은 일관 된 도구 집합을 용이 하 게 하기 위해 로그 저장소 및 보존 전략, 디자인 및 구현을 정의 합니다.

* **AZURE AD 로그** 진단 설정, 로그 보존 및 SIEM 수집을 포함 하 여 일관 된 모범 사례에 따라 생성 된 로그 및 신호를 수집 합니다. 로그 전략에는 다음 Azure AD 로그가 포함 되어야 합니다.
   * 로그인 작업 

   * 감사 로그 

   * 위험 이벤트 

Azure AD는 로그인 활동 로그 및 감사 로그에 대 한 [Azure Monitor 통합](../reports-monitoring/concept-activity-logs-azure-monitor.md) 을 제공 합니다. 위험 이벤트는 [MICROSOFT GRAPH API](https://aka.ms/AzureADSecuredAzure/32b)를 통해 수집 수 있습니다. Azure [AD 로그를 azure monitor 로그로 스트리밍할](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)수 있습니다.

* **하이브리드 인프라 OS 보안 로그.** 모든 하이브리드 id 인프라 OS 로그를 보관 하 고 신중 하 게 모니터링 해야 합니다. <br>계층 0 시스템 (노출 영역에 영향을 주어) 다음 내용이 포함됩니다. 

   *  Azure AD Connect. Id 동기화를 모니터링 하려면 [Azure AD Connect Health](https://aka.ms/AzureADSecuredAzure/32e) 를 배포 해야 합니다.

   *  응용 프로그램 프록시 에이전트 


   * 암호 쓰기 복구 에이전트 

   * 암호 보호 게이트웨이 컴퓨터  

   * Azure MFA RADIUS 확장이 있는 NPS 

## <a name="next-steps"></a>다음 단계
* [Azure AD를 사용하여 ID 및 액세스 관리에 대한 복원력 구현](resilience-overview.md)

* [리소스에 대한 외부 액세스 보호](secure-external-access-resources.md) 
* [모든 앱을 Azure AD와 통합](five-steps-to-full-application-integration-with-azure-ad.md)
