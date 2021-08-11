---
title: Azure AD 권한 관리에서 외부 사용자에 대한 액세스 제어 - Azure Active Directory
description: Azure Active Directory 권한 관리에서 외부 사용자의 액세스 권한을 관리하는 데 사용되는 설정에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dae1695d372db94c7bd0fa12d20a7d327143b8a
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109786486"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 외부 사용자에 대한 액세스 제어

Azure AD 권한 관리는 [Azure AD B2B](../external-identities/what-is-b2b.md)를 사용하여 액세스를 공유하므로 조직 외부 사람과 협업할 수 있습니다. Azure AD B2B를 사용하면 외부 사용자가 홈 디렉터리를 인증하지만 디렉터리에 표시됩니다. 디렉터리의 표현을 사용하면 사용자에게 리소스에 대한 액세스 권한을 할당할 수 있습니다.

이 문서에서는 외부 사용자의 액세스 권한을 제어하는 데 사용할 수 있는 설정에 대해 설명합니다.

## <a name="how-entitlement-management-can-help"></a>권한 관리의 작동 방식

[Azure AD B2B](../external-identities/what-is-b2b.md) 초대 환경을 사용하는 경우 리소스 디렉터리에 초대하여 협업하려는 외부 게스트 사용자의 이메일 주소를 이미 알고 있어야 합니다. 소규모 또는 단기 프로젝트를 진행 중이고 모든 참가자를 이미 알고 있는 경우 각 사용자를 직접 초대하는 것이 좋지만, 협업하려는 사용자 수가 많거나 시간이 지나면서 참가자가 바뀌는 경우에는 이 프로세스를 관리하기가 어렵습니다.  예를 들어 다른 조직과 협업 중이고 해당 조직과의 연락 담당자가 지금은 한 명이지만, 이후에 해당 조직의 사용자가 추가되면 그 사용자에게도 액세스 권한이 필요합니다.

권한 관리를 사용하면 관리자가 지정하는 조직의 사용자가 액세스 패키지를 직접 요청할 수 있도록 허용하는 정책을 정의할 수 있습니다. 이 정책에는 승인이 필요한지 여부, 액세스 검토가 필요한지 여부 및 액세스 권한의 만료 날짜가 포함됩니다. 승인이 필요한 경우 외부 조직의 사용자 한 명 이상을 디렉터리에 초대하여 스폰서로 지정하고, 해당 스폰서를 승인자로 구성하는 것이 좋습니다. 이들은 소속 조직의 외부 사용자 중에서 누구에게 액세스 권한이 필요한지 알고 있을 가능성이 높기 때문입니다. 액세스 패키지를 구성한 후에는 액세스 패키지의 요청 링크를 얻어서 외부 조직의 연락 담당자(스폰서)에게 전달할 수 있습니다. 이 연락 담당자는 소속 외부 조직의 다른 사용자와 이 링크를 공유할 수 있고, 다른 사용자는 이 링크를 사용하여 액세스 패키지를 요청할 수 있습니다. 이미 디렉터리에 초대된 조직의 사용자도 이 링크를 사용할 수 있습니다.

일반적으로 요청이 승인되면 권한 관리에서는 사용자에게 필요한 액세스 권한을 프로비저닝합니다. 사용자가 아직 디렉터리에 없는 경우 권한 관리에서는 먼저 사용자를 초대합니다. 사용자가 초대되면 Azure AD는 자동으로 해당 사용자에 대한 B2B 게스트 계정을 만들지만 사용자에게 이메일을 보내지는 않습니다. 관리자가 이전에 [B2B 허용 또는 거부 목록](../external-identities/allow-deny-list.md)을 설정하여 다른 조직의 초대를 허용 또는 차단함으로써 협업이 허용되는 조직을 제한했을 수 있습니다.  허용 또는 차단 목록에 의해 허용되지 않는 사용자는 초대되지 않으며 목록이 업데이트될 때까지 이들에게 액세스 권한을 할당할 수 없습니다.

외부 사용자의 액세스 권한이 영원히 지속되지 않도록 정책에서 만료 날짜를 지정합니다. 예를 들어 180일로 지정합니다. 180일 후에 액세스 권한이 연장되지 않으면 권한 관리에서는 해당 액세스 패키지와 연결된 모든 액세스 권한을 제거합니다. 기본적으로 권한 관리를 통해 초대된 사용자에게 다른 액세스 패키지 할당이 없는 경우 마지막 할당이 사라지면 게스트 계정의 로그인이 30일 동안 차단된 후 게스트 계정이 제거됩니다. 이는 불필요한 계정이 늘어나지 않도록 차단하는 조치입니다. 이러한 설정은 다음 섹션에 설명된 것처럼 구성할 수 있습니다.

## <a name="how-access-works-for-external-users"></a>외부 사용자의 액세스 권한 작동 원리

다음 다이어그램과 단계는 외부 사용자에게 액세스 패키지에 대한 액세스 권한을 부여하는 방법에 대한 개요를 제공합니다.

![외부 사용자의 수명 주기를 보여주는 다이어그램](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. 협업하려는 Azure AD 디렉터리 또는 도메인에 대해 [연결된 조직을 추가](entitlement-management-organization.md)합니다.

1. [디렉터리에 없는 사용자](entitlement-management-access-package-create.md#for-users-not-in-your-directory) 정책이 포함된 액세스 패키지를 디렉터리에 만듭니다.

1. 외부 조직의 연락 담당자에게 외부 조직의 사용자와 공유할 수 있는 [내 액세스 포털 링크](entitlement-management-access-package-settings.md)를 보냅니다. 외부 조직의 사용자는 이 링크를 통해 액세스 패키지를 요청할 수 있습니다.

1. 외부 사용자(이 예제에서는 **요청자 A**)가 내 액세스 포털 링크를 사용하여 액세스 패키지에 대한 [액세스 권한을 요청](entitlement-management-request-access.md)합니다. 사용자가 로그인하는 방법은 연결된 조직 및 외부 사용자 설정에 정의된 디렉터리 또는 도메인의 인증 유형에 따라 달라집니다.

1. 승인자가 [요청을 승인](entitlement-management-request-approve.md)합니다. 또는 요청이 자동 승인됩니다.

1. 요청이 [전송 중 상태](entitlement-management-process.md)로 전환됩니다.

1. B2B 초대 프로세스를 사용하여 디렉터리에 게스트 사용자 계정이 생성됩니다(이 예제에서는 **요청자 A(게스트)** ). [허용 목록 또는 거부 목록](../external-identities/allow-deny-list.md)이 정의된 경우 목록 설정이 적용됩니다.

1. 게스트 사용자에게 액세스 패키지의 모든 리소스에 대한 액세스 권한이 할당됩니다. Azure AD 및 다른 Microsoft Online Services 또는 연결된 SaaS 애플리케이션에서 변경 내용이 적용될 때까지 다소 시간이 걸릴 수 있습니다. 자세한 내용은 [변경 내용 적용 시기](entitlement-management-access-package-resources.md#when-changes-are-applied)를 참조하세요.

1. 액세스 권한이 [전달](entitlement-management-process.md)되었다는 이메일이 외부 사용자에게 전송됩니다.

1. 외부 사용자는 리소스에 액세스하려면 이메일의 링크를 클릭하거나 디렉터리 리소스에 직접 액세스하여 초대 프로세스를 완료하면 됩니다.

1. 정책 설정에 만료 날짜가 포함된 경우 나중에 외부 사용자에게 할당된 액세스 패키지가 만료되면 해당 액세스 패키지에서 외부 사용자의 액세스 권한이 제거됩니다.

1. 외부 사용자 설정의 수명 주기에 따라, 외부 사용자에게 할당된 액세스 패키지가 더 이상 없는 경우 외부 사용자의 로그인이 차단되고 게스트 사용자 계정이 디렉터리에서 제거됩니다.

## <a name="settings-for-external-users"></a>외부 사용자에 대한 설정

조직 외부 사용자가 액세스 패키지를 요청하고 해당 액세스 패키지의 리소스에 대한 액세스 권한을 얻을 수 있도록 몇 가지 설정이 제대로 구성되었는지 확인해야 합니다.

### <a name="enable-catalog-for-external-users"></a>외부 사용자를 위한 카탈로그 사용

- 기본적으로 [새 카탈로그](entitlement-management-catalog-create.md)를 만들면 외부 사용자가 카탈로그에서 액세스 패키지를 요청할 수 있도록 카탈로그가 설정됩니다. **외부 사용자에 대해 사용** 이 **예** 로 설정되었는지 확인합니다.

    ![카탈로그 설정 편집](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Azure AD B2B 외부 협업 설정 구성

- 게스트가 디렉터리에 다른 게스트를 초대할 수 있도록 허용하면 권한 관리 외부에서 게스트 초대가 발생할 수 있습니다. 적절하게 관리되는 초대만 허용하려면 **게스트가 초대할 수 있음** 을 **아니요** 로 설정하는 것이 좋습니다.
- B2B 허용 목록을 사용하는 경우 권한 관리를 사용하여 파트너 관계를 맺으려는 모든 조직의 모든 도메인을 목록에 추가해야 합니다. 또는 B2B 거부 목록을 사용하는 경우 파트너 관계를 맺으려는 조직의 도메인이 거부 목록에 포함되면 안 됩니다.
- **모든 사용자**(모든 연결된 조직 + 새 외부 사용자)에 대한 권한 관리 정책을 만들었는데 사용자가 디렉터리의 연결된 조직에 속하지 않은 경우 사용자가 패키지를 요청할 때 연결된 조직이 자동으로 만들어집니다. 현재 지정된 B2B 허용 또는 거부 목록 설정이 우선적으로 적용됩니다. 따라서 허용 목록을 사용 중인 경우 이 정책에 포함하려는 도메인을 허용 목록에 포함해야 하고, 거부 목록을 사용 중인 경우 거부 목록에서 제외해야 합니다.
- **모든 사용자**(모든 연결된 조직 + 새 외부 사용자)를 포함하는 권한 관리 정책을 만들려면 먼저 디렉터리에 대한 이메일 일회용 암호 인증을 설정해야 합니다. 자세한 내용은 [이메일 일회용 암호 인증](../external-identities/one-time-passcode.md)을 참조하세요.
- Azure AD B2B 외부 협업 설정에 대한 자세한 내용은 [B2B 외부 협업을 사용하도록 설정 및 게스트를 초대할 수 있는 사용자 관리](../external-identities/delegate-invitations.md)를 참조하세요.

    ![Azure AD 외부 협업 설정](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>조건부 액세스 정책 검토

- 새 게스트 사용자가 충족할 수 없는 조건부 액세스 정책에서 게스트를 제외해야 합니다. 그렇지 않으면 게스트 사용자가 디렉터리에 로그인할 수 없습니다. 예를 들어 게스트가 등록된 디바이스를 갖고 있지 않고, 알려진 위치에 있지 않고, MFA(다단계 인증)를 다시 등록하지 않으려는 경우 이러한 요구 사항을 조건부 액세스 정책에 추가하면 게스트가 권한 관리를 사용할 수 없습니다. 자세한 내용은 [Azure Active Directory 조건부 액세스의 조건이란?](../conditional-access/concept-conditional-access-conditions.md)을 참조하세요.

    ![Azure AD 조건부 액세스 정책 제외 설정](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>SharePoint Online 외부 공유 설정 검토

- 외부 사용자에 대한 액세스 패키지에 SharePoint Online 사이트를 포함하려면 조직 수준 외부 공유 설정을 **모든 사람**(사용자 로그인이 필요 없음) 또는 **신규 및 기존 게스트**(게스트는 로그인하거나 확인 코드를 입력해야 함)로 설정합니다. 자세한 내용은 [외부 공유 설정 또는 해제](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)를 참조하세요.

- 권한 관리 외부에서 외부 공유를 제한하려는 경우 외부 공유 설정을 **기존 게스트** 로 설정하면 됩니다. 그러면 권한 관리를 통해 초대된 신규 사용자만 이러한 사이트에 액세스할 수 있습니다. 자세한 내용은 [외부 공유 설정 또는 해제](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)를 참조하세요.

- 사이트 수준 설정에서 게스트 액세스 권한을 설정(앞에서 나열한 옵션과 동일한 옵션 선택)합니다. 자세한 내용은 [사이트에 대한 외부 공유를 설정하거나 해제하기](/sharepoint/change-external-sharing-site)를 참조하세요.

### <a name="review-your-microsoft-365-group-sharing-settings"></a>Microsoft 365 그룹 공유 설정 검토

- 외부 사용자에 대한 액세스 패키지에 Microsoft 365 그룹을 포함하려면 **사용자가 조직에 새 게스트를 추가할 수 있도록 허용** 을 **켜기** 로 설정하여 게스트 액세스를 허용합니다. 자세한 내용은 [Microsoft 365 그룹에 대한 게스트 액세스 관리](/Microsoft 365/admin/create-groups/manage-guest-access-in-groups?view=Microsoft 365-worldwide#manage-groups-guest-access)를 참조하세요.

- 외부 사용자가 SharePoint Online 사이트 및 Microsoft 365 그룹과 연결된 리소스에 액세스할 수 있게 하려면 SharePoint Online 외부 공유를 켜야 합니다. 자세한 내용은 [외부 공유 설정 또는 해제](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)를 참조하세요.

- PowerShell의 디렉터리 수준에서 Microsoft 365 그룹에 대한 게스트 정책을 설정하는 방법에 대한 자세한 내용은 [예: 디렉터리 수준에서 그룹의 게스트 정책 구성](../enterprise-users/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level)을 참조하세요.

### <a name="review-your-teams-sharing-settings"></a>Teams 공유 설정 검토

- 외부 사용자에 대한 액세스 패키지에 Teams를 포함하려면 **Microsoft Teams에서 게스트 액세스 허용** 을 **켜기** 로 설정하여 게스트 액세스를 허용합니다. 자세한 내용은 [Microsoft Teams 관리 센터에서 게스트 액세스 구성](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center)을 참조하세요.

## <a name="manage-the-lifecycle-of-external-users"></a>외부 사용자의 수명 주기 관리

액세스 패키지 요청을 통해 디렉터리에 초대된 외부 사용자에게 할당된 액세스 패키지가 더 이상 없는 경우에 수행할 작업을 선택할 수 있습니다. 사용자가 모든 액세스 패키지 할당을 포기하거나 마지막 액세스 패키지 할당이 만료되는 경우 이 상황이 발생할 수 있습니다. 외부 사용자에게 더 이상 액세스 패키지 할당이 없는 경우 기본값으로 디렉터리에 로그인하지 못하도록 차단됩니다. 30일 후에는 해당 게스트 사용자 계정이 디렉터리에서 제거됩니다.

**필수 역할:** 전역 관리자, Identity Governance 관리자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴의 **권한 관리** 섹션에서 **설정** 을 클릭합니다.

1. **편집** 을 클릭합니다.

    ![외부 사용자의 수명 주기를 관리하는 설정](./media/entitlement-management-external-users/settings-external-users.png)

1. **외부 사용자의 수명 주기 관리** 섹션에서 외부 사용자에 대한 다양한 설정을 선택합니다.

1. 외부 사용자에게 할당된 마지막 액세스 패키지가 만료되는 경우 이 디렉터리에 로그인하지 못하도록 차단하려면 **외부 사용자가 이 디렉터리에 로그인하지 못하도록 차단** 을 **예** 로 설정합니다.

    > [!NOTE]
    > 이 디렉터리에 로그인하지 못하도록 차단된 사용자는 액세스 패키지를 다시 요청하거나 이 디렉터리에서 다른 액세스 권한을 요청할 수 없습니다. 나중에 사용자가 다른 액세스 패키지에 대한 액세스 권한을 요청해야 하는 경우에는 사용자의 로그인을 차단하도록 구성하지 마세요.

1. 외부 사용자가 액세스 패키지에 대한 마지막 할당을 상실한 후 이 디렉터리에서 게스트 사용자 계정을 제거하려면 **외부 사용자 제거** 를 **예** 로 설정합니다.

    > [!NOTE]
    > 권한 관리는 권한 관리를 통해 초대된 계정만 제거합니다. 또한 액세스 패키지 할당이 아닌 디렉터리의 리소스에 사용자가 추가된 경우에도 사용자는 로그인하지 못하도록 차단되며 디렉터리에서 제거됩니다. 게스트는 액세스 패키지 할당을 받기 전에 이 디렉터리에 있는 경우 유지됩니다. 그러나 게스트가 액세스 패키지 할당을 통해 초대되고 초대된 후에 비즈니스용 OneDrive 또는 SharePoint Online 사이트에 할당된 경우에도 제거됩니다.

1. 해당 디렉터리에서 게스트 사용자 계정을 제거하려면 제거 전 일 수를 설정할 수 있습니다. 액세스 패키지에 대한 마지막 할당을 잃는 즉시 게스트 사용자 계정을 제거하려면 **해당 디렉터리에서 외부 사용자를 제거하기 전까지 남은 일 수** 를 **0** 으로 설정합니다.

1. **저장** 을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [연결된 조직 추가](entitlement-management-organization.md)
- [디렉터리에 없는 사용자](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [문제 해결](entitlement-management-troubleshoot.md)
