---
title: B2B 외부 협업 설정 사용 - Azure AD
description: Active Directory B2B 외부 협업을 사용하도록 설정하고 게스트 사용자를 초대할 수 있는 사용자를 관리하는 방법을 알아봅니다. 게스트 초대자 역할을 사용하여 초대를 위임합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/24/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c6dbac365b10b7abaeea6c4ae600cef24fcd1d2
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821805"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>B2B 외부 협업을 사용하도록 설정 및 게스트를 초대할 수 있는 사용자 관리

이 문서에서는 Azure Active Directory(Azure AD) B2B 협업을 사용하도록 설정하고, 게스트를 초대할 수 있는 사용자를 지정하고, Azure AD에서 게스트 사용자가 가지는 사용 권한을 결정하는 방법을 설명합니다. 

기본적으로 디렉터리에 있는 모든 사용자와 게스트는 관리자 역할에 할당되지 않은 경우에도 게스트를 초대할 수 있습니다. 외부 협업 설정을 사용하여 조직에서 다양한 유형의 사용자에 대해 게스트 초대를 설정하거나 해제할 수 있습니다. 또한 게스트 초대를 허용하는 역할을 할당하여 개별 사용자에게 초대를 위임할 수도 있습니다.

Azure AD를 사용하면 Azure AD 디렉터리에서 외부 게스트 사용자에게 표시되는 항목을 제한할 수 있습니다. 기본적으로 게스트 사용자는 사용자, 그룹 또는 기타 디렉터리 리소스를 열거하지 못하도록 차단하는 제한된 권한 수준으로 설정되지만 숨겨지지 않은 그룹의 구성원 자격은 볼 수 있습니다. 새 미리 보기 설정을 사용하여 게스트가 자신의 프로필 정보만 볼 수 있도록 게스트 액세스 권한을 추가로 제한할 수 있습니다. 자세한 내용은 [게스트 액세스 권한(미리 보기) 제한](../enterprise-users/users-restrict-guest-permissions.md)을 참조하세요.

## <a name="configure-b2b-external-collaboration-settings"></a>B2B 외부 협업 설정 구성

Azure AD B2B 협업을 사용하면 테넌트 관리자가 다음 초대 정책을 설정할 수 있습니다.

- 초대 해제
- 관리자 및 게스트 초대자 역할의 사용자만 초대 가능
- 관리자, 게스트 초대자 역할 및 구성원은 초대 가능
- 게스트를 포함한 모든 사용자가 초대를 수행할 수 있습니다.

기본적으로 게스트를 포함한 모든 사용자가 게스트 사용자를 초대할 수 있습니다.

### <a name="to-configure-external-collaboration-settings"></a>외부 협업 설정을 구성하려면 다음을 수행합니다.

1. 테넌트 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** 를 선택합니다.
3. **외부 ID** > **외부 협업 설정** 을 선택합니다.

4. **게스트 사용자 액세스 제한(미리 보기)** 아래에서 게스트 사용자에게 부여할 액세스 수준을 선택합니다.
  
   - **게스트 사용자가 구성원과 동일한 액세스 권한을 갖습니다(가장 포괄적).** : 이 옵션은 게스트에게 Azure AD 리소스와 디렉터리 데이터에 대한 액세스 권한을 구성원 사용자와 동일하게 제공합니다.

   - **게스트 사용자가 디렉터리 개체의 속성 및 구성원 자격에 대해 제한된 액세스 권한을 갖습니다(기본값).** : 이 설정은 사용자, 그룹 또는 기타 디렉터리 리소스를 열거하는 등의 특정 디렉터리 작업에서 게스트를 차단합니다. 게스트가 숨겨지지 않은 모든 그룹의 구성원 자격을 볼 수 있습니다.

   - **게스트 사용자 액세스가 자신의 디렉터리 개체의 속성 및 구성원 자격으로 제한됩니다(가장 제한적)** : 이 설정을 사용하면 게스트가 자신의 프로필에만 액세스할 수 있습니다. 게스트는 다른 사용자의 프로필, 그룹 또는 그룹 구성원 자격을 볼 수 없습니다.

5. **게스트 초대 설정** 에서 적절한 설정을 선택합니다.

    ![게스트 초대 설정](./media/delegate-invitations/guest-invite-settings.png)

   - **조직 내 모든 사용자가 게스트 및 비관리자를 포함하여 게스트 사용자를 초대할 수 있음(가장 포괄적)** : 조직의 게스트가 조직의 구성원이 아닌 사용자를 포함하는 다른 게스트를 초대하도록 허용하려면 이 라디오 단추를 선택합니다.
   - **특정 관리자 역할에 할당된 사용자와 멤버 사용자가 멤버 권한이 있는 게스트를 비롯하여 게스트 사용자를 초대할 수 있음**: 구성원인 사용자와 특정 관리자 역할이 있는 사용자가 게스트를 초대할 수 있도록 허용하려면 이 라디오 단추를 선택합니다.
   - **특정 관리자 역할에 할당된 사용자만 게스트 사용자를 초대할 수 있음**: 관리자 역할이 있는 사용자만 게스트를 초대하도록 허용하려면 이 라디오 단추를 선택합니다. 관리자 역할에는 [전역 관리자](../roles/permissions-reference.md#global-administrator), [사용자 관리자](../roles/permissions-reference.md#user-administrator) 및 [게스트 초대자](../roles/permissions-reference.md#guest-inviter)가 포함됩니다.
   - **조직 내에서 누구도 관리자를 비롯하여 게스트 사용자를 초대할 수 없음(가장 제한적)** : 조직의 모든 사람이 게스트를 초대하는 것을 거부하려면 이 라디오 단추를 선택합니다.
     > [!NOTE]
     > **구성원이 초대할 수 있음** 을 **아니요** 로 설정하고 **관리자 및 게스트 초대자 역할의 사용자가 초대할 수 있음** 을 **예** 로 설정한 경우 **게스트 초대자** 역할의 사용자가 게스트를 계속 초대할 수 있습니다.

6. **사용자 흐름을 통한 게스트 셀프 서비스 등록 사용하기** 에서 사용자가 앱에 등록할 수 있는 사용자 흐름을 만들 수 있도록 하려면 **예** 를 선택합니다. 이 설정에 대한 자세한 내용은 [앱에 셀프 서비스 등록 사용자 흐름 추가](self-service-sign-up-user-flow.md)를 참조하세요.

    ![사용자 흐름 설정을 통한 셀프 서비스 등록](./media/delegate-invitations/self-service-sign-up-setting.png)

7. **협업 제한** 에서 지정하는 도메인에 대한 초대를 허용할지 아니면 거부할지를 선택하고 텍스트 상자에 특정 도메인 이름을 입력할 수 있습니다. 여러 도메인 경우 도메인을 하나씩 새 줄에 입력합니다. 자세한 내용은 [특정 조직의 B2B 사용자 초대 허용 또는 차단](allow-deny-list.md)을 참조하세요.

    ![협업 제한 설정](./media/delegate-invitations/collaboration-restrictions.png)
## <a name="assign-the-guest-inviter-role-to-a-user"></a>사용자에게 게스트 초대자 역할 할당

게스트 초대자 역할을 사용하면 전역 관리자 또는 다른 관리자 역할을 할당하지 않고도 개별 사용자에게 게스트를 초대할 수 있는 기능을 제공할 수 있습니다. 개인에게 게스트 초대자 역할을 할당합니다. 그런 다음, **관리자 및 게스트 초대자 역할의 사용자가 초대할 수 있음** 을 **예** 로 설정합니다.

다음은 PowerShell을 사용하여 게스트 초대자 역할에 사용자를 추가하는 방법을 보여주는 예입니다.

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [초대 없이 B2B Collaboration 게스트 사용자 추가](add-user-without-invite.md)
- [역할에 B2B 협업 사용자 추가](./add-users-administrator.md)