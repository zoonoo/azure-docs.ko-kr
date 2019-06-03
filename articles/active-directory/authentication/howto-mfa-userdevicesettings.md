---
title: 관리자 관리 사용자 및 장치-Azure MFA-Azure Active Directory
description: 관리자는 사용자가 증명 프로세스를 다시 수행 하는 등의 사용자 설정을 변경 하는 방법 수 있습니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d4848a00fd645bcf23342f27fe820ccf034a8b
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298834"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 설정 관리

관리자는 다음 사용자 및 디바이스 설정을 관리할 수 있습니다.

* 사용자가 연락 방법을 다시 제공하도록 요청
* 앱 암호 삭제
* 모든 신뢰할 수 있는 디바이스에서 MFA 요청

## <a name="require-users-to-provide-contact-methods-again"></a>사용자가 연락 방법을 다시 제공하도록 요청

이 설정은 사용자가 등록 프로세스를 다시 완료하도록 합니다. 비브라우저 앱은 사용자에게 해당 앱 암호가 있는 경우 계속 작동됩니다.  또한 **선택한 사용자에 의해 생성된 모든 기존 앱 암호 삭제**를 선택하여 사용자 앱 암호를 삭제할 수 있습니다.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>연락처 메서드를 다시 제공할 사용자를 요구하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.
3. 오른쪽의 도구 모음에서 **Multi-Factor Authentication**을 선택합니다. 다단계 인증 페이지가 열립니다.
4. 관리하려는 사용자 한 명 또는 여러 명 옆의 상자를 선택합니다. 오른쪽의 빠른 단계 옵션 목록이 표시 됩니다.
5. **Manage user settings**(사용자 설정 관리)를 선택합니다.
6. **Require selected users to provide contact methods again**(선택한 사용자가 연락 방법을 다시 제공하도록 요청) 상자를 선택합니다.
   ![사용자가 연락 방법을 다시 제공 하도록 요구](./media/howto-mfa-userdevicesettings/reproofup.png)
7. **저장**을 클릭합니다.
8. **닫기**를 클릭합니다.

조직 지침으로 다음을 사용 하 여 선택을 취소 하는 PowerShell을 사용 하 여 이러한 단계를 완료할 수는 `StrongAuthenticationMethods` 특성:

```PowerShell
$Upn = "theuser@domain.com"
$noMfaConfig = @()
Set-MsolUser -UserPrincipalName $Upn -StrongAuthenticationMethods $noMfaConfig
```

## <a name="delete-users-existing-app-passwords"></a>사용자 기존 앱 암호 삭제

이 설정은 사용자가 만든 모든 앱 암호를 삭제합니다. 이러한 앱 암호와 연결된 브라우저가 아닌 앱은 새 앱 암호가 생성될 때까지 작동 중지됩니다.

### <a name="how-to-delete-users-existing-app-passwords"></a>사용자 기존 앱 암호를 삭제하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.
3. 오른쪽의 도구 모음에서 **Multi-Factor Authentication**을 선택합니다. 다단계 인증 페이지가 열립니다.
4. 관리하려는 사용자 한 명 또는 여러 명 옆의 상자를 선택합니다. 오른쪽의 빠른 단계 옵션 목록이 표시 됩니다.
5. **Manage user settings**(사용자 설정 관리)를 선택합니다.
6. **선택한 사용자에 의해 생성된 모든 기존 앱 암호 삭제** 상자를 선택합니다.
   ![모든 기존 앱 암호 삭제](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. **저장**을 클릭합니다.
8. **닫기**를 클릭합니다.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>사용자에 대해 기억된 모든 디바이스에서 MFA 복원

Azure Multi-Factor Authentication의 구성 가능한 기능 중 하나는 사용자에게 디바이스를 신뢰할 수 있는 것으로 표시하는 옵션을 제공하는 것입니다. 자세한 내용은 [Azure Multi-Factor Authentication 설정 구성](howto-mfa-mfasettings.md#remember-multi-factor-authentication)을 참조하세요.

사용자는 일반 디바이스에서 구성 가능한 일 수 동안 2단계 인증을 옵트아웃할 수 있습니다. 계정이 손상되거나 신뢰할 수 있는 디바이스를 분실한 경우 신뢰할 수 있는 상태를 제거할 수 있어야 하고 다시 2단계 인증이 필요합니다.

옵션을 선택 하면 **모든 기억 된 장치에서 multi-factor authentication 복원** 사용자가 장치의 신뢰할 수 있는 상태로 표시 하는 경우에 다음에 로그인 하는 2 단계 인증을 수행 해야 합니다.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>사용자에 대해 일시 중단된 모든 디바이스에서 MFA를 복원하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.
3. 오른쪽의 도구 모음에서 **Multi-Factor Authentication**을 선택합니다. 다단계 인증 페이지가 열립니다.
4. 관리하려는 사용자 한 명 또는 여러 명 옆의 상자를 선택합니다. 오른쪽의 빠른 단계 옵션 목록이 표시 됩니다.
5. **Manage user settings**(사용자 설정 관리)를 선택합니다.
6. 확인란 **모든 기억 된 장치에서 multi-factor authentication 복원**
   ![모든 기억 된 장치에서 multi-factor authentication 복원](./media/howto-mfa-userdevicesettings/rememberdevices.png)
7. **저장**을 클릭합니다.
8. **닫기**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Multi-Factor Authentication 설정 구성](howto-mfa-mfasettings.md) 방법에 대해 자세히 알아보기
- 사용자에게 지원이 필요한 경우 [2단계 인증에 대한 사용자 가이드](../user-help/multi-factor-authentication-end-user.md) 안내
