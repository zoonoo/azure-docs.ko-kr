---
title: PIM에서 관리할 수 있는 Azure AD 디렉터리 역할 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 관리할 수 있는 Azure AD 디렉터리 역할을 설명합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 07/23/2018
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 63f4c4c48dd0fed3130dc99929a0e84c3cba2026
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466050"
---
# <a name="azure-ad-directory-roles-you-can-manage-in-pim"></a>PIM에서 관리할 수 있는 Azure AD 디렉터리 역할
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

조직의 사용자를 Azure AD의 다른 관리 역할에 할당할 수 있습니다. 이러한 역할 할당은 사용자를 추가 또는 제거하거나 서비스 설정을 변경하는 등 사용자가 Azure AD, Office 365, 기타 Microsoft Online Services, 연결된 응용 프로그램에서 수행할 수 있는 작업을 제어합니다.  

전역 관리자는 [Azure Active Directory에서 관리자 역할 할당](../users-groups-roles/directory-assign-admin-roles.md)에 설명된 대로 포털을 통해 또는 [PowerShell 명령](/powershell/module/azuread#directory_roles)을 사용하여 Azure AD에서 역할에 **영구적으로** 할당된 사용자를 업데이트할 수 있습니다.

Azure AD Privileged Identity Management(PIM)는 Azure AD에서 사용자에 대한 권한 있는 액세스의 정책을 관리합니다. PIM은 Azure AD에서 사용자에게 하나 이상의 역할을 할당하고, 다른 사용자를 역할에 영구적으로 할당하거나 또는 역할에 대해 적격이 되도록 할당할 수 있습니다. 사용자가 역할에 영구적으로 할당되거나 적격 역할 할당을 활성화하는 경우 해당 역할에 할당된 권한으로 Azure Active Directory, Office 365 및 기타 응용 프로그램을 관리할 수 있습니다.

영구 및 적격 역할 할당을 비교했을 때 이 둘을 통해 다른 사람에게 주어진 액세스에는 차이가 없습니다. 유일한 차이는 사람들이 그 액세스를 항상 필요로 하지 않는다는 점입니다. 그런 사람들은 역할에 대해 적격이 되도록 하며, 언제라도 필요할 때 켜고 끄도록 할 수 있습니다.

## <a name="roles-managed-in-pim"></a>PIM에서 관리되는 역할
Privileged Identity Management를 사용하면 사용자를 다음과 같이 일반적인 관리자 역할에 할당할 수 있습니다.

* **글로벌 관리자**(회사 관리자라고도 함)는 모든 관리 기능에 액세스할 수 있습니다. 조직에는 글로벌 관리자를 둘 이상 둘 수 있습니다. Office 365를 구입하기 위해 등록한 사람은 자동으로 글로벌 관리자가 됩니다.
* **권한 있는 역할 관리자** 는 Azure AD PIM을 관리하고 다른 사용자에 대한 역할 할당을 업데이트합니다.  
* **대금 청구 관리자**는 구입을 수행하고, 구독을 관리하고, 지원 티켓을 관리하고, 서비스 상태를 모니터링합니다.
* **암호 관리자**는 암호를 재설정하고, 서비스 요청을 관리하고, 서비스 상태를 모니터링합니다. 암호 관리자는 사용자 암호를 재설정하는 것으로 제한됩니다.
* **서비스 관리자**는 서비스 요청을 관리하고 서비스 상태를 모니터링합니다.
  
  > [!NOTE]
  > Office 365를 사용하는 경우 사용자에게 서비스 관리자 역할을 할당하기 전에 먼저 Exchange Online 등의 서비스에 대한 관리 권한을 사용자에게 할당합니다.
  > 
  > 
* **사용자 관리자**는 암호를 다시 설정하고, 서비스 상태를 모니터링하고, 사용자 계정과 사용자 그룹 및 서비스 요청을 관리합니다. 사용자 관리자는 글로벌 관리자를 삭제하거나 다른 관리자 역할을 만들거나 청구, 글로벌 및 서비스 관리에 대한 암호를 재설정할 수 없습니다.
* **Exchange 관리자**에게는 EAC(Exchange 관리 센터)를 통해 Exchange Online에 대한 관리 액세스 권한이 있으며 Exchange Online에서 거의 모든 작업을 수행할 수 있습니다.
* **SharePoint 서비스 관리자**에게는 SharePoint Online 관리 센터를 통해 SharePoint Online에 대한 관리 액세스 권한이 있으며 SharePoint Online에서 거의 모든 작업을 수행할 수 있습니다. 적격 사용자는 PIM에서 정품 인증을 한 후 SharePoint 내에서 이 역할을 사용할 때 지연이 발생할 수 있습니다.
* **비즈니스용 Skype 관리자**는 비즈니스용 Skype 관리 센터를 통해 비즈니스용 Skype에 대한 관리 액세스 권한을 보유하고 비즈니스용 Skype Online에서 거의 모든 작업을 수행할 수 있습니다.

[Azure AD에서 관리자 역할 할당](../users-groups-roles/directory-assign-admin-roles.md) 및 [Office 365에서 관리자 역할 할당](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504)에 대한 자세한 내용을 보려면 이 문서를 읽으세요.

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


PIM에서는 사용자가 [필요할 때 역할을 활성화](pim-how-to-add-role-to-user.md)할 수 있도록 [이러한 역할을 사용자에게 할당](pim-how-to-activate-role.md)할 수 있습니다.

다른 사용자가 PIM에 액세스하여 관리할 수 있도록 하려는 경우 PIM에 필요한 사용자 역할은 [PIM에 대한 액세스 권한을 제공하는 방법](pim-how-to-give-access-to-pim.md)에 자세히 설명되어 있습니다.

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>PIM에서 관리되지 않는 역할
위에서 언급한 것을 제외하고 Exchange Online 또는 SharePoint Online 내에 있는 역할은 Azure AD에 표시되지 않으므로 PIM에서 볼 수 없습니다. 이러한 Office 365 서비스에서 세분화된 역할 할당 변경에 대한 자세한 내용은 [Office 365의 사용 권한](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d)을 참조하세요.

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>사용자 역할 및 로그인
일부 Microsoft 서비스 및 응용 프로그램의 경우 사용자를 역할에 할당하는 방법만으로 사용자를 관리자로 지정하지 못할 수 있습니다.

Azure Portal에 액세스하려면 사용자가 Azure 구독을 관리할 필요가 없더라도 Azure 구독의 소유자여야 합니다.  예를 들어 Azure AD의 구성 설정을 관리하려면 사용자는 Azure AD의 글로벌 관리자인 동시에 Azure 구독의 소유자여야 합니다.  Azure 구독에 사용자를 추가하는 방법을 알아보려면 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../..//role-based-access-control/role-assignments-portal.md)를 참조하세요.

Microsoft Online Services에 액세스하려면 서비스 포털을 열거나 관리 작업을 수행하기 전에 사용자에게 라이선스도 할당되어야 합니다.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Azure AD에서 사용자에게 라이선스 할당

1. 글로벌 관리자 또는 소유자 역할로 [Azure Portal](http://portal.azure.com)에 로그인합니다.

1. 연결된 라이선스가 있고, 사용하려는 Azure AD 디렉터리를 선택합니다.

1. 왼쪽 탐색 메뉴에서 **Azure Active Directory**를 클릭합니다.

1. **라이선스**를 클릭합니다. 사용 가능한 라이선스 목록이 나타납니다.

    ![Azure Active Directory 라이선스](./media/pim-roles/licenses-overview.png)

1. **제품**을 클릭합니다.

1. 배포하려는 라이선스가 포함된 라이선스 계획을 클릭합니다.

    ![라이선스 제품](./media/pim-roles/licenses-products.png)

1. **할당**을 클릭하여 라이선스 할당 창을 엽니다.

    ![사용 허가된 사용자](./media/pim-roles/licenses-licensed-users.png)

1. 라이선스를 할당하려는 사용자 또는 그룹을 선택합니다.

    ![라이선스 할당](./media/pim-roles/licenses-assign-license.png)

1. **할당 옵션**을 클릭하여 할당 옵션을 구성합니다.

    ![할당 옵션](./media/pim-roles/licenses-assignment-options.png)

1. **할당**을 클릭하여 라이선스를 할당합니다. 이제 사용자가 사용 허가되었습니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계

- [PIM 사용 시작](pim-getting-started.md)
- [PIM에서 Azure AD 디렉터리 역할 할당](pim-how-to-add-role-to-user.md)

