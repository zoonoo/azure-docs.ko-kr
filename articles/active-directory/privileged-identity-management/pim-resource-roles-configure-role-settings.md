---
title: PIM에서 Azure 리소스 역할 설정 구성-Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할 설정을 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: abb6c2212549c3d92f9c1dbc42c31f838a704af2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144264"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 역할 설정 구성

Azure 리소스 역할 설정을 구성 하는 경우 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)에서 Azure 리소스 역할 할당에 적용 되는 기본 설정을 정의 합니다. 다음 절차에 따라 승인 워크플로를 구성하고 요청을 승인하거나 거부할 수 있는 사용자를 지정합니다.

## <a name="open-role-settings"></a>역할 설정 열기

다음 단계에 따라 Azure 리소스 역할에 대한 설정을 엽니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 사용자를 사용 하 여 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 선택합니다.

1. 관리 하려는 리소스 (예: 구독 또는 관리 그룹)를 선택 합니다.

    ![관리할 수 있는 리소스를 나열 하는 Azure 리소스 페이지](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. **역할 설정**을 선택 합니다.

    ![Azure 리소스 역할을 나열 하는 역할 설정 페이지](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. 설정을 구성 하려는 역할을 선택 합니다.

    ![몇 가지 할당 및 활성화 설정을 나열 하는 역할 설정 세부 정보 페이지](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. **편집** 을 선택 하 여 **역할 설정** 창을 엽니다. 첫 번째 탭에서는 Privileged Identity Management에서 역할 활성화에 대 한 구성을 업데이트할 수 있습니다.

    ![활성화 탭이 열려 있는 역할 설정 편집 페이지](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. **할당 탭 또는** 페이지 맨 아래에 있는 **다음: 할당** 단추를 선택 하 여 할당 설정 탭을 엽니다. 이러한 설정은 Privileged Identity Management 인터페이스 내에서 수행 되는 역할 할당을 제어 합니다.

    ![역할 설정 페이지의 역할 할당 탭](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. **알림** 탭 또는 페이지 맨 아래에 있는 **다음: 활성화** 단추를 사용 하 여이 역할에 대 한 알림 설정 탭으로 이동할 수 있습니다. 이러한 설정은이 역할과 관련 된 모든 전자 메일 알림을 제어 합니다.

    ![역할 설정 페이지의 역할 알림 탭](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

1. 언제 든 지 **업데이트** 단추를 선택 하 여 역할 설정을 업데이트 합니다.

## <a name="assignment-duration"></a>할당 기간

역할에 대한 설정을 구성할 때 각 할당 유형에 대한 2가지 할당 기간 옵션(적격 및 활성) 중에서 선택할 수 있습니다. 이러한 옵션은 사용자가 Privileged Identity Management의 역할에 할당 되는 기본 최대 기간이 됩니다.

다음 **적격** 할당 기간 옵션 중 하나를 선택할 수 있습니다.

| | |
| --- | --- |
| **영구 적격 할당 허용** | 리소스 관리자가 영구 적격 할당을 할당할 수 있습니다. |
| **적격 할당 만료 기준 시간** | 리소스 관리자는 모든 적격 할당에 시작 및 종료 날짜가 지정되도록 요구할 수 있습니다. |

또한 다음 **활성** 할당 기간 옵션 중 하나를 선택할 수 있습니다.

| | |
| --- | --- |
| **영구 활성 할당 허용** | 리소스 관리자는 영구 활성 할당을 할당할 수 있습니다. |
| **활성 할당 만료 기준 시간** | 리소스 관리자는 모든 활성 할당에 시작 및 종료 날짜가 지정되도록 요구할 수 있습니다. |

> [!NOTE]
> 리소스 관리자는 지정된 종료 날짜가 있는 모든 할당을 갱신할 수 있습니다. 또한 사용자는 [역할 할당을 확장 하거나 갱신](pim-resource-roles-renew-extend.md)하는 셀프 서비스 요청을 시작할 수 있습니다.

## <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication 필요

Privileged Identity Management는 서로 다른 두 가지 시나리오에 대해 선택적인 Azure Multi-Factor Authentication 적용을 제공합니다.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>활성 할당에 대한 Multi-Factor Authentication 필요

경우에 따라 짧은 기간 동안 사용자 또는 그룹을 역할에 할당할 수 있습니다 (예: 1 일). 이 경우 할당 된 사용자가 활성화를 요청할 필요가 없습니다. 이 시나리오에서는 사용자가 역할 할당을 사용 하는 경우 사용자가 역할 할당을 사용 하는 경우 multi-factor authentication Privileged Identity Management을 적용할 수 없습니다 .이는 역할이 할당 된 시간부터 이미 활성화 되어 있기 때문입니다.

할당을 수행 하는 리소스 관리자가 사용자의 신원을 확인 하려면 활성 **할당에 대 한 Multi-Factor Authentication 필요** 상자를 선택 하 여 활성 할당에 대해 multi-factor authentication을 적용할 수 있습니다.

### <a name="require-multi-factor-authentication-on-activation"></a>활성화 시 Multi-Factor Authentication 필요

역할에 대 한 자격이 있는 사용자가 Azure Multi-Factor Authentication를 사용 하 여 인증을 받을 수 있도록 요구할 수 있습니다. Multi-factor authentication을 사용 하면 사용자에 게 적절 한 확신을 갖고 있다고 사용자에 게 표시 됩니다. 이 옵션을 적용하면 사용자의 계정이 손상되었을 수 있는 상황에서 중요한 리소스를 보호할 수 있습니다.

활성화 하기 전에 multi-factor authentication을 요구 하려면 **활성화 시 Multi-Factor Authentication 필요** 확인란을 선택 합니다.

자세한 내용은 [Multi-Factor Authentication 및 Privileged Identity Management](pim-how-to-require-mfa.md)를 참조하세요.

## <a name="activation-maximum-duration"></a>최대 활성화 기간

**최대 활성화 기간** 슬라이더를 사용하여 역할이 만료되기 전에 활성 상태로 지속되는 최대 시간을 설정합니다. 이 값은 1 ~ 24 시간 일 수 있습니다.

## <a name="require-justification"></a>근거 필요

사용자가 활성화할 때 비즈니스 근거를 입력 하도록 요구할 수 있습니다. 근거를 요구하려면 **활성 할당에 대한 근거 필요** 확인란 또는 **활성화에 대한 근거 필요** 확인란을 선택합니다.

## <a name="require-approval-to-activate"></a>활성화할 승인 필요

역할을 활성화하기 위해 승인을 요구하려는 경우 다음 단계를 수행합니다.

1. **활성화하려면 승인 필요** 확인란을 선택합니다.

1. **승인자 선택** 을 선택 하 여 **멤버 또는 그룹 선택** 페이지를 엽니다.

    ![승인자를 선택할 사용자 또는 그룹 창을 선택 합니다.](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 하나 이상의 사용자 또는 그룹을 선택 하 고 **선택**을 클릭 합니다. 사용자 및 그룹의 조합을 추가할 수 있습니다. 하나 이상의 승인자를 선택해야 합니다. 기본 승인자가 없습니다.

    선택한 항목이 선택한 승인자 목록에 표시됩니다.

1. 모든 역할 설정을 지정 했으면 **업데이트** 를 선택 하 여 변경 내용을 저장 합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
- [Privileged Identity Management에서 Azure 리소스 역할에 대 한 보안 경고 구성](pim-resource-roles-configure-alerts.md)
