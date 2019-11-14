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
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee99a8e75fe8da85b1cf82623ed110991db24b66
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021807"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할 설정 구성

권한 있는 역할 관리자는 적격 역할 할당을 활성화 하는 사용자에 대 한 환경 변경을 포함 하 여 해당 Azure Active Directory (Azure AD) 조직에서 Privileged Identity Management (PIM)을 사용자 지정할 수 있습니다.

## <a name="open-role-settings"></a>역할 설정 열기

다음 단계에 따라 Azure AD 역할에 대한 설정을 엽니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure AD 역할**을 클릭합니다.

1. **설정**을 클릭합니다.

    ![Azure AD 역할-설정](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. **역할**을 클릭합니다.

1. 설정을 구성하려는 역할을 선택합니다.

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

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

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

1. 승인자를 지정하려면 **승인자 선택**을 클릭합니다.

    ![Azure AD 역할-설정-승인 필요](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. 권한 있는 역할 관리자 외에 승인자를 하나 이상 선택한 다음 **선택**을 클릭 합니다. 사용자 또는 그룹을 선택할 수 있습니다. 두 개 이상의 승인자가 있는 것이 좋습니다. 자신을 승인자로 추가 하더라도 역할 활성화를 자체 승인할 수 없습니다. 선택한 항목이 선택한 승인자 목록에 표시됩니다.

1. 모든 역할 설정을 지정한 후에는 **저장** 을 선택 하 여 변경 내용을 저장 합니다.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management에서 Azure AD 역할에 대 한 보안 경고 구성](pim-how-to-configure-security-alerts.md)
