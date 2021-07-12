---
title: 셀프 서비스 그룹 관리 설정 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 보안 그룹 또는 Microsoft 365 그룹을 만들고 관리하고 보안 그룹 또는 Microsoft 365 그룹 멤버 자격을 요청합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47e3ea0a8ea5dc8dbb01d532a52436ed581311e7
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110089900"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Azure Active Directory에서 셀프 서비스 그룹 관리 설정 

사용자가 Azure AD(Azure Active Directory)에서 자신의 보안 그룹 또는 Microsoft 365 그룹을 만들고 관리할 수 있도록 설정할 수 있습니다. 그룹 소유자는 멤버 자격 요청을 승인 또는 거부할 수 있으며 그룹 멤버 자격에 대한 제어를 위임할 수 있습니다. 셀프 서비스 그룹 관리 기능은 보안 그룹 및 Office 365 그룹에 대해서만 사용할 수 있지만 메일 사용 가능 보안 그룹 및 메일링 그룹에는 사용할 수 없습니다.

## <a name="self-service-group-membership-defaults"></a>셀프 서비스 그룹 구성원 자격 기본값

Azure Portal 또는 Azure AD PowerShell을 사용하여 보안 그룹을 만들 때 그룹의 소유자만 멤버 자격을 업데이트할 수 있습니다. [액세스 패널](https://account.activedirectory.windowsazure.com/r#/joinGroups)에서 셀프 서비스로 만든 보안 그룹과 모든 Microsoft 365 그룹은 소유자 승인이든 자동 승인이든 모든 사용자가 가입할 수 있습니다. 액세스 패널에서 그룹을 만들 때 멤버 자격 옵션을 변경할 수 있습니다.

생성된 그룹 | 보안 그룹 기본 동작 | Microsoft 365 그룹 기본 동작
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](../enterprise-users/groups-settings-cmdlets.md) | 소유자만 회원을 추가할 수 있음<br>표시되지만 액세스 패널에서 조인할 수 없음 | 모든 사용자에 대해 조인하려면 열기
[Azure Portal](https://portal.azure.com) | 소유자만 회원을 추가할 수 있음<br>표시되지만 액세스 패널에서 조인할 수 없음<br>소유자는 그룹을 만들 때 자동으로 할당되지 않습니다. | 모든 사용자에 대해 조인하려면 열기
[액세스 패널](https://account.activedirectory.windowsazure.com/r#/joinGroups) | 모든 사용자에 대해 조인하려면 열기<br>그룹을 만들 때 멤버 자격 옵션을 변경할 수 있습니다. | 모든 사용자에 대해 조인하려면 열기<br>그룹을 만들 때 멤버 자격 옵션을 변경할 수 있습니다.

## <a name="self-service-group-management-scenarios"></a>셀프 서비스 그룹 관리 시나리오

* **위임된 그룹 관리** 회사에서 사용하고 있는 SaaS 애플리케이션에 대한 액세스를 관리하는 관리자를 예로 들 수 있습니다. 이러한 액세스 권한 관리가 번거로워지자 관리자는 비즈니스 소유자에게 요청하여 새 그룹을 만듭니다. 관리자는 애플리케이션에 대한 액세스 권한을 새 그룹에 할당하고 이미 애플리케이션에 액세스하는 모든 사용자를 그룹에 추가합니다. 그러면 비즈니스 소유자가 더 많은 사용자를 추가할 수 있으며 해당 사용자는 애플리케이션에 자동으로 프로비전됩니다. 비즈니스 소유자는 사용자에 대한 액세스를 관리하는 관리자를 대기할 필요가 없습니다. 관리자가 다른 비즈니스 그룹의 관리자에게 동일한 사용 권한을 부여하는 경우 해당 사용자는 고유한 그룹 구성원에 대한 액세스를 관리할 수도 있습니다. 비즈니스 소유자와 관리자는 모두 다른 그룹 멤버 자격을 보거나 관리할 수 없습니다. 관리자는 계속 해당 애플리케이션에 액세스할 수 있는 모든 사용자를 보고 필요한 경우 액세스 권한을 차단할 수 있습니다.
* **셀프 서비스 그룹 관리** - 이 시나리오의 예제에서는 독립적으로 설정한 SharePoint Online 사이트를 보유한 두 사용자를 가정합니다. 이들은 상대방 팀을 해당 사이트에 액세스할 수 있도록 하려고 합니다. 이를 위해서 두 사용자는 Azure AD에 하나의 그룹을 만들고 각각 SharePoint Online에서 해당 사이트에 대한 액세스 권한을 제공할 그룹을 선택합니다. 사용자가 액세스 권한이 필요하면 액세스 패널에서 요청하고 승인을 받으면 자동으로 두 SharePoint Online 사이트에 대한 액세스 권한을 얻습니다. 이후 사이트에 액세스하는 모든 사용자가 특정 SaaS 애플리케이션에 대한 액세스 권한도 가져야 하는지를 둘 중 한 명이 결정할 수 있습니다. SaaS 애플리케이션의 관리자는 애플리케이션에 대한 액세스 권한을 SharePoint Online 사이트에 추가할 수 있습니다. 이후로 승인된 모든 요청은 두 SharePoint Online 사이트 및 해당 SaaS 애플리케이션에 대한 액세스 권한을 부여합니다.

## <a name="make-a-group-available-for-user-self-service"></a>사용자 셀프서비스에 사용할 수 있는 그룹 만들기

1. 디렉터리에 대한 전역 관리자 또는 권한 있는 역할 관리자 역할이 할당된 계정으로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **그룹** 을 선택한 다음, **일반** 설정을 선택합니다.

    ![Azure Active Directory 그룹 일반 설정](./media/groups-self-service-management/groups-settings-general.png)

1. **소유자가 액세스 패널에서 그룹 구성원 요청을 관리할 수 있음** 을 **예** 로 설정합니다.

1. **액세스 패널에서 그룹 기능에 액세스하는 사용자 권한 제한** 을 **아니요** 로 설정합니다.

1. **사용자가 Azure Portal, API 또는 PowerShell에서 보안 그룹을 만들 수 있음** 또는 **사용자가 Azure Portal, API 또는 PowerShell에서 Microsoft 365 그룹을 만들 수 있음** 을 다음으로 설정합니다.

    - **예**: Azure AD 조직의 모든 사용자는 새 보안 그룹을 만들고 Azure Portal, API 또는 PowerShell에서 이러한 그룹에 구성원을 추가할 수 있습니다. 이러한 새 그룹은 다른 모든 사용자에 대한 액세스 패널에도 표시됩니다. 그룹에 대한 정책 설정에서 허용하는 경우 다른 사용자가 이러한 그룹에 가입하도록 요청할 수 있습니다.
    - **아니요**: 사용자는 그룹을 만들 수 없으며 자신이 소유자인 기존 그룹을 변경할 수 없습니다. 그러나 여전히 해당 그룹의 구성원을 관리하고 다른 사용자의 해당 그룹 가입 요청을 승인할 수 있습니다.

    이러한 설정은 최근에 API 및 PowerShell에 대한 지원을 추가하도록 변경되었습니다. 이 변경 사항에 대한 자세한 내용은 다음 섹션 [그룹 설정 변경](#groups-setting-change)을 참조하세요.

또한 **Azure Portal에서 구성원을 그룹 소유자로 할당할 수 있는 소유자** 를 사용하여 사용자의 셀프 서비스 그룹 관리를 보다 세밀하게 제어할 수 있습니다.

사용자가 그룹을 만들 수 있으면 조직의 모든 사용자가 새 그룹을 만들 수 있으며 기본 소유자로서 이러한 그룹에 구성원을 추가할 수 있습니다. 고유의 그룹을 만들 수 있는 개인은 지정할 수 없습니다. 다른 그룹 구성원을 그룹 소유자로 만들기 위한 개인만 지정할 수 있습니다.

> [!NOTE]
> 사용자가 보안 그룹 또는 Microsoft 365 그룹에 참여하도록 요청하고 소유자가 멤버 자격 요청을 승인 또는 거부하도록 요청하려면 Azure Active Directory Premium(P1 또는 P2) 라이선스가 필요합니다. Azure Active Directory Premium 라이선스가 없으면 사용자는 액세스 패널 그룹을 관리할 수 있지만 액세스 패널 소유자 승인이 필요한 그룹을 만들 수는 없으며 그룹에 가입하도록 요청할 수 없습니다.

## <a name="groups-setting-change"></a>그룹 설정 변경

현재 보안 그룹 및 Microsoft 365 그룹 설정은 더 이상 사용되지 않으며 교체됩니다. 현재 설정은 Azure Portal에서 그룹 만들기만 제어하고 API 또는 PowerShell에는 적용되지 않기 때문에 대체되고 있습니다. 새 설정은 Azure Portal에서 그리고 API 및 PowerShell 에서도 그룹을 만듭니다.

| 사용되지 않는 설정 | 새 설정 |
| --- | --- |
| 사용자가 Azure Portal에서 보안 그룹을 만들 수 있음 | 사용자가 Azure Portal, API 또는 PowerShell에서 보안 그룹을 만들 수 있음 |
| 사용자가 Azure Portal에서 Microsoft 365 그룹을 만들 수 있음 | 사용자가 Azure Portal, API 또는 PowerShell에서 Microsoft 365 그룹을 만들 수 있음 |

현재 설정이 완전히 사용되지 않을 때까지 두 설정이 모두 Azure Portal에 표시됩니다. 이 새 설정은 **2021년 5월** 이 끝나기 전에 구성해야 합니다. 보안 그룹 설정을 구성하려면 전역 관리자 또는 권한 있는 역할 관리자 역할이 할당되어야 합니다. 

![Azure Active Directory 보안 그룹 설정 변경](./media/groups-self-service-management/security-groups-setting.png)

다음 표에서는 선택할 값을 결정하는 데 도움이 됩니다.

| 수행하려는 작업 ... | 다음 값 선택 |
| --- | --- |
| 사용자는 Azure Portal, API 또는 PowerShell을 사용하여 그룹을 만들 수 있습니다. | 두 설정을 모두 **예** 로 설정합니다. 변경 내용을 적용하는 데 최대 15분이 걸릴 수 있습니다. |
| 사용자는 Azure Portal, API 또는 PowerShell을 사용하여 그룹을 만들 수 **없습니다**. | 두 설정을 모두 **아니요** 로 설정합니다. 변경 내용을 적용하는 데 최대 15분이 걸릴 수 있습니다. |
| 사용자는 API 또는 PowerShell을 사용하지 않고 Azure Portal을 사용하여 그룹을 만들 수 있습니다. | 지원되지 않음 |
| 사용자는 API 또는 PowerShell을 사용하여 그룹을 만들 수 있지만 Azure Portal은 사용할 수 없습니다. | 지원되지 않음 |

다음 표에서는 이러한 설정에 대해 다른 값에 대해 발생하는 것을 나열합니다. 사용되지 않는 설정과 새 설정을 다른 값으로 설정하지 않는 것이 좋습니다.

| 사용자는 Azure Portal을 사용하여 그룹을 만들 수 있습니다. | 사용자는 Azure Portal, API 또는 PowerShell을 사용하여 그룹을 만들 수 있습니다. | 테넌트에게 미치는 영향 |
| :---: | :---: | --- |
| 예 | 예 | 사용자는 Azure Portal, API 또는 PowerShell을 사용하여 그룹을 만들 수 있습니다. 변경 내용을 적용하는 데 최대 15분이 걸릴 수 있습니다.|
| 아니요 | 아니요 | 사용자는 Azure Portal, API 또는 PowerShell을 사용하여 그룹을 만들 수 **없습니다**. 변경 내용을 적용하는 데 최대 15분이 걸릴 수 있습니다. |
| 예 | 아니요 | 사용자는 Azure Portal, API 또는 PowerShell을 사용하여 그룹을 만들 수 **없습니다**. 이러한 설정을 다른 값으로 설정하지 않는 것이 좋습니다. 변경 내용을 적용하는 데 최대 15분이 걸릴 수 있습니다. |
| 아니요 | 예 | **사용자는 Azure Portal을 사용하여 그룹을 만들 수 있음** 설정이 **2021년 6월** 에 완전히 지원 중단될 때까지 사용자는 API 또는 PowerShell을 사용하여 그룹을 만들 수 있지만 Azure Portal은 사용할 수 없습니다. **2021년 6월** 부터 **사용자는 Azure Portal, API 또는 PowerShell을 사용하여 그룹을 만들 수 있음** 설정이 적용되고 사용자는 Azure Portal, API 또는 PowerShell을 사용하여 그룹을 만들 수 있습니다. |

## <a name="next-steps"></a>다음 단계

이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](../fundamentals/active-directory-manage-groups.md)
* [그룹 설정을 구성하는 Azure Active Directory cmdlets](../enterprise-users/groups-settings-cmdlets.md)
* [Azure Active Directory의 애플리케이션 관리](../manage-apps/what-is-application-management.md)
* [Azure Active Directory란?](../fundamentals/active-directory-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](../hybrid/whatis-hybrid-identity.md)
