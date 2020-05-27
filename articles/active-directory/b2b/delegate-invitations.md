---
title: B2B 외부 협업 설정 사용 - Azure AD
description: Active Directory B2B 외부 협업을 사용하도록 설정하고 게스트 사용자를 초대할 수 있는 사용자를 관리하는 방법을 알아봅니다. 게스트 초대자 역할을 사용하여 초대를 위임합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf872991adbf1361a482f6618e57304e33ec1261
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591287"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>B2B 외부 협업을 사용하도록 설정 및 게스트를 초대할 수 있는 사용자 관리

이 문서에서는 Azure AD(Azure Active Directory) B2B 협업을 사용하도록 설정하고 게스트를 초대할 수 있는 사용자를 결정하는 방법을 설명합니다. 기본적으로 디렉터리에 있는 모든 사용자와 게스트는 관리자 역할에 할당되지 않은 경우에도 게스트를 초대할 수 있습니다. 외부 협업 설정을 사용하여 조직에서 다양한 유형의 사용자에 대해 게스트 초대를 설정하거나 해제할 수 있습니다. 또한 게스트 초대를 허용하는 역할을 할당하여 개별 사용자에게 초대를 위임할 수도 있습니다.

## <a name="configure-b2b-external-collaboration-settings"></a>B2B 외부 협업 설정 구성

Azure AD B2B 협업을 사용하면 테넌트 관리자가 다음 초대 정책을 설정할 수 있습니다.

- 초대 해제
- 관리자 및 게스트 초대자 역할의 사용자만 초대 가능
- 관리자, 게스트 초대자 역할 및 구성원은 초대 가능
- 게스트를 포함한 모든 사용자가 초대를 수행할 수 있습니다.

기본적으로 게스트를 포함한 모든 사용자가 게스트 사용자를 초대할 수 있습니다.

### <a name="to-configure-external-collaboration-settings"></a>외부 협업 설정을 구성하려면 다음을 수행합니다.

1. 테넌트 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory**를 선택합니다.
3. **외부 ID** > **외부 협업 설정**을 선택합니다.
6. **외부 협업 설정** 페이지에서 사용하도록 설정할 정책을 선택합니다.

   ![외부 협업 설정](./media/delegate-invitations/control-who-to-invite.png)

  - **게스트 사용자 권한이 제한됨**: 이 정책은 디렉터리에서 게스트에 대한 사용 권한을 결정합니다. 사용자, 그룹 또는 기타 디렉터리 리소스를 열거하는 등의 특정 디렉터리 작업에서 게스트를 차단하려면 **예**를 선택합니다. 디렉터리에서 일반 사용자로 디렉터리 데이터에 대한 동일한 액세스 권한을 게스트에게 제공하려면 **아니오**를 선택합니다.
   - **관리자 및 게스트 초대자 역할의 사용자가 초대할 수 있음**: "게스트 초대자" 역할의 관리자와 사용자가 게스트를 초대하도록 허용하려면 이 정책을 **예**로 설정합니다.
   - **구성원이 초대할 수 있음**: 디렉터리의 관리자가 아닌 구성원이 게스트를 초대하는 것을 허용하려면 이 정책을 **예**로 설정합니다.
   - **게스트가 초대할 수 있음**: 게스트가 다른 게스트를 초대하도록 허용하려면 이 정책을 **예**로 설정합니다.
   - **게스트에 이메일 일회용 암호 사용(미리 보기)** : 일회용 암호 기능에 대한 자세한 내용은 [이메일 일회용 암호 인증(미리 보기)](one-time-passcode.md)을 참조하세요.
   - **협업 제한 사항**: 특정 도메인에 대한 초대를 허용하거나 차단하는 방법에 대한 자세한 내용은 [특정 조직의 B2B 사용자 초대 허용 또는 차단](allow-deny-list.md)을 참조하세요.

## <a name="assign-the-guest-inviter-role-to-a-user"></a>사용자에게 게스트 초대자 역할 할당

게스트 초대자 역할을 사용하면 전역 관리자 또는 다른 관리자 역할을 할당하지 않고도 개별 사용자에게 게스트를 초대할 수 있는 기능을 제공할 수 있습니다. 개인에게 게스트 초대자 역할을 할당합니다. 그런 다음, **관리자 및 게스트 초대자 역할의 사용자가 초대할 수 있음**을 **예**로 설정합니다.

다음은 PowerShell을 사용하여 게스트 초대자 역할에 사용자를 추가하는 방법을 보여주는 예입니다.

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [초대 없이 B2B Collaboration 게스트 사용자 추가](add-user-without-invite.md)
- [역할에 B2B 협업 사용자 추가](add-guest-to-role.md)


