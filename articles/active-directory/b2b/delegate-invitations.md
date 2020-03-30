---
title: B2B 외부 공동 작업 설정 사용 - Azure AD
description: Active Directory B2B 외부 공동 작업을 사용하도록 설정하고 게스트 사용자를 초대할 수 있는 사용자를 관리하는 방법을 알아봅니다. 게스트 초대자 역할을 사용하여 초대를 위임합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fb10863334392b207c7cfd2172dc9260cf15e2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272894"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>B2B 외부 협업 활성화 및 게스트 초대 할 수있는 사람 관리

이 문서에서는 Azure Active Directory(Azure AD) B2B 공동 작업을 사용하도록 설정하고 게스트를 초대할 수 있는 사용자를 결정하는 방법을 설명합니다. 기본적으로 디렉터리의 모든 사용자와 게스트는 관리자 역할에 할당되지 않은 경우에도 게스트를 초대할 수 있습니다. 외부 공동 작업 설정을 사용하면 조직의 다양한 유형의 사용자에 대해 게스트 초대를 켜거나 끌 수 있습니다. 게스트를 초대할 수 있는 역할을 할당하여 개별 사용자에게 초대를 위임할 수도 있습니다.

## <a name="configure-b2b-external-collaboration-settings"></a>B2B 외부 공동 작업 설정 구성

Azure AD B2B 협업을 사용하면 테넌트 관리자가 다음 초대 정책을 설정할 수 있습니다.

- 초대 해제
- 관리자 및 게스트 초대자 역할의 사용자만 초대 가능
- 관리자, 게스트 초대자 역할 및 구성원은 초대 가능
- 게스트를 포함한 모든 사용자가 초대를 수행할 수 있습니다.

기본적으로 게스트를 포함한 모든 사용자는 게스트 사용자를 초대할 수 있습니다.

### <a name="to-configure-external-collaboration-settings"></a>외부 공동 작업 설정을 구성하려면 다음을 수행하십시오.

1. [Azure 포털에](https://portal.azure.com) 테넌트 관리자로 로그인합니다.
2. **Azure Active 디렉터리** > **사용자** > **사용자 설정 선택합니다.**
3. **외부 사용자**에서 외부 공동 작업 **설정 관리를**선택합니다.
   > [!NOTE]
   > **외부 협업 설정**은 **조직 관계** 페이지에서도 사용할 수 있습니다. Azure Active Directory의 **관리** 아래에 있는 **조직 관계** > **설정**으로 이동합니다.
4. 외부 **공동 작업 설정** 페이지에서 사용하려는 정책을 선택합니다.

   ![외부 협업 설정](./media/delegate-invitations/control-who-to-invite.png)

  - **게스트 사용자 권한은 제한되어 있습니다:** 이 정책에 따라 디렉터리에서 게스트에 대한 사용 권한이 결정됩니다. **사용자,** 그룹 또는 기타 디렉터리 리소스 를 등록하는 등 특정 디렉터리 작업에서 게스트를 차단하려면 예를 선택합니다. 게스트에게 디렉터리 데이터에 대한 액세스 권한을 디렉터리에 동일하게 지정하려면 **아니요를** 선택합니다.
   - **게스트 초대자 역할의 관리자와 사용자는 초대할 수 있습니다:**"게스트 초대자" 역할의 관리자와 사용자가 게스트를 초대할 수 있도록 하려면 이 정책을 **예로 설정합니다.**
   - **구성원은 초대할 수 있습니다:** 디렉터리에서 관리자가 아닌 구성원이 게스트를 초대할 수 있도록 하려면 이 정책을 **예로 설정합니다.**
   - **게스트 초대:** 게스트가 다른 게스트를 초대할 수 있도록 이 정책을 **예로 설정합니다.**
   - **게스트를 위한 이메일 일회용 암호 사용(미리 보기)**: 일회용 암호 기능에 대한 자세한 내용은 [전자 메일 일회성 암호 인증(미리 보기)을](one-time-passcode.md)참조하십시오.
   - **공동 작업 제한**: 특정 도메인에 대한 초대 허용 또는 차단에 대한 자세한 내용은 [특정 조직의 B2B 사용자에게 초대 허용 또는 차단을](allow-deny-list.md)참조하세요.

## <a name="assign-the-guest-inviter-role-to-a-user"></a>사용자에게 게스트 초대자 역할 할당

게스트 초대자 역할을 사용하면 개별 사용자에게 글로벌 관리자 또는 기타 관리자 역할을 할당하지 않고 게스트를 초대할 수 있습니다. 개인에게 게스트 초대자 역할을 할당합니다. 그런 다음 **게스트 초대자 역할의 관리자와 사용자가** **Yes에**초대할 수 있도록 설정해야 합니다.

다음은 PowerShell을 사용하여 게스트 초대자 역할에 사용자를 추가하는 방법을 보여주는 예입니다.

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [초대 없이 B2B Collaboration 게스트 사용자 추가](add-user-without-invite.md)
- [역할에 B2B 협업 사용자 추가](add-guest-to-role.md)


