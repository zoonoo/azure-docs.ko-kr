---
title: Azure AD 권한 관리 - Azure Active Directory에서 액세스 패키지에 대한 리소스 역할 변경
description: Azure Active Directory 권한 관리에서 기존 액세스 패키지에 대한 리소스 역할을 변경하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 102bbfbd1c02c93830f5c7fce89fe95d7fde54c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261893"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지에 대한 리소스 역할 변경

액세스 패키지 관리자는 새 리소스에 대한 사용자의 액세스를 프로비전하거나 이전 리소스에서 액세스를 제거할 염려 없이 언제든지 액세스 패키지의 리소스를 변경할 수 있습니다. 이 문서에서는 기존 액세스 패키지의 리소스 역할을 변경하는 방법을 설명합니다.

이 비디오에서는 액세스 패키지를 변경하는 방법에 대한 개요를 제공합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>카탈로그에서 리소스 확인

액세스 패키지에 리소스를 추가해야 하는 경우 카탈로그에서 필요한 리소스를 사용할 수 있는지 확인해야 합니다. 액세스 패키지 관리자인 경우 카탈로그를 소유하고 있더라도 카탈로그에 리소스를 추가할 수 없습니다. 카탈로그에서 사용할 수 있는 리소스를 사용할 수 있습니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그를** 클릭한 다음 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **리소스를** 클릭하여 이 카탈로그의 리소스 목록을 확인합니다.

    ![카탈로그의 리소스 목록](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. 액세스 패키지 관리자이고 카탈로그에 리소스를 추가해야 하는 경우 카탈로그 소유자에게 리소스를 추가하도록 요청할 수 있습니다.

## <a name="add-resource-roles"></a>리소스 역할 추가

리소스 역할은 리소스와 연결된 사용 권한의 모음입니다. 사용자가 요청할 수 있는 리소스를 만드는 방법은 액세스 패키지에 리소스 역할을 추가하는 것입니다. 그룹, 팀, 응용 프로그램 및 SharePoint 사이트에 대한 리소스 역할을 추가할 수 있습니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **패키지 액세스를** 클릭한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **리소스 역할을**클릭합니다.

1. **리소스 역할 추가를** 클릭하여 패키지에 액세스하는 리소스 역할 추가를 엽니다.

    ![액세스 패키지 - 리소스 역할 추가](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. 그룹, 팀, 응용 프로그램 또는 SharePoint 사이트를 추가할지 여부에 따라 다음 리소스 역할 섹션 중 하나에서 단계를 수행합니다.

## <a name="add-a-group-or-team-resource-role"></a>그룹 또는 팀 리소스 역할 추가

권한 부여 관리를 통해 액세스 패키지가 할당되면 Microsoft Teams의 그룹 이나 팀에 사용자를 자동으로 추가할 수 있습니다. 

- 그룹 또는 팀이 액세스 패키지의 일부이고 사용자가 해당 액세스 패키지에 할당되면 아직 존재하지 않는 경우 해당 그룹 또는 팀에 추가됩니다.
- 사용자의 액세스 패키지 할당이 만료되면 동일한 그룹 또는 팀을 포함하는 다른 액세스 패키지에 대한 할당이 없는 한 그룹 또는 팀에서 제거됩니다.

[Azure AD 보안 그룹 또는 Office 365 그룹을](../fundamentals/active-directory-groups-create-azure-portal.md)선택할 수 있습니다. 관리자는 모든 그룹을 카탈로그에 추가할 수 있습니다. 카탈로그 소유자는 그룹의 소유자인 경우 카탈로그에 그룹을 추가할 수 있습니다. 그룹을 선택할 때 다음 Azure AD 제약 조건을 염두에 두십시오.

- 게스트를 포함한 사용자가 그룹 또는 팀에 구성원으로 추가되면 해당 그룹 또는 팀의 다른 모든 구성원을 볼 수 있습니다.
- Azure AD는 Azure AD Connect를 사용하여 Windows Server Active Directory에서 동기화된 그룹의 구성원을 변경할 수 없거나 Exchange Online에서 메일 그룹으로 만든 그룹의 구성원을 변경할 수 없습니다.  
- 동적 그룹의 구성원은 구성원을 추가하거나 제거하여 업데이트할 수 없으므로 동적 그룹 구성원 자격 관리에 사용하기에는 적합하지 않습니다.

자세한 내용은 그룹 및 [Office 365 그룹 및 Microsoft 팀](https://docs.microsoft.com/microsoftteams/office-365-groups) [비교를](https://docs.microsoft.com/office365/admin/create-groups/compare-groups) 참조하십시오.

1. 패키지 페이지에 **액세스하는 리소스 역할 추가에서** **그룹 및 팀을** 클릭하여 그룹 선택 창을 엽니다.

1. 액세스 패키지에 포함할 그룹 및 팀을 선택합니다.

    ![액세스 패키지 - 리소스 역할 추가 - 그룹 선택](./media/entitlement-management-access-package-resources/group-select.png)

1. **선택**을 클릭합니다.

    그룹 또는 팀을 선택하면 **하위 유형** 열에 다음 하위 유형 중 하나가 나열됩니다.

    |  |  |
    | --- | --- |
    | 보안 | 리소스에 대한 액세스 권한을 부여하는 데 사용됩니다. |
    | 배포 | 그룹에 알림을 보내는 데 사용됩니다. |
    | O365 | 팀을 사용할 수 없는 Office 365 그룹입니다. 회사 내부와 외부의 사용자 간의 공동 작업에 사용됩니다. |
    | 팀 | 팀 이 활성화된 Office 365 그룹입니다. 회사 내부와 외부의 사용자 간의 공동 작업에 사용됩니다. |

1. **역할** 목록에서 **소유자** 또는 **구성원을**선택합니다.

    일반적으로 멤버 역할을 선택합니다. 소유자 역할을 선택하면 사용자가 다른 구성원또는 소유자를 추가하거나 제거할 수 있습니다.

    ![액세스 패키지 - 그룹 또는 팀의 리소스 역할 추가](./media/entitlement-management-access-package-resources/group-role.png)

1. **추가**를 클릭합니다.

    액세스 패키지에 대한 기존 할당이 있는 사용자는 추가될 때 자동으로 이 그룹 또는 팀의 구성원이 됩니다.

## <a name="add-an-application-resource-role"></a>응용 프로그램 리소스 역할 추가

Azure AD는 사용자에게 액세스 패키지가 할당된 경우 Azure AD에 페더레이션된 SaaS 응용 프로그램과 조직의 응용 프로그램을 모두 포함하여 Azure AD 엔터프라이즈 응용 프로그램에 대한 액세스 권한을 자동으로 할당하도록 할 수 있습니다. 페더레이션된 단일 사인온을 통해 Azure AD와 통합되는 응용 프로그램의 경우 Azure AD는 응용 프로그램에 할당된 사용자에 대해 페더레이션 토큰을 발급합니다.

응용 프로그램에는 여러 역할이 있을 수 있습니다. 액세스 패키지에 응용 프로그램을 추가할 때 해당 응용 프로그램에 두 개 이상의 역할이 있는 경우 해당 사용자에 대 한 적절 한 역할을 지정 해야 합니다. 응용 프로그램을 개발하는 경우 해당 역할이 응용 프로그램에 추가되는 방법에 대해 자세히 확인할 수 [있습니다.](../develop/active-directory-enterprise-app-role-management.md)

응용 프로그램 역할이 액세스 패키지의 일부인 경우:

- 사용자에게 해당 액세스 패키지가 할당되면 아직 존재하지 않는 경우 해당 응용 프로그램 역할에 추가됩니다.
- 사용자의 액세스 패키지 할당이 만료되면 해당 응용 프로그램 역할이 포함된 다른 액세스 패키지에 대한 할당이 없는 한 해당 액세스가 응용 프로그램에서 제거됩니다.

다음은 응용 프로그램을 선택할 때 고려해야 할 몇 가지 고려 사항입니다.

- 응용 프로그램에는 해당 역할에 할당된 그룹도 있을 수 있습니다.  액세스 패키지에 응용 프로그램 역할 대신 그룹을 추가하도록 선택할 수 있지만 응용 프로그램은 내 액세스 포털의 액세스 패키지의 일부로 사용자에게 표시되지 않습니다.

1. 패키지 페이지에 **액세스하는 리소스 역할 추가에서** **응용 프로그램을** 클릭하여 응용 프로그램 선택 창을 엽니다.

1. 액세스 패키지에 포함할 응용 프로그램을 선택합니다.

    ![액세스 패키지 - 리소스 역할 추가 - 응용 프로그램 선택](./media/entitlement-management-access-package-resources/application-select.png)

1. **선택**을 클릭합니다.

1. **역할** 목록에서 응용 프로그램 역할을 선택합니다.

    ![액세스 패키지 - 응용 프로그램에 대한 리소스 역할 추가](./media/entitlement-management-access-package-resources/application-role.png)

1. **추가**를 클릭합니다.

    액세스 패키지에 대한 기존 할당이 있는 모든 사용자에게는 추가될 때 이 응용 프로그램에 대한 액세스 권한이 자동으로 부여됩니다.

## <a name="add-a-sharepoint-site-resource-role"></a>SharePoint 사이트 리소스 역할 추가

Azure AD는 액세스 패키지가 할당될 때 SharePoint 온라인 사이트 또는 SharePoint Online 사이트 모음에 대한 액세스 권한을 자동으로 할당할 수 있습니다.

1. 패키지 페이지에 **액세스하는 리소스 역할 추가에서** **SharePoint 사이트를** 클릭하여 SharePoint 온라인 선택 사이트 창을 엽니다.

1. 액세스 패키지에 포함할 SharePoint 온라인 사이트를 선택합니다.

    ![액세스 패키지 - 리소스 역할 추가 - SharePoint 온라인 사이트 선택](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. **선택**을 클릭합니다.

1. **역할** 목록에서 SharePoint 온라인 사이트 역할을 선택합니다.

    ![액세스 패키지 - SharePoint 온라인 사이트에 대한 리소스 역할 추가](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. **추가**를 클릭합니다.

    액세스 패키지에 대한 기존 할당이 있는 모든 사용자에게는 추가될 때 이 SharePoint Online 사이트에 대한 액세스 권한이 자동으로 부여됩니다.

## <a name="remove-resource-roles"></a>리소스 역할 제거

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **패키지 액세스를** 클릭한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **리소스 역할을**클릭합니다.

1. 리소스 역할 목록에서 제거할 리소스 역할을 찾습니다.

1. 타원 **(...**) 을 클릭한 다음 **리소스 역할 제거를**클릭합니다.

    액세스 패키지에 대한 기존 할당이 있는 사용자는 제거될 때 이 리소스 역할에 대한 액세스가 자동으로 해지됩니다.

## <a name="when-changes-are-applied"></a>변경 사항이 적용되는 경우

권한 관리에서 Azure AD는 액세스 패키지의 할당 및 리소스에 대한 대량 변경을 하루에 여러 번 처리합니다. 따라서 할당을 수행하거나 액세스 패키지의 리소스 역할을 변경하는 경우 Azure AD에서 변경하는 데 최대 24시간이 걸릴 수 있으며 이러한 변경 내용을 다른 Microsoft 온라인 서비스 또는 연결된 SaaS로 전파하는 데 걸리는 시간이 더걸릴 수 있습니다. 응용 프로그램. 변경 사항이 몇 가지 개체에만 영향을 주는 경우 변경 사항은 Azure AD에 적용하는 데 몇 분 밖에 걸리지 않으며, 그 후에는 다른 Azure AD 구성 요소가 해당 변경 사항을 감지하고 SaaS 응용 프로그램을 업데이트합니다. 변경이 수천 개의 개체에 영향을 미치는 경우 변경 시간이 더 오래 걸릴 수 있습니다. 예를 들어 2개의 응용 프로그램과 100개의 사용자 할당이 있는 액세스 패키지가 있고 액세스 패키지에 SharePoint 사이트 역할을 추가하기로 결정한 경우 모든 사용자가 해당 SharePoint 사이트 역할의 일부가 될 때까지 지연될 수 있습니다. Azure AD 감사 로그, Azure AD 프로비저닝 로그 및 SharePoint 사이트 감사 로그를 통해 진행 률을 모니터링할 수 있습니다.

팀 구성원을 제거하면 Office 365 그룹에서도 제거됩니다. 팀 채팅 기능을 제거하면 작업이 지연될 수 있습니다. 자세한 내용은 [그룹 구성원 을](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)참조하십시오.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory를 사용하여 기본 그룹 만들기 및 멤버 추가](../fundamentals/active-directory-groups-create-azure-portal.md)
- [방법: 엔터프라이즈 애플리케이션에 대한 SAML 토큰에서 발행된 역할 클레임 구성](../develop/active-directory-enterprise-app-role-management.md)
- [쉐어포인트 온라인 소개](/sharepoint/introduction)