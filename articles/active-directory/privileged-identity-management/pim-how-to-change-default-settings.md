---
title: PIM - Azure AD에서 Azure AD 역할 설정 구성 | 마이크로 소프트 문서
description: PIM(Azure AD 권한 ID 관리)에서 Azure AD 역할 설정을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd525973a8c8ba5bb3103126b4ddb5b6973590a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205027"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>권한 있는 ID 관리에서 Azure AD 역할 설정 구성

권한 있는 역할 관리자는 적격 역할 할당을 활성화하는 사용자의 환경을 변경하는 등 Azure Active Directory(Azure AD) 조직에서 PIM(권한 있는 ID 관리)을 사용자 지정할 수 있습니다.

## <a name="determine-your-version-of-pim"></a>PIM 버전 확인

2019년 11월부터 권한 있는 Id Management의 Azure AD 역할 부분이 Azure 리소스 역할에 대한 환경과 일치하는 새 버전으로 업데이트됩니다. 이렇게 하면 [기존 API에 대한 변경](azure-ad-roles-features.md#api-changes)사항뿐만 아니라 추가 기능이 만들어집니다. 새 버전이 롤아웃되는 동안 이 문서에서 따르는 절차는 현재 가지고 있는 권한 있는 ID 관리 버전에 따라 다릅니다. 이 섹션의 단계에 따라 사용 중인 권한 ID 관리 버전을 확인합니다. 권한 있는 ID 관리 버전을 알고 나면 이 문서에서 해당 버전과 일치하는 절차를 선택할 수 있습니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할에 있는 사용자와 Azure [포털에](https://portal.azure.com/) 로그인합니다.
1. Azure **AD 권한 ID 관리**를 엽니다. 개요 페이지 상단에 배너가 있는 경우 이 문서의 **새 버전** 탭의 지침을 따릅니다. 그렇지 않으면 **이전 버전** 탭의 지침을 따릅니다.

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

이 문서의 단계에 따라 Azure AD 역할에 대한 요청을 승인하거나 거부합니다.

# <a name="new-version"></a>[새 버전](#tab/new)

## <a name="open-role-settings"></a>역할 설정 열기

다음 단계에 따라 Azure AD 역할에 대한 설정을 엽니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 사용자와 함께 Azure [포털에](https://portal.azure.com/) 로그인합니다.
gt
1. 열기 **Azure AD 권한 ID 관리** &gt; **Azure AD 역할** &gt; **역할 설정**입니다.

    ![역할 설정 페이지에 Azure AD 역할 나열](./media/pim-how-to-change-default-settings/role-settings.png)

1. 구성할 설정을 지정하는 역할을 선택합니다.

    ![역할 설정 세부 정보 페이지 나열 여러 할당 및 활성화 설정](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. **편집**을 선택하여 역할 설정 페이지를 엽니다.

    ![과제 및 활성화 설정을 업데이트하는 옵션으로 역할 설정 페이지 편집](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    각 역할에 대한 역할 설정 페이지에서 몇 가지 설정을 구성할 수 있습니다.

## <a name="assignment-duration"></a>할당 기간

역할에 대한 설정을 구성할 때 각 할당 유형에 대한 2가지 할당 기간 옵션(적격 및 활성) 중에서 선택할 수 있습니다. 이러한 옵션은 사용자가 권한 있는 ID 관리의 역할에 할당될 때 기본 최대 기간이 됩니다.

다음 **적격** 할당 기간 옵션 중 하나를 선택할 수 있습니다.

| | |
| --- | --- |
| **영구 적격 할당 허용** | 글로벌 관리자 및 권한 있는 역할 관리자는 영구적인 할당을 할당할 수 있습니다. |
| **적격 할당 만료 기준 시간** | 글로벌 관리자 및 권한 있는 역할 관리자는 모든 적격 할당에 지정된 시작 및 종료 날짜가 있어야 할 수 있습니다. |

또한 다음 **활성** 할당 기간 옵션 중 하나를 선택할 수 있습니다.

| | |
| --- | --- |
| **영구 활성 할당 허용** | 글로벌 관리자 및 권한 있는 역할 관리자는 영구 활성 할당을 할당할 수 있습니다. |
| **활성 할당 만료 기준 시간** | 전역 관리자 및 권한 있는 역할 관리자는 모든 활성 할당에 지정된 시작 및 종료 날짜가 있어야 할 수 있습니다. |

> [!NOTE]
> 지정된 종료 날짜가 있는 모든 할당은 글로벌 관리자 및 권한 있는 역할 관리자가 갱신할 수 있습니다. 또한 사용자는 [역할 할당을 확장하거나 갱신하기](pim-resource-roles-renew-extend.md)위해 셀프 서비스 요청을 시작할 수 있습니다.

## <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication 필요

Privileged Identity Management는 서로 다른 두 가지 시나리오에 대해 선택적인 Azure Multi-Factor Authentication 적용을 제공합니다.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>활성 할당에 대한 Multi-Factor Authentication 필요

경우에 따라 짧은 기간(예: 1일)동안 역할에 사용자를 할당할 수 있습니다. 이 경우 할당된 사용자는 활성화를 요청할 필요가 없습니다. 이 시나리오에서 권한 ID 관리는 사용자가 할당된 시점부터 역할에 이미 활성 상태이므로 역할 할당을 사용할 때 다단계 인증을 적용할 수 없습니다.

할당을 충족하는 관리자가 자신의 사람이 누구인지 확인하려면 활성 할당에서 **다단계 인증 요구** 상자를 선택하여 활성 할당시 다단계 인증을 적용할 수 있습니다.

### <a name="require-multi-factor-authentication-on-activation"></a>활성화 시 Multi-Factor Authentication 필요

역할을 받을 자격이 있는 사용자가 Azure 다단계 인증을 사용 중인 사용자를 인증하기 전에 증명하도록 요구할 수 있습니다. 다단계 인증은 사용자가 합리적인 확신을 가지고 있다고 말하는 사용자를 보장합니다. 이 옵션을 적용하면 사용자의 계정이 손상되었을 수 있는 상황에서 중요한 리소스를 보호할 수 있습니다.

활성화하기 전에 다단계 인증이 필요하려면 **역할 편집 설정의**할당 **탭에서 활성화시 다단계 인증 필요** 상자를 선택합니다.

자세한 내용은 [Multi-Factor Authentication 및 Privileged Identity Management](pim-how-to-require-mfa.md)를 참조하세요.

## <a name="activation-maximum-duration"></a>최대 활성화 기간

**최대 활성화 기간** 슬라이더를 사용하여 역할이 만료되기 전에 활성 상태로 지속되는 최대 시간을 설정합니다. 이 값은 1시간에서 24시간까지가능합니다.

## <a name="require-justification"></a>근거 필요

사용자가 활성화할 때 비즈니스 근거를 입력하도록 요구할 수 있습니다. 근거를 요구하려면 **활성 할당에 대한 근거 필요** 확인란 또는 **활성화에 대한 근거 필요** 확인란을 선택합니다.

## <a name="require-approval-to-activate"></a>활성화할 승인 필요

여러 승인자를 설정하는 경우 승인 중 하나가 승인 또는 거부되는 즉시 승인이 완료됩니다. 두 명 이상의 사용자의 승인을 요구할 수 없습니다. 역할을 활성화하기 위해 승인을 요구하려면 다음 단계를 따르십시오.

1. **활성화하려면 승인 필요** 확인란을 선택합니다.

1. **승인자 선택**을 선택합니다.

    ![승인자를 선택하려면 사용자 또는 그룹 창 선택](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 하나 이상의 사용자를 선택한 다음 **을 선택을**클릭합니다. 하나 이상의 승인자를 선택해야 합니다. 기본 승인자가 없습니다.

    선택한 항목이 선택한 승인자 목록에 표시됩니다.

1. 모든 역할 설정을 지정한 후에는 **업데이트를** 선택하여 변경 내용을 저장합니다.

# <a name="previous-version"></a>[이전 버전](#tab/previous)

## <a name="open-role-settings"></a>역할 설정 열기

다음 단계에 따라 Azure AD 역할에 대한 설정을 엽니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure AD 역할을**선택합니다.

1. **설정**을 선택합니다.

    ![Azure AD 역할 - 설정](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. **역할**을 선택합니다.

1. 구성할 설정을 지정하는 역할을 선택합니다.

    ![Azure AD 역할 - 설정 역할](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    각 역할에 대한 설정 페이지에서 몇 가지 설정을 구성할 수 있습니다. 이러한 설정은 **영구** 할당이 아닌, **적격** 할당인 사용자에게만 영향을 미칩니다.

## <a name="activations"></a>활성화

**활성화** 슬라이더를 사용하여 역할이 만료되기 전에 활성 상태로 지속되는 최대 시간(시간 단위)을 설정합니다. 이 값은 1 ~ 72시간 사이가 될 수 있습니다.

## <a name="notifications"></a>공지

**알림** 스위치를 사용하여 역할이 활성화될 때 관리자가 이메일 알림을 받을지 여부를 지정합니다. 이 알림은 무단 또는 불법 활성화를 검색하는 데 유용할 수 있습니다.

**사용**으로 설정되면 다음 사용자에게 알림이 전송됩니다.

- 권한 있는 역할 관리자
- 보안 관리자
- 전역 관리자

자세한 내용은 [권한 있는 ID 관리의 전자 메일 알림을](pim-email-notifications.md)참조하십시오.

## <a name="incidentrequest-ticket"></a>문제/요청 티켓

**인시던트/요청 티켓** 스위치를 사용하여 자격을 갖춘 관리자가 역할을 활성화할 때 티켓 번호를 포함하도록 요구합니다. 이렇게 하면 역할 액세스 감사를 보다 효과적으로 만들 수 있습니다.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

**Multi-Factor Authentication** 스위치를 사용하여 사용자가 자신의 역할을 활성화하기 전에 MFA로 신원을 확인하도록 요구할지 여부를 지정할 수 있습니다. 역할을 활성화할 때마다 세션당 한 번만 ID를 확인해야 합니다. MFA를 사용할 때 염두에 두어야 할 두 가지가 있습니다.

- Microsoft 계정이 있는 사용자는 전자 메일 @outlook.com주소(일반적으로 항상 그런 것은 아님)를 Azure 다단계 인증에 등록할 수 없습니다. Microsoft 계정이 있는 사용자에게 역할을 할당하려면 해당 역할에 대한 다단계 인증을 영구적으로 관리자로 지정하거나 비활성화해야 합니다.
- Azure AD 및 Office 365에 대해 권한이 높은 역할에 대해 Azure 다단계 인증을 비활성화할 수 없습니다. 이 안전 기능은 다음과 같은 역할을 보호하는 데 도움이 됩니다.  
  
  - Azure 정보 보호 관리자
  - 대금 청구 관리자
  - 클라우드 애플리케이션 관리자
  - 규정 준수 관리자
  - 조건부 액세스 관리자
  - 역학 365 관리자
  - 고객 LockBox 액세스 승인자
  - 디렉터리 작성자
  - Exchange 관리자
  - 전역 관리자
  - Intune 관리자
  - 전원 BI 관리자
  - 권한 있는 역할 관리자
  - 보안 관리자
  - SharePoint 관리자
  - 비즈니스용 Skype 관리자
  - 사용자 관리자

자세한 내용은 [Multi-Factor Authentication 및 Privileged Identity Management](pim-how-to-require-mfa.md)를 참조하세요.

## <a name="require-approval"></a>승인 필요

역할을 활성화하는 데 필요한 승인을 위임하려면 다음 단계를 따르십시오.

1. **승인 필요** 스위치를 **사용**으로 설정합니다. 창은 승인자를 선택하는 옵션을 표시하도록 확장합니다.

    ![Azure AD 역할 - 설정 - 승인 필요](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    승인자를 지정하지 않으면 권한 있는 역할 관리자가 기본 승인자가 되고 이 역할에 대한 모든 활성화 요청을 승인해야 합니다.

1. 승인자를 추가하려면 **승인자 선택을**클릭합니다.

    ![Azure AD 역할 - 설정 - 승인 필요](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. 권한 있는 역할 관리자 외에 하나 이상의 승인자를 선택한 다음 **선택**을 클릭합니다. 승인자를 두 명 이상 추가하는 것이 좋습니다. 자신을 승인자로 추가하더라도 역할 활성화를 자체 승인할 수 없습니다. 선택한 항목이 선택한 승인자 목록에 표시됩니다.

1. 모든 역할 설정을 지정한 후 **저장을** 선택하여 변경 내용을 저장합니다.

---

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
- [권한 있는 ID 관리에서 Azure AD 역할에 대한 보안 경고 구성](pim-how-to-configure-security-alerts.md)
