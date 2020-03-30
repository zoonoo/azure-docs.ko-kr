---
title: PIM - Azure AD에서 Azure 리소스 역할 설정 구성 | 마이크로 소프트 문서
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
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4db330a875b8241b642bcbc71fb0866c9833ee7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638683"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>권한 있는 ID 관리에서 Azure 리소스 역할 설정 구성

Azure 리소스 역할 설정을 구성할 때 Azure Active Directory(Azure AD) 권한 있는 ID 관리(PIM)에서 Azure 리소스 역할 할당에 적용되는 기본 설정을 정의합니다. 다음 절차에 따라 승인 워크플로를 구성하고 요청을 승인하거나 거부할 수 있는 사용자를 지정합니다.

## <a name="open-role-settings"></a>역할 설정 열기

다음 단계에 따라 Azure 리소스 역할에 대한 설정을 엽니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 사용자와 함께 Azure [포털에](https://portal.azure.com/) 로그인합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure 리소스**를 선택합니다.

1. 구독 또는 관리 그룹과 같이 관리할 리소스를 선택합니다.

    ![관리할 수 있는 Azure 리소스 페이지 목록 리소스](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. **역할 설정을**선택합니다.

    ![역할 설정 페이지 나열 Azure 리소스 역할](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. 구성할 설정을 지정하는 역할을 선택합니다.

    ![역할 설정 세부 정보 페이지 나열 여러 할당 및 활성화 설정](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. **역할 설정** 창을 열려면 **편집을** 선택합니다. 첫 번째 탭을 사용하면 권한 있는 ID 관리에서 역할 활성화를 위한 구성을 업데이트할 수 있습니다.

    ![활성화 탭이 열려 있는 역할 설정 페이지 편집](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. 페이지 하단의 **과제** 탭 또는 **다음: 과제** 단추를 선택하여 과제 설정 탭을 엽니다. 이러한 설정은 권한 있는 ID 관리 인터페이스 내에서 수행된 역할 할당을 제어합니다.

    ![역할 설정 페이지에서 역할 할당 탭](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. **알림** 탭 또는 **다음:** 페이지 하단의 활성화 버튼을 사용하여 이 역할의 알림 설정 탭으로 이동합니다. 이러한 설정은 이 역할과 관련된 모든 전자 메일 알림을 제어합니다.

    ![역할 설정 페이지에서 역할 알림 탭](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    역할 설정 페이지의 **알림** 탭에서 권한 있는 ID 관리를 사용하면 알림을 받는 사람과 알림을 받는 알림을 세부적으로 제어할 수 있습니다.

    - **이메일 끄기**<br>기본 수신자 확인란을 지우고 추가 수신자를 삭제하여 특정 이메일을 끌 수 있습니다.  

    - **지정된 이메일 주소로 이메일 제한**<br>기본 받는 사람 확인란을 지우면 기본 수신자에게 보낸 이메일을 끌 수 있습니다. 그런 다음 추가 수신자로 이메일 주소를 추가할 수 있습니다. 둘 이상의 이메일 주소를 추가하려면 세미콜론(;)을 사용하여 이메일 주소를 분리합니다.

    - **기본 받는 사람과 추가 받는 사람 모두에게 이메일 보내기**<br>기본 받는 사람 확인란을 선택하고 추가 받는 사람에 대한 이메일 주소를 추가하여 기본 받는 사람과 추가 받는 사람 모두에게 이메일을 보낼 수 있습니다.

    - **중요한 이메일만**<br>각 이메일 유형에 대해 중요한 이메일만 수신하려면 확인란을 선택할 수 있습니다. 즉, 권한 있는 ID 관리는 전자 메일에 즉각적인 조치가 필요한 경우에만 구성된 받는 사람에게 이메일을 계속 보냅니다. 예를 들어 사용자에게 역할 할당을 연장하도록 요청하는 이메일은 트리거되지 않으며 관리자가 확장 요청을 승인하도록 요구하는 이메일은 트리거됩니다.

1. 역할 설정을 업데이트하려면 언제든지 **업데이트** 단추를 선택합니다.

## <a name="assignment-duration"></a>할당 기간

역할에 대한 설정을 구성할 때 각 할당 유형에 대한 2가지 할당 기간 옵션(적격 및 활성) 중에서 선택할 수 있습니다. 이러한 옵션은 사용자가 권한 있는 ID 관리의 역할에 할당될 때 기본 최대 기간이 됩니다.

다음 **적격** 할당 기간 옵션 중 하나를 선택할 수 있습니다.

| | |
| --- | --- |
| **영구 적격 할당 허용** | 리소스 관리자는 영구적인 할당을 할당할 수 있습니다. |
| **적격 할당 만료 기준 시간** | 리소스 관리자는 모든 적격 할당에 시작 및 종료 날짜가 지정되도록 요구할 수 있습니다. |

또한 다음 **활성** 할당 기간 옵션 중 하나를 선택할 수 있습니다.

| | |
| --- | --- |
| **영구 활성 할당 허용** | 리소스 관리자는 영구 활성 할당을 할당할 수 있습니다. |
| **활성 할당 만료 기준 시간** | 리소스 관리자는 모든 활성 할당에 시작 및 종료 날짜가 지정되도록 요구할 수 있습니다. |

> [!NOTE]
> 리소스 관리자는 지정된 종료 날짜가 있는 모든 할당을 갱신할 수 있습니다. 또한 사용자는 [역할 할당을 확장하거나 갱신하기](pim-resource-roles-renew-extend.md)위해 셀프 서비스 요청을 시작할 수 있습니다.

## <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication 필요

Privileged Identity Management는 서로 다른 두 가지 시나리오에 대해 선택적인 Azure Multi-Factor Authentication 적용을 제공합니다.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>활성 할당에 대한 Multi-Factor Authentication 필요

경우에 따라 짧은 기간(예: 1일)동안 역할에 사용자 또는 그룹을 할당할 수 있습니다. 이 경우 할당된 사용자는 활성화를 요청할 필요가 없습니다. 이 시나리오에서 권한 ID 관리는 사용자가 할당된 시점부터 역할에 이미 활성 상태이므로 역할 할당을 사용할 때 다단계 인증을 적용할 수 없습니다.

할당을 충족하는 리소스 관리자가 자신의 말인지 확인하려면 활성 할당에서 **다단계 인증 요구** 상자를 선택하여 활성 할당시 다단계 인증을 적용할 수 있습니다.

### <a name="require-multi-factor-authentication-on-activation"></a>활성화 시 Multi-Factor Authentication 필요

역할을 받을 자격이 있는 사용자가 Azure 다단계 인증을 사용 중인 사용자를 인증하기 전에 증명하도록 요구할 수 있습니다. 다단계 인증은 사용자가 합리적인 확신을 가지고 있다고 말하는 사용자를 보장합니다. 이 옵션을 적용하면 사용자의 계정이 손상되었을 수 있는 상황에서 중요한 리소스를 보호할 수 있습니다.

활성화하기 전에 다단계 인증이 필요하려면 **활성화시 다단계 인증 필요** 상자를 선택합니다.

자세한 내용은 [Multi-Factor Authentication 및 Privileged Identity Management](pim-how-to-require-mfa.md)를 참조하세요.

## <a name="activation-maximum-duration"></a>최대 활성화 기간

**최대 활성화 기간** 슬라이더를 사용하여 역할이 만료되기 전에 활성 상태로 지속되는 최대 시간을 설정합니다. 이 값은 1시간에서 24시간까지가능합니다.

## <a name="require-justification"></a>근거 필요

사용자가 활성화할 때 비즈니스 근거를 입력하도록 요구할 수 있습니다. 근거를 요구하려면 **활성 할당에 대한 근거 필요** 확인란 또는 **활성화에 대한 근거 필요** 확인란을 선택합니다.

## <a name="require-approval-to-activate"></a>활성화할 승인 필요

역할을 활성화하기 위해 승인을 요구하려는 경우 다음 단계를 수행합니다.

1. **활성화하려면 승인 필요** 확인란을 선택합니다.

1. **승인자 선택을** 선택하여 구성원 또는 그룹 선택 페이지를 **엽니다.**

    ![승인자를 선택하려면 사용자 또는 그룹 창 선택](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 하나 이상의 사용자 또는 그룹을 선택한 다음 **선택**을 클릭합니다. 사용자 및 그룹의 조합을 추가할 수 있습니다. 하나 이상의 승인자를 선택해야 합니다. 기본 승인자가 없습니다.

    선택한 항목이 선택한 승인자 목록에 표시됩니다.

1. 모든 역할 설정을 지정한 후에는 **업데이트를** 선택하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
- [권한 있는 ID 관리에서 Azure 리소스 역할에 대한 보안 경고 구성](pim-resource-roles-configure-alerts.md)
