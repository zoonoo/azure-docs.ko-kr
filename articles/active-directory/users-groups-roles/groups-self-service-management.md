---
title: 셀프 서비스 그룹 관리 설정 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 보안 그룹 또는 Office 365 그룹을 만들고 관리하고 보안 그룹 또는 Office 365 그룹 멤버 자격을 요청합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e52c37e293941a767621cf56ef75f8cc83b1925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298006"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Azure Active Directory에서 셀프 서비스 그룹 관리 설정 

사용자가 Azure Active Directory(Azure AD)에서 자체 보안 그룹 또는 Office 365 그룹을 만들고 관리할 수 있도록 설정할 수 있습니다. 그룹의 소유자는 구성원 자격 요청을 승인하거나 거부할 수 있으며 그룹 구성원의 제어를 위임할 수 있습니다. 메일 지원 보안 그룹이나 메일 그룹에는 셀프 서비스 그룹 관리 기능을 사용할 수 없습니다.

## <a name="self-service-group-membership-defaults"></a>셀프 서비스 그룹 멤버십 기본값

Azure 포털에서 보안 그룹을 만들거나 Azure AD PowerShell을 사용하는 경우 그룹의 소유자만 구성원 자격을 업데이트할 수 있습니다. [Access 패널의](https://account.activedirectory.windowsazure.com/r#/joinGroups) 셀프 서비스에서 만든 보안 그룹과 모든 Office 365 그룹은 소유자 승인 또는 자동 승인 여부에 관계없이 모든 사용자에 대해 가입할 수 있습니다. 액세스 패널에서 그룹을 만들 때 멤버 자격 옵션을 변경할 수 있습니다.

에서 만든 그룹 | 보안 그룹 기본 동작 | Office 365 그룹 기본 동작
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | 소유자만 구성원을 추가할 수 있습니다.<br>액세스 패널에 참여할 수 있지만 참여할 수 없음 | 모든 사용자에 대해 가입 할 수 열려 있습니다.
[Azure 포털](https://portal.azure.com) | 소유자만 구성원을 추가할 수 있습니다.<br>액세스 패널에 참여할 수 있지만 참여할 수 없음<br>그룹 생성 시 소유자가 자동으로 할당되지 않음 | 모든 사용자에 대해 가입 할 수 열려 있습니다.
[액세스 패널](https://account.activedirectory.windowsazure.com/r#/joinGroups) | 모든 사용자에 대해 가입 할 수 열려 있습니다.<br>그룹을 만들 때 멤버 자격 옵션을 변경할 수 있습니다. | 모든 사용자에 대해 가입 할 수 열려 있습니다.<br>그룹을 만들 때 멤버 자격 옵션을 변경할 수 있습니다.

## <a name="self-service-group-management-scenarios"></a>셀프 서비스 그룹 관리 시나리오

* **위임된 그룹 관리** 예를 들어 회사에서 사용 중인 SaaS 응용 프로그램에 대한 액세스를 관리하는 관리자가 있습니다. 이러한 액세스 권한 관리가 번거로워지자 관리자는 비즈니스 소유자에게 요청하여 새 그룹을 만듭니다. 관리자는 새 그룹에 응용 프로그램에 대한 액세스를 할당하고 이미 응용 프로그램에 액세스하는 모든 사람을 그룹에 추가합니다. 그러면 비즈니스 소유자가 더 많은 사용자를 추가할 수 있으며 해당 사용자는 애플리케이션에 자동으로 프로비전됩니다. 비즈니스 소유자는 사용자에 대한 액세스를 관리하는 관리자를 대기할 필요가 없습니다. 관리자가 다른 비즈니스 그룹의 관리자에게 동일한 권한을 부여하는 경우 해당 사용자는 자신의 그룹 구성원에 대한 액세스를 관리할 수도 있습니다. 비즈니스 소유자나 관리자는 서로의 그룹 구성원 을 보거나 관리할 수 없습니다. 관리자는 계속 해당 애플리케이션에 액세스할 수 있는 모든 사용자를 보고 필요한 경우 액세스 권한을 차단할 수 있습니다.
* **셀프 서비스 그룹 관리** 이 시나리오의 예로는 두 사용자가 모두 SharePoint Online 사이트를 독립적으로 설정한 경우입니다. 그들은 서로의 팀이 자신의 사이트에 대한 액세스 권한을 부여하려고 합니다. 이를 위해서 두 사용자는 Azure AD에 하나의 그룹을 만들고 각각 SharePoint Online에서 해당 사이트에 대한 액세스 권한을 제공할 그룹을 선택합니다. 사용자가 액세스 권한이 필요하면 액세스 패널에서 요청하고 승인을 받으면 자동으로 두 SharePoint Online 사이트에 대한 액세스 권한을 얻습니다. 이후 사이트에 액세스하는 모든 사용자가 특정 SaaS 애플리케이션에 대한 액세스 권한도 가져야 하는지를 둘 중 한 명이 결정할 수 있습니다. SaaS 애플리케이션의 관리자는 애플리케이션에 대한 액세스 권한을 SharePoint Online 사이트에 추가할 수 있습니다. 이후로 승인된 모든 요청은 두 SharePoint Online 사이트 및 해당 SaaS 애플리케이션에 대한 액세스 권한을 부여합니다.

## <a name="make-a-group-available-for-user-self-service"></a>사용자 셀프서비스에 사용할 수 있는 그룹 만들기

1. 디렉터리의 전역 관리자인 계정으로 [Azure AD 관리자 센터에](https://aad.portal.azure.com) 로그인합니다.
1. **그룹을**선택한 다음 **일반** 설정을 선택합니다.
1. Set 소유자는 액세스 패널에서 **예로** **그룹 구성원 자격 요청을 관리할 수 있습니다.**
1. **액세스 패널의 그룹에 대한 액세스 제한을** **아니요로**설정합니다.
1. **사용자가 Azure 포털에서 보안 그룹을 만들 수** 있도록 설정하거나 Azure **포털에서 Office 365 그룹을 만들 수 있습니다.**

    - **예**: Azure AD 조직의 모든 사용자는 새 보안 그룹을 만들고 이러한 그룹에 구성원을 추가할 수 있습니다. 이러한 새 그룹은 다른 모든 사용자에 대한 액세스 패널에도 표시됩니다. 그룹의 정책 설정에서 허용하는 경우 다른 사용자는 이러한 그룹에 가입할 요청을 만들 수 있습니다.
    - **아니오**: 사용자는 그룹을 만들 수 없으며 소유자인 기존 그룹을 변경할 수 없습니다. 그러나 여전히 해당 그룹의 구성원을 관리하고 다른 사용자의 해당 그룹 가입 요청을 승인할 수 있습니다.

또한 Azure **포털에서 그룹 소유자로 구성원을 할당할 수** 있는 소유자를 사용할 수 있으며, Azure **포털에서 그룹 소유자로 구성원을 할당할 수 있는 소유자를** 사용하여 사용자에 대한 셀프 서비스 그룹 관리에 대한 보다 세분화된 액세스 제어를 수행할 수 있습니다.

사용자가 그룹을 만들 수 있는 경우 조직의 모든 사용자가 새 그룹을 만들 수 있으며 기본 소유자로 이러한 그룹에 구성원을 추가할 수 있습니다. 자신의 그룹을 만들 수 있는 개인은 지정할 수 없습니다. 다른 그룹 구성원을 그룹 소유자로 만들기 위한 개인만 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](../fundamentals/active-directory-manage-groups.md)
* [그룹 설정을 구성하는 Azure Active Directory cmdlets](groups-settings-cmdlets.md)
* [Azure Active Directory의 애플리케이션 관리](../manage-apps/what-is-application-management.md)
* [Azure Active Directory란?](../fundamentals/active-directory-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](../hybrid/whatis-hybrid-identity.md)
