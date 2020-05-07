---
title: Azure AD의 관리자를 위한 보안 액세스 방법 | Microsoft Docs
description: 조직의 관리 액세스 및 관리자 계정이 안전한지 확인합니다. Azure AD, Azure 및 Microsoft Online Services를 구성하는 시스템 설계자 및 IT 전문가를 대상으로 합니다.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 04/30/2020
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c580a39db97e1ce50c3d244db3023bf422bca08
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837195"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안

비즈니스 자산의 보안은 IT 시스템을 관리 하는 권한 있는 계정의 무결성에 따라 달라 집니다. 사이버 공격자는 자격 증명 도난 공격을 통해 관리자 계정 및 기타 권한 있는 액세스를 사용 하 여 중요 한 데이터에 대 한 액세스를 시도 합니다.

클라우드 서비스의 경우 예방 및 대응은 클라우드 서비스 공급자와 고객의 공동 책임입니다. 엔드포인트와 클라우드에 대한 최신 위협에 대한 자세한 내용은 [Microsoft 보안 인텔리전스 보고서](https://www.microsoft.com/security/operations/security-intelligence-report)를 참조하세요. 이 문서는 현재 계획과 여기서 설명하는 지침 사이의 차이를 제거하기 위한 로드맵을 개발하는 데 도움이 될 수 있습니다.

> [!NOTE]
> Microsoft는 최고 수준의 신뢰, 투명도, 표준 적합성 및 규정 준수를 위해 노력하고 있습니다. Microsoft 글로벌 인시던트 대응 팀이 클라우드 서비스에 대한 공격의 영향을 완화하는 방법과 Microsoft 비즈니스 제품 및 클라우드 서비스에 보안을 기본 제공하는 방법은 [Microsoft 보안 센터 - 보안](https://www.microsoft.com/trustcenter/security) 및 [Microsoft 보안 센터 - 준수](https://www.microsoft.com/trustcenter/compliance)에서 자세히 알아보세요.

일반적으로 조직 보안은 네트워크의 진입 및 종료 지점의 보안 경계를 중심으로 합니다. 그러나 인터넷 상의 SaaS 앱 및 개인 장치는이 접근 방식을 보다 효과적으로 만들었습니다. Azure AD에서 조직의 id 계층의 인증으로 네트워크 보안 경계를 대체 하 고 제어에서 권한 있는 관리 역할에 할당 된 사용자를 사용 합니다. 환경이 온-프레미스, 클라우드 또는 하이브리드 인지 여부에 관계 없이 해당 액세스를 보호 해야 합니다.

권한 있는 액세스를 보호 하려면 다음 사항을 변경 해야 합니다.

* 프로세스, 관리 사례 및 기술 자료 관리
* 호스트 방어, 계정 보호 및 ID 관리와 같은 기술 구성 요소

관심 있는 Microsoft 서비스에서 관리 및 보고 되는 방식으로 권한 있는 액세스를 보호 합니다. 온-프레미스 관리자 계정이 있는 경우 [권한 있는 액세스 보안](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)의 Active Directory에서 온-프레미스 및 하이브리드 권한 있는 액세스에 대 한 지침을 참조 하세요.

> [!NOTE]
> 이 문서의 지침은 주로 Azure Active Directory Premium 계획 P1 및 P2에 포함된 Azure Active Directory의 기능을 인용합니다. Azure Active Directory Premium P2는 EMS E5 제품군 및 Microsoft 365 E5 제품군에 포함되어 있습니다. 이 지침에서는 사용자를 위해 구입한 Azure AD Premium P2 라이선스가 조직에 이미 있다고 가정합니다. 이러한 라이선스가 없으면 일부 지침이 조직에 적용되지 않을 수 있습니다. 또한이 문서 전체에서 전역 관리자 (또는 전역 관리자) 라는 용어는 "회사 관리자" 또는 "테 넌 트 관리자"와 동일한 의미를 의미 합니다.

## <a name="develop-a-roadmap"></a>로드맵 개발

사이버 공격자로부터 권한 있는 액세스를 보호하기 위한 로드맵을 개발하고 따르는 것이 좋습니다. 언제든지 조직 내의 기존 기능과 특정 요구 사항에 맞게 로드맵을 조정할 수 있습니다. 로드맵의 각 단계에서는 악의적 사용자가 온-프레미스, 클라우드 및 하이브리드 자산에 대한 권한 있는 액세스를 공격하는 데 드는 비용과 어려움을 제시해야 합니다. Microsoft는 다음과 같은 4 가지 로드맵 단계를 권장 합니다. 가장 효과적이 고 빠른 구현을 가장 먼저 예약 합니다. 이 문서는 사이버 공격 인시던트 및 응답 구현에 대 한 Microsoft의 경험을 기반으로 하는 가이드 일 수 있습니다. 이 로드맵의 타임 라인은 근사치입니다.

![타임라인이 있는 로드맵 단계](./media/directory-admin-roles-secure/roadmap-timeline.png)

* 1단계(24~48시간): 즉시 수행하도록 권장되는 중요한 항목

* 2단계(2~4주): 가장 자주 사용되는 공격 기법에 대한 완화

* 3단계(1~3개월): 가시성 빌드 및 관리자 활동에 대한 모든 권한 빌드

* 4단계(6개월 이상): 보안 플랫폼 강화를 위한 방어 체계 구축 계속

이 로드맵 프레임워크는 이미 배포한 Microsoft 기술을 최대한 사용하도록 설계되었습니다. 이미 배포 했거나 배포를 고려 하 고 있는 다른 공급 업체의 모든 보안 도구에 연결할 수 있습니다.

## <a name="stage-1-critical-items-to-do-right-now"></a>1 단계: 지금 바로 수행할 중요 항목

![가장 먼저 수행할 중요 한 항목 1 단계](./media/directory-admin-roles-secure/stage-one.png)

로드맵의 1단계는 빠르고 쉽게 구현할 수 있는 중요한 작업에 집중합니다. 기본 수준의 권한 있는 보안 액세스를 보장하기 위해 처음 24~48시간 내에 이러한 몇 가지 항목을 즉시 수행하는 것이 좋습니다. 권한 있는 보안 액세스 로드맵의 이 단계에 포함되는 작업은 다음과 같습니다.

### <a name="general-preparation"></a>일반적인 준비

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management 설정

Azure AD 프로덕션 환경에서 Azure AD Privileged Identity Management (PIM)을 설정 하는 것이 좋습니다. PIM을 켜면 권한 있는 액세스 역할 변경에 대 한 알림 전자 메일 메시지를 받게 됩니다. 알림은 높은 권한이 있는 역할에 추가 사용자를 추가 하는 경우 조기 경고를 제공 합니다.

Azure AD Privileged Identity Management는 Azure AD Premium P2 또는 EMS E5에 포함되어 있습니다. 온-프레미스 및 클라우드에서 응용 프로그램 및 리소스에 대 한 액세스를 보호 하려면 [Enterprise Mobility + Security 무료 90 일 평가판](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)에 등록 하세요. Azure AD Privileged Identity Management 및 Azure AD ID 보호 Azure AD 보고, 감사 및 경고를 사용 하 여 보안 작업을 모니터링 합니다.

Azure AD Privileged Identity Management 설정한 후:

1. Azure AD 프로덕션 조직의 전역 관리자 인 계정으로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.

2. Privileged Identity Management를 사용 하려는 Azure AD 조직을 선택 하려면 Azure Portal의 오른쪽 위 모서리에서 사용자 이름을 선택 합니다.

3. Azure Portal 메뉴에서 **모든 서비스** 를 선택 하 고 **Azure AD Privileged Identity Management**목록을 필터링 합니다.

4. **모든 서비스** 목록에서 Privileged Identity Management를 열고 대시보드에 고정합니다.

조직에서 PIM을 사용 하는 첫 번째 사용자는 **보안 관리자** 및 권한 있는 **역할 관리자** 역할에 할당 됩니다. 권한 있는 역할 관리자만 사용자에 대한 Azure AD 디렉터리 역할 할당을 관리할 수 있습니다. PIM 보안 마법사는 초기 검색 및 할당 환경을 안내 합니다. 이 시점에서 추가로 변경하지 않고도 마법사를 종료할 수 있습니다.

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>권한이 높은 역할이 있는 계정 식별 및 분류

Azure AD Privileged Identity Management를 켠 후에 다음 Azure AD 역할에 있는 사용자를 확인 합니다.

* 전역 관리자
* 권한 있는 역할 관리자
* Exchange Online 관리자
* SharePoint Online 관리자

조직에 Azure AD Privileged Identity Management 없는 경우 [POWERSHELL API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)를 사용할 수 있습니다. 전역 관리자는 조직에서 구독 한 모든 클라우드 서비스에서 동일한 사용 권한을 갖고 있기 때문에 전역 관리자 역할부터 시작 합니다. 이러한 권한은 Microsoft 365 관리 센터, Azure Portal 또는 Microsoft PowerShell 용 Azure AD 모듈에서 할당 된 위치에 관계 없이 부여 됩니다.

이러한 역할에 더 이상 필요하지 않은 계정을 모두 제거합니다. 그런 다음, 관리자 역할에 할당된 나머지 계정을 분류합니다.

* 관리자에 게 할당 되지만 관리자가 아닌 목적 (예: 개인 메일)에도 사용 됨
* 관리자에 게 할당 되 고 관리 목적 으로만 사용 됩니다.
* 여러 사용자 간에 공유됩니다.
* 비상(break-glass) 응급 액세스 시나리오용
* 자동화된 스크립트용
* 외부 사용자용

#### <a name="define-at-least-two-emergency-access-accounts"></a>둘 이상의 응급 액세스 계정 정의

사용자가 실수로 자신의 역할을 잠글 수 있습니다. 예를 들어 페더레이션된 온-프레미스 id 공급자를 사용할 수 없는 경우 사용자는 기존 관리자 계정을 로그인 하거나 활성화할 수 없습니다. 두 개 이상의 응급 액세스 계정을 저장 하 여 실수로 인 한 액세스 부족을 준비할 수 있습니다.

응급 액세스 계정은 Azure AD 조직 내에서 권한 있는 액세스를 제한 하는 데 도움이 됩니다. 이러한 계정은 매우 특권 이며 특정 사용자에 게 할당 되지 않습니다. 응급 액세스 계정은 일반 관리 계정을 사용할 수 없는 "중단" 시나리오의 경우 응급로 제한 됩니다. 응급 계정의 사용을 제어 하 고 필요한 시간 으로만 줄여야 합니다.

전역 관리자 역할에 할당되었거나 적합한 계정을 평가합니다. \*. Onmicrosoft.com 도메인을 사용 하 여 클라우드 전용 계정이 표시 되지 않는 경우 ("중단" 응급 액세스의 경우) 만듭니다. 자세한 내용은 [Azure AD에서 응급 액세스 관리 계정의 관리](directory-emergency-access.md)를 참조하세요.

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Multi-factor authentication을 설정 하 고 다른 높은 권한의 단일 사용자 페더레이션 관리자 계정을 등록 합니다.

로그인 시 Azure AD 관리자 역할(전역 관리자, 권한 있는 역할 관리자, Exchange Online 관리자 및 SharePoint Online 관리자) 중 하나 이상에 영구적으로 할당된 모든 개별 사용자에 대해 Azure MFA(Multi-Factor Authentication)를 요구합니다. 이 가이드를 사용하여 [관리자 계정에 대해 MFA(Multi-Factor Authentication)](../authentication/howto-mfa-userstates.md)를 사용하도록 설정하고, 해당 사용자가 모두 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)에 등록되었는지 확인합니다. 자세한 내용은 [Office 365에서 데이터 및 서비스에 대한 액세스 보호](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) 가이드의 2단계 및 3단계에서 찾을 수 있습니다. 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>2 단계: 자주 사용 되는 공격 완화

![2 단계는 자주 사용 하는 공격을 완화 합니다.](./media/directory-admin-roles-secure/stage-two.png)

로드맵의 2단계는 가장 자주 사용되는 자격 증명 도난 공격 기법과 악용을 완화하는 데 집중하며, 약 2-4주 내에 구현할 수 있습니다. 권한 있는 보안 액세스 로드맵의 이 단계에 포함되는 작업은 다음과 같습니다.

### <a name="general-preparation"></a>일반적인 준비

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>서비스, 소유자 및 관리자의 인벤토리 수행

"사용자의 장치 가져오기" 및 홈 정책에서의 작업 및 무선 연결의 증가에 따라 네트워크에 연결 하는 사용자를 모니터링 하는 것이 중요 합니다. 보안 감사는 조직에서 지원 하지 않으며 높은 위험을 나타내는 장치, 응용 프로그램 및 프로그램을 네트워크에서 표시할 수 있습니다. 자세한 내용은 [Azure 보안 관리 및 모니터링 개요](../../security/fundamentals/management-monitoring-overview.md)를 참조하세요. 인벤토리 프로세스에는 다음 작업이 모두 포함되어야 합니다.

* 관리 역할과 관리할 수 있는 서비스가 있는 사용자를 식별합니다.
* Azure AD PIM을 사용 하 여 Azure AD에 대 한 관리자 액세스 권한이 있는 조직의 사용자를 확인 합니다.
* Azure AD에 정의된 역할 외에도, Office 365에는 조직의 사용자에게 할당할 수 있는 일단의 관리자 역할이 제공됩니다. 각 관리자 역할은 일반적인 비즈니스 기능에 매핑되고 조직의 사용자에 게 [Microsoft 365 관리 센터](https://admin.microsoft.com)에서 특정 작업을 수행할 수 있는 권한을 부여 합니다. Microsoft 365 관리 센터를 사용 하 여 Azure AD에서 관리 되지 않는 역할을 비롯 하 여 조직 내 사용자에 게 Office 365에 대 한 관리자 액세스 권한이 있는 사용자를 확인 합니다. 자세한 내용은 office 365의 [office 365 관리자 역할](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) 및 [보안 방법](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)정보를 참조 하세요.
* 조직에서 사용 하는 서비스 (예: Azure, Intune 또는 Dynamics 365)의 인벤토리를 수행 합니다.
* 관리 목적으로 사용 되는 계정 확인:

  * 작업 전자 메일 주소를 연결 해야 합니다.
  * Azure Multi-Factor Authentication에 등록 하거나 온-프레미스에서 MFA를 사용 합니다.
* 사용자에게 관리 액세스 권한에 대한 비즈니스상의 사유를 요청합니다.
* 필요하지 않은 개인 및 서비스에 대한 관리자 액세스 권한을 제거합니다.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>직장 또는 학교 계정으로 전환해야 하는 관리 역할의 Microsoft 계정 식별

초기 전역 관리자가 Azure AD를 사용 하기 시작할 때 기존 Microsoft 계정 자격 증명을 다시 사용 하는 경우 Microsoft 계정을 개별 클라우드 기반 또는 동기화 된 계정으로 바꿉니다.

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>전역 관리자 계정에 대해 별도의 사용자 계정 및 메일 전달 보장

개인 전자 메일 계정은 사이버 공격자가 정기적으로 피싱 전역 관리자 계정에 대해 개인 전자 메일 주소를 사용할 수 없게 되는 위험이 있습니다. 관리자 권한으로 인터넷 위험을 분리 하려면 관리자 권한으로 각 사용자에 대 한 전용 계정을 만듭니다.

* 사용자가 전역 관리자 작업을 수행 하는 별도의 계정을 만들어야 합니다.
* 전역 관리자가 실수로 메일을 열거나 관리자 계정으로 프로그램을 실행 하지 않는지 확인 합니다.
* 이러한 계정에는 해당 메일을 작업 사서함에 전달 해야 합니다.

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>관리 계정의 암호가 최근에 변경되었는지 확인

모든 사용자가 자신의 관리 계정에 로그인 하 고 최근 90 일 동안 한 번 이상 암호를 변경 했는지 확인 합니다. 또한 공유 계정의 암호가 최근에 변경 되었는지 확인 합니다.

#### <a name="turn-on-password-hash-synchronization"></a>암호 해시 동기화 켜기

Azure AD Connect은 사용자 암호 해시의 해시를 온-프레미스 Active Directory에서 클라우드 기반 Azure AD 조직으로 동기화 합니다. Active Directory Federation Services (AD FS)와 함께 페더레이션을 사용 하는 경우 암호 해시 동기화를 백업으로 사용할 수 있습니다. 이 백업은 온-프레미스 Active Directory 또는 AD FS 서버를 일시적으로 사용할 수 없는 경우에 유용할 수 있습니다.

암호 해시 동기화를 사용 하면 사용자가 온-프레미스 Active Directory 인스턴스에 로그인 하는 데 사용 하는 암호와 동일한 암호를 사용 하 여 서비스에 로그인 할 수 있습니다. 암호 해시 동기화를 통해 Id 보호는 손상 된 것으로 알려진 암호와 암호 해시를 비교 하 여 손상 된 자격 증명을 검색할 수 있습니다. 자세한 내용은 [Azure AD Connect 동기화를 사용하여 암호 해시 동기화 구현](../hybrid/how-to-connect-password-hash-synchronization.md)을 참조하세요.

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>권한 있는 역할 및 노출 된 사용자의 사용자에 대 한 multi-factor authentication 필요

Azure AD는 모든 사용자에 대해 MFA (multi-factor authentication)를 요구 하는 것이 좋습니다. 계정이 손상 된 경우 상당한 영향을 주는 사용자를 고려해 야 합니다 (예: 재무 직원). MFA는 암호 손상으로 인 한 공격의 위험을 줄여 줍니다.

설정되는 MFA는 다음과 같습니다.

* 조직의 모든 사용자에 대해 [조건부 액세스 정책을 사용 하는 MFA](../authentication/howto-mfa-getstarted.md)

비즈니스용 Windows Hello를 사용 하는 경우 Windows Hello 로그인 환경을 사용 하 여 MFA 요구 사항을 충족할 수 있습니다. 자세한 내용은 [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport)를 참조하세요.

#### <a name="configure-identity-protection"></a>Identity Protection 구성

Azure AD ID 보호은 조직의 id에 영향을 주는 잠재적 취약성을 검색 하는 알고리즘 기반 모니터링 및 보고 도구입니다. 검색된 의심스러운 활동에 대해 자동화된 대응을 구성하고 이를 해결하기 위한 적절한 조치를 수행할 수 있습니다. 자세한 내용은 [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)을 참조하세요.

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Office 365 보안 점수 가져오기(Office 365를 사용하는 경우)

보안 점수는 사용 중인 Office 365 서비스의 설정 및 활동을 살펴보고 Microsoft에서 설정한 기준과 비교 합니다. 보안 방법에 따라 정렬 된 방법에 따라 점수를 얻을 수 있습니다. Office 365 Business Premium 또는 Enterprise 구독에 대 한 관리자 권한이 있는 사용자는에서 [https://securescore.office.com](https://securescore.office.com/)보안 점수에 액세스할 수 있습니다.

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Office 365 보안 및 규정 준수 지침 검토(Office 365를 사용하는 경우)

[보안 및 규정 준수에 대 한 계획](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) 은 office 365 고객이 office 365를 구성 하 고 다른 EMS 기능을 사용 하도록 설정 하는 방법을 간략하게 설명 합니다. 그런 다음, [Office 365에서 데이터 및 서비스에 대한 액세스를 보호하는 방법](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)의 3~6단계 및 [Office 365에서 보안 및 규정 준수를 모니터링하는 방법](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6) 가이드를 검토합니다.

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Office 365 활동 모니터링 구성(Office365를 사용하는 경우)

Office 365를 사용 하는 사용자가 관리자 계정을가지고 있지만 해당 포털에 로그인 하지 않으므로 Office 365 액세스를 필요로 하지 않는 직원을 확인 하는 조직을 모니터링 합니다. 자세한 내용은 [Microsoft 365 관리 센터의 작업 보고서](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263)를 참조 하세요.

#### <a name="establish-incidentemergency-response-plan-owners"></a>인시던트/비상 대응 계획 소유자 설정

성공적인 인시던트 응답 기능을 설정 하려면 상당한 계획과 리소스가 필요 합니다. 사이버 공격을 지속적으로 모니터링 하 고 인시던트 처리를 위한 우선 순위를 설정 해야 합니다. 인시던트 데이터를 수집, 분석 및 보고 하 여 관계를 빌드하고 다른 내부 그룹 및 계획 소유자와의 통신을 설정 합니다. 자세한 내용은 [Microsoft 보안 대응 센터](https://technet.microsoft.com/security/dn440717)를 참조하세요.

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>아직 보안이 설정되지 않은 권한 있는 온-프레미스 관리 계정에 대한 보안

Azure Active Directory 조직이 온-프레미스 Active Directory와 동기화 되는 경우 [권한 있는 보안 액세스 로드맵](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)의 지침을 따릅니다 .이 단계에는 다음이 포함 됩니다.

* 온-프레미스 관리 작업을 수행 해야 하는 사용자를 위한 별도의 관리자 계정 만들기
* Active Directory 관리자를 위한 권한 있는 액세스 워크스테이션 배포
* 워크스테이션 및 서버에 대 한 고유한 로컬 관리자 암호 만들기

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure에 대한 액세스를 관리하는 조직을 위한 추가 단계

#### <a name="complete-an-inventory-of-subscriptions"></a>구독의 인벤토리 완성

엔터프라이즈 포털과 Azure Portal을 사용하여 프로덕션 애플리케이션을 호스팅하는 조직의 구독을 식별합니다.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>관리자 역할에서 Microsoft 계정 제거

Xbox, Live 및 Outlook과 같은 다른 프로그램의 Microsoft 계정은 조직의 구독에 대 한 관리자 계정으로 사용 하면 안 됩니다. 모든 Microsoft 계정에서 관리자 상태를 제거 하 고를 Azure AD (예: chris@contoso.com) 회사 또는 학교 계정으로 바꿉니다. 관리 목적으로는 Azure AD에서 인증 되 고 다른 서비스에는 인증 되지 않은 계정에 따라 달라 집니다.

#### <a name="monitor-azure-activity"></a>Azure 활동 모니터링

Azure 활동 로그는 Azure에서 구독 수준 이벤트 기록을 제공합니다. 즉 누가 어떤 리소스를 생성, 업데이트, 삭제했는지와 언제 이러한 이벤트가 발생했는지에 대한 정보를 제공합니다. 자세한 내용은 [Azure 구독에서 중요한 작업에 대한 알림 감사 및 수신](../../azure-monitor/platform/quick-audit-notify-action-subscription.md)을 참조하세요.

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Azure AD를 통해 다른 클라우드 앱에 대한 액세스를 관리하는 조직을 위한 추가 단계

#### <a name="configure-conditional-access-policies"></a>조건부 액세스 정책 구성

온-프레미스 및 클라우드 호스팅 응용 프로그램에 대 한 조건부 액세스 정책을 준비 합니다. 사용자 작업 공간에 연결 된 장치를 사용 하는 경우 [Azure Active Directory 장치 등록을 사용 하 여 온-프레미스 조건부 액세스 설정](../active-directory-device-registration-on-premises-setup.md)에서 자세한 정보를 확인 하세요.

## <a name="stage-3-take-control-of-admin-activity"></a>3 단계: 관리 활동 제어

![3 단계: 관리 활동 제어](./media/directory-admin-roles-secure/stage-three.png)

3 단계는 2 단계의 완화를 기반으로 하며 약 1-3 개월 이내에 구현 해야 합니다. 권한 있는 보안 액세스 로드맵의 이 단계에 포함되는 구성 요소는 다음과 같습니다.

### <a name="general-preparation"></a>일반적인 준비

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>관리자 역할의 사용자에 대한 액세스 검토 수행

더 많은 회사 사용자가 클라우드 서비스를 통해 권한 있는 액세스 권한을 획득 하 여 관리 되지 않는 액세스를 유발할 수 있습니다. 오늘날의 사용자는 Office 365, Azure 구독 관리자에 대 한 전역 관리자가 되거나 Vm 또는 SaaS 앱을 통해 관리자 액세스 권한을 보유 하 게 됩니다.

조직에서는 모든 직원이 일반 비즈니스 트랜잭션을 권한 없는 사용자로 처리 하 고 필요한 경우에만 관리 권한을 부여 해야 합니다. 관리자 권한을 활성화 하는 데 적합 한 사용자를 식별 하 고 확인 하기 위해 액세스 검토를 완료 합니다.

다음을 권장합니다.

1. Azure AD 관리자인 사용자를 확인하고, 요청 시 JIT(Just-In-Time) 관리자 액세스 권한 및 역할 기반 보안 제어를 사용하도록 설정합니다.
2. 관리자 권한 액세스에 대한 명확한 이유가 없는 사용자를 다른 역할로 변환합니다(적격 역할이 아닌 경우 제거함).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>더 강력한 모든 사용자 인증에 대한 지속적인 공개

매우 노출 된 사용자에 게 Azure MFA 또는 Windows Hello와 같은 최신의 강력한 인증을 요구 합니다. 매우 노출 되는 사용자의 예는 다음과 같습니다.

* C 제품군 임원
* 상위 수준 관리자
* 중요 IT 및 보안 담당자

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Azure AD에 대한 관리 전용 워크스테이션 사용

공격자는 데이터의 무결성과 신뢰성을 방해할 수 있도록 권한 있는 계정을 대상으로 지정할 수 있습니다. 프로그램 논리를 변경 하거나 관리자가 자격 증명을 입력 하는 것을 염탐 악성 코드를 종종 사용 합니다. PAW(Privileged Access Workstation)는 인터넷 공격 및 위협 벡터로부터 보호되는 중요한 작업에 대한 전용 운영 체제를 제공합니다. 이러한 중요 한 작업 및 계정을 매일 사용 하는 워크스테이션 및 장치에서 분리 하면 다음 으로부터 강력한 보호 기능을 제공 합니다.

* 피싱 공격
* 응용 프로그램 및 운영 체제 취약성
* 가장 공격
* 키 입력 로깅, 해시 패스, 티켓 전달 등의 자격 증명 도난 공격

권한 있는 액세스 워크스테이션을 배포 하면 관리자가 확정 되지 않은 데스크톱 환경에서 자격 증명을 입력 하는 위험을 줄일 수 있습니다. 자세한 내용은 [액세스 권한 있는 워크스테이션](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)을 참조하세요.

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>인시던트 처리에 대한 NIST 권장 사항 검토

NIST(미국 표준 기술 연구소)에서는 인시던트 처리 지침, 특히 인시던트 관련 데이터를 분석하고 각 인시던트에 대한 적절한 대응을 결정하기 위한 지침을 제공합니다. 자세한 내용은 [NIST 컴퓨터 보안 인시던트 처리 지침(SP 800-61, 수정 버전 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)을 참조하세요.

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>추가 관리 역할에 JIT에 대한 PIM(Privileged Identity Management) 구현

Azure Active Directory의 경우 [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) 기능을 사용합니다. 권한 있는 역할을 시간 제한으로 활성화하려면 다음 작업을 수행하도록 설정해야 합니다.

* 특정 작업을 수행 하기 위해 관리자 권한 활성화
* 활성화 프로세스 중에 MFA 적용
* 경고를 사용하여 관리자에게 대역 외 변경 내용을 알림
* 사용자가 미리 구성 된 시간 동안 권한 있는 액세스를 유지할 수 있도록 설정
* 보안 관리자가 다음을 수행할 수 있도록 허용:

  * 모든 권한 있는 id 검색
  * 감사 보고서 보기
  * 관리자 권한을 활성화 하는 데 적합 한 모든 사용자를 식별 하는 액세스 검토 만들기

Azure AD Privileged Identity Management를 이미 사용하고 있는 경우 필요에 따라 시간 제한 권한에 대한 시간 범위(예: 유지 관리 기간)를 조정합니다.

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>암호 기반 로그인 프로토콜에 대한 노출 확인(Exchange Online을 사용하는 경우)

자격 증명이 손상 된 경우 조직에 치명적이 될 수 있는 모든 잠재적 사용자를 식별 하는 것이 좋습니다. 이러한 사용자의 경우 강력한 인증 요구 사항을 충족 하 고 Azure AD 조건부 액세스를 사용 하 여 사용자 이름 및 암호를 사용 하 여 전자 메일에 로그인 하지 않도록 합니다. [조건부 액세스를 사용 하 여 레거시 인증](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication)을 차단할 수 있으며 Exchange online을 통해 [기본 인증을 차단할](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) 수 있습니다.

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Office 365 역할에 대한 역할 검토 평가 수행(Office 365를 사용하는 경우)

모든 관리자 사용자가 올바른 역할에 있는지 여부를 평가합니다(이 평가에 따라 삭제 및 다시 할당).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Office 365에서 사용되는 보안 인시던트 관리 방법을 검토하고 자신의 조직과 비교합니다.

이 보고서는 [Microsoft Office 365의 보안 인시던트 관리](https://www.microsoft.com/download/details.aspx?id=54302)에서 다운로드할 수 있습니다.

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>권한 있는 온-프레미스 관리 계정 보안 유지

Azure Active Directory 테넌트가 온-프레미스 Active Directory에 연결되는 경우 [권한 있는 보안 액세스 로드맵](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): 2단계의 지침을 따릅니다. 이 단계에서는 다음을 수행 합니다.

* 모든 관리자에 대해 권한 있는 액세스 워크스테이션 배포
* MFA 필요
* 도메인 컨트롤러 유지 관리에 충분 한 관리자를 사용 하 여 도메인의 공격 노출 영역을 줄입니다.
* 공격 감지를 위한 고급 위협 평가 배포

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure에 대한 액세스를 관리하는 조직을 위한 추가 단계

#### <a name="establish-integrated-monitoring"></a>통합 모니터링 설정

[Azure Security Center](../../security-center/security-center-intro.md):

* Azure 구독 전반에서 통합 보안 모니터링 및 정책 관리를 제공 합니다.
* 그렇지 않을 수도 있는 위협을 검색 하는 데 도움이 됩니다.
* 광범위 한 보안 솔루션을 사용 하 여 작동

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>호스팅된 Virtual Machines 내에서 권한 있는 계정 인벤토리 구성

일반적으로 모든 Azure 구독 또는 리소스에 대 한 무제한 사용 권한을 사용자에 게 제공할 필요는 없습니다. Azure AD 관리자 역할을 사용 하 여 사용자가 작업을 수행 하는 데 필요한 액세스만 부여할 수 있습니다. Azure AD 관리자 역할을 사용 하 여 한 관리자가 구독의 Vm만 관리 하도록 하 고, 다른 관리자는 동일한 구독 내에서 SQL 데이터베이스를 관리할 수 있습니다. 자세한 내용은 [Azure Portal에서 역할 기반 Access Control 시작](../../role-based-access-control/overview.md)을 참조하세요.

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Azure AD 관리자 역할에 대한 PIM 구현

Azure AD 관리자 역할이 있는 Privileged Identity Management를 사용하여 Azure 리소스에 대한 액세스를 관리, 제어 및 모니터링합니다. PIM을 사용 하면 권한 노출 시간을 줄이고 보고서 및 경고를 통해 사용에 대 한 가시성을 향상 시켜 보호 합니다. 자세한 내용은 [Privileged Identity Management를 사용하여 Azure 리소스에 대한 RBAC 액세스 관리](../../role-based-access-control/pim-azure-resource.md)를 참조하세요.

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Azure 로그 통합을 사용하여 SIEM 시스템에 관련 Azure 로그 보내기

Azure 로그 통합을 사용하면 Azure 리소스의 원시 로그를 조직의 기존 SIEM(보안 정보 및 이벤트 관리) 시스템에 통합할 수 있습니다. [Azure 로그 통합](../../security/fundamentals/azure-log-integration-overview.md) 은 windows 이벤트 뷰어 로그 및 azure 리소스에서 windows 이벤트를 수집 합니다.

* Azure 활동 로그
* Azure Security Center 경고
* Azure 리소스 로그

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Azure AD를 통해 다른 클라우드 앱에 대한 액세스를 관리하는 조직을 위한 추가 단계

#### <a name="implement-user-provisioning-for-connected-apps"></a>연결된 앱에 대한 사용자 프로비저닝 구현

Azure AD를 사용 하면 Dropbox, Salesforce, ServiceNow 등과 같은 클라우드 앱에서 사용자 id를 만들고 유지 관리할 수 있습니다. 자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

#### <a name="integrate-information-protection"></a>정보 보호 통합

Microsoft Cloud App Security를 사용 하면 파일을 조사 하 고 Azure Information Protection 분류 레이블을 기반으로 정책을 설정 하 여 클라우드 데이터를 더 잘 표시 하 고 제어할 수 있습니다. 클라우드에서 파일을 검색 및 분류하고 Azure Information Protection 레이블을 적용합니다. 자세한 내용은 [Azure Information Protection 통합](https://docs.microsoft.com/cloud-app-security/azip-integration)을 참조하세요.

#### <a name="configure-conditional-access"></a>조건부 액세스 구성

[SaaS 앱](https://azure.microsoft.com/overview/what-is-saas/) 및 Azure AD 연결 앱에 대 한 그룹, 위치 및 응용 프로그램 민감도에 따라 조건부 액세스를 구성 합니다. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>연결된 클라우드 앱의 활동 모니터링

연결 된 응용 프로그램 에서도 사용자 액세스를 보호 하도록 [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) 를 사용 하는 것이 좋습니다. 이 기능은 클라우드 앱에 대 한 엔터프라이즈 액세스를 보호 하 고 관리자 계정을 보호 하 여 다음과 같은 작업을 허용 합니다.

* 클라우드 앱에 대한 가시성 및 제어 확장
* 액세스, 활동 및 데이터 공유에 대한 정책 만들기
* 위험한 활동, 비정상적인 동작 및 위협을 자동으로 식별
* 데이터 유출 방지
* 위험 방지, 자동화된 위협 방지 및 정책 적용 최소화

Cloud App Security SIEM 에이전트는 Cloud App Security와 SIEM 서버를 통합하여 Office 365 경고 및 활동을 중앙 집중식으로 모니터링할 수 있도록 합니다. 서버에서 실행되고 Cloud App Security에서 경고 및 활동을 가져와서 SIEM 서버로 스트리밍합니다. 자세한 내용은 [SIEM 통합](https://docs.microsoft.com/cloud-app-security/siem)을 참조하세요.

## <a name="stage-4-continue-building-defenses"></a>4 단계: 방어 계속 빌드

![4 단계: 활성 보안 상태 채택](./media/directory-admin-roles-secure/stage-four.png)

로드맵의 4 단계는 6 개월 이상에서 구현 되어야 합니다. 로드맵을 완료 하 여 현재 알려진 잠재적 공격 으로부터 권한 있는 액세스 보호를 강화 하세요. 내일의 보안 위협에 대해 비용을 증가 시키고 환경을 대상으로 하는 악의적 사용자의 성공률을 줄이기 위해 지속적인 프로세스로 보안을 보는 것이 좋습니다.

권한 있는 액세스 보안은 비즈니스 자산에 대 한 보안 보증을 설정 하는 데 중요 합니다. 그러나 지속적인 보안 보증을 제공 하는 완전 한 보안 프로그램의 일부 여야 합니다. 이 프로그램에는 다음과 같은 요소가 포함 되어야 합니다.

* 정책
* 작업
* 정보 보안
* 서버
* 애플리케이션
* 들
* 디바이스
* 클라우드 패브릭

권한 있는 액세스 계정을 관리 하는 경우 다음 방법을 사용 하는 것이 좋습니다.

* 관리자가 일상 업무를 권한 없는 사용자로 수행 하 고 있는지 확인
* 필요한 경우에만 권한 있는 액세스를 부여 하 고 나중에 제거 (just-in-time)
* 권한 있는 계정과 관련 된 감사 활동 로그를 유지 합니다.

완벽한 보안 로드맵 작성에 대한 자세한 내용은 [Microsoft 클라우드 IT 아키텍처 리소스](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources)를 참조하세요. 로드맵의 모든 부분을 구현 하는 데 도움이 되는 Microsoft 서비스와 관련 하려면 Microsoft 담당자에 게 문의 하거나 [엔터프라이즈를 보호 하기 위한 중요 한 사이버 방어 빌드를](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)참조 하세요.

권한 있는 보안 액세스 로드맵의 마지막 단계에 포함되는 구성 요소는 다음과 같습니다.

### <a name="general-preparation"></a>일반적인 준비

#### <a name="review-admin-roles-in-azure-ad"></a>Azure AD에서 관리자 역할 검토

현재 기본 제공 Azure AD 관리자 역할이 최신 상태 인지 확인 하 고 사용자가 필요한 역할만 갖도록 합니다. Azure AD를 사용 하 여 서로 다른 기능을 제공 하는 별도의 관리자를 할당할 수 있습니다. 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](directory-assign-admin-roles.md)을 참조하세요.

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Azure AD 가입 디바이스를 관리하는 사용자 검토

자세한 내용은 [하이브리드 Azure Active Directory 가입 디바이스를 구성하는 방법](../device-management-hybrid-azuread-joined-devices-setup.md)을 참조하세요.

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>[기본 제공 Office 365 관리자 역할](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)의 멤버 검토
Office 365을 사용 하지 않는 경우이 단계를 건너뜁니다.
‎
#### <a name="validate-incident-response-plan"></a>인시던트 대응 계획의 유효성 검사

계획을 향상시키려면 다음과 같이 계획이 예상대로 작동하는지 정기적으로 확인하는 것이 좋습니다.

* 기존 로드맵을 살펴보고 누락된 항목 확인
* 사후 분석에 따라 기존 작업을 수정 하거나 새 사례를 정의 합니다.
* 업데이트 된 인시던트 대응 계획 및 사례가 조직 전체에 배포 되었는지 확인


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure에 대한 액세스를 관리하는 조직을 위한 추가 단계 

[Azure 구독의 소유권을 다른 계정으로 양도](../../cost-management-billing/manage/billing-subscription-transfer.md)할 필요가 있는지 결정합니다.
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"비상": 긴급 상황에서 수행할 작업

![응급 투명 액세스를 위한 계정](./media/directory-admin-roles-secure/emergency.jpeg)

1. 주요 관리자 및 보안 담당자에 게 인시던트에 대 한 정보를 알립니다.

2. 공격 플레이 북을 검토합니다.

3. "사용 중단" 계정 사용자 이름 및 암호 조합에 액세스 하 여 Azure AD에 로그인 합니다.

4. [Azure 지원 요청을 열어](../../azure-portal/supportability/how-to-create-azure-support-request.md) Microsoft로부터 도움을 받습니다.

5. [Azure AD 로그인 보고서](../reports-monitoring/overview-reports.md)를 확인합니다. 이벤트가 발생 하는 시간과 보고서에 포함 될 때 사이에 시간이 있을 수 있습니다.

6. 하이브리드 환경의 경우 온-프레미스 인프라와 페더레이션 된 AD FS 서버를 사용할 수 없는 경우에는 페더레이션 인증에서 암호 해시 동기화를 사용 하도록 일시적으로 전환할 수 있습니다. 이 스위치는 AD FS 서버를 사용할 수 있게 될 때까지 도메인 페더레이션을 다시 관리 인증으로 되돌립니다.

7. 권한 있는 계정에 대한 이메일을 모니터링합니다.

8. 잠재적인 법의학 및 법적 조사를 위해 관련 로그의 백업을 저장해야 합니다.

Microsoft Office 365에서 보안 인시던트를 처리하는 방법에 대한 자세한 내용은 [Microsoft Office 365의 보안 인시던트 관리](https://aka.ms/Office365SIM)를 참조하세요.

## <a name="faq-answers-for-securing-privileged-access"></a>FAQ: 권한 있는 액세스 보안에 대 한 답변  

**Q:** 보안 액세스 구성 요소를 아직 구현하지 않은 경우 어떻게 해야 할까요?

**답변:** 둘 이상의 비상 계정을 정의하고, 권한 있는 관리자 계정에 MFA를 할당하고, 사용자 계정과 전역 관리자 계정을 분리합니다.

**Q:** 위반이 발생되면 가장 먼저 해결해야 하는 문제는 무엇인가요?

**답변:** 매우 노출 되는 개인에 대해 가장 강력한 인증을 요구 해야 합니다.

**Q:** 권한 있는 관리자가 비활성화되면 어떻게 되나요?

**답변:** 항상 최신 상태로 유지 되는 전역 관리자 계정을 만듭니다.

**Q:** 전역 관리자가 한 명 뿐 이며 연결할 수 없는 경우 어떻게 되나요?

**답변:** 비상 계정 중 하나를 사용하여 즉각적인 권한 있는 액세스를 얻습니다.

**Q:** 조직 내에서 관리자를 보호하려면 어떻게 해야 할까요?

**답변:** 관리자는 항상 "권한이 없는" 표준 사용자로 일상적인 업무를 수행해야 합니다.

**Q:** Azure AD 내에서 관리자 계정을 만드는 가장 좋은 방법은 무엇인가요?

**답변:** 특정 관리 작업에 대한 권한 있는 액세스를 예약합니다.

**Q:** 영구 관리자 액세스를 줄이기 위한 도구는 무엇인가요?

**답변:** PIM(Privileged Identity Management) 및 Azure AD 관리자 역할입니다.

**Q:** Azure AD에 관리자 계정을 동기화할 때 Microsoft의 입장은 무엇인가요?

**답변:** 계층 0 관리자 계정은 온-프레미스 AD 계정에만 사용 됩니다. 이러한 계정은 일반적으로 클라우드의 Azure AD와 동기화 되지 않습니다. 계층 0 관리자 계정에는 온-프레미스 Active Directory 포리스트, 도메인, 도메인 컨트롤러 및 자산에 대 한 직접 또는 간접 관리 제어 권한이 있는 계정, 그룹 및 기타 자산이 포함 됩니다.

**Q:** 관리자가 포털에서 임의의 관리자 액세스 권한을 할당하지 못하게 하려면 어떻게 해야 할까요?

**답변:** 모든 사용자와 대부분의 관리자에게 권한이 없는 계정을 사용합니다. 먼저 조직의 공간을 개발하여 권한이 부여되어야 하는 몇 개의 관리자 계정을 결정합니다. 그리고 새로 만든 관리자를 모니터링 합니다.

## <a name="next-steps"></a>다음 단계

* [Microsoft 보안 센터 - 제품 보안](https://www.microsoft.com/trustcenter/security) – Microsoft 클라우드 제품 및 서비스의 보안 기능

* [Microsoft 보안 센터 - 준수](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – 클라우드 서비스를 위한 포괄적인 Microsoft 규정 준수 제품 집합

* [위험 평가를 수행 하는 방법에](https://www.microsoft.com/trustcenter/guidance/risk-assessment) 대 한 지침-Microsoft 클라우드 서비스에 대 한 보안 및 규정 준수 요구 사항 관리

### <a name="other-microsoft-online-services"></a>기타 Microsoft Online Services

* [Microsoft Intune 보안](https://www.microsoft.com/trustcenter/security/intune-security) – Intune은 클라우드에서 모바일 디바이스 관리, 모바일 애플리케이션 관리 및 PC 관리 기능을 제공합니다.

* [Microsoft Dynamics 365 보안](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365는 CRM(고객 관계 관리) 및 ERP(전사적 자원 관리) 기능을 통합하는 Microsoft 클라우드 기반 솔루션입니다.
