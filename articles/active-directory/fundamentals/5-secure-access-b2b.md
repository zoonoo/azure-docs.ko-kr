---
title: Azure Active Directory B2B Collaboration을 사용하여 관리되는 협업으로 전환하기
description: Azure AD B2B Collaboration을 사용하여 관리되는 협업으로 이동합니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4206ba7617032e34310682d1468e6b1b661b8c8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648590"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B Collaboration을 사용하여 관리되는 협업으로 전환하기 

컨트롤에서 협업을 가져오는 작업은 리소스에 대한 외부 액세스를 보호하는 데 있어 중요합니다. 이 문서를 진행하기 전에 다음을 수행해야 합니다.

* [보안 태세 결정](1-secure-access-posture.md)

* [현재 상태 파악](2-secure-access-current-state.md)

* [보안 계획 수립](3-secure-access-plan.md)

* [그룹 및 보안을 함께 사용하는 방법 이해](4-secure-access-groups.md)

해당 작업을 마치면 사용자는 제어된 협업으로의 이동 준비가 완료된 것입니다. 이 문서에서는 모든 외부 협업을 [Azure Active Directory B2B Collaboration](../external-identities/what-is-b2b.md)(Azure AD B2B)으로 이동하는 방법을 안내합니다. Azure Ad B2B는 [Azure AD External Identities](../external-identities/compare-with-b2c.md)의 기능입니다.

## <a name="control-who-your-organization-collaborates-with"></a>조직과 협업하는 사용자 제어하기

사용자는 협업을 진행할 조직과 사용자 조직 내에서 협업을 개시할 사람의 범위를 제한할지 여부를 결정해야 합니다. 대부분의 조직에서는 사업부에서 협업할 대상을 결정하도록 허락하는 방식을 취하고 필요에 따라 승인 및 감독을 위임합니다. 예를 들어 일부 정부, 교육 및 금융 서비스 조직은 오픈 협업을 허용하지 않습니다. 이 섹션의 나머지 부분에 설명된 대로 Azure AD 기능을 사용하여 협업 범위를 정할 수 있습니다.

### <a name="determine-collaboration-partners"></a>협업 파트너 확인하기

먼저, 현재 협업 중인 조직과 해당 조직 사용자의 도메인을 문서화해야 합니다. 협업 파트너가 하나라도 여러 도메인이 있을 수 있습니다. 예를 들어 파트너에게 개별 도메인을 사용하는 여러 사업부가 있을 수 있습니다.

다음으로, 다음을 사용하여 나중에 협업을 할 것인지 결정합니다. 

* 모든 도메인(가장 포괄적)

* 명시적으로 거부된 도메인을 제외한 모든 도메인 

* 특정 도메인만(가장 제한적)

> [!NOTE]
> 협업 설정이 제한적일수록 사용자가 승인된 협업 프레임워크를 벗어날 가능성이 높아집니다. 보안 요구 사항이 허용하는 가장 광범위한 협업을 사용하도록 설정하고 지나치게 제한적이지 않게 해당 협업을 면밀히 검토하는 것이 좋습니다. 

또한 단일 도메인으로 제한하면 해당 사용자와 관련이 없는 다른 도메인을 가진 조직과의 승인 받은 협업을 실수로 막을 수도 있습니다. 예를 들어 Contoso 조직과 비즈니스를 진행하는 경우 Contoso와의 최초 연락 지점이 “.com” 도메인이 포함된 메일을 가지고 있는 미국 내 직원일 수 있습니다. 그러나 “.com” 도메인만 허용하면 “. ca” 도메인을 가진 캐나다 직원을 실수로 생략할 수 있습니다. 

특정 협업 파트너만을 허용하고자 하는 경우가 있을 수 있습니다. 예를 들어 대학 시스템은 해당 교직원이 리소스 테넌트에만 액세스하도록 허용할 수 있습니다. 또는 대기업이 특정 자회사만 서로 협업을 하도록 허용하여 필수 프레임워크를 준수할 수도 있습니다.

#### <a name="using-allow-and-deny-lists"></a>허용 및 거부 목록 사용하기

허용 목록이나 거부 목록을 사용하여 특정 조직의 [B2B 사용자 초대를 제한](../external-identities/allow-deny-list.md)할 수 있습니다. 허용 및 거부 목록 중 하나만 사용할 수 있으며 둘 다 사용할 수는 없습니다.

* [허용 목록](../external-identities/allow-deny-list.md)은 열거된 도메인에만 협업을 제한합니다. 다른 모든 도메인은 거부 목록에 있습니다.

* [거부 목록](../external-identities/allow-deny-list.md)을 활용하여 거부 목록에 없는 도메인과 협업을 진행할 수 있습니다.

> [!IMPORTANT]
> 해당 목록은 디렉터리에 이미 있는 사용자에게는 적용되지 않습니다. 또한 비즈니스용 OneDrive에는 적용되지 않으며 SharePoint에서는 별도의 거부 목록을 허용하지 않습니다.

일부 조직에서는 해당 거부 목록에 대해 관리형 보안 공급자가 제공하는 ‘잘못된 작업자’라고 알려진 도메인 목록을 사용합니다. 예를 들어 조직이 Contoso와 합법적으로 비즈니스를 진행하면서 .com 도메인을 사용하는 경우, Contoso. org 도메인을 사용하는 관련 없는 조직이 피싱 공격을 시도하여 Contoso 직원을 가장할 수도 있습니다. 

## <a name="control-how-external-users-gain-access"></a>외부 사용자가 액세스 권한을 얻는 방법 제어하기

Azure AD B2B를 사용하여 외부 파트너와 협업을 하는 방법에는 여러 가지가 있습니다. 협업을 시작하려면 초대하거나 파트너가 리소스에 액세스할 수 있도록 설정합니다. 사용자는 다음에 응답하여 액세스 권한을 얻을 수 있습니다.

* [메일을 통해 보낸 초대](../external-identities/redemption-experience.md)나 리소스를 [공유하기 위한 직접 링크](../external-identities/redemption-experience.md)를 교환합니다.

* 만든 [애플리케이션을 통해](../external-identities/self-service-sign-up-overview.md) 액세스 요청하기

* [내 액세스](../governance/entitlement-management-request-access.md) 포털을 통해 액세스 요청하기

Azure AD B2B를 사용하도록 설정하면 기본적으로 직접 링크 및 메일 초대를 통해 게스트 사용자를 초대하는 기능을 사용할 수 있습니다. 메일 OTP 및 셀프 서비스 포털을 통한 초대는 현재 미리 보기가 제공되며 외부 ID 내에서 사용하도록 설정해야 합니다. | Azure AD 포털의 외부 협업 설정

### <a name="control-who-can-invite-guest-users"></a>게스트 사용자를 초대할 수 있는 사용자 제어하기

게스트 사용자가 리소스에 액세스하도록 초대할 수 있는 사용자를 결정합니다.

* 가장 제한적인 설정은 [게스트 초대자 역할](../external-identities/delegate-invitations.md)을 부여받은 관리자 및 해당 사용자만 게스트를 초대하도록 허용하는 것입니다.

* 보안 요구 사항이 허용한다면 userType이 Member인 모든 사용자에게 게스트 초대를 허용하는 것이 좋습니다.

* Azure AD B2B 사용자의 기본 계정 유형인 userType이 Guest인 사용자가 다른 게스트를 초대할 수 있는지 결정합니다. 

![게스트 초대 설정을 보여 주는 스크린샷](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>외부 사용자에 대한 추가 정보 수집

Azure AD 권한 관리를 사용하는 경우 외부 사용자가 답변할 질문을 구성할 수 있습니다. 그런 다음 승인자에게 질문을 제시하여 결정을 내리도록 돕습니다. 승인자가 승인하는 액세스에 대한 관련 정보를 알 수 있도록 각 [액세스 패키지 정책](../governance/entitlement-management-access-package-approval-policy.md)에 대해 서로 다른 질문 집합을 구성할 수 있습니다. 예를 들어 액세스 패키지 하나가 공급업체 액세스를 위한 것일 경우 요청자에게 공급업체 계약 번호를 요청할 수 있습니다. 공급자를 위해 마련된 액세스 패키지에 대해서는 해당 공급자의 소재 국가를 밝히도록 요청할 수 있습니다.

셀프 서비스 포털을 사용하는 경우 사용자가 [API 커넥터](../external-identities/api-connectors-overview.md)를 사용하여 등록하는 동안 사용자에 대한 추가 특성을 수집할 수 있습니다. 그다음 이 특성을 사용하여 액세스 권한을 할당할 수 있습니다. 예를 들어 등록 프로세스 중에 공급자 ID를 수집하는 경우 해당 특성을 사용하여 해당 공급자의 그룹 또는 액세스 패키지에 동적으로 할당할 수 있습니다. Azure Portal에 사용자 지정 특성을 만들고 셀프 서비스 가입 사용자 흐름에서 사용할 수 있습니다. 또한 [Microsoft Graph API](../../active-directory-b2c/microsoft-graph-operations.md)를 사용하여 이러한 특성을 읽고 쓸 수도 있습니다. 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>Azure AD 사용자에 대한 초대 사용 문제 해결

Azure AD를 사용하는 협업 파트너에서 게스트 사용자를 초대하는 경우 초대를 사용하는 데 있어 세 가지 문제가 있습니다.

* 허용 목록을 사용하고 사용자의 도메인이 허용 목록에 포함되지 않는 경우

* 협업 파트너의 홈 테넌트에 외부 사용자와 협업을 방해하는 테넌트 제한이 있는 경우

* 사용자가 파트너의 Azure AD 테넌트에 속하지 않는 경우 예를 들어 Active Directory(또는 다른 온-프레미스 IdP)에만 있는 사용자가 contoso.com에 있는 경우 메일 OTP 프로세스를 통해서만 초대를 사용할 수 있습니다. 자세한 내용은 [초대 사용 흐름](../external-identities/redemption-experience.md)을 참조하세요.

## <a name="control-what-external-users-can-access"></a>외부 사용자가 액세스할 수 있는 항목 제어하기

대부분의 조직은 모놀리식 방식을 채택하지 않습니다. 즉, 외부 사용자와 공유하기에 적합한 리소스가 있으며 외부 사용자가 액세스하면 안 되는 리소스도 있습니다. 따라서 외부 사용자가 액세스할 수 있는 리소스를 제어해야 합니다. [액세스 제어를 위한 권한 관리 및 액세스 패키지](6-secure-access-entitlement-managment.md)를 사용하여 특정 리소스에 대한 액세스를 제어하는 것이 좋습니다.

기본적으로 게스트 사용자는 그룹 구성원 자격을 비롯하여 테넌트 구성원 및 기타 파트너에 대한 정보 및 특성을 볼 수 있습니다. 보안 요구 사항에 따라 이 정보에 대한 외부 사용자 액세스를 제한할 필요가 있는지 고려합니다.

![외부 협업 설정 구성을 보여 주는 스크린샷](media/secure-external-access/5-external-collaboration-settings.png)

게스트 사용자에게 다음과 같은 제한 사항을 두는 것이 좋습니다.

* **디렉터리의 검색 그룹 및 기타 속성에 대한 게스트 액세스 제한**

   * 외부 협업 설정을 사용하여 구성원이 아닌 그룹에 대한 게스트의 읽기 기능을 제한합니다.

* **직원 전용 앱에 대한 액세스 차단하기**

   * 게스트가 아닌 사용자에게만 적합한 Azure AD 통합 애플리케이션에 대한 액세스를 차단하는 조건부 액세스 정책을 만듭니다. 

* **Azure Portal에 대한 액세스를 차단합니다. 드물게 필요한 예외를 만들 수 있습니다.** 

   * 모든 게스트 및 외부 사용자를 포함하는 조건부 액세스 정책을 만든 다음 [액세스를 차단하는 정책을 구현](../../role-based-access-control/conditional-access-azure-management.md)합니다.

 

## <a name="remove-users-who-no-longer-need-access"></a>더 이상 액세스할 필요가 없는 사용자 제거하기

[더 이상 액세스할 필요가 없는 사용자를 검토하고 제거](../governance/access-reviews-external-users.md)할 수 있도록 현재 액세스를 평가합니다. 테넌트의 외부 사용자와 구성원 계정을 가진 사용자를 게스트로 포함합니다. 

일부 조직에서는 공급 업체, 파트너 및 계약자와 같은 외부 사용자를 구성원으로 추가했습니다. 예를 들면 해당 구성원은 특정 특성 또는 다음으로 시작하는 사용자 이름을 가질 수 있습니다.

* v- 공급업체

* p- 파트너

* c- 계약자

구성원 계정을 사용하는 외부 사용자를 평가하여 여전히 액세스 권한이 필요한지 확인합니다. 그렇다면 다음 섹션에 설명된 대로 해당 사용자를 Azure AD B2B로 전환합니다.

또한 권한 관리 또는 Azure AD B2B를 통해 초대되지 않은 게스트 사용자가 있을 수 있습니다.

해당 사용자를 찾으려면 다음을 수행합니다.

* [권한 관리를 통해 초대되지 않은 게스트 사용자를 찾습니다](../governance/access-reviews-external-users.md).

   * [SAMPLE PowerShell 스크립트](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)를 제공합니다.

다음 섹션에 설명된 대로 해당 사용자를 Azure AD B2B 사용자로 전환합니다.

## <a name="transition-your-current-external-users-to-b2b"></a>현재 외부 사용자를 B2B로 전환하기

Azure AD B2B를 사용하지 않은 경우 테넌트에 직원이 아닌 사용자가 있을 가능성이 높습니다. 이 계정을 Azure AD B2B 외부 사용자 계정으로 전환하고 UserType을 Guest로 변경하는 것이 좋습니다. 이렇게 하면 여러 가지 방법으로 Azure AD 및 Microsoft 365를 활용하여 외부 사용자를 다르게 처리할 수 있습니다. 그 방법에는 다음이 포함됩니다.

* 조건부 액세스 정책에서 게스트 사용자를 포함하거나 제외합니다.

* 액세스 패키지 및 액세스 검토에서 게스트 사용자를 포함하거나 제외합니다.

* Teams, SharePoint 및 기타 리소스에 대한 외부 액세스를 포함하거나 제외합니다.

현재 액세스, UPN 및 그룹 멤버 자격을 유지하면서 해당 내부 사용자를 전환하려면 [외부 사용자를 B2B 협업에 초대](../external-identities/invite-internal-users.md)를 참조하세요.

## <a name="decommission-undesired-collaboration-methods"></a>원치 않는 협업 방법 해제

관리되는 협업으로 전환을 완료하려면 원치 않는 협업 방법을 해제해야 합니다. IT가 협업을 제어하길 바라는 정도와 보안 상태에 따라 해제합니다. IT 대 최종 사용자 정의 컨트롤에 대한 자세한 내용은 [외부 액세스를 위한 보안 상태 결정](1-secure-access-posture.md)을 참조하세요.

다음과 같은 협업 매개 수단에 대해 사용자가 평가하고자 할 수 있습니다.

### <a name="direct-invitation-through-microsoft-teams"></a>Microsoft Teams를 통한 직접 초대

기본적으로 Teams는 외부 액세스를 허용합니다. 즉, 조직이 모든 외부 도메인과 통신할 수 있습니다. Teams만을 위해서 특정 도메인을 제한하거나 허용하려면 [Teams Admin 포털](https://admin.teams.microsoft.com/company-wide-settings/external-communications)에서 이 작업을 수행할 수 있습니다. 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>SharePoint와 OneDrive를 통한 직접 공유

SharePoint 및 OneDrive를 통한 직접 공유는 권한 관리 프로세스 외부에서 사용자를 추가할 수 있습니다. 이 구성에 대한 자세한 내용은 [Microsoft Teams, SharePoint 및 비즈니스용 OneDrive 를 사용한 액세스 관리](9-secure-access-teams-sharepoint.md)를 참조하세요. 원하는 경우 [사용자의 개인 OneDrive 사용을 차단](/office365/troubleshoot/group-policy/block-onedrive-use-from-office)할 수도 있습니다.

### <a name="sending-documents-through-email"></a>메일을 통해 문서 보내기

사용자는 메일을 통해 외부 사용자에게 문서를 보냅니다. 민감도 레이블을 사용하여 해당 문서에 대한 액세스를 제한하고 암호화하는 방법을 고려합니다. 자세한 내용은 민감도 레이블을 사용한 액세스 관리를 참조하세요.

### <a name="unsanctioned-collaboration-tools"></a>비사용 권한 협업 도구

협업 도구의 환경은 광범위합니다. 사용자는 Google Docs, DropBox, Slack 또는 Zoom과 같은 플랫폼을 포함하여 공식 업무 이외에서 많이 사용할 가능성이 높습니다. 해당 도구를 회사 네트워크에서 사용하지 못하도록 방화벽 수준에서 차단할 수 있으며 조직 관리 디바이스를 위한 모바일 애플리케이션 관리를 사용하여 차단할 수도 있습니다. 그러나 이렇게 하면 해당 플랫폼의 모든 사용 권한 인스턴스가 차단되고 관리되지 않는 디바이스에서의 액세스는 차단되지 않습니다. 필요한 경우, 사용하지 않는 플랫폼을 차단하고 사용해야 하는 플랫폼에 대해 비사용 권한 사용을 금하는 비즈니스 정책을 만듭니다. 

비사용 권한 애플리케이션을 관리하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [연결된 앱을 제어합니다.](/cloud-app-security/governance-actions)

* [애플리케이션 사용 권한 부여 및 취소](/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>다음 단계

리소스에 대한 외부 액세스 보안에 대해서는 다음 문서를 참조하세요. 이 작업은 나열된 순서대로 수행하는 것이 좋습니다.

1. [외부 액세스를 위한 보안 상태 결정](1-secure-access-posture.md)

2. [현재 상태 파악](2-secure-access-current-state.md)

3. [거버넌스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위한 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)(여기에 표시)

6. [권한 관리를 사용하여 안전하게 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용하여 안전하게 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용하여 안전하게 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams, OneDrive 및 SharePoint에 대한 액세스 보호](9-secure-access-teams-sharepoint.md)