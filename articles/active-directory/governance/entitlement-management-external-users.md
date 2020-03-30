---
title: Azure AD 권한 관리 - Azure Active Directory에서 외부 사용자에 대한 액세스 관리
description: Azure Active Directory 권한 관리에서 외부 사용자에 대한 액세스를 제어하도록 지정할 수 있는 설정에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0f68ca9520c1715463212da80aaabed48f8269
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128694"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 외부 사용자에 대한 액세스 관리

Azure AD 권한 부여 관리는 [Azure AD 비즈니스 간(B2B)을](../b2b/what-is-b2b.md) 사용하여 다른 디렉터리에서 조직 외부의 사용자와 공동 작업을 합니다. Azure AD B2B를 사용하면 외부 사용자가 홈 디렉터리로 인증되지만 디렉터리에 표현이 있습니다. 디렉터리에서 표현을 사용하면 사용자에게 리소스에 대한 액세스 권한을 할당받을 수 있습니다.

이 문서에서는 외부 사용자의 액세스를 제어하기 위해 지정할 수 있는 설정에 대해 설명합니다.

## <a name="how-entitlement-management-can-help"></a>권한 부여 관리가 도움이 되는 방법

[Azure AD B2B](../b2b/what-is-b2b.md) 초대 환경을 사용하는 경우 리소스 디렉터리로 가져오고 작업하려는 외부 게스트 사용자의 전자 메일 주소를 이미 알고 있어야 합니다. 이 작업은 소규모 또는 단기 프로젝트에서 작업하고 모든 참가자를 이미 알고 있을 때 효과가 있지만 함께 작업하려는 사용자가 많거나 시간이 지남에 따라 참가자가 변경되는 경우 관리하기가 어렵습니다.  예를 들어 다른 조직과 작업하고 해당 조직과 한 지점의 연락을 취하지만 시간이 지남에 따라 해당 조직의 추가 사용자도 액세스해야 합니다.

권한 관리에서는 지정한 조직의 사용자가 액세스 패키지를 자체 요청할 수 있도록 허용하는 정책을 정의할 수 있습니다. 승인이 필요한지 여부와 액세스의 만료 날짜를 지정할 수 있습니다. 승인이 필요한 경우 조직의 외부 사용자에게 액세스가 필요한 사용자를 알 수 있으므로 외부 조직의 한 명 이상의 사용자를 디렉터리로 초대하고 승인자로 지정할 수도 있습니다. 액세스 패키지를 구성하면 외부 조직의 담당자(스폰서)에게 액세스 패키지의 링크를 보낼 수 있습니다. 해당 연락처는 외부 조직의 다른 사용자와 공유할 수 있으며 이 링크를 사용하여 액세스 패키지를 요청할 수 있습니다. 디렉터리에 이미 초대된 해당 조직의 사용자도 해당 링크를 사용할 수 있습니다.

요청이 승인되면 권한 부여 관리는 사용자에게 필요한 액세스 권한을 프로비전하며, 여기에는 디렉터리에 아직 없는 경우 사용자 초대가 포함될 수 있습니다. Azure AD는 자동으로 B2B 게스트 계정을 만듭니다. 관리자는 [이전에 B2B 허용 또는 거부 목록을](../b2b/allow-deny-list.md) 설정하여 다른 조직에 대한 초대를 허용하거나 차단하도록 설정하여 공동 작업을 허용하는 조직을 제한했을 수 있습니다.  허용 또는 차단 목록에서 사용자를 허용하지 않으면 초대되지 않습니다.

외부 사용자의 액세스가 영원히 지속되는 것을 원하지 않으므로 180일과 같은 만료 날짜를 정책에 지정합니다. 180일이 지나면 액세스가 확장되지 않으면 권한 관리에서 해당 액세스 패키지와 연결된 모든 액세스 가 제거됩니다. 기본적으로 권한 부여 관리를 통해 초대된 사용자에게 다른 액세스 패키지 할당이 없는 경우 마지막 할당을 잃으면 게스트 계정이 30일 동안 로그인하지 못하도록 차단되고 이후에 제거됩니다. 이렇게 하면 불필요한 계정이 확산되는 것을 방지할 수 있습니다. 다음 섹션에 설명된 대로 이러한 설정을 구성할 수 있습니다.

## <a name="how-access-works-for-external-users"></a>외부 사용자에 대한 액세스 작동 방식

다음 다이어그램 및 단계는 외부 사용자에게 액세스 패키지에 대한 액세스 권한을 부여하는 방법에 대한 개요를 제공합니다.

![외부 사용자의 수명 주기를 보여주는 다이어그램](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. 공동 작업하려는 Azure AD 디렉터리 또는 도메인에 대해 [연결된 조직을 추가합니다.](entitlement-management-organization.md)

1. 디렉터리에서 디렉터리에 [없는 사용자에 대](entitlement-management-access-package-create.md#for-users-not-in-your-directory)한 정책을 포함 하는 액세스 패키지를 만듭니다.

1. 내 [액세스 포털 링크를](entitlement-management-access-package-settings.md) 외부 조직의 연락처로 보내 사용자가 사용자와 공유하여 액세스 패키지를 요청할 수 있습니다.

1. 외부 사용자(이 예제의**요청자 A)는** 내 액세스 포털 링크를 사용하여 액세스 패키지에 [대한 액세스를 요청합니다.](entitlement-management-request-access.md) 사용자가 로그인하는 방법은 연결된 조직에 정의된 디렉터리 또는 도메인의 인증 유형에 따라 다릅니다.

1. 승인자가 [요청을 승인하거나](entitlement-management-request-approve.md) 요청이 자동으로 승인됨).

1. 요청은 배달 [상태로](entitlement-management-process.md)이동합니다.

1. B2B 초대 프로세스를 사용하면 디렉터리(이 예제의**요청자 A(게스트))에서** 게스트 사용자 계정이 만들어집니다. 허용 [목록 또는 거부 목록이](../b2b/allow-deny-list.md) 정의된 경우 목록 설정이 적용됩니다.

1. 게스트 사용자에게 액세스 패키지의 모든 리소스에 대한 액세스 권한이 할당됩니다. Azure AD 및 다른 Microsoft 온라인 서비스 또는 연결된 SaaS 응용 프로그램에서 변경하려면 다소 시간이 걸릴 수 있습니다. 자세한 내용은 [변경 내용이 적용되는 경우](entitlement-management-access-package-resources.md#when-changes-are-applied)를 참조하십시오.

1. 외부 사용자는 액세스가 [배달되었음을](entitlement-management-process.md)나타내는 전자 메일을 받습니다.

1. 리소스에 액세스하려면 외부 사용자는 전자 메일의 링크를 클릭하거나 디렉터리 리소스에 직접 액세스하여 초대 프로세스를 완료할 수 있습니다.

1. 정책 설정에 따라 시간이 지남에 따라 외부 사용자에 대한 액세스 패키지 할당이 만료되고 외부 사용자의 액세스가 제거됩니다.

1. 외부 사용자 설정의 수명 주기에 따라 외부 사용자가 더 이상 액세스 패키지 할당을 사용할 수 없는 경우 외부 사용자가 로그인하지 못하도록 차단되고 게스트 사용자 계정이 디렉터리에서 제거됩니다.

## <a name="settings-for-external-users"></a>외부 사용자에 대한 설정

조직 외부의 사용자가 액세스 패키지를 요청하고 해당 액세스 패키지의 리소스에 액세스할 수 있도록 하려면 제대로 구성되었는지 확인해야 하는 몇 가지 설정이 있습니다.

### <a name="enable-catalog-for-external-users"></a>외부 사용자를 위한 카탈로그 사용

- 기본적으로 [새 카탈로그를](entitlement-management-catalog-create.md)만들 때 외부 사용자가 카탈로그에서 액세스 패키지를 요청할 수 있도록 허용됩니다. 외부 **사용자에 대해 사용하도록 설정되어** 있는지 **확인합니다.**

    ![카탈로그 설정 편집](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Azure AD B2B 외부 공동 작업 설정 구성

- 게스트가 디렉터리로 다른 게스트를 초대할 수 있도록 허용하면 자격 관리 외부에서 게스트 초대가 발생할 수 있습니다. **게스트를 아니요로 초대할 수 있도록** 설정하여 적절한 관리 초대만 허용하는 것이 좋습니다. **No**
- B2B 허용 목록을 사용하는 경우 권한 부여 관리를 사용하여 파트너로 삼으려는 도메인이 목록에 추가되었는지 확인해야 합니다. 또는 B2B 거부 목록을 사용하는 경우 파트너로 사용할 도메인이 목록에 추가되지 않았는지 확인해야 합니다.
- **모든 사용자(연결된** 모든 조직 + 새 외부 사용자)에 대한 권한 관리 정책을 만드는 경우 B2B가 가지고 있는 목록 설정을 허용하거나 거부합니다. 따라서 이 정책에 포함하려는 도메인을 허용 목록에 포함시키고 거부 목록을 사용하는 경우 거부 목록에서 제외해야 합니다.
- **모든 사용자(연결된** 모든 조직 + 새 외부 사용자)를 포함하는 권한 부여 관리 정책을 만들려면 먼저 디렉터리에 대해 전자 메일 일회용 암호 인증을 사용하도록 설정해야 합니다. 자세한 내용은 [전자 메일 일회용 암호 인증(미리 보기)을](../b2b/one-time-passcode.md#opting-in-to-the-preview)참조하십시오.
- Azure AD B2B 외부 공동 작업 설정에 대한 자세한 내용은 [B2B 외부 공동 작업 활성화를 참조하고 게스트를 초대할 수 있는 사용자를 관리합니다.](../b2b/delegate-invitations.md)

    ![Azure AD 외부 공동 작업 설정](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>조건부 액세스 정책 검토

- 새 게스트 사용자가 충족할 수 없는 조건부 액세스 정책에서 게스트를 제외해야 디렉터리에 로그인할 수 없습니다. 예를 들어, 등록된 기기가 없고, 알려진 위치에 없으며, MFA(다단계 인증)에 다시 등록하지 않으려는 경우 조건부 액세스 정책에 이러한 요구 사항을 추가하면 게스트가 사용 권한을 사용하지 못하도록 차단할 수 있습니다. 관리. 자세한 내용은 [Azure Active Directory 조건부 액세스의 조건을 참조하십시오.](../conditional-access/concept-conditional-access-conditions.md)

    ![Azure AD 조건부 액세스 정책에서 설정 제외](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>SharePoint 온라인 외부 공유 설정 검토

- 외부 사용자를 위한 액세스 패키지에 SharePoint Online 사이트를 포함하려면 조직 수준 외부 공유 설정이 **누구(사용자가** 로그인할 필요가 없는 경우)로 설정되어 있는지 또는 **신규 및 기존 게스트(게스트는** 로그인하거나 확인 코드를 제공해야 합니다)로 설정되어 있는지 확인합니다. 자세한 내용은 [외부 공유 켜기 또는 끄기](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)를 참조하십시오.

- 권한 부여 관리 외부의 외부 공유를 제한하려면 외부 공유 설정을 **기존 게스트로**설정할 수 있습니다. 그런 다음 권한 부여 관리를 통해 초대된 새 사용자만 이러한 사이트에 액세스할 수 있습니다. 자세한 내용은 [외부 공유 켜기 또는 끄기](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)를 참조하십시오.

- 사이트 수준 설정에서 게스트 액세스를 사용하도록 설정해야 합니다(이전에 나열된 것과 동일한 옵션 선택). 자세한 내용은 [사이트의 외부 공유 켜기 또는 끄기](https://docs.microsoft.com/sharepoint/change-external-sharing-site)를 참조하십시오.

### <a name="review-your-office-365-group-sharing-settings"></a>Office 365 그룹 공유 설정 검토

- 외부 사용자를 위한 액세스 패키지에 Office 365 그룹을 포함하려면 **사용자가 조직에 새 게스트를 추가하도록 허용하여** 게스트 액세스를 허용하도록 **설정되어** 있는지 확인합니다. 자세한 내용은 [Office 365 그룹에 대한 게스트 액세스 관리를](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-groups-guest-access)참조하십시오.

- 외부 사용자가 SharePoint 온라인 사이트 및 Office 365 그룹과 연결된 리소스에 액세스할 수 있도록 하려면 SharePoint Online 외부 공유를 사용 설정해야 합니다. 자세한 내용은 [외부 공유 켜기 또는 끄기](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)를 참조하십시오.

- PowerShell의 디렉터리 수준에서 Office 365 그룹에 대한 게스트 정책을 설정하는 방법에 대한 자세한 내용은 [예제: 디렉터리 수준에서 그룹에 대한 게스트 정책 구성을](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level)참조하십시오.

### <a name="review-your-teams-sharing-settings"></a>팀 공유 설정 검토

- 외부 사용자를 위한 액세스 패키지에 Teams를 포함하려면 **Microsoft Teams의 게스트 액세스 허용을** **켜서** 게스트 액세스를 허용하도록 설정되어 있는지 확인합니다. 자세한 내용은 [Microsoft Teams 관리 센터의 게스트 액세스 구성을](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center)참조하십시오.

## <a name="manage-the-lifecycle-of-external-users"></a>외부 사용자의 수명 주기 관리

액세스 패키지 요청이 승인되어 디렉터리로 초대된 외부 사용자가 더 이상 액세스 패키지 할당이 없는 경우 어떤 일이 발생하는지 선택할 수 있습니다. 이 문제는 사용자가 모든 액세스 패키지 할당을 포기하거나 마지막 액세스 패키지 할당이 만료되는 경우에 발생할 수 있습니다. 기본적으로 외부 사용자에게 더 이상 액세스 패키지 할당이 없는 경우 디렉터리에 로그인하지 못하도록 차단됩니다. 30일이 지나면 게스트 사용자 계정이 디렉터리에서 제거됩니다.

**필수 구성 조건 역할:** 글로벌 관리자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 권한 **관리** 섹션에서 **설정을**클릭합니다.

1. **편집**을 클릭합니다.

    ![외부 사용자의 수명 주기를 관리하는 설정](./media/entitlement-management-external-users/settings-external-users.png)

1. 외부 **사용자의 수명 주기 관리** 섹션에서 외부 사용자에 대한 다양한 설정을 선택합니다.

1. 외부 사용자가 액세스 패키지에 대한 마지막 할당을 잃으면 이 디렉터리에 로그인하지 못하도록 차단하려면 **외부 사용자가 이 디렉터리에 로그인하지** 못하도록 외부 사용자를 **예로**설정합니다.

    > [!NOTE]
    > 사용자가 이 디렉터리에 로그인하지 못하도록 차단된 경우 사용자는 액세스 패키지를 다시 요청하거나 이 디렉터리에서 추가 액세스를 요청할 수 없습니다. 나중에 다른 액세스 패키지에 대한 액세스를 요청해야 하는 경우 로그인을 차단하도록 구성하지 마십시오.

1. 외부 사용자가 액세스 패키지에 대한 마지막 할당을 잃으면 이 디렉터리에서 게스트 사용자 계정을 제거하려면 **외부 사용자 제거를** **예로**설정합니다.

    > [!NOTE]
    > 권한 부여 관리는 권한 관리를 통해 초대된 계정만 제거합니다. 또한 해당 사용자가 이 디렉터리에서 패키지 할당에 액세스하지 않은 리소스에 추가된 경우에도 사용자가 로그인하지 못하도록 차단되고 이 디렉터리에서 제거됩니다. 액세스 패키지 할당을 받기 전에 게스트가 이 디렉터리에 있는 경우 게스트는 유지됩니다. 그러나 게스트가 액세스 패키지 할당을 통해 초대되고 초대된 후에도 비즈니스용 OneDrive 또는 SharePoint Online 사이트에 할당된 경우에도 게스트는 제거됩니다.

1. 이 디렉터리에서 게스트 사용자 계정을 제거하려면 제거되기 까지의 일 수를 설정할 수 있습니다. 게스트 사용자 계정이 액세스 패키지에 대한 마지막 할당을 잃어버리자마자 제거하려면 **이 디렉터리에서 외부 사용자를 제거하기 전에 일 수를** **0으로**설정합니다.

1. **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [연결된 조직 추가](entitlement-management-organization.md)
- [디렉터리에 없는 사용자의 경우](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [문제 해결](entitlement-management-troubleshoot.md)
