---
title: Azure Active Directory B2B 공동 작업으로 관리 되는 공동 작업으로 전환
description: Azure Ad B2B 공동 작업을 사용 하 여 관리 되는 공동 작업으로 이동 합니다.
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
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648590"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B 공동 작업으로 관리 되는 공동 작업으로 전환 

제어에서 공동 작업을 가져오는 작업은 리소스에 대 한 외부 액세스를 보호 하기 위한 핵심입니다. 이 문서를 진행 하기 전에 다음을 수행 해야 합니다.

* [보안 상태를 결정 했습니다.](1-secure-access-posture.md)

* [현재 상태를 검색 했습니다.](2-secure-access-current-state.md)

* [보안 계획을 만듦](3-secure-access-plan.md)

* [그룹 및 보안을 함께 사용 하는 방법 이해](4-secure-access-groups.md)

이러한 작업을 완료 하면 제어 된 공동 작업으로 이동할 준비가 된 것입니다. 이 문서에서는 모든 외부 공동 작업을 [AZURE ACTIVE DIRECTORY B2B 공동 작업](../external-identities/what-is-b2b.md) (AZURE AD B2B)으로 이동 하는 방법을 안내 합니다. Azure Ad B2B는 [AZURE Ad 외부 id](../external-identities/compare-with-b2c.md)의 기능입니다.

## <a name="control-who-your-organization-collaborates-with"></a>조직에서 협업 하는 사용자 제어

사용자가 공동 작업을 수행할 수 있는 조직과 조직 내에서 공동 작업을 시작할 수 있는 사용자를 제한할지 여부를 결정 해야 합니다. 대부분의 조직에서는 비즈니스 단위를 사용 하도록 허용 하는 방법을 사용 하 여 공동 작업을 결정 하 고 필요에 따라 승인 및 감독을 위임 합니다. 예를 들어 일부 정부, 교육 및 금융 서비스 조직은 오픈 공동 작업을 허용 하지 않습니다. 이 섹션의 나머지 부분에 설명 된 대로 Azure AD 기능을 사용 하 여 공동 작업의 범위를 바꿀 수 있습니다.

### <a name="determine-collaboration-partners"></a>공동 작업 파트너 확인

먼저, 현재 공동 작업 중인 조직과 해당 조직의 사용자에 대 한 도메인을 문서화 했는지 확인 합니다. 하나의 공동 작업 파트너에 여러 도메인이 있을 수 있습니다. 예를 들어 파트너가 개별 도메인을 사용 하는 여러 비즈니스 단위를 가질 수 있습니다.

다음으로, 다음을 사용 하 여 나중에 공동 작업을 수행할 것인지 결정 합니다. 

* 모든 도메인 (가장 포함)

* 명시적으로 거부 된 도메인을 제외한 모든 도메인 

* 특정 도메인만 (가장 제한적)

> [!NOTE]
> 공동 작업 설정을 더 제한적으로 설정 하면 사용자가 승인 된 공동 작업 프레임 워크의 외부에서 이동할 가능성이 높아집니다. 보안 요구에서 허용 하는 가장 광범위 한 공동 작업을 사용 하도록 설정 하 고 지나치게 제한적이 지 않고 해당 공동 작업을 면밀히 검토 하는 것이 좋습니다. 

또한 단일 도메인으로 제한 하면 해당 사용자에 대해 관련이 없는 다른 도메인이 있는 조직과의 권한 있는 공동 작업을 실수로 방지할 수 있습니다. 예를 들어 Contoso 조직에서 비즈니스를 수행 하는 경우 Contoso와의 최초 연락 지점은 ".com" 도메인이 포함 된 전자 메일이 있는 미국 기반 직원 중 하나일 수 있습니다. 그러나 ".com" 도메인만 허용 하는 경우 ". ca" 도메인이 있는 캐나다 직원은 실수로 생략할 수 있습니다. 

특정 공동 작업 파트너만 허용 하려는 경우가 있습니다. 예를 들어 대학 시스템은 자신의 교직원에 게 리소스 테 넌 트 액세스를 허용 하려는 경우에만 사용할 수 있습니다. 또는 해당 계열사가 필요한 프레임 워크를 준수 하기 위해 특정 자회사의 공동 작업을 허용할 수 있습니다.

#### <a name="using-allow-and-deny-lists"></a>허용 및 거부 목록 사용

허용 목록 또는 거부 목록을 사용 하 여 특정 조직의 [B2B 사용자에 대 한 초대를 제한할](../external-identities/allow-deny-list.md) 수 있습니다. 허용 또는 거부 목록만 사용할 수 있으며 둘 다 사용할 수는 없습니다.

* [허용 목록은](../external-identities/allow-deny-list.md) 나열 된 도메인 으로만 공동 작업을 제한 합니다. 다른 모든 도메인은 실제로 거부 목록에 있습니다.

* [거부 목록](../external-identities/allow-deny-list.md) 에는 거부 목록에 없는 도메인과의 공동 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 이러한 목록은 디렉터리에 이미 있는 사용자에 게는 적용 되지 않습니다. 또한 비즈니스용 OneDrive에는 적용 되지 않으며 SharePoint에는 개별 인 거부 허용 목록이 적용 되지 않습니다.

일부 조직에서는 해당 거부 목록에 대해 관리 되는 보안 공급자가 제공 하는 알려진 ' 잘못 된 작업자 ' 도메인 목록을 사용 합니다. 예를 들어 조직이 Contoso를 사용 하 여 비즈니스를 합법적으로 수행 하 고 .com 도메인을 사용 하는 경우 Contoso. org 도메인을 사용 하 고 Contoso 직원을 가장 하는 피싱 공격을 시도 하 고 있는 관련이 없는 조직이 있을 수 있습니다. 

## <a name="control-how-external-users-gain-access"></a>외부 사용자가 액세스 권한을 얻는 방법 제어

Azure AD B2B를 사용 하 여 외부 파트너와 공동 작업 하는 방법에는 여러 가지가 있습니다. 공동 작업을 시작 하려면 파트너에 게 리소스에 대 한 액세스를 초대 하거나 사용 하도록 설정 합니다. 사용자는 다음에 응답 하 여 액세스 권한을 얻을 수 있습니다.

* [전자 메일을 통해 보낸 초대](../external-identities/redemption-experience.md)를 교환 하거나 리소스를 [공유 하기 위한 직접 링크를 만듭니다](../external-identities/redemption-experience.md) .

* 만든 [응용 프로그램을 통해](../external-identities/self-service-sign-up-overview.md) 액세스 요청

* [내 액세스](../governance/entitlement-management-request-access.md) 포털을 통해 액세스 요청

Azure AD B2B를 사용 하도록 설정 하면 기본적으로 직접 링크 및 메일 초대를 통해 게스트 사용자를 초대 하는 기능을 사용할 수 있습니다. 전자 메일 OTP 및 셀프 서비스 포털을 통한 초대는 현재 미리 보기로 제공 되며 외부 Id 내에서 사용 하도록 설정 해야 합니다. | Azure AD 포털의 외부 공동 작업 설정.

### <a name="control-who-can-invite-guest-users"></a>게스트 사용자를 초대할 수 있는 사용자 제어

게스트 사용자가 리소스에 액세스 하도록 초대할 수 있는 사용자를 결정 합니다.

* 가장 제한적인 설정은 [게스트 초대자 역할이](../external-identities/delegate-invitations.md) 게스트를 초대할 수 있는 관리자와 해당 사용자만 허용 하는 것입니다.

* 보안 요구 사항에 따라 허용 되는 경우 userType 멤버의 모든 사용자에 게 게스트 초대를 허용 하는 것이 좋습니다.

* 다른 게스트를 초대할 수 있도록 Azure AD B2B 사용자의 기본 계정 유형인 userType를 사용 하는 사용자를 결정 합니다. 

![게스트 초대 설정의 스크린샷](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>외부 사용자에 대 한 추가 정보 수집

Azure AD 자격 관리를 사용 하는 경우 외부 사용자가 답변할 질문을 구성할 수 있습니다. 그런 다음 결정을 내리는 데 도움이 되는 승인자에 게 질문을 표시 합니다. 승인자가 승인 하는 액세스에 대 한 관련 정보를 가질 수 있도록 각 [액세스 패키지 정책](../governance/entitlement-management-access-package-approval-policy.md) 에 대해 서로 다른 질문 집합을 구성할 수 있습니다. 예를 들어 공급 업체 액세스를 위한 액세스 패키지가 하나인 경우 요청자에 게 공급 업체 계약 번호를 요청 하는 메시지가 표시 될 수 있습니다. 공급 업체를 위해 설계 된 다른 액세스 패키지는 자신의 국가를 요청할 수 있습니다.

셀프 서비스 포털을 사용 하는 경우 [API 커넥터](../external-identities/api-connectors-overview.md) 를 사용 하 여 사용자가 등록 하는 동안 사용자에 대 한 추가 특성을 수집할 수 있습니다. 그런 다음 이러한 특성을 사용 하 여 액세스 권한을 할당할 수 있습니다. 예를 들어 등록 프로세스 중에 공급자 ID를 수집 하는 경우 해당 특성을 사용 하 여 해당 공급자의 그룹 또는 액세스 패키지에 동적으로 할당할 수 있습니다. Azure Portal에 사용자 지정 특성을 만들고 셀프 서비스 가입 사용자 흐름에서 사용할 수 있습니다. 또한 [Microsoft Graph API](../../active-directory-b2c/microsoft-graph-operations.md)를 사용하여 이러한 특성을 읽고 쓸 수도 있습니다. 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>Azure AD 사용자에 대 한 초대 상환 문제 해결

Azure AD를 사용 하 여 공동 작업 파트너의 게스트 사용자를 초대 하는 경우 세 개의 인스턴스가 초대를 교환 하는 데 문제가 있습니다.

* 허용 목록을 사용 하면 사용자의 도메인이 허용 목록에 포함 되지 않습니다.

* 공동 작업 파트너의 홈 테 넌 트에 외부 사용자와 공동 작업을 방해 하는 테 넌 트 제한이 있는 경우.

* 사용자가 파트너의 Azure AD 테 넌 트에 속하지 않는 경우 예를 들어 Active Directory (또는 다른 온-프레미스 IdP)에만 있는 사용자가 contoso.com에 있는 경우 전자 메일 OTP 프로세스를 통해서만 초대를 교환할 수 있습니다. 자세한 내용은 [초대 상환 흐름](../external-identities/redemption-experience.md)을 참조 하세요.

## <a name="control-what-external-users-can-access"></a>외부 사용자가 액세스할 수 있는 항목 제어

대부분의 조직은 모놀리식 되지 않습니다. 즉, 외부 사용자와 공유 하는 데 적합 한 일부 리소스가 있으며, 일부는 외부 사용자가 액세스 하지 못하도록 합니다. 따라서 외부 사용자가 액세스할 수 있는 항목을 제어 해야 합니다. 권한 [관리 및 액세스 패키지](6-secure-access-entitlement-managment.md) 를 사용 하 여 특정 리소스에 대 한 액세스를 제어 하는 것이 좋습니다.

기본적으로 게스트 사용자는 그룹 구성원 자격을 비롯 하 여 테 넌 트 구성원 및 기타 파트너에 대 한 정보 및 특성을 볼 수 있습니다. 보안 요구 사항이이 정보에 대 한 외부 사용자 액세스를 제한 하기 위해를 호출 하는지 고려 합니다.

![외부 공동 작업 설정 구성의 스크린샷](media/secure-external-access/5-external-collaboration-settings.png)

게스트 사용자에 게 다음과 같은 제한 사항을 적용 하는 것이 좋습니다.

* **디렉터리의 검색 그룹 및 기타 속성에 대 한 게스트 액세스 제한**

   * 외부 공동 작업 설정을 사용 하 여 구성원이 아닌 그룹을 읽는 게스트 기능을 제한할 수 있습니다.

* **직원 전용 앱에 대 한 액세스를 차단** 합니다.

   * 비 게스트 사용자 에게만 적합 한 Azure AD 통합 응용 프로그램에 대 한 액세스를 차단 하는 조건부 액세스 정책을 만듭니다. 

* **Azure Portal에 대 한 액세스를 차단 합니다. 거의 필요한 예외를 만들 수** 있습니다. 

   * 모든 게스트 및 외부 사용자를 포함 하는 조건부 액세스 정책을 만든 다음 [액세스를 차단 하는 정책을 구현](../../role-based-access-control/conditional-access-azure-management.md)합니다.

 

## <a name="remove-users-who-no-longer-need-access"></a>더 이상 액세스할 필요가 없는 사용자 제거

[더 이상 액세스할 필요가 없는 사용자를 검토 하 고 제거할](../governance/access-reviews-external-users.md)수 있도록 현재 액세스를 평가 합니다. 테 넌 트의 외부 사용자를 게스트로, 구성원 계정을 가진 사용자를 포함 합니다. 

일부 조직에서는 공급 업체, 파트너 및 계약자와 같은 외부 사용자를 구성원으로 추가 했습니다. 이러한 멤버는 특정 특성 또는로 시작 하는 사용자 이름을 가질 수 있습니다. 예를 들면 다음과 같습니다.

* v-공급 업체

* p-파트너

* c-계약자 용

구성원 계정을 사용 하는 외부 사용자를 평가 하 여 여전히 액세스 권한이 필요한 지 확인 합니다. 그렇다면 다음 섹션에 설명 된 대로 이러한 사용자를 Azure AD B2B로 전환 합니다.

또한 자격 관리 또는 Azure AD B2B를 통해 초대 되지 않은 게스트 사용자가 있을 수 있습니다.

이러한 사용자를 찾으려면 다음을 수행할 수 있습니다.

* [자격 관리를 통해 초대 되지 않은 게스트 사용자를 찾습니다](../governance/access-reviews-external-users.md).

   * [샘플 PowerShell 스크립트를 제공 합니다.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)

다음 섹션에 설명 된 대로 이러한 사용자를 Azure AD B2B 사용자로 전환 합니다.

## <a name="transition-your-current-external-users-to-b2b"></a>현재 외부 사용자를 B2B로 전환

Azure AD B2B를 사용 하지 않은 경우 테 넌 트에 직원이 아닌 사용자가 있을 가능성이 높습니다. 이러한 계정을 Azure AD B2B 외부 사용자 계정으로 전환 하 고 UserType를 Guest로 변경 하는 것이 좋습니다. 이렇게 하면 여러 가지 방법으로 Azure AD Microsoft 365를 활용 하 여 외부 사용자를 다르게 처리할 수 있습니다. 이러한 방법에는 다음이 포함 됩니다.

* 조건부 액세스 정책에서 게스트 사용자를 포함 하거나 제외 합니다.

* 액세스 패키지 및 액세스 검토에서 게스트 사용자를 포함 하거나 제외 합니다.

* 팀, SharePoint 및 기타 리소스에 대 한 외부 액세스를 포함 하거나 제외 합니다.

현재 액세스, UPN 및 그룹 멤버 자격을 유지 하면서 이러한 내부 사용자를 전환 하려면 [외부 사용자를 B2B 공동 작업에 초대](../external-identities/invite-internal-users.md)를 참조 하세요.

## <a name="decommission-undesired-collaboration-methods"></a>원치 않는 공동 작업 방법 서비스 해제

관리 되는 공동 작업으로의 전환을 완료 하려면 원치 않는 공동 작업 방법의 서비스를 해제 해야 합니다. 서비스를 해제 하는 것은 공동 작업 및 보안 상태에 대 한 제어 수준을 결정 하는 것입니다. 최종 사용자 정의 컨트롤에 대 한 자세한 내용은 [외부 액세스를 위한 보안 상태 결정](1-secure-access-posture.md)을 참조 하세요.

평가 하려는 공동 작업 차량은 다음과 같습니다.

### <a name="direct-invitation-through-microsoft-teams"></a>Microsoft 팀을 통한 직접 초대

기본적으로 팀은 외부 액세스를 허용 합니다. 즉, 조직이 모든 외부 도메인과 통신할 수 있습니다. 팀의 특정 도메인만 제한 하거나 허용 하려면 [팀 관리 포털](https://admin.teams.microsoft.com/company-wide-settings/external-communications)에서이 작업을 수행할 수 있습니다. 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>SharePoint 및 OneDrive를 통한 직접 공유

SharePoint 및 OneDrive를 통한 직접 공유는 자격 관리 프로세스 외부에 사용자를 추가할 수 있습니다. 이러한 구성에 대 한 자세한 내용은 [Microsoft 팀, SharePoint 및 비즈니스용 OneDrive](9-secure-access-teams-sharepoint.md) 를 사용 하 여 액세스 관리를 참조 하세요. 원하는 경우 [사용자의 개인 onedrive 사용을 차단할](/office365/troubleshoot/group-policy/block-onedrive-use-from-office) 수도 있습니다.

### <a name="sending-documents-through-email"></a>전자 메일을 통해 문서 보내기

사용자는 전자 메일을 통해 외부 사용자에 게 문서를 보냅니다. 민감도 레이블을 사용 하 여 이러한 문서에 대 한 액세스를 제한 하 고 암호화 하는 방법을 고려 합니다. 자세한 내용은 민감도 레이블을 사용 하 여 액세스 관리를 참조 하세요.

### <a name="unsanctioned-collaboration-tools"></a>비 사용 권한 공동 작업 도구

공동 작업 도구의 가로는 광범위 합니다. 사용자는 Google Docs, DropBox, 여유 시간 또는 확대/축소와 같은 플랫폼을 포함 하 여 공식 의무 외부에서 다 수를 사용할 가능성이 높습니다. 조직에서 관리 하는 장치에 대 한 모바일 응용 프로그램 관리 및 방화벽 수준에서 이러한 도구를 회사 네트워크에서 사용 하지 못하도록 차단할 수 있습니다. 그러나 이렇게 하면 이러한 플랫폼의 모든 사용 권한 인스턴스가 차단 되 고 관리 되지 않는 장치에서의 액세스는 차단 되지 않습니다. 필요한 경우 사용 하지 않으려는 플랫폼을 차단 하 고 사용 해야 하는 플랫폼에 대 한 비 사용 권한 사용에 대 한 비즈니스 정책을 만듭니다. 

비 사용 권한 응용 프로그램을 관리 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

* [연결된 앱을 제어합니다.](/cloud-app-security/governance-actions)

* [응용 프로그램을 권한 부여 하 고 취소 합니다.](/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>다음 단계

리소스에 대 한 외부 액세스 보안에 대 한 다음 문서를 참조 하세요. 이러한 작업은 나열 된 순서 대로 수행 하는 것이 좋습니다.

1. [외부 액세스를 위한 보안 상태 결정](1-secure-access-posture.md)

2. [현재 상태 검색](2-secure-access-current-state.md)

3. [거 버 넌 스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위해 그룹 사용](4-secure-access-groups.md)

5. [AZURE AD B2B로 전환](5-secure-access-b2b.md) 합니다 (여기에 표시 됨).

6. [권한 관리를 사용 하 여 안전 하 게 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용 하 여 안전 하 게 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용 하 여 안전 하 게 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft 팀, OneDrive 및 SharePoint에 대 한 액세스 보호](9-secure-access-teams-sharepoint.md)