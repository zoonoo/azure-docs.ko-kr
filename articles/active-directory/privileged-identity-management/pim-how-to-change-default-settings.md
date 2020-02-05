---
title: PIM에서 Azure AD 역할 설정 구성-Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure AD 역할 설정을 구성 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/13/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a480327efacee2d1eb74353b2d0ef7885a6194
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024214"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할 설정 구성

권한 있는 역할 관리자는 적격 역할 할당을 활성화 하는 사용자에 대 한 환경 변경을 포함 하 여 해당 Azure Active Directory (Azure AD) 조직에서 Privileged Identity Management (PIM)을 사용자 지정할 수 있습니다.

## <a name="determine-your-version-of-pim"></a>PIM 버전 확인

11 월 2019부터 Privileged Identity Management의 Azure AD 역할 부분은 Azure 리소스 역할의 환경과 일치 하는 새 버전으로 업데이트 됩니다. 그러면 [기존 API에 대 한 변경](azure-ad-roles-features.md#api-changes)뿐만 아니라 추가 기능이 생성 됩니다. 새 버전이 롤아웃 되는 동안이 문서에서 수행 하는 절차는 현재 보유 하 고 있는 Privileged Identity Management 버전에 따라 달라 집니다. 이 섹션의 단계에 따라 Privileged Identity Management 버전을 확인 합니다. Privileged Identity Management 버전을 확인 한 후에는이 문서에서 해당 버전과 일치 하는 절차를 선택할 수 있습니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할에 있는 사용자로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
1. **Azure AD Privileged Identity Management**를 엽니다. 개요 페이지의 맨 위에 배너가 있는 경우이 문서의 **새 버전** 탭에 있는 지침을 따르세요. 그렇지 않으면 **이전 버전** 탭의 지침을 따릅니다.

    ![Azure AD 역할 새 버전](./media/pim-how-to-add-role-to-user/pim-new-version.png)

이 문서의 단계에 따라 Azure AD 역할에 대 한 요청을 승인 하거나 거부 합니다.

# <a name="new-versiontabnew"></a>[새 버전](#tab/new)

## <a name="open-role-settings"></a>역할 설정 열기

다음 단계에 따라 Azure AD 역할에 대한 설정을 엽니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 사용자를 사용 하 여 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **AZURE AD 역할**을 선택 합니다.

1. **역할 설정**을 선택 합니다.

    ![Azure 리소스 역할을 나열 하는 역할 설정 페이지](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. 설정을 구성 하려는 역할을 선택 합니다.

    ![몇 가지 할당 및 활성화 설정을 나열 하는 역할 설정 세부 정보 페이지](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. **편집** 을 선택 하 여 역할 설정 페이지를 엽니다.

    ![할당 및 활성화 설정 업데이트 옵션을 사용 하 여 역할 설정 페이지 편집](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    각 역할에 대한 역할 설정 페이지에서 몇 가지 설정을 구성할 수 있습니다.

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

# <a name="previous-versiontabprevious"></a>[이전 버전](#tab/previous)

## <a name="open-role-settings"></a>역할 설정 열기

다음 단계에 따라 Azure AD 역할에 대한 설정을 엽니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **AZURE AD 역할**을 선택 합니다.

1. **설정**을 선택합니다.

    ![Azure AD 역할-설정](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. **역할**을 선택합니다.

1. 설정을 구성 하려는 역할을 선택 합니다.

    ![Azure AD 역할-설정 역할](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    각 역할에 대한 설정 페이지에서 몇 가지 설정을 구성할 수 있습니다. 이러한 설정은 **영구** 할당이 아닌, **적격** 할당인 사용자에게만 영향을 미칩니다.

## <a name="activations"></a>활성화

**활성화** 슬라이더를 사용하여 역할이 만료되기 전에 활성 상태로 지속되는 최대 시간(시간 단위)을 설정합니다. 이 값은 1 ~ 72시간 사이가 될 수 있습니다.

## <a name="notifications"></a>알림

**알림** 스위치를 사용하여 역할이 활성화될 때 관리자가 이메일 알림을 받을지 여부를 지정합니다. 이 알림은 무단 또는 불법 활성화를 검색 하는 데 유용할 수 있습니다.

**사용**으로 설정되면 다음 사용자에게 알림이 전송됩니다.

- 권한 있는 역할 관리자
- 보안 관리자
- 전역 관리자

자세한 내용은 [Privileged Identity Management에서 전자 메일 알림](pim-email-notifications.md)을 참조 하세요.

## <a name="incidentrequest-ticket"></a>문제/요청 티켓

**인시던트/요청 티켓** 스위치를 사용 하 여 적격 관리자가 자신의 역할을 활성화할 때 티켓 번호를 포함 하도록 요구할 수 있습니다. 이 방법을 사용 하면 역할 액세스 감사를 보다 효과적으로 수행할 수 있습니다.

## <a name="multi-factor-authentication"></a>다중 인증(Multi-Factor Authentification)

**Multi-Factor Authentication** 스위치를 사용하여 사용자가 자신의 역할을 활성화하기 전에 MFA로 신원을 확인하도록 요구할지 여부를 지정할 수 있습니다. 세션 마다 한 번만 id를 확인 하면 되므로 역할을 활성화할 때마다 해당 id를 확인 해야 합니다. MFA를 사용할 때 염두에 두어야 할 두 가지가 있습니다.

- 전자 메일 주소 (일반적으로 @outlook.com이지만 항상 그렇지는 않음)에 대 한 Microsoft 계정을 가진 사용자는 Azure Multi-Factor Authentication에 등록할 수 없습니다. Microsoft 계정을 사용 하는 사용자에 게 역할을 할당 하려면 해당 역할에 대해 영구 관리자를 설정 하거나 multi-factor authentication을 사용 하지 않도록 설정 해야 합니다.
- Azure AD 및 Office 365에 대 한 높은 권한이 있는 역할에 대해서는 Azure Multi-Factor Authentication를 사용 하지 않도록 설정할 수 없습니다. 이 보안 기능은 다음 역할을 보호 하는 데 도움이 됩니다.  
  
  - Azure Information Protection 관리자
  - 대금 청구 관리자
  - 클라우드 애플리케이션 관리자
  - 규정 준수 관리자
  - 조건부 액세스 관리자
  - Dynamics 365 관리자
  - 고객 LockBox 액세스 승인자
  - 디렉터리 작성자
  - Exchange 관리자
  - 전역 관리자
  - Intune 관리자
  - Power BI 관리자
  - 권한 있는 역할 관리자
  - 보안 관리자
  - SharePoint 관리자
  - 비즈니스용 Skype 관리자
  - 사용자 관리자

자세한 내용은 [Multi-Factor Authentication 및 Privileged Identity Management](pim-how-to-require-mfa.md)를 참조하세요.

## <a name="require-approval"></a>승인 필요

역할을 활성화 하는 데 필요한 승인을 위임 하려는 경우 다음 단계를 수행 합니다.

1. **승인 필요** 스위치를 **사용**으로 설정합니다. 창은 승인자를 선택하는 옵션을 표시하도록 확장합니다.

    ![Azure AD 역할-설정-승인 필요](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    승인자를 지정 하지 않으면 권한 있는 역할 관리자가 기본 승인자가 되며이 역할에 대 한 모든 활성화 요청을 승인 하는 데 필요 합니다.

1. 승인자를 추가 하려면 **승인자 선택**을 클릭 합니다.

    ![Azure AD 역할-설정-승인 필요](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. 권한 있는 역할 관리자 외에 승인자를 하나 이상 선택한 다음 **선택**을 클릭 합니다. 사용자 또는 그룹을 선택할 수 있습니다. 승인자를 두 개 이상 추가 하는 것이 좋습니다. 자신을 승인자로 추가 하더라도 역할 활성화를 자체 승인할 수 없습니다. 선택한 항목이 선택한 승인자 목록에 표시됩니다.

1. 모든 역할 설정을 지정한 후에는 **저장** 을 선택 하 여 변경 내용을 저장 합니다.

---

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management에서 Azure AD 역할에 대 한 보안 경고 구성](pim-how-to-configure-security-alerts.md)
