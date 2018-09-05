---
title: PIM에서 Azure 리소스 역할 설정 구성| Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할 설정을 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 901eb5ef43ddb2840ed7a3d83fc08f2f05849461
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189738"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>PIM에서 Azure 리소스 역할 설정 구성

역할 설정을 구성할 때 PIM(Privileged Identity Management) 환경에서 할당에 적용되는 기본 설정을 정의합니다. 리소스에 대해 이러한 설정을 정의하려면 왼쪽 창에서 **역할 설정** 탭을 선택합니다. 작업 표시줄(역할에서)에서 역할 설정 단추를 선택하여 현재 옵션을 볼 수도 있습니다.

## <a name="overview"></a>개요

관리자는 Azure 리소스 역할에 대한 PIM(Privileged Identity Management)의 승인 워크플로를 통해 중요한 리소스에 대한 액세스 권한을 추가로 보호하거나 제한할 수 있습니다. 즉, 관리자는 역할 할당을 활성화하기 위해 승인을 요구할 수 있습니다. 

리소스 계층의 개념은 Azure 리소스 역할에 고유한 개념입니다. 이 계층을 사용하면 부모 리소스 개체에서 부모 컨테이너 내의 모든 자식 리소스로 역할 할당을 상속할 수 있습니다. 

예를 들어, 리소스 관리자인 Bob이 PIM을 사용하여 Alice를 Contoso 구독의 소유자 역할에 적격 멤버로 할당합니다. 이 할당에서 Alice는 Contoso 구독 내 모든 리소스 그룹 컨테이너의 적격 소유자입니다. 또한 Alice는 구독의 각 리소스 그룹 내에서 가상 머신과 같은 모든 리소스의 적격 소유자이기도 합니다. 

Contoso 구독에 Fabrikam Test, Fabrikam Dev 및 Fabrikam Prod라는 세 개의 리소스 그룹이 있다고 가정하겠습니다. 이러한 각 리소스 그룹에는 단일 가상 머신이 포함됩니다.

PIM 설정은 리소스의 각 역할에 대해 구성됩니다. 할당과 달리 이 설정은 상속되지 않고 리소스 역할에 엄격히 적용됩니다. [적격 할당 및 리소스 표시 유형에 대해 자세히 알아보세요](pim-resource-roles-eligible-visibility.md).

계속 같은 예제로 설명하면, Bob은 PIM을 사용하여 Contoso 구독 소유자 역할의 모든 멤버에 활성화를 위한 승인 절차를 요청합니다. Fabrikam Prod 리소스 그룹의 리소스를 보호하기 위해 Bob은 또한 이 리소스의 소유자 역할 멤버에 대해 승인을 요청합니다. Fabrikam Test 및 Dev Fabrikam의 소유자 역할은 활성화를 위한 승인이 필요하지 않습니다.

Alice가 Contoso 구독에 대해 소유자 역할의 활성화를 요청하는 경우 승인자는 Alice의 역할이 활성 상태가 되기 전에 해당 요청을 승인하거나 거부해야 합니다. Alice가 Fabrikam Prod 리소스 그룹으로 [활성화 범위를 지정](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices)하려는 경우에도 승인자는 이 요청을 승인하거나 거부해야 합니다. 그러나 Alice가 Fabrikam Test 또는 Fabrikam Dev로 활성화 범위를 지정하려는 경우 승인이 필요하지 않습니다.

승인 워크플로는 역할의 일부 멤버에게만 필요할 수 있습니다. Azure 구독에서 실행될 응용 프로그램 개발을 지원하기 위해 조직이 계약 직원 몇명을 채용하는 시나리오를 가정합니다. 리소스 관리자로서 사용자는 직원이 승인을 요청하지 않고도 적격 액세스 권한을 갖기를 원하지만, 계약 직원은 반드시 승인을 요청해야 합니다. 계약 직원에 대해서만 승인 워크플로를 구성하려면 직원에게 할당된 역할과 동일한 권한으로 사용자 지정 역할을 만들 수 있습니다. 사용자 정의 역할을 활성화하려면 승인이 필요할 수 있습니다. [사용자 지정 역할을 자세히 알아보세요](pim-resource-roles-custom-role-policy.md).

승인 워크플로를 구성하고 요청을 승인하거나 거부할 수 있는 사용자를 지정하려면 다음 단계를 따릅니다.

## <a name="require-approval-to-activate"></a>활성화할 승인 필요

1. Azure Portal에서 PIM으로 이동하고, 목록에서 리소스를 선택합니다.

   ![리소스가 선택된 “Azure 리소스” 창](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. 왼쪽 창에서 **역할 설정**을 선택합니다.

3. 역할을 검색하여 선택한 후 **편집**을 선택하여 설정을 수정합니다.

   ![운영자 역할 “편집” 단추](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. **활성화** 섹션에서 **Require approval to activate**(활성화하려면 승인 필요) 확인란을 선택합니다.

   ![역할 설정의 “활성화” 섹션](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>승인자 지정

**승인자 선택**을 클릭하여 **사용자 또는 그룹 선택** 창을 엽니다.

>[!NOTE]
>설정을 업데이트할 하나 이상의 사용자 또는 그룹을 선택해야 합니다. 기본 승인자가 없습니다.

리소스 관리자는 사용자 및 그룹의 조합을 승인자 목록에 추가할 수 있습니다. 

![사용자가 선택된 “사용자 또는 그룹 선택” 창](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>활성화할 승인 요청

승인 요청을 한다고 해서 활성화를 위해 멤버가 따라야 하는 절차에 영향을 주지 않습니다. [역할을 활성화하는 단계를 검토합니다](pim-resource-roles-activate-your-roles.md).

멤버가 승인이 필요한 역할의 활성화를 요청했지만 그 역할이 더 이상 필요하지 않은 경우, 멤버는 PIM에서 해당 요청을 취소할 수 있습니다.

취소하려면 PIM로 이동하고, **내 요청**을 선택합니다. 요청을 찾아 **취소를** 선택합니다.

![“내 요청” 창](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할에 대한 다단계 인증 필요](pim-resource-roles-require-mfa.md)
- [PIM에서 Azure 리소스에 대한 보안 경고 구성](pim-resource-roles-configure-alerts.md)
