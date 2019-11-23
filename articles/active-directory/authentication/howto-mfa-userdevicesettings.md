---
title: Manage users and devices Azure MFA - Azure Active Directory
description: How can administrators change user settings such as forcing the users to do the proof-up process again.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fd03ea807e48f6f0e287bb4497e4d20268995db
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404175"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 설정 관리

관리자는 다음 사용자 및 디바이스 설정을 관리할 수 있습니다.

* 사용자가 연락 방법을 다시 제공하도록 요청
* 앱 암호 삭제
* 모든 신뢰할 수 있는 디바이스에서 MFA 요청

## <a name="manage-authentication-methods"></a>Manage authentication methods

As an administrator assigned the Authentication Administrator role you can require users to reset their password, re-register for MFA, or revoke existing MFA sessions from their user object.

![Manage authentication methods from the Azure portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. [Azure portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.
1. Choose the user you wish to perform an action on and select **Authentication methods**.
   - **Reset Password** will reset the user's password and assign a temporary password that must be changed on the next sign in.
   - **Require Re-register MFA** will make it so that when the user signs in next time, they will be requested to setup a new MFA authentication method.
   - **Revoke MFA Sessions** clears the user's remembered MFA sessions and requires them to perform MFA the next time it is required by the policy on the device.

## <a name="delete-users-existing-app-passwords"></a>사용자 기존 앱 암호 삭제

이 설정은 사용자가 만든 모든 앱 암호를 삭제합니다. 이러한 앱 암호와 연결된 브라우저가 아닌 앱은 새 앱 암호가 생성될 때까지 작동 중지됩니다. Global administrator permissions are required to perform this action.

### <a name="how-to-delete-users-existing-app-passwords"></a>사용자 기존 앱 암호를 삭제하는 방법

1. [Azure portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.
3. 오른쪽의 도구 모음에서 **Multi-Factor Authentication**을 선택합니다. 다단계 인증 페이지가 열립니다.
4. 관리하려는 사용자 한 명 또는 여러 명 옆의 상자를 선택합니다. A list of quick step options appears on the right.
5. **Manage user settings**(사용자 설정 관리)를 선택합니다.
6. **선택한 사용자에 의해 생성된 모든 기존 앱 암호 삭제** 상자를 선택합니다.
   ![Delete all existing app passwords](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. **저장**을 클릭합니다.
8. **닫기**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Multi-Factor Authentication 설정 구성](howto-mfa-mfasettings.md) 방법에 대해 자세히 알아보기
- 사용자에게 지원이 필요한 경우 [2단계 인증에 대한 사용자 가이드](../user-help/multi-factor-authentication-end-user.md) 안내
