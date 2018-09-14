---
title: PIM에서 Azure AD 디렉터리 역할 설정 구성 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure AD 디렉터리 역할 설정을 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 2d7226f18eb922eaba3c8184656560c33202ef56
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665437"
---
# <a name="configure-azure-ad-directory-role-settings-in-pim"></a>PIM에서 Azure AD 디렉터리 역할 설정 구성

권한 있는 역할 관리자는 적격 역할 할당을 활성화한 사용자의 환경을 변경하는 등, 해당 조직에서 Azure AD PIM(Privileged Identity Management)을 사용자 지정할 수 있습니다.

## <a name="open-role-settings"></a>역할 설정 열기

다음 단계에 따라 Azure AD 디렉터리 역할에 대한 설정을 엽니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure AD 디렉터리 역할**을 클릭합니다.

1. **설정**을 클릭합니다.

    ![Azure AD 디렉터리 역할 - 설정](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. **역할**을 클릭합니다.

1. 설정을 구성하려는 역할을 선택합니다.

    ![Azure AD 디렉터리 역할 - 역할 설정](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    각 역할에 대한 설정 페이지에서 몇 가지 설정을 구성할 수 있습니다. 이러한 설정은 **영구** 할당이 아닌, **적격** 할당인 사용자에게만 영향을 미칩니다.

## <a name="activations"></a>활성화

**활성화** 슬라이더를 사용하여 역할이 만료되기 전에 활성 상태로 지속되는 최대 시간(시간 단위)을 설정합니다. 이 값은 1 ~ 72시간 사이가 될 수 있습니다.

## <a name="notifications"></a>공지

**알림** 스위치를 사용하여 시스템에서 역할이 활성화되었음을 확인하는 이메일을 관리자에게 보낼지 여부를 지정할 수 있습니다. 이 정보는 무단 또는 불법 활성화를 탐지하는 데 유용할 수 있습니다.

## <a name="incidentrequest-ticket"></a>문제/요청 티켓

**인시던트/요청 티켓** 스위치를 사용하여 적격 관리자가 역할을 활성화할 때 티켓 번호를 포함할 것인지 여부를 지정할 수 있습니다. 이 기능은 역할 액세스 감사를 수행할 때 유용할 수 있습니다.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

**Multi-Factor Authentication** 스위치를 사용하여 사용자가 자신의 역할을 활성화하기 전에 MFA로 신원을 확인하도록 요구할지 여부를 지정할 수 있습니다. 이 작업은 매번 역할을 활성화할 때마다가 아니라 세션당 한 번만 수행해야 합니다. MFA를 사용할 때 염두에 두어야 할 두 가지가 있습니다.

* 전자 메일 주소로 Microsoft 계정을 이용하는 사용자(일반적으로 @outlook.com이지만 항상 그렇지는 않음)는 Azure MFA에 등록할 수 없습니다. Microsoft 계정 가진 사용자에게 역할을 할당하려는 경우, 영구 관리자가 되도록 하거나 해당 역할에 대해 MFA를 사용하지 않도록 설정해야 합니다.
* Azure AD 및 Office365에 대해 높은 권한이 있는 역할에 대한 MFA를 사용하지 않도록 설정할 수 없습니다. 이런 안전 기능을 둔 것은 이러한 역할을 신중하게 보호해야 하기 때문입니다.  
  
  * 응용 프로그램 관리자
  * 응용 프로그램 프록시 서버 관리자
  * 대금 청구 관리자  
  * 규정 준수 관리자  
  * CRM 서비스 관리자
  * 고객 LockBox 액세스 승인자
  * 디렉터리 기록기  
  * Exchange 관리자  
  * 전역 관리자
  * Intune 서비스 관리자
  * 사서함 관리자  
  * 파트너 계층1 지원  
  * 파트너 계층2 지원  
  * 권한 있는 역할 관리자
  * 보안 관리자  
  * SharePoint 관리자  
  * 비즈니스용 Skype 관리자  
  * 사용자 계정 관리자  

자세한 내용은 [MFA(Multi-Factor Authentication) 및 PIM](pim-how-to-require-mfa.md)을 참조하세요.

## <a name="require-approval"></a>승인 필요

역할을 활성화하기 위해 승인을 요구하려는 경우 다음 단계를 수행합니다.

1. **승인 필요** 스위치를 **사용**으로 설정합니다. 창은 승인자를 선택하는 옵션을 표시하도록 확장합니다.

    ![Azure AD 디렉터리 역할 - 설정 - 승인 필요](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    승인자를 **지정하지 않으면** 권한 있는 역할 관리자가 기본 승인자가 됩니다. 권한 있는 역할 관리자는 이 역할에 대한 **모든** 활성화 요청을 승인해야 합니다.

1. 승인자를 지정하려면 **승인자 선택**을 클릭합니다.

    ![Azure AD 디렉터리 역할 - 설정 - 승인 필요](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. 승인자를 하나 이상 선택한 다음, **선택**을 클릭합니다. 사용자 또는 그룹을 선택할 수 있습니다. 승인자는 둘 이상을 선택하는 것이 좋습니다. 자체 승인은 허용되지 않습니다.

    선택한 항목이 선택한 승인자 목록에 표시됩니다.

1. 모든 역할 설정을 지정하면 **저장**을 클릭하여 변경 사항을 저장합니다.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure AD 디렉터리 역할 할당](pim-how-to-add-role-to-user.md)
- [PIM에서 Azure AD 디렉터리 역할에 대한 보안 경고 구성](pim-how-to-configure-security-alerts.md)
