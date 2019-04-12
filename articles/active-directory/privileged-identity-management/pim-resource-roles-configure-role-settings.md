---
title: PIM-Azure Active Directory에서에서 Azure 리소스 역할 설정 구성 | Microsoft Docs
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
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73d42c693fae6b538136d1e8c93094a0ea9e2077
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494870"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>PIM에서 Azure 리소스 역할 설정 구성

Azure 리소스 역할 설정을 구성한 경우에 Azure 리소스 역할 할당에서 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)에 적용 되는 기본 설정을 정의 합니다. 다음 절차에 따라 승인 워크플로를 구성하고 요청을 승인하거나 거부할 수 있는 사용자를 지정합니다.

## <a name="open-role-settings"></a>역할 설정 열기

다음 단계에 따라 Azure 리소스 역할에 대한 설정을 엽니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 구성원인 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 클릭합니다.

1. 관리하려는 리소스(예: 구독 또는 관리 그룹)를 클릭합니다.

    ![관리할 Azure 리소스 목록](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. **역할 설정**을 클릭합니다.

    ![역할 설정](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. 설정을 구성하려는 역할을 선택합니다.

    ![역할 설정 정보](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. **편집**을 클릭하여 역할 설정 창을 엽니다.

    ![역할 설정 편집](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    각 역할에 대한 역할 설정 페이지에서 몇 가지 설정을 구성할 수 있습니다.

## <a name="assignment-duration"></a>할당 기간

역할에 대한 설정을 구성할 때 각 할당 유형에 대한 2가지 할당 기간 옵션(적격 및 활성) 중에서 선택할 수 있습니다. 멤버가 PIM의 역할에 할당된 경우 이러한 옵션은 최대 기본 기간이 됩니다.

다음 **적격** 할당 기간 옵션 중 하나를 선택할 수 있습니다.

| | |
| --- | --- |
| **영구 적격 할당 허용** | 리소스 관리자는 영구 적격 멤버 자격을 할당할 수 있습니다. |
| **다음 적격 할당 만료** | 리소스 관리자는 모든 적격 할당에 시작 및 종료 날짜가 지정되도록 요구할 수 있습니다. |

또한 다음 **활성** 할당 기간 옵션 중 하나를 선택할 수 있습니다.

| | |
| --- | --- |
| **영구 활성 할당 허용** | 리소스 관리자는 영구 활성 멤버 자격을 할당할 수 있습니다. |
| **후 활성 할당 만료** | 리소스 관리자는 모든 활성 할당에 시작 및 종료 날짜가 지정되도록 요구할 수 있습니다. |

> [!NOTE] 
> 리소스 관리자는 지정된 종료 날짜가 있는 모든 할당을 갱신할 수 있습니다. 또한 멤버는 [역할 할당을 확장 또는 갱신](pim-resource-roles-renew-extend.md)하도록 셀프 서비스 요청을 시작할 수 있습니다.

## <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication 필요

PIM은 서로 다른 두 가지 시나리오에 대해 Azure MFA(Multi-Factor Authentication)의 선택적 적용을 제공합니다.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>활성 할당에 대한 Multi-Factor Authentication 필요

멤버를 단기간(예: 하루) 동안 역할에 할당하려는 경우가 있습니다. 이 경우 할당된 멤버가 활성화를 요청하게 할 필요가 없습니다. 이 시나리오에서는 멤버가 자신의 역할 할당을 사용하는 경우 PIM이 MFA를 적용할 수 없습니다. 해당 멤버가 할당된 순간부터 역할에서 이미 활성 상태이기 때문입니다.

할당을 수행하는 리소스 관리자가 자신이 말하는 본인이 맞는지 확인하기 위해 **활성 할당에 대한 Multi-Factor Authentication 필요** 확인란을 선택하여 활성 할당에 MFA를 적용할 수 있습니다.

### <a name="require-multi-factor-authentication-on-activation"></a>활성화 시 Multi-Factor Authentication 필요

멤버가 활성화하려면 먼저 역할의 적격 멤버에게 MFA를 실행하도록 요구할 수 있습니다. 이 프로세스를 통해 활성화를 요청한 사용자가 합당한 확신이 있는 당사자 본인임을 확인할 수 있습니다. 이 옵션을 적용하면 사용자의 계정이 손상되었을 수 있는 상황에서 중요한 리소스를 보호할 수 있습니다.

적격 멤버가 활성화하기 전에 MFA를 실행하도록 요구하려면 **활성화 시 Multi-Factor Authentication 필요** 확인란을 선택합니다.

자세한 내용은 [MFA(Multi-Factor Authentication) 및 PIM](pim-how-to-require-mfa.md)을 참조하세요.

## <a name="activation-maximum-duration"></a>최대 활성화 기간

**최대 활성화 기간** 슬라이더를 사용하여 역할이 만료되기 전에 활성 상태로 지속되는 최대 시간을 설정합니다. 이 값은 1 ~ 24시간 사이가 될 수 있습니다.

## <a name="require-justification"></a>근거 필요

멤버가 활성 할당에 대해 또는 활성화될 때 근거를 입력하도록 요구할 수 있습니다. 근거를 요구하려면 **활성 할당에 대한 근거 필요** 확인란 또는 **활성화에 대한 근거 필요** 확인란을 선택합니다.

## <a name="require-approval-to-activate"></a>활성화할 승인 필요

역할을 활성화하기 위해 승인을 요구하려는 경우 다음 단계를 수행합니다.

1. **활성화하려면 승인 필요** 확인란을 선택합니다.

1. **승인자 선택**을 클릭하여 멤버 또는 그룹 선택 창을 엽니다.

    ![멤버 또는 그룹 선택](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 하나 이상의 멤버 또는 그룹을 선택하고 **선택**을 클릭합니다. 멤버 및 그룹의 조합을 추가할 수 있습니다. 하나 이상의 승인자를 선택해야 합니다. 기본 승인자가 없습니다.

    선택한 항목이 선택한 승인자 목록에 표시됩니다.

1. 모든 역할 설정을 지정하면 **업데이트**를 클릭하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
- [PIM에서 Azure 리소스에 대한 보안 경고 구성](pim-resource-roles-configure-alerts.md)
